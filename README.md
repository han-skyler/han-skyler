# 2021

- **임베디드 SW 설계**
    - Android Studio App 개발
    - 그림을 통한 아이 학습 앱
    - 역할 및 담당업무
        - 팀장
        - Firebase Realtime database 관리, Youtube API 연결, 메인페이지 UI, 로그인 기능 구현, todolist 기능 구현
    - Github : https://github.com/han-skyler/2022_Embeded_SW_Design
    - 코드설명
        - 로그인 페이지
            
            ### 1. 클래스 및 변수 선언
            
            ```java
            public class LoginActivity extends AppCompatActivity {
                private FirebaseAuth mFirebaseAuth;     // 파이어베이스 인증 처리
                private DatabaseReference mDatabasRef;  // 실시간 데이터베이스
                private EditText mEtEmail, mEtPwd;      // 로그인 입력 필드
            }
            
            ```
            
            - `LoginActivity` 클래스는 `AppCompatActivity`를 상속받아 안드로이드 액티비티로서의 기능을 수행합니다.
            - Firebase 인증과 데이터베이스를 사용하기 위한 객체와 로그인 입력 필드를 위한 `EditText` 변수를 선언합니다.
            
            ### 2. onCreate 메서드
            
            ```java
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_login);
            
                mFirebaseAuth = FirebaseAuth.getInstance();
                mDatabasRef = FirebaseDatabase.getInstance().getReference("EightClass");
            
                mEtEmail = findViewById(R.id.et_email);
                mEtPwd = findViewById(R.id.et_pwd);
            }
            
            ```
            
            - `onCreate` 메서드에서 레이아웃을 설정하고 Firebase 인스턴스와 데이터베이스 참조를 초기화합니다.
            - 로그인에 사용할 `EditText` 필드를 UI에서 가져옵니다.
            
            ### 3. 로그인 처리
            
            ```java
            ImageView imageView = findViewById(R.id.ibtn_login);
            imageView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    String strEmail = mEtEmail.getText().toString();
                    String strPwd = mEtPwd.getText().toString();
            
                    mFirebaseAuth.signInWithEmailAndPassword(strEmail, strPwd).addOnCompleteListener(LoginActivity.this, new OnCompleteListener<AuthResult>() {
                        @Override
                        public void onComplete(@NonNull Task<AuthResult> task) {
                            if (task.isSuccessful()) {
                                // 로그인 성공
                                Intent intent = new Intent(LoginActivity.this, SelectActivity.class);
                                startActivity(intent);
                                finish(); // 현재 액티비티 파괴
                            } else {
                                // 로그인 실패
                                Toast.makeText(LoginActivity.this, "로그인 실패", Toast.LENGTH_SHORT).show();
                            }
                        }
                    });
                }
            });
            
            ```
            
            - 로그인 버튼 클릭 시 이메일과 비밀번호를 가져와 Firebase 인증을 사용하여 로그인을 시도합니다.
            - 로그인 성공 시 `SelectActivity`로 전환하고 현재 액티비티를 종료합니다.
            - 실패 시, 사용자에게 로그인 실패 메시지를 표시합니다.
            
            ### 4. 회원가입 화면 이동
            
            ```java
            Button btn_resister = findViewById(R.id.btn_register);
            btn_resister.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    // 회원가입 화면으로 이동
                    Intent intent = new Intent(LoginActivity.this, ResisterActivity.class);
                    startActivity(intent);
                }
            });
            
            ```
            
            - 회원가입 버튼 클릭 시 `ResisterActivity`로 이동하는 인텐트를 설정합니다.
        - 회원가입 페이지
            
            ### 1. 클래스 및 변수 선언
            
            ```java
            public class ResisterActivity extends AppCompatActivity {
                private FirebaseAuth mFirebaseAuth;     // 파이어베이스 인증 처리
                private DatabaseReference mDatabasRef;  // 실시간 데이터베이스
                private EditText mEtEmail, mEtPwd;      // 회원가입 입력 필드
                private Button mBtnRegister;             // 회원가입 버튼
            }
            ```
            
            - `ResisterActivity` 클래스는 사용자 회원가입을 처리하기 위해 `AppCompatActivity`를 상속받습니다.
            - Firebase 인증과 데이터베이스를 위한 객체, 입력 필드 및 버튼을 선언합니다.
            
            ### 2. onCreate 메서드
            
            ```java
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_resister);
            
                mFirebaseAuth = FirebaseAuth.getInstance();
                mDatabasRef = FirebaseDatabase.getInstance().getReference();
            
                mEtEmail = findViewById(R.id.et_email);
                mEtPwd = findViewById(R.id.et_pwd);
                mBtnRegister = findViewById(R.id.btn_register);
            }
            ```
            
            - `onCreate` 메서드에서 레이아웃을 설정하고 Firebase 인스턴스를 초기화합니다.
            - UI에서 입력 필드와 버튼을 가져와서 변수에 할당합니다.
            
            ### 3. 회원가입 처리
            
            ```java
            mBtnRegister.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    String strEmail = mEtEmail.getText().toString();
                    String strPwd = mEtPwd.getText().toString();
            
                    // Firebase Auth 진행
                    mFirebaseAuth.createUserWithEmailAndPassword(strEmail, strPwd).addOnCompleteListener(ResisterActivity.this, new OnCompleteListener<AuthResult>() {
                        @Override
                        public void onComplete(@NonNull Task<AuthResult> task) {
                            if (task.isSuccessful()) {
                                FirebaseUser firebaseUser = mFirebaseAuth.getCurrentUser();
                                UserAccount account = new UserAccount();
                                account.setIdToken(firebaseUser.getUid());
                                account.setEmailId(firebaseUser.getEmail());
                                account.setPassword(strPwd);
            
                                // setValue : database에 insert(삽입) 행위
                                mDatabasRef.child("UserAccount").child(firebaseUser.getUid()).setValue(account);
            
                                Intent intent = new Intent(ResisterActivity.this, LoginActivity.class);
                                startActivity(intent);
                                Toast.makeText(ResisterActivity.this, "회원가입 성공!", Toast.LENGTH_SHORT).show();
                                finish();
                            } else {
                                Toast.makeText(ResisterActivity.this, "회원가입 실패", Toast.LENGTH_SHORT).show();
                            }
                        }
                    });
                }
            });
            ```
            
            - 회원가입 버튼 클릭 시 이메일과 비밀번호를 가져와 Firebase에 사용자 생성 요청을 보냅니다.
            - 성공적으로 계정을 생성하면 `UserAccount` 객체를 만들어 사용자 정보를 데이터베이스에 저장합니다.
            - 회원가입 성공 시 `LoginActivity`로 이동하고 성공 메시지를 표시합니다.
            - 실패 시, 사용자에게 실패 메시지를 보여줍니다.
            
            ### 4. UserAccount 클래스
            
            ```java
            public class UserAccount {
                private String idToken;
                private String emailId;
                private String password;
            
                // Getter 및 Setter 메서드
            }
            ```
            
            - 사용자 정보를 저장하기 위한 `UserAccount` 클래스입니다. 사용자 ID, 이메일, 비밀번호를 포함합니다.
            - 추가적인 메서드를 통해 데이터베이스에 저장할 수 있는 형태로 정의합니다.
        - 부모 페이지
            
            ### 1. 클래스 및 onCreate 메서드
            
            ```java
            public class ParentsActivity extends AppCompatActivity {
                @Override
                protected void onCreate(Bundle savedInstanceState) {
                    super.onCreate(savedInstanceState);
                    setContentView(R.layout.activity_parents);
            
            ```
            
            - `ParentsActivity` 클래스는 사용자 인터페이스와 상호작용을 처리하는 안드로이드 액티비티입니다.
            - `onCreate` 메서드에서 액티비티가 생성될 때 호출되어 UI 레이아웃을 설정합니다.
            
            ### 2. 버튼 클릭 이벤트 설정
            
            ```java
            ImageView btn_setting = findViewById(R.id.btn_setting);
            btn_setting.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Intent intent = new Intent(ParentsActivity.this, SettingActivity.class);
                    startActivity(intent);
                }
            });
            
            ```
            
            - `btn_setting` 버튼 클릭 시 `SettingActivity`로 이동하는 인텐트를 설정합니다.
            
            ### 3. 계정 변경 기능
            
            ```java
            ImageView btn_user = findViewById(R.id.btn_user);
            btn_user.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Toast.makeText(ParentsActivity.this, "계정을 변경합니다", Toast.LENGTH_SHORT).show();
                    Intent intent2 = new Intent(ParentsActivity.this, SelectActivity.class);
                    startActivity(intent2);
                }
            });
            
            ```
            
            - `btn_user` 버튼 클릭 시 계정 변경 메시지를 Toast로 표시하고 `SelectActivity`로 이동합니다.
            
            ### 4. 투두리스트 및 그림판 기능
            
            ```java
            ImageView btn_todo = findViewById(R.id.btn_todo);
            btn_todo.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Intent intent3 = new Intent(ParentsActivity.this, MainActivity.class);
                    startActivity(intent3);
                }
            });
            
            ImageView btn_canvas = findViewById(R.id.btn_canvas);
            btn_canvas.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Intent intent4 = new Intent(ParentsActivity.this, DrawActivity.class);
                    startActivity(intent4);
                }
            });
            
            ```
            
            - `btn_todo` 클릭 시 `MainActivity`로, `btn_canvas` 클릭 시 `DrawActivity`로 이동하는 인텐트를 설정합니다.
            
            ### 5. 외부 링크 열기
            
            ```java
            public void clickcafe(View view) {
                Intent intentUrl1 = new Intent(Intent.ACTION_VIEW, Uri.parse("<https://cafe.naver.com/eightedu?iframe_url=/MyCafeIntro.nhn%3Fclubid=30573061>"));
                startActivity(intentUrl1);
            }
            
            public void clicknews(View view) {
                Intent intentUrl2 = new Intent(Intent.ACTION_VIEW, Uri.parse("<https://www.koreacen.com/>"));
                startActivity(intentUrl2);
            }
            
            public void clickchild(View view) {
                Intent intentUrl3 = new Intent(Intent.ACTION_VIEW, Uri.parse("<https://www.childfund.or.kr/main.do>"));
                startActivity(intentUrl3);
            }
            
            public void clickunicef(View view) {
                Intent intentUrl4 = new Intent(Intent.ACTION_VIEW, Uri.parse("<https://www.unicef.or.kr/>"));
                startActivity(intentUrl4);
            }
            
            ```
            
            - 각 메서드는 버튼 클릭 시 특정 웹 페이지를 열도록 설정되어 있습니다. 각 링크는 `Intent.ACTION_VIEW`를 통해 외부 브라우저에서 열립니다.
        - 아이 페이지
            
            ### 1. 클래스 및 onCreate 메서드
            
            ```java
            public class KidActivity extends AppCompatActivity {
                @Override
                protected void onCreate(Bundle savedInstanceState) {
                    super.onCreate(savedInstanceState);
                    setContentView(R.layout.activity_kid);
            
            ```
            
            - `KidActivity` 클래스는 아동 관련 기능을 제공하는 안드로이드 액티비티입니다.
            - `onCreate` 메서드에서 UI 레이아웃을 설정하고 초기화 작업을 수행합니다.
            
            ### 2. 설정 버튼 클릭 이벤트
            
            ```java
            ImageView btn_setting = findViewById(R.id.btn_setting);
            btn_setting.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Intent intent1 = new Intent(KidActivity.this, SettingActivity.class);
                    startActivity(intent1);
                }
            });
            
            ```
            
            - `btn_setting` 버튼 클릭 시 `SettingActivity`로 이동하는 인텐트를 설정합니다.
            
            ### 3. 계정 변경 기능
            
            ```java
            ImageView btn_user = findViewById(R.id.btn_user);
            btn_user.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Toast.makeText(KidActivity.this, "계정을 변경합니다", Toast.LENGTH_SHORT).show();
                    Intent intent2 = new Intent(KidActivity.this, SelectActivity.class);
                    startActivity(intent2);
                }
            });
            
            ```
            
            - `btn_user` 클릭 시 계정 변경 메시지를 Toast로 표시하고 `SelectActivity`로 이동합니다.
            
            ### 4. 그림판 및 투두리스트 기능
            
            ```java
            ImageView btn_canvas = findViewById(R.id.btn_canvas);
            btn_canvas.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Intent intent7 = new Intent(KidActivity.this, DrawActivity.class);
                    startActivity(intent7);
                }
            });
            
            ImageView btn_todo = findViewById(R.id.btn_todo);
            btn_todo.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Intent intent3 = new Intent(KidActivity.this, MainActivity.class);
                    startActivity(intent3);
                }
            });
            
            ```
            
            - `btn_canvas` 클릭 시 `DrawActivity`로, `btn_todo` 클릭 시 `MainActivity`로 이동하는 인텐트를 설정합니다.
            
            ### 5. 과목 선택 기능
            
            ```java
            ImageView btn_kor = findViewById(R.id.btn_kor);
            btn_kor.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Intent intent4 = new Intent(KidActivity.this, KorActivity.class);
                    startActivity(intent4);
                }
            });
            
            ImageView btn_math = findViewById(R.id.btn_math);
            btn_math.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Intent intent5 = new Intent(KidActivity.this, MathActivity.class);
                    startActivity(intent5);
                }
            });
            
            ImageView btn_eng = findViewById(R.id.btn_eng);
            btn_eng.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Intent intent5 = new Intent(KidActivity.this, EngActivity.class);
                    startActivity(intent5);
                }
            });
            
            ImageView btn_art = findViewById(R.id.btn_art);
            btn_art.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Intent intent6 = new Intent(KidActivity.this, ArtActivity.class);
                    startActivity(intent6);
                }
            });
            
            ```
            
            - 각 버튼 클릭 시 해당 과목의 활동으로 이동하는 인텐트를 설정합니다:
                - 국어: `KorActivity`
                - 수학: `MathActivity`
                - 영어: `EngActivity`
                - 미술: `ArtActivity`
        - todo 페이지
            
            ### 1. 클래스 및 onCreate 메서드
            
            ```java
            public class MainActivity extends AppCompatActivity {
                @Override
                protected void onCreate(Bundle savedInstanceState) {
                    super.onCreate(savedInstanceState);
                    setContentView(R.layout.activity_main);
            
            ```
            
            - `MainActivity` 클래스는 투두 리스트 기능을 제공하는 안드로이드 액티비티입니다.
            - `onCreate` 메서드에서 UI 레이아웃을 설정하고 초기화 작업을 수행합니다.
            
            ### 2. 투두 리스트 추가 버튼
            
            ```java
            Button btnAddTask = findViewById(R.id.btn_add_task);
            btnAddTask.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    // 투두 리스트에 새 작업 추가
                    String task = editTextTask.getText().toString();
                    if (!task.isEmpty()) {
                        addTaskToList(task);
                        editTextTask.setText(""); // 입력 필드 초기화
                    } else {
                        Toast.makeText(MainActivity.this, "작업을 입력하세요", Toast.LENGTH_SHORT).show();
                    }
                }
            });
            
            ```
            
            - `btnAddTask` 클릭 시 입력된 작업을 체크합니다.
            - 작업이 비어 있지 않으면 `addTaskToList` 메서드를 호출하여 리스트에 추가하고 입력 필드를 초기화합니다.
            - 비어 있다면 사용자에게 작업 입력을 요구하는 Toast 메시지를 표시합니다.
            
            ### 3. 작업 리스트 표시
            
            ```java
            private void addTaskToList(String task) {
                tasksList.add(task); // 리스트에 작업 추가
                adapter.notifyDataSetChanged(); // 어댑터에 데이터 변경 알림
            }
            
            ```
            
            - `addTaskToList` 메서드는 입력된 작업을 내부 리스트에 추가하고 어댑터에 데이터 변경을 알립니다.
            - 이를 통해 사용자 인터페이스에 새로운 작업이 즉시 반영됩니다.
            
            ### 4. 작업 삭제 기능
            
            ```java
            adapter.setOnItemClickListener(new TaskAdapter.OnItemClickListener() {
                @Override
                public void onItemClick(int position) {
                    // 리스트에서 작업 삭제
                    tasksList.remove(position);
                    adapter.notifyItemRemoved(position); // 어댑터에 삭제 알림
                    Toast.makeText(MainActivity.this, "작업이 삭제되었습니다", Toast.LENGTH_SHORT).show();
                }
            });
            
            ```
            
            - 어댑터의 클릭 리스너를 설정하여 특정 작업이 클릭되었을 때 해당 작업을 리스트에서 삭제합니다.
            - 삭제 후 어댑터에 해당 작업이 삭제되었음을 알리고, 사용자에게 삭제 성공 메시지를 표시합니다.
            
            ### 5. 데이터 저장 및 로드 기능 (선택 사항)
            
            ```java
            @Override
            protected void onPause() {
                super.onPause();
                saveTasksToStorage(); // 작업 저장
            }
            
            @Override
            protected void onResume() {
                super.onResume();
                loadTasksFromStorage(); // 작업 로드
            }
            
            ```
            
            - `onPause` 메서드에서 작업 리스트를 저장하는 메서드를 호출합니다.
            - `onResume` 메서드에서 이전에 저장된 작업 리스트를 로드합니다.
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/28649092-317d-4ac8-a318-d181bd40c4b0/912da80f-0151-4b65-870c-918ebe664b84/image.png)
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/28649092-317d-4ac8-a318-d181bd40c4b0/83ed1622-94db-40e7-a582-386298ee8863/image.png)
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/28649092-317d-4ac8-a318-d181bd40c4b0/ab13a72a-03bf-4439-afcf-e736f173f2e1/image.png)
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/28649092-317d-4ac8-a318-d181bd40c4b0/41bec63b-4b4e-42d9-b165-c9aa423ccb76/image.png)
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/28649092-317d-4ac8-a318-d181bd40c4b0/7dcf95e6-28bf-41e8-8603-f10c92502df9/image.png)
    

---

# 2022

- **멀티미디어시스템설계**
    - MFC프로그램 개발
    - RGB영상처리 활용한 필름카메라 앱
    
    **구현 내용**
    
    - 흑백 raw파일 영상처리
        
        흑백이미지는 0~255까지의 값을 가짐 
        
        0은 흑색 255는 흰색
        
        - **기본적인 영상처리 코드**
        
        ```cpp
        for (int i = 0; i < width * height; ++i) {
            unsigned char r = buffer[i * 3];
            unsigned char g = buffer[i * 3 + 1];
            unsigned char b = buffer[i * 3 + 2];
            
            // Grayscale conversion
            unsigned char gray = static_cast<unsigned char>(0.3 * r + 0.59 * g + 0.11 * b);
            
            // Applying the grayscale value
            buffer[i * 3] = buffer[i * 3 + 1] = buffer[i * 3 + 2] = gray;
        }
        ```
        
        - **가우시안 흐림효과 :** 이미지를 부드럽게 만드는 필터, 주변 픽셀들의 값을 가중 평균한 값을 중앙 픽셀에 적용
            
            ```cpp
            void applyGaussianBlur(std::vector<unsigned char>& buffer, int width, int height) {
                // 간단한 3x3 가우시안 커널
                const float kernel[3][3] = {
                    {1 / 16.0f, 2 / 16.0f, 1 / 16.0f},
                    {2 / 16.0f, 4 / 16.0f, 2 / 16.0f},
                    {1 / 16.0f, 2 / 16.0f, 1 / 16.0f}
                };
            
                std::vector<unsigned char> tempBuffer = buffer;
            
                for (int y = 1; y < height - 1; ++y) {
                    for (int x = 1; x < width - 1; ++x) {
                        float r = 0, g = 0, b = 0;
            
                        for (int ky = -1; ky <= 1; ++ky) {
                            for (int kx = -1; kx <= 1; ++kx) {
                                int pixelIndex = ((y + ky) * width + (x + kx)) * 3;
                                r += tempBuffer[pixelIndex] * kernel[ky + 1][kx + 1];
                                g += tempBuffer[pixelIndex + 1] * kernel[ky + 1][kx + 1];
                                b += tempBuffer[pixelIndex + 2] * kernel[ky + 1][kx + 1];
                            }
                        }
            
                        int index = (y * width + x) * 3;
                        buffer[index] = static_cast<unsigned char>(r);
                        buffer[index + 1] = static_cast<unsigned char>(g);
                        buffer[index + 2] = static_cast<unsigned char>(b);
                    }
                }
            }
            
            ```
            
        - **에지 검출(Edge Detection)** :  이미지 내에서 명암이 급격하게 변화하는 부분을 찾아내는 기법
            
            ```cpp
            // Sobel Edge Detection 구현 (수직과 수평 필터를 사용)
            void sobelEdgeDetection(std::vector<unsigned char>& image, int width, int height) {
                // Sobel 필터 커널 정의
                int gx[3][3] = {{-1, 0, 1}, {-2, 0, 2}, {-1, 0, 1}}; // x 방향 커널
                int gy[3][3] = {{-1, -2, -1}, {0, 0, 0}, {1, 2, 1}}; // y 방향 커널
                
                std::vector<unsigned char> output(width * height, 0);
            
                for (int y = 1; y < height - 1; ++y) {
                    for (int x = 1; x < width - 1; ++x) {
                        int sumX = 0, sumY = 0;
            
                        // 3x3 윈도우 내의 픽셀에 대해 필터 적용
                        for (int ky = -1; ky <= 1; ++ky) {
                            for (int kx = -1; kx <= 1; ++kx) {
                                int pixelValue = image[(y + ky) * width + (x + kx)];
                                sumX += pixelValue * gx[ky + 1][kx + 1];
                                sumY += pixelValue * gy[ky + 1][kx + 1];
                            }
                        }
            
                        // 에지 강도 계산
                        int magnitude = static_cast<int>(sqrt(sumX * sumX + sumY * sumY));
                        magnitude = std::min(255, std::max(0, magnitude));
            
                        // 에지 강도 값을 출력 이미지에 저장
                        output[y * width + x] = magnitude;
                    }
                }
            
                // 결과를 원본 이미지에 반영
                image = output;
            }
            
            ```
            
        - **확대 및 축소 (Image Scaling)** : Nearest Neighbor 확대할 때는 가장 가까운 픽셀 값을 복사하고, 축소할 때는 일부 픽셀 값을 단순히 제거
            
            ```cpp
            // Nearest Neighbor를 사용한 이미지 확대
            void scaleNearestNeighbor(std::vector<unsigned char>& image, int originalWidth, int originalHeight, int newWidth, int newHeight) {
                std::vector<unsigned char> output(newWidth * newHeight, 0);
            
                float xRatio = (float)originalWidth / newWidth;
                float yRatio = (float)originalHeight / newHeight;
            
                for (int y = 0; y < newHeight; ++y) {
                    for (int x = 0; x < newWidth; ++x) {
                        int nearestX = (int)(x * xRatio);
                        int nearestY = (int)(y * yRatio);
                        output[y * newWidth + x] = image[nearestY * originalWidth + nearestX];
                    }
                }
            
                image = output;
            }
            
            ```
            
        - **모자이크 (Mosaic) :** 이미지의 픽셀을 덩어리로 묶어 각 블록의 평균값으로 설정하여, 전체 이미지를 흐리게 만드는 기법
            
            ```cpp
            // 간단한 모자이크 처리 구현 (blockSize 크기로 픽셀 묶기)
            void mosaic(std::vector<unsigned char>& image, int width, int height, int blockSize) {
                for (int y = 0; y < height; y += blockSize) {
                    for (int x = 0; x < width; x += blockSize) {
                        int sum = 0;
                        int count = 0;
            
                        // 블록 내의 픽셀 값 평균 계산
                        for (int dy = 0; dy < blockSize && (y + dy) < height; ++dy) {
                            for (int dx = 0; dx < blockSize && (x + dx) < width; ++dx) {
                                sum += image[(y + dy) * width + (x + dx)];
                                ++count;
                            }
                        }
            
                        unsigned char average = sum / count;
            
                        // 블록 내의 모든 픽셀을 평균 값으로 설정
                        for (int dy = 0; dy < blockSize && (y + dy) < height; ++dy) {
                            for (int dx = 0; dx < blockSize && (x + dx) < width; ++dx) {
                                image[(y + dy) * width + (x + dx)] = average;
                            }
                        }
                    }
                }
            }
            
            ```
            
        - 히스토그램 평활화 (Histogram Equalization) : **히스토그램 평활화**는 이미지의 **대비**를 개선하는 기법입니다. 밝기 값의 분포가 고르게 퍼지도록 하여, 어두운 이미지의 밝기를 높이거나 너무 밝은 이미지를 어둡게 조정해 더 넓은 범위의 밝기 정보를 제공
            
            ```cpp
            // 히스토그램 평활화 구현
            void histogramEqualization(std::vector<unsigned char>& image, int width, int height) {
                int hist[256] = {0};
                int lut[256] = {0};
                int size = width * height;
            
                // 1. 히스토그램 계산
                for (int i = 0; i < size; ++i) {
                    hist[image[i]]++;
                }
            
                // 2. 누적 분포 함수(CDF) 계산
                lut[0] = hist[0];
                for (int i = 1; i < 256; ++i) {
                    lut[i] = lut[i - 1] + hist[i];
                }
            
                // 3. 모든 픽셀 값에 대해 새로운 값 매핑 (0~255 사이의 값으로 정규화)
                for (int i = 0; i < size; ++i) {
                    image[i] = (unsigned char)((lut[image[i]] * 255) / size);
                }
            }
            
            ```
            
    - jpeg파일 영상처리 (3차원 RGB영상처리) : 필름카메라 효과 적용
        - **기본적인 영상처리 코드**
            
            ```cpp
            void AccessPixelData(CImage& image) {
                int width = image.GetWidth();
                int height = image.GetHeight();
            
                // 픽셀 데이터에 접근
                for (int y = 0; y < height; ++y) {
                    for (int x = 0; x < width; ++x) {
                        // RGB 값을 읽기
                        COLORREF color = image.GetPixel(x, y);
                        BYTE r = GetRValue(color);  // Red 값
                        BYTE g = GetGValue(color);  // Green 값
                        BYTE b = GetBValue(color);  // Blue 값
            
                        // 필요에 따라 픽셀 데이터를 처리하거나 수정
                        // 예시: 모든 픽셀을 회색으로 변환
                        BYTE gray = static_cast<BYTE>(0.3 * r + 0.59 * g + 0.11 * b);
                        image.SetPixelRGB(x, y, gray, gray, gray);
                    }
                }
            }
            
            ```
            
        - **컬러이미지 그레이스케일 변환** : 그레이스케일 변환 공식은 **가중 평균법**을 사용
            
            ```cpp
            gray = 0.299 * R + 0.587 * G + 0.114 * B;
            ```
            
        - **따뜻하게(Warming) : 빨간색**과 **노란색**을 강조하는 방식, 빨간색 채널 값을 늘리고, 파란색 채널 값을 줄여 색감을 따뜻하게 만듬
            
            ```cpp
            void ApplyWarmingEffect(CImage& image) {
                int width = image.GetWidth();
                int height = image.GetHeight();
            
                for (int y = 0; y < height; ++y) {
                    for (int x = 0; x < width; ++x) {
                        COLORREF color = image.GetPixel(x, y);
            
                        BYTE r = GetRValue(color);
                        BYTE g = GetGValue(color);
                        BYTE b = GetBValue(color);
            
                        // 빨간색을 증가시키고, 파란색을 감소시켜 따뜻한 색감
                        r = min(r + 20, 255);
                        b = max(b - 20, 0);
            
                        image.SetPixelRGB(x, y, r, g, b);
                    }
                }
            }
            ```
            
        - **차갑게(Cooling)** : **파란색**을 강조하고 **빨간색**을 줄이는 방식, 파란색 채널 값을 늘리고, 빨간색을 줄이는 방식으로 차가운 느낌 만듬
            
            ```cpp
            void ApplyCoolingEffect(CImage& image) {
                int width = image.GetWidth();
                int height = image.GetHeight();
            
                for (int y = 0; y < height; ++y) {
                    for (int x = 0; x < width; ++x) {
                        COLORREF color = image.GetPixel(x, y);
            
                        BYTE r = GetRValue(color);
                        BYTE g = GetGValue(color);
                        BYTE b = GetBValue(color);
            
                        // 파란색을 증가시키고, 빨간색을 감소시켜 차가운 색감
                        b = min(b + 20, 255);
                        r = max(r - 20, 0);
            
                        image.SetPixelRGB(x, y, r, g, b);
                    }
                }
            }
            ```
            
        - **가우시안 노이즈 생성 :** 각 픽셀의 값에 가우시안 분포를 따르는 랜덤한 값을 추가하여 이미지를 노이즈로 변형하는 방법, 자연스러운 노이즈를 생성
            1. 가우시안 노이즈를 위한 난수 생성기
                
                ```cpp
                #include <cstdlib>
                #include <cmath>
                #include <ctime>
                
                double GenerateGaussianNoise(double mean, double stddev) {
                    static bool hasSpare = false;
                    static double spare;
                    if (hasSpare) {
                        hasSpare = false;
                        return mean + stddev * spare;
                    }
                
                    hasSpare = true;
                    double u, v, s;
                    do {
                        u = (rand() / ((double)RAND_MAX)) * 2.0 - 1.0;
                        v = (rand() / ((double)RAND_MAX)) * 2.0 - 1.0;
                        s = u * u + v * v;
                    } while (s >= 1.0 || s == 0);
                
                    s = sqrt(-2.0 * log(s) / s);
                    spare = v * s;
                    return mean + stddev * u * s;
                }
                ```
                
            2. 가우시안 노이즈를 이미지에 적용
                
                ```cpp
                void ApplyGaussianNoise(CImage& image, double mean = 0.0, double stddev = 30.0) {
                    int width = image.GetWidth();
                    int height = image.GetHeight();
                
                    srand(static_cast<unsigned>(time(0)));  // 난수 생성기 시드 설정
                
                    for (int y = 0; y < height; ++y) {
                        for (int x = 0; x < width; ++x) {
                            COLORREF color = image.GetPixel(x, y);
                
                            BYTE r = GetRValue(color);
                            BYTE g = GetGValue(color);
                            BYTE b = GetBValue(color);
                
                            // 각 채널에 가우시안 노이즈 추가
                            int noiseR = static_cast<int>(GenerateGaussianNoise(mean, stddev));
                            int noiseG = static_cast<int>(GenerateGaussianNoise(mean, stddev));
                            int noiseB = static_cast<int>(GenerateGaussianNoise(mean, stddev));
                
                            // 채널 값에 노이즈를 더하고, 범위(0~255)를 초과하지 않도록 처리
                            r = min(max(r + noiseR, 0), 255);
                            g = min(max(g + noiseG, 0), 255);
                            b = min(max(b + noiseB, 0), 255);
                
                            // 노이즈가 추가된 새로운 색상으로 픽셀 설정
                            image.SetPixelRGB(x, y, r, g, b);
                        }
                    }
                }
                ```
                
    - 학부연구생을 제안받게 된 프로젝트
    
    **발표자료**
    
    [최종발표 멀티미디어.pdf](https://prod-files-secure.s3.us-west-2.amazonaws.com/28649092-317d-4ac8-a318-d181bd40c4b0/f926d85c-6554-425d-b23b-c1bf10d6f526/%EC%B5%9C%EC%A2%85%EB%B0%9C%ED%91%9C_%EB%A9%80%ED%8B%B0%EB%AF%B8%EB%94%94%EC%96%B4.pdf)
    

---

- **단기인턴십**
    - (주)CANS 한전 협력사 인턴십
    - YOLO 이미지 데이터 전처리, 웹페이지 UI 제작
    - 했던일
        - **비디오 프레임 추출**:
            - 비디오 파일을 열고, 각 프레임을 이미지로 추출합니다. OpenCV와 같은 라이브러리를 사용하여 비디오를 읽고, `cv2.VideoCapture` 클래스를 통해 프레임 단위로 접근할 수 있습니다.
            - `read()` 메소드를 호출하여 프레임을 순차적으로 읽고, 각 프레임을 저장합니다.
        - **이미지 리사이즈**:
            - YOLOv5는 고정된 입력 크기를 요구하므로, 추출한 이미지를 모델의 입력 크기(예: 640x640 픽셀)로 리사이즈합니다. OpenCV의 `cv2.resize()` 함수를 사용하여 이 작업을 수행합니다.
        - **정규화**:
            - 이미지를 정규화하여 픽셀 값의 범위를 0과 1 사이로 조정합니다. 이를 위해 각 픽셀 값을 255로 나누는 방식이 일반적입니다.
        - **데이터 증강** (선택적):
            - 데이터 증강 기법을 통해 학습 데이터의 다양성을 높이고 모델의 일반화 능력을 향상시킵니다. 이 과정에서 랜덤한 회전, 크롭, 색상 변환 등을 적용할 수 있습니다.
        - **라벨링**:
            - YOLOv5는 각 객체에 대한 바운딩 박스와 클래스 정보를 필요로 합니다. 이를 위해 이미지에 대한 어노테이션 파일을 생성하고, 각 객체의 위치(좌표)와 클래스를 기록합니다.
            
    
    **발표자료**
    
    [웹사이트최종.pdf](https://prod-files-secure.s3.us-west-2.amazonaws.com/28649092-317d-4ac8-a318-d181bd40c4b0/40d3c540-650e-4784-ab6e-a5bd972c717e/%EC%9B%B9%EC%82%AC%EC%9D%B4%ED%8A%B8%EC%B5%9C%EC%A2%85.pdf)
    

---

- **통신SW설계**
    - TCP Socket
    - 캐치마인드 게임
    - 역할 및 담당 업무
        - 팀장
        - MFC UI제작, Server 및 Client 통신, 메세지 별 역할 정의
    - 코드설명
        - Server
            
            ### 1. 기본 설정 및 애플리케이션 초기화
            
            ```cpp
            CServerApp::CServerApp()
            	: m_pServer(NULL)
            	, m_pChild(NULL)
            	, QueC(_T(""))
            	, AnsC(_T(""))
            	, ThicknessC(0)
            	, cnum(0)
            	, snum(0)
            	, CReadyflag(0)
            {
            	m_dwRestartManagerSupportFlags = AFX_RESTART_MANAGER_SUPPORT_RESTART;
            	m_pServer=NULL;
            	m_pChild=NULL;
            }
            
            ```
            
            - **기본 생성자**: `CServerApp` 클래스의 생성자로, 서버 소켓 및 클라이언트 소켓 포인터를 초기화합니다. 이 외에 질문, 답변, 두께, 클라이언트 수, 점수 수, 준비 상태를 나타내는 변수들도 초기화합니다.
            
            ### 2. 애플리케이션 시작
            
            ```cpp
            BOOL CServerApp::InitInstance()
            {
            	INITCOMMONCONTROLSEX InitCtrls;
            	InitCtrls.dwSize = sizeof(InitCtrls);
            	InitCtrls.dwICC = ICC_WIN95_CLASSES;
            	InitCommonControlsEx(&InitCtrls);
            
            	CWinApp::InitInstance();
            
            	if (!AfxSocketInit())
            	{
            		AfxMessageBox(IDP_SOCKETS_INIT_FAILED);
            		return FALSE;
            	}
            
            	AfxEnableControlContainer();
            	CShellManager *pShellManager = new CShellManager;
            
            	SetRegistryKey(_T("로컬 응용 프로그램 마법사에서 생성된 응용 프로그램"));
            
            	CServerDlg dlg;
            	m_pMainWnd = &dlg;
            	INT_PTR nResponse = dlg.DoModal();
            	if (nResponse == IDOK) { /* 확인 클릭 시 처리 */ }
            	else if (nResponse == IDCANCEL) { /* 취소 클릭 시 처리 */ }
            
            	if (pShellManager != NULL)
            	{
            		delete pShellManager;
            	}
            
            	return FALSE;
            }
            
            ```
            
            - **InitInstance**: 애플리케이션의 초기화 과정입니다. 공용 컨트롤을 설정하고 소켓을 초기화합니다. 대화상자를 생성하고 실행하며, 응용 프로그램의 메인 윈도우를 설정합니다.
            
            ### 3. 서버 소켓 초기화
            
            ```cpp
            void CServerApp::InitServer(void)
            {
            	m_pServer=new CListenSock; // 소켓 생성
            	m_pServer->Create(7777); // 포트 번호 7777로 고정 생성
            	m_pServer->Listen();
            }
            
            ```
            
            - **InitServer**: 서버 소켓을 생성하고, 포트 7777에서 클라이언트의 연결을 수신 대기합니다.
            
            ### 4. 클라이언트 수락
            
            ```cpp
            void CServerApp::Accept(void)
            {
            	CServerDlg *dlg = (CServerDlg*)AfxGetMainWnd();
            	if((cnum<10)&&(dlg->gameflag==0)) // 클라이언트 10명까지로 제한
            	{
            		m_pChild=new clientsock; // 클라이언트 소켓 생성
            		m_pServer->Accept(*m_pChild); // 서버와 클라이언트 연결
            		m_pMainWnd->GetDlgItem(IDC_BUTTON_SEND)->EnableWindow(TRUE); // 채팅창의 보내기 버튼 활성화
            		cnum++; // 클라이언트 수 증가
            		m_pClient.AddTail(m_pChild); // 새로운 클라이언트 추가
            	}
            }
            
            ```
            
            - **Accept**: 클라이언트의 연결 요청을 수락합니다. 최대 10명의 클라이언트만 수용 가능하며, 게임이 시작되지 않은 상태에서만 수락합니다.
            
            ### 5. 클라이언트 소켓 클래스 생성자 및 소멸자
            
            ```cpp
            clientsock::clientsock() { }
            clientsock::~clientsock() { }
            
            ```
            
            - **clientsock**: 클라이언트 소켓을 위한 기본 생성자와 소멸자입니다. 별다른 초기화는 하지 않습니다.
            
            ### 6. 데이터 수신 처리
            
            ```cpp
            void clientsock::OnReceive(int nErrorCode)
            {
            	((CServerApp*)AfxGetApp())->ReceiveData(this);
            	CAsyncSocket::OnReceive(nErrorCode);
            }
            
            ```
            
            - **OnReceive**: 클라이언트로부터 데이터를 수신할 때 호출되는 함수입니다. 수신된 데이터는 `ReceiveData` 메서드를 통해 처리됩니다.
            
            ### 7. 데이터 송신
            
            ```cpp
            void CServerApp::SendData(char& header, CString& id, CString& str)
            {
            	POSITION pos = m_pClient.GetHeadPosition(); // 클라이언트의 위치 지정
            	CServerDlg *dlg = (CServerDlg*)AfxGetMainWnd(); // 대화상자 참조
            	packet.header=header;
            	packet.ID=id;
            	packet.IDlen='0'+packet.ID.GetLength();
            	CString sdata;
            	packet.data=str;
            	sdata.Format(_T("%c%c%s%s"), packet.header, packet.IDlen, packet.ID, packet.data); // 데이터 포맷팅
            
            	while(pos != NULL) // 클라이언트가 연결된 만큼 데이터 송신
            	{
            		m_pChild = m_pClient.GetNext(pos);
            		m_pChild->Send((LPCTSTR)sdata, sizeof(TCHAR)*(sdata.GetLength()+1)); // 클라이언트에 데이터 송신
            	}
            }
            ```
            
            - **SendData**: 특정 클라이언트에게 데이터를 송신하는 함수입니다. 송신할 데이터는 패킷으로 포맷팅되며, 연결된 모든 클라이언트에게 전송됩니다.
            
            ### 8. 데이터 수신 처리 로직
            
            ```cpp
            void CServerApp::ReceiveData(clientsock* ClientAddress)
            {
            	CServerDlg *dlg = (CServerDlg*)AfxGetMainWnd(); // 대화상자 참조
            	char buf[200];
            	ClientAddress->Receive(buf, sizeof(buf)); // 데이터 수신
            	CString data((LPCTSTR) buf); // char -> CString 변환
            	CString strText, strPoint[4], strColor[3], ID;
            	int idlen;
            	CPoint ptStart, ptEnd; // 그림 데이터 수신을 위한 변수
            
            	idlen=_ttoi(data.Mid(1,1)); // 유저 ID 길이 저장
            	ID=data.Mid(2,idlen); // ID 추출
            	// 데이터 유형에 따라 처리
            	if(data.Left(1)=='Q') { /* 문제 데이터 처리 */ }
            	else if(data.Left(1)=='A') { /* 정답 데이터 처리 */ }
            	else if(data.Left(1)=='M') { /* 채팅 메시지 처리 */ }
            	// 추가적인 데이터 처리 로직...
            }
            
            ```
            
            - **ReceiveData**: 수신된 데이터를 유형에 따라 처리하는 함수입니다. 데이터의 첫 글자를 기준으로 문제, 정답, 메시지 등을 구분하여 적절한 처리를 수행합니다.
        - Client

---

# 2023

- **캡스톤디자인**
    - Wi-Fi Fingerprinting 기반 실내 위치 측위를 활용한 서비스 로봇
    - 역할 및 담당업무
        - 팀장
        - HW제어 SW 설계, DC모터 제어, IR센서 제어, IR센서 노이즈 필터링, 라즈베리파이-아두이노 통신, 초음파센서 제어, Firebase DB관리, Wi-Fi RSS data 수집, Wi-Fi 데이터 전처리(moving average filter), WKNN 오류 측정, YOLOv5 사이즈별 학습, 이미지 증강, 이미지 데이터 전처리, 데이터 라벨링, 논문 작성(공동저자), 최단거리 알고리즘 설계, 앱 UI 제작, 이벤트 리스너 오류 수정, 앱 전체 오류 수정, PPT제작 및 발표
    - Github : https://github.com/han-skyler/Library-Kiosk
    - 성과
        - ICTC2023 국제논문 게재, 경진대회 4개 수상 (대상, 금상, 동상 2개), SW저작권 등록
    - **발표자료**
        
        [캡스톤디자인 최종.pdf](https://prod-files-secure.s3.us-west-2.amazonaws.com/28649092-317d-4ac8-a318-d181bd40c4b0/23afd7d6-730a-4eb5-ad9d-6560c751d59c/%EC%BA%A1%EC%8A%A4%ED%86%A4%EB%94%94%EC%9E%90%EC%9D%B8_%EC%B5%9C%EC%A2%85.pdf)
        
        [1005 발표_eng_v3.pdf](https://prod-files-secure.s3.us-west-2.amazonaws.com/28649092-317d-4ac8-a318-d181bd40c4b0/5a46b7c3-5aa0-4a6a-b22e-a2143c027122/1005_%EB%B0%9C%ED%91%9C_eng_v3.pdf)
        
        [Line Tracking Delivery Robot using Wi-Fi-based Indoor Positioning_v0.8.pdf](https://prod-files-secure.s3.us-west-2.amazonaws.com/28649092-317d-4ac8-a318-d181bd40c4b0/915c4432-b787-4fee-9048-f523872283fc/Line_Tracking_Delivery_Robot_using_Wi-Fi-based_Indoor_Positioning_v0.8.pdf)
        

---

- **AI헬스케어융합캡스톤디자인**
    - 의약품 안전을 위한 YOLOv5기반 알약 식별 시스템
    - 역할 및 담당업무
        - 팀장
        - 안드로이드 앱 개발, Firebase DB관리, 의약품 데이터 크롤링, 알약 이미지 데이터 수집, 이미지 데이터 전처리, 이미지 증강(flip, rotate, brightness, shift, crop 등), 이미지 데이터 라벨링, PPT제작 및 발표

---

- **2023 오아시스 해커톤**, **DMC Lab**.
    - 경도인지장애 검사 앱
    - 역할 및 담당업무
        - 기획자(팀장), 기획안 작성 및 개발일정 조율, 개발 및 디자인 팀 진행 사항 정리, 멘토 미팅 및 회의록 정리, 안드로이드 앱 개발, 경도인지장애 테스트페이지, 보호자용 게임 페이지, 커뮤니티 페이지, Firebase DB 관리, 앱 UI제작, 전체 오류 수정 및 테스트, 발표, PM, 프로토타입 제작
    
    **발표자료**
    

# **2024**

- **모빌리티 경진대회**
    - 캡스톤디자인 주제로 대상 수상함
- **SSAFY Embeded Robot Track**
