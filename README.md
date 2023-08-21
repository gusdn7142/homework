## 📝 프로젝트 개요
- 목적 : Spring Boot 기반의 게시글 API 서버 개발
- 기간 : 2023년 8월 12일 ~ 2023년 8월 21일 
- 팀 구성 : 백엔드 1명

</br>

## 💁‍♂️ Wiki 
- 📰 [API 명세서](https://fir-lancer-6bb.notion.site/API-cb5c062b87934332b588e9f0549a3f3e?pvs=4)

</br>

## 🛠 사용 기술
![tech stack](https://github.com/gusdn7142/homework/assets/62496215/60810954-1712-46e0-b849-a97f2a9a2b97)

</br>

## 📦 ERD와 엔티티 설계
### 1. ERD 설계
![MailPlug_ERD](https://github.com/gusdn7142/homework/assets/62496215/f4dfd4a0-98dc-4ef9-b7ee-3c2e5ba8b8fc)  


### 2. 엔티티 설계
<details>
  <summary> 게시글(Post) 엔티티 설계 </summary>
  <div markdown="1">

  ```java
@Getter
@DynamicInsert
@DynamicUpdate
@Entity
@Table(name = "post")
public class Post {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;        //인덱스

    @Column (nullable=false, columnDefinition="varchar(100)")
    private String title;    //제목

    @Column (nullable=false, columnDefinition="varchar(500)")
    private String content;    //내용

    @ManyToOne(fetch = FetchType.LAZY)   
    @JoinColumn(name = "user_id")
    private User user;    //글쓴이 인덱스

    @ManyToOne(fetch = FetchType.LAZY)   
    @JoinColumn(name = "board_id")
    private Board board;    //게시판 인덱스

    @Column (columnDefinition = "bigint default 0")
    private Long lookupCount;    //조회수

    @Column (columnDefinition = "varchar(10) default 'ACTIVE'")
    @Enumerated(EnumType.STRING)
    private DataStatus status; //데이터 상태 (INACTIVE or ACTIVE)

    @Column (columnDefinition = "timestamp default CURRENT_TIMESTAMP")
    private LocalDateTime createdAt;  //데이터 생성 시각

    @Column (columnDefinition = "timestamp default CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP")
    private LocalDateTime updatedAt; //데이터 갱신 시각

    @Builder
    public Post(String title, String content, User user, Board board, Long lookupCount, DataStatus status, LocalDateTime createdAt, LocalDateTime updatedAt) {
        this.title = title;
        this.content = content;
        this.user = user;
        this.board = board;
        this.lookupCount = lookupCount;
        this.status = status;
        this.createdAt = createdAt;
        this.updatedAt = updatedAt;
    }

    public Post(){ }


    public void updateTitle(String title){
        this.title = title;
    }

    public void updateContent(String content){
        this.content = content;
    }

    public void updateStatus(DataStatus status){
        this.status = status;
    }

    public void updateLookupCount(Long lookupCount){
        this.lookupCount = lookupCount;
    }

}

   ```
  </div>
</details>



<details>
  <summary> 게시판(Board) 엔티티 설계 </summary>
  <div markdown="1">

  ```java
@Getter
@DynamicInsert
@DynamicUpdate
@Entity
@Table(name = "board")
public class Board {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;        //인덱스

    @Column (nullable=false, columnDefinition="varchar(100)")
    private String name;    //이름

    @Column (columnDefinition = "varchar(10) default 'ACTIVE'")
    @Enumerated(EnumType.STRING)
    private DataStatus status; //데이터 상태 (INACTIVE or ACTIVE)

    @Column (columnDefinition = "timestamp default CURRENT_TIMESTAMP")
    private LocalDateTime createdAt;  //데이터 생성 시각

    @Column (columnDefinition = "timestamp default CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP")
    private LocalDateTime updatedAt; //데이터 갱신 시각

    public Board(){ }

    @Builder
    public Board(String name, DataStatus status, LocalDateTime createdAt, LocalDateTime updatedAt) {
        this.name = name;
        this.status = status;
        this.createdAt = createdAt;
        this.updatedAt = updatedAt;
    }
}



   ```
  </div>
</details>



<details>
  <summary> 사용자(User) 엔티티 설계 </summary>
  <div markdown="1">

  ```java
@Getter
@DynamicInsert
@DynamicUpdate
@Entity
@Table(name = "user")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;        //인덱스

    @Column (nullable=false, columnDefinition="varchar(10)")
    private String name;    //이름

    @Column (columnDefinition = "varchar(10) default 'ACTIVE'")
    @Enumerated(EnumType.STRING)
    private DataStatus status; //데이터 상태 (INACTIVE or ACTIVE)

    @Column (columnDefinition = "timestamp default CURRENT_TIMESTAMP")
    private LocalDateTime createdAt;  //데이터 생성 시각

    @Column (columnDefinition = "timestamp default CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP")
    private LocalDateTime updatedAt; //데이터 갱신 시각


    @Builder
    public User(String name, DataStatus status, LocalDateTime createdAt, LocalDateTime updatedAt){
        this.name = name;
        this.status = status;
        this.createdAt = createdAt;
        this.updatedAt = updatedAt;
    }

    public User(){ }
}
   ```
  </div>
</details>

</br>


## 🔩 시스템 구성도
### 1. 전체 서비스 구조  
![Architecture](https://github.com/gusdn7142/homework/assets/62496215/2cbffcfc-0489-4c31-9bf9-7f83af66c498)

### 2. 서버 동작 흐름 
![Server Flow](https://github.com/gusdn7142/homework/assets/62496215/609072b6-25b1-4e9f-a9ae-65fea0d7f077)
- 게시글 등록과 게시글 목록 조회 API 호출시의 서버 동작 흐름입니다.
  - Client <-> PostController <-> PostService <-> PostRepository <-> MySQL DB
  - 기타 API의 백엔드 로직 흐름은 아래의 시퀀스 다이어그램을 참고해 주시면 감사합니다. 

### 3. 디렉터리 구조
```bash
📂 src
 └── 📂 main         
      ├── 📂 java.com.example.homework          			
      |    ├── 📂 domain                     #도메인 관리
      |    |    ├── 📂 board                   #게시판 도메인
      |    |    ├── 📂 post                    #게시글 도메인
      |    |    |    ├── 📂 controller           #컨트롤러 계층
      |    |    |    ├── 📂 service              #서비스 계층
      |    |    |    ├── 📂 repository           #레포지토리 계층
      |    |    |    ├── 📂 dto                  #데이터 전송 
      |    |    |    └── 📂 entity               #엔티티 관리
      |    |    ├── 📂 user                    #사용자 도메인
      |    |    └── 📂 model                   #공통으로 사용되는 ENUM 클래스 정의
      |    ├── 📂 global                     #전체적인 설정 관리
      |    |    ├── 📂 config                  #환경설정 관리
      |    |    ├── 📂 exception               #예외 처리 클래스 관리
      |    |    └── 📂 util                    #유틸 클래스 관리
      |    ├── 📂 infra                      #외부 인프라스트럭처 관리
      |    └── 📄 HomeworkApplication.java    #애플리케이션 실행 클래스
      └── 📂 resources
           └── 📄 applicaiton.yml            #DB, 외부서비스, 로그 등의 연결 설정
📄 .gitignore                                #깃허브 업로드시 제외 파일 관리  
📄 build.gradle                                                                   
📄 README.md
```

- 도메인을 기준으로 패키지 구조를 설계했습니다.
  - 도메인 응집도를 높여 도메인의 흐름파악과 패키지 변경 범위를 최소화 하였습니다.


</br>



## 👨🏻‍🏫 구현한 기능

###  예외 처리 
- 예외 발생시 로직 흐름
   ```
   1. BasicResponseCode를 담은 BasicException 객체 생성 후 Controller 밖으로 throw
   2. BasicExceptionHandler에서 BasicException Catch
   3. BasicException 객체에서 BasicResponseCode를 꺼낸 후 BasicResponse 객체에 담아 ResponseEntity 타입으로 리턴
   ```
- 관련 코드
  <details>
    <summary> BasicException 클래스  </summary>
    <div markdown="1">
  
    ```java
    @Getter
    public class BasicException extends RuntimeException{
    
        private final BasicResponseCode basicResponseCode;
    
        public BasicException(BasicResponseCode basicResponseCode){
            this.basicResponseCode = basicResponseCode;
        }
    }
     ```
  
    </div>
  </details>

  <details>
    <summary> BasicResponse 클래스  </summary>
    <div markdown="1">
  
    ```java
    @Getter  
    @JsonPropertyOrder({"statusCode", "statusName", "devMessage", "userMessage", "result"})
    public class BasicResponse<T> {
    
        private final int statusCode;
        private final String statusName;
        private final String devMessage;
        private final String userMessage;
        @JsonInclude(JsonInclude.Include.NON_NULL)
        private T result;
    
    
        //정상 응답 처리
        public BasicResponse(T result) {
            this.statusCode = SUCCESS.getHttpStatus().value();
            this.statusName = SUCCESS.getHttpStatus().name();
            this.devMessage = SUCCESS.name();
            this.userMessage = SUCCESS.getDetail();
            this.result = result;
        }
    
        //예외 응답 처리
        public BasicResponse(BasicResponseCode basicResponseCode) {
            this.statusCode = basicResponseCode.getHttpStatus().value();
            this.statusName = basicResponseCode.getHttpStatus().name();
            this.devMessage = basicResponseCode.name();
            this.userMessage = basicResponseCode.getDetail();
        }
    }
     ```
  
    </div>
  </details>

  <details>
    <summary> BasicResponseCode 클래스  </summary>
    <div markdown="1">
  
    ```java
    @Getter
    public enum BasicResponseCode {
    
        /* 200  OK : 정상 응답 */
        SUCCESS (HttpStatus.OK, "요청 정상 처리"),
    
        /* 400 BAD_REQUEST : 잘못된 요청 */
        INVALID_NAME (BAD_REQUEST, "이름 형식 오류"),
        INVALID_TITLE (BAD_REQUEST, "게시글 제목 100자 초과"),
    
        /* 401 UNAUTHORIZED : 인증되지 않은 사용자 */
        AUTHOR_MISMATCH (UNAUTHORIZED, "게시글 작성자 불일치"),
    
        /* 500 INTERNAL_SERVER_ERROR  */
        SERVER_ERROR (INTERNAL_SERVER_ERROR, "서버에서 에러 발생"),
        DB_SERVER_ERROR (INTERNAL_SERVER_ERROR, "DB 서버에서 에러 발생");
    
    
        private final HttpStatus httpStatus;
        private final String detail;
    
        private BasicResponseCode(HttpStatus httpStatus, String detail) {
            this.httpStatus = httpStatus;
            this.detail = detail;
        }
    }
     ```
  
    </div>
  </details>

  <details>
    <summary> BasicExceptionHandler 클래스  </summary>
    <div markdown="1">
  
    ```java
    @RestControllerAdvice(basePackages = "com.example.homework")
    public class BasicExceptionHandler {
    
        //BasicException 예외 처리 (status code = 300 ~ 500)
        @ExceptionHandler(BasicException.class)
        public ResponseEntity<BasicResponse> handleBasicException(BasicException basicException){
    
            BasicResponseCode basicResponseCode = basicException.getBasicResponseCode();
            return ResponseEntity.status(basicResponseCode.getHttpStatus())
                    .body(new BasicResponse(basicResponseCode));
        }
    
    
        //SQLException 예외 처리 (status code = 500)
        @ExceptionHandler(SQLException.class)
        public ResponseEntity<BasicResponse> handleSQLException(){ 
    
            return ResponseEntity.status(BasicResponseCode.DB_SERVER_ERROR.getHttpStatus())
                    .body(new BasicResponse(BasicResponseCode.DB_SERVER_ERROR));  
        }
    
    }
     ```
  
    </div>
  </details>

- 응답 예시
   ```
    {
        "statusCode": 400,
        "statusName": "BAD_REQUEST",
        "devMessage": "INVALID_TITLE",
        "userMessage": "게시글 제목 100자 초과"
    }
   ```

</br>

###  게시글 등록 API 
- `POST /posts`
- 시퀀스 다이어그램
  ![Sequence_Diagram_posts](https://github.com/gusdn7142/homework/assets/62496215/692d6a40-6175-4c94-b310-7de2e583ae37)
- 구현 코드 & 실행 결과
  <details>
    <summary> Request DTO  </summary>
    <div markdown="1">
  
    ```java
    @Getter
    @Setter
    public class PostReq {
    
        @Size(min=1, max=100, message="게시글 제목 100자 초과")
        private String title;    //제목
    
        private String content;    //내용
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 컨트롤러 계층  </summary>
    <div markdown="1">
  
    ```java
    @RestController
    @RequestMapping("posts")
    public class PostController {
    
        private final PostService postService;
    
        @Autowired
        public PostController(PostService postService) {
            this.postService = postService;
        }
    
        /*
         * 1. 게시글 등록 API
         * [POST] /posts
         * @return BasicResponse<String>
         */
        @PostMapping("")
        public BasicResponse<String> createPost(@Valid @RequestBody PostReq postReq, BindingResult bindingResult,
                                                @RequestHeader(value = "X-USERID") Long userId,
                                                @RequestHeader(value = "X-BOARDID") Long boardId) {
    
            //유효성 검사
            if (bindingResult.hasErrors()) {
                FieldError fieldError = bindingResult.getFieldError();
                String errorCode = fieldError.getCode() + "." + fieldError.getObjectName() + "." + fieldError.getField();
                if (errorCode.equals("Size.postReq.title")) {
                    throw new BasicException(INVALID_TITLE);
                }
            }
    
            //post 테이블에 게시글 등록
            String responseMessage = postService.createPost(postReq, userId, boardId);
            return new BasicResponse<>(responseMessage);
        }
     ```
  
    </div>
  </details>


  <details>
    <summary> 서비스 계층  </summary>
    <div markdown="1">
  
    ```java
    @Service
    public class PostService {
    
        private final PostRepository postRepository;
        private final UserRepository userRepository;
        private final BoardRepository boardRepository;
    
        @Autowired
        public PostService(PostRepository postRepository, UserRepository userRepository, BoardRepository boardRepository){
            this.postRepository = postRepository;
            this.userRepository = userRepository;
            this.boardRepository = boardRepository;
        }
    
        /* 1. 게시글 등록 API */
        public String createPost(PostReq postReq, Long userId, Long boardId){
    
            User user = userRepository.findById(userId).orElseThrow(
                    () -> new BasicException(DB_SERVER_ERROR)
            );
    
            Board board = boardRepository.findById(boardId).orElseThrow(
                    () -> new BasicException(DB_SERVER_ERROR)
            );
    
            postRepository.save(Post.builder()
                    .title(postReq.getTitle())
                    .content(postReq.getContent())
                    .user(user)
                    .board(board)
                    .build());
    
            return "post 테이블에 게시글 등록 성공";
        }
     ```
  
    </div>
  </details>


  <details>
    <summary> 레포지토리 계층  </summary>
    <div markdown="1">
  
    ```java
    public interface PostRepository extends JpaRepository<Post, Long> { 
    
    }    
     ```
  
    </div>
  </details>


  <details>
    <summary> 실행 결과  </summary>  
    <img src="https://github.com/gusdn7142/homework/assets/62496215/8470850a-f063-41f7-acd4-17534cdae80f" alt="이미지를 찾을 수 없습니다.">
  </details>

  
 
- 테스트 코드 & 실행 결과
  <details>
    <summary> 컨트롤러 계층의 게시글 등록 메서드 테스트 코드  </summary>
    <div markdown="1">
  
    ```java
    @Transactional    
    @SpringBootTest
    class PostControllerTest {
    
        @Autowired
        private PostController postController;
    
        @Autowired
        private PostService postService;
    
        @Autowired
        private PostRepository postRepository;
    
        @Test
        //@Rollback(false)                        
        @Tag("단일_실행_테스트")
        @DisplayName("게시글 등록 API 테스트 ")   
        void createPost(){
    
            //given
            PostReq postReq = new PostReq();
            postReq.setTitle("메일플러그 공지1");
            postReq.setContent("메일플러그에 오신것을 환영합니다.1");
            Long userId = 1L;   
            Long boardId = 1L;   
            BindingResult bindingResult = mock(BindingResult.class);
    
            //when
            BasicResponse<String> response = postController.createPost(postReq, bindingResult, userId, boardId);
    
            //then
            assertThat(response.getResult()).isEqualTo("post 테이블에 게시글 등록 성공");                           //게시글 정보 수정 성공 확인
        }
    }
     ```
  
    </div>
  </details>
 

  <details>
    <summary> 실행 결과  </summary>  
    <img src="https://github.com/gusdn7142/homework/assets/62496215/c2d86105-95b7-41c8-9325-08e15bcdd74a" alt="이미지를 찾을 수 없습니다.">
  </details>


- API 명세
  - Link : https://fir-lancer-6bb.notion.site/API-fec8a9009cfe4718b8ca8f3d4174f250?pvs=4 


</br>



###  게시글 단건 조회 API 
- `GET /posts/:postId`
- 시퀀스 다이어그램
  ![Sequence_Diagram_posts/:postId](https://github.com/gusdn7142/homework/assets/62496215/11d27ba4-4960-43d9-ad26-6c61b59aede6)

- 구현 코드 & 실행 결과
   <details>
    <summary> Response DTO  </summary>
    <div markdown="1">
  
    ```java
    @Getter
    @Setter
    public class PostRes {
    
        private String boardName;   //게시판 이름
        private Long postId;        //게시글 인덱스
        private String postTitle;   //게시글 제목
        private String postContent; //게시글 내용
        private String userName;     //게시글 글쓴이 이름
        private String postUpdateAt;  //게시글 수정일
        private Long postLookupCount;  //게시글 조회수
    
    
        @Builder
        public PostRes(String boardName, Long postId, String postTitle, String postContent, String userName, LocalDateTime postUpdateAt, Long postLookupCount) {
            this.boardName = boardName;
            this.postId = postId;
            this.postTitle = postTitle;
            this.postContent = postContent;
            this.userName = userName;
            this.postUpdateAt = convertToDate(postUpdateAt);
            this.postLookupCount = postLookupCount;
        }
    
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 컨트롤러 계층  </summary>
    <div markdown="1">
  
    ```java
    @RestController
    @RequestMapping("posts")
    public class PostController {
    
        private final PostService postService;
    
        @Autowired
        public PostController(PostService postService) {
            this.postService = postService;
        }
    
    		/*
         * 2. 게시글 단건 조회 API
         * [GET] /posts/:postId
         * @return BasicResponse<PostRes>
         */
        @GetMapping("/{postId}")
        public BasicResponse<PostRes> getPost(@PathVariable(value = "postId") Long postId) {
    
            //post 테이블에서 게시글 단건 조회
            PostRes postRes = postService.getPost(postId);
    
            return new BasicResponse<>(postRes);
        }
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 서비스 계층  </summary>
    <div markdown="1">
  
    ```java
    @Service
    public class PostService {
    
        private final PostRepository postRepository;
        private final UserRepository userRepository;
        private final BoardRepository boardRepository;
    
    		@Autowired
        public PostService(PostRepository postRepository, UserRepository userRepository, BoardRepository boardRepository){
            this.postRepository = postRepository;
            this.userRepository = userRepository;
            this.boardRepository = boardRepository;
        }
    
    		/* 2. 게시글 단건 조회 (+조회수 증가) API */
        @Transactional   //(readOnly = true)
        public PostRes getPost(Long postId) {
    
            //게시글 단건 조회
            Post post = postRepository.findById(postId).orElseThrow(
                    () -> new BasicException(DB_SERVER_ERROR)
            );
    
            //게시글 조회수 증가
            post.updateLookupCount(post.getLookupCount()+1);
    
    
            PostRes postRes = PostRes.builder()
                    .boardName(post.getBoard().getName())
                    .postId(postId)
                    .postTitle(post.getTitle())
                    .postContent(post.getContent())
                    .userName(post.getUser().getName())
                    .postUpdateAt(post.getUpdatedAt())
                    .postLookupCount(post.getLookupCount())
                    .build();
    
            return postRes;
        }
    
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 레포지토리 계층  </summary>
    <div markdown="1">
  
    ```java
    public interface PostRepository extends JpaRepository<Post, Long> {
    
        /* 2. 게시글 단건 조회  - N+1문제 해결*/
        @Query(value="select p from Post p \n" +
                "join fetch p.user u \n" +
                "join fetch p.board b \n" +
                "where p.id =:postId \n", nativeQuery = false)
        Optional<Post> findById(@Param("postId") Long postId);
    
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 실행 결과  </summary>  
    <img src="https://github.com/gusdn7142/homework/assets/62496215/ddb69040-fadc-4e22-b98c-0c4c497d548f" alt="이미지를 찾을 수 없습니다.">
  </details>



- 테스트 코드 & 실행 결과
  <details>
    <summary> 컨트롤러 계층의 게시글 단건 조회 메서드 테스트 코드  </summary>
    <div markdown="1">
  
    ```java
    @Transactional     
    @SpringBootTest
    class PostControllerTest {
    
        @Autowired
        private PostController postController;
    
        @Autowired
        private PostService postService;
    
        @Autowired
        private PostRepository postRepository;
    
        @Autowired
        private UserRepository userRepository;
    
        @Autowired
        private BoardRepository boardRepository;
    
        private Board boardCreation;
        private User userCreation;
        private List<Post> postCreations;		
    
    
        @Test
        //@Rollback(false)                         
        @DisplayName("게시글 단건 조회 API 테스트 ")   
        void getPost(){
    
            //given
            Post postCreation = postCreations.get(0);
            Long postId = postCreation.getId();
            Long beforeLookupCount = postCreation.getLookupCount();
    
            //when
            BasicResponse<PostRes> response = postController.getPost(postId);
    
            //then
            assertThat(response.getResult()).isNotNull();                                     //response 값이 null이 아닌지 확인
    
            PostRes postRes = response.getResult();
            assertThat(postRes.getBoardName()).isEqualTo(boardCreation.getName());             //게시판 이름 검증
            assertThat(postRes.getPostId()).isEqualTo(postCreation.getId());                    //게시글 id 검증
            assertThat(postRes.getPostTitle()).isEqualTo(postCreation.getTitle());              //게시글 제목 검증
            assertThat(postRes.getPostContent()).isEqualTo(postCreation.getContent());          //게시글 내용 검증
            assertThat(postRes.getUserName()).isEqualTo(userCreation.getName());                //사용자 이름 검증
            assertThat(postRes.getPostUpdateAt()).isEqualTo(convertToDate(postCreation.getUpdatedAt()));   //게시글 수정일 검증
            assertThat(postRes.getPostLookupCount()).isEqualTo(postCreation.getLookupCount());   //게시글 조회수 검증
    
            assertThat(response.getResult().getPostLookupCount()).isEqualTo(beforeLookupCount+1);         //lookupCount 값 증가 여부 확인
    
        }
    
    
    		@BeforeEach
        void before(TestInfo testInfo) {
            if (testInfo.getTags().contains("단일_실행_테스트")) {
                return;
            }
    
            //DB에 게시판 등록
            boardCreation = boardRepository.save(Board.builder()
                    .name("자유 게시판")
                    .status(DataStatus.ACTIVE)
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build());
    
            //DB에 사용자 등록
            userCreation = userRepository.save(User.builder()
                    .name("Depth")
                    .status(DataStatus.ACTIVE)//기본 값 필요
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build());
    
            //DB에 게시글 등록
            postCreations = new ArrayList<>();
            for(int i=0; i<5; i++){
                postCreations.add(postRepository.save(Post.builder()
                    .title("메일플러그 공지")
                    .content("메일플러그에 오신것을 환영합니다.")
                    .user(userCreation)
                    .board(boardCreation)
                    .lookupCount(0L)              
                    .status(DataStatus.ACTIVE)    
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build()));
            }
    
    
    }
     ```
  
    </div>
  </details>

  <details>
    <summary> 실행 결과  </summary>  
    <img src="https://github.com/gusdn7142/homework/assets/62496215/25adcb10-8699-45fd-84de-8689e0e31227" alt="이미지를 찾을 수 없습니다.">
  </details>

- API 명세
  - Link : https://fir-lancer-6bb.notion.site/API-76f78bcef97e45709850604f38248949?pvs=4





</br>




###  게시글 수정 API 
- `PATCH /posts/:postId`
- 시퀀스 다이어그램
  ![Sequence_Diagram_posts](https://github.com/gusdn7142/homework/assets/62496215/c707d425-17b5-40fb-9efc-d2e6b636715b)

- 구현 코드 & 실행 결과
  <details>
    <summary> Request DTO  </summary>
    <div markdown="1">
  
    ```java
    @Getter
    @Setter
    public class PostReq {
    
        @Size(min=1, max=100, message="게시글 제목 100자 초과")
        private String title;    //제목
    
        private String content;    //내용
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 컨트롤러 계층  </summary>
    <div markdown="1">
  
    ```java
    @RestController
    @RequestMapping("posts")
    public class PostController {
    
        private final PostService postService;
    
        @Autowired
        public PostController(PostService postService) {
            this.postService = postService;
        }
    
    
    		/*
         * 3. 게시글 수정 API
         * [PATCH] /posts/:postId
         * @return BasicResponse<String>
         */
        @PatchMapping("/{postId}")
        public BasicResponse<String> modifyPost(@Valid @RequestBody PostReq postReq, BindingResult bindingResult,
                                                @PathVariable(value = "postId") Long postId,
                                                @RequestHeader(value = "X-USERID") Long userId) {
    
            //유효성 검사
            if (bindingResult.hasErrors()) {
                FieldError fieldError = bindingResult.getFieldError();
                String errorCode = fieldError.getCode() + "." + fieldError.getObjectName() + "." + fieldError.getField();
                if (errorCode.equals("Size.postReq.title")) {
                    throw new BasicException(INVALID_TITLE);
                }
            }
    
            //post 테이블의 게시글 수정
            String responseMessage = postService.modifyPost(postReq, postId, userId);
            return new BasicResponse<>(responseMessage);
        }
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 서비스 계층  </summary>
    <div markdown="1">
  
    ```java
    @Service
    public class PostService {
    
        private final PostRepository postRepository;
        private final UserRepository userRepository;
        private final BoardRepository boardRepository;
    
        @Autowired
        public PostService(PostRepository postRepository, UserRepository userRepository, BoardRepository boardRepository){
            this.postRepository = postRepository;
            this.userRepository = userRepository;
            this.boardRepository = boardRepository;
        }
    
    
    /* 3. 게시글 수정 API */
        @Transactional
        public String modifyPost(PostReq postReq, Long postId, Long userId){
    
            Post post = postRepository.findById(postId).orElseThrow(
                    () -> new BasicException(DB_SERVER_ERROR)
            );
    
            //게시글 작성자 일치여부 확인
            if(userId != post.getUser().getId()) {
                new BasicException(AUTHOR_MISMATCH);
            }
    
            //게시글 제목 수정
            if(postReq.getTitle() != null){
                post.updateTitle(postReq.getTitle());
            }
    
            //게시글 내용 수정
            if(postReq.getContent() != null){
                post.updateContent(postReq.getContent());
    
            }
    
            return "post 테이블에서 게시글 수정 성공";
        }
    
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 레포지토리 계층  </summary>
    <div markdown="1">
  
    ```java
    public interface PostRepository extends JpaRepository<Post, Long> {
    
    		/* 2. 게시글 단건 조회 */
        @Query(value="select p from Post p \n" +
                "join fetch p.user u \n" +
                "join fetch p.board b \n" +
                "where p.id =:postId \n", nativeQuery = false)
        Optional<Post> findById(@Param("postId") Long postId);
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 실행 결과  </summary>  
    <img src="https://github.com/gusdn7142/homework/assets/62496215/dc1e456e-eeb1-4980-822b-315f81f20e59" alt="이미지를 찾을 수 없습니다.">
  </details>

 
- 테스트 코드 & 실행 결과

  <details>
    <summary> 컨트롤러 계층의 게시글 수정 메서드 테스트 코드  </summary>
    <div markdown="1">
  
    ```java
    @Transactional     //before() 메서드 롤백을 위해 필요
    @SpringBootTest
    class PostControllerTest {
    
        @Autowired
        private PostController postController;
    
        @Autowired
        private PostService postService;
    
        @Autowired
        private PostRepository postRepository;
    
        @Autowired
        private UserRepository userRepository;
    
        @Autowired
        private BoardRepository boardRepository;
    
        private Board boardCreation;
        private User userCreation;
        private List<Post> postCreations;		
    
    
        @Test
        //@Rollback(false)                         
        @DisplayName("게시글 수정 API 테스트 ")    
        void modifyPost(){
    
            //given
            PostReq postReq = new PostReq();
            postReq.setTitle("메일플러그 공지2");
            postReq.setContent("메일플러그에 오신것을 환영합니다.2");
            Long postId = postCreations.get(0).getId();
            Long userId = userCreation.getId();
            BindingResult bindingResult = mock(BindingResult.class);
    
            //when
            BasicResponse<String> response = postController.modifyPost(postReq, bindingResult, postId, userId);
    
            //then
            assertThat(response.getResult()).isEqualTo("post 테이블에서 게시글 수정 성공");
        }
    
    
    
    
    		@BeforeEach
        void before(TestInfo testInfo) {
            if (testInfo.getTags().contains("단일_실행_테스트")) {
                return;
            }
    
            //DB에 게시판 등록
            boardCreation = boardRepository.save(Board.builder()
                    .name("자유 게시판")
                    .status(DataStatus.ACTIVE)
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build());
    
            //DB에 사용자 등록
            userCreation = userRepository.save(User.builder()
                    .name("Depth")
                    .status(DataStatus.ACTIVE)//기본 값 필요
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build());
    
            //DB에 게시글 등록
            postCreations = new ArrayList<>();
            for(int i=0; i<5; i++){
                postCreations.add(postRepository.save(Post.builder()
                    .title("메일플러그 공지")
                    .content("메일플러그에 오신것을 환영합니다.")
                    .user(userCreation)
                    .board(boardCreation)
                    .lookupCount(0L)              
                    .status(DataStatus.ACTIVE)    
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build()));
            }
    }
     ```
  
    </div>
  </details>



  <details>
    <summary> 실행 결과  </summary>  
    <img src="https://github.com/gusdn7142/homework/assets/62496215/8814c9ae-9a7b-4219-bf27-5f390b8c3ffc" alt="이미지를 찾을 수 없습니다.">
  </details>
  

- API 명세
  - Link : https://fir-lancer-6bb.notion.site/API-650b31a0a7934d1f9876747effe77780?pvs=4





</br>





###  게시글 삭제 API 
- `PATCH /posts/:postId/status`
- 시퀀스 다이어그램
  ![Sequence_Diagram_posts](https://github.com/gusdn7142/homework/assets/62496215/8343902d-f068-40b2-b403-27c74bb1016c)

- 구현 코드 & 실행 결과

  <details>
    <summary> 컨트롤러 계층  </summary>
    <div markdown="1">
  
    ```java
    @RestController
    @RequestMapping("posts")
    public class PostController {
    
        private final PostService postService;
    
        @Autowired
        public PostController(PostService postService) {
            this.postService = postService;
        }
    
    		/*
         * 4. 게시글 삭제 API
         * [PATCH] /posts/:postId/status
         * @return BasicResponse<String>
         */
        @PatchMapping("/{postId}/status")
        public BasicResponse<String> deletePost(@PathVariable(value = "postId") Long postId,
                                                @RequestHeader(value = "X-USERID") Long userId) {
    
            //post 테이블의 게시글 삭제
            String responseMessage = postService.deletePost(postId, userId);
            return new BasicResponse<>(responseMessage);
        }
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 서비스 계층  </summary>
    <div markdown="1">
  
    ```java
    @Service
    public class PostService {
    
        private final PostRepository postRepository;
        private final UserRepository userRepository;
        private final BoardRepository boardRepository;
    
        @Autowired
        public PostService(PostRepository postRepository, UserRepository userRepository, BoardRepository boardRepository){
            this.postRepository = postRepository;
            this.userRepository = userRepository;
            this.boardRepository = boardRepository;
        }
    
    		/* 4. 게시글 삭제 API */
        @Transactional
        public String deletePost(Long postId, Long userId){
    
            Post post = postRepository.findById(postId).orElseThrow(
                    () -> new BasicException(DB_SERVER_ERROR)
            );
    
            //게시글 작성자 일치여부 확인
            if(userId != post.getUser().getId()) {
                new BasicException(AUTHOR_MISMATCH);
            }
    
            //게시글 삭제
            post.updateStatus(DataStatus.INACTIVE);
    
            return "post 테이블에서 게시글 삭제 성공";
        }
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 레포지토리 계층  </summary>
    <div markdown="1">
  
    ```java
    public interface PostRepository extends JpaRepository<Post, Long> {
    
        /* 2. 게시글 단건 조회 */
        @Query(value="select p from Post p \n" +
                "join fetch p.user u \n" +
                "join fetch p.board b \n" +
                "where p.id =:postId \n", nativeQuery = false)
        Optional<Post> findById(@Param("postId") Long postId);
    
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 실행 결과  </summary>  
    <img src="https://github.com/gusdn7142/homework/assets/62496215/7018ceec-5546-412a-a3c6-c065870386cf" alt="이미지를 찾을 수 없습니다.">
  </details>


- 테스트 코드 & 실행 결과
  <details>
    <summary> 컨트롤러 계층의 게시글 삭제 메서드 테스트 코드  </summary>
    <div markdown="1">
  
    ```java
    @Transactional     
    @SpringBootTest
    class PostControllerTest {
    
        @Autowired
        private PostController postController;
    
        @Autowired
        private PostService postService;
    
        @Autowired
        private PostRepository postRepository;
    
        @Autowired
        private UserRepository userRepository;
    
        @Autowired
        private BoardRepository boardRepository;
    
        private Board boardCreation;
        private User userCreation;
        private List<Post> postCreations;		
    
    
        @Test
        //@Rollback(false)                         
        @DisplayName("게시글 삭제 API 테스트 ")    
        void deletePost(){
    
            //given
            Long postId = postCreations.get(0).getId();
            Long userId = userCreation.getId();
    
            //when
            BasicResponse<String> response = postController.deletePost(postId, userId);
    
            //then
            assertThat(response.getResult()).isEqualTo("post 테이블에서 게시글 삭제 성공");                           //게시글 정보 수정 성공 확인
        }
    
    
    
    		@BeforeEach
        void before(TestInfo testInfo) {
            if (testInfo.getTags().contains("단일_실행_테스트")) {
                return;
            }
    
            //DB에 게시판 등록
            boardCreation = boardRepository.save(Board.builder()
                    .name("자유 게시판")
                    .status(DataStatus.ACTIVE)
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build());
    
            //DB에 사용자 등록
            userCreation = userRepository.save(User.builder()
                    .name("Depth")
                    .status(DataStatus.ACTIVE)//기본 값 필요
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build());
    
            //DB에 게시글 등록
            postCreations = new ArrayList<>();
            for(int i=0; i<5; i++){
                postCreations.add(postRepository.save(Post.builder()
                    .title("메일플러그 공지")
                    .content("메일플러그에 오신것을 환영합니다.")
                    .user(userCreation)
                    .board(boardCreation)
                    .lookupCount(0L)              
                    .status(DataStatus.ACTIVE)    
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build()));
            }
    }
     ```
  
    </div>
  </details>



  <details>
    <summary> 실행 결과  </summary>  
    <img src="https://github.com/gusdn7142/homework/assets/62496215/0d0335b3-2128-438b-b5eb-d2ec29ce5cd4" alt="이미지를 찾을 수 없습니다.">
  </details>


- API 명세
  - Link : https://fir-lancer-6bb.notion.site/API-a6e7025e27c944d4b2d76255de34399d?pvs=4





</br>





###  게시판에 속한 게시글 목록 조회 API
- `GET /posts/all/:boardId?pageIndex=?&size=?&searchWord=?`
- 시퀀스 다이어그램
  ![Sequence_Diagram_posts](https://github.com/gusdn7142/homework/assets/62496215/c3b65af7-b163-4c48-8fc0-ebf7cd1fd15d)

- 구현 코드 & 실행 결과

  <details>
    <summary> Response DTO </summary>
    <div markdown="1">
  
    ```java
    @Getter
    @Setter
    public class AllPostRes {
    
        private String boardName;   //게시판 이름
        private Long postId;           //게시글 인덱스
        private String postTitle;      //게시글 제목
        private String userName;   //게시글 글쓴이 이름
        private String postUpdateAt;  //게시글 수정일  ..String
        private Long postLookupCount;  //게시글 조회수
    
        @Builder
        public AllPostRes(String boardName, Long postId , String postTitle, String userName, LocalDateTime postUpdateAt, Long postLookupCount) {
            this.boardName = boardName;
            this.postId = postId;
            this.postTitle = postTitle;
            this.userName = userName;
            this.postUpdateAt = convertToDate(postUpdateAt);
            this.postLookupCount = postLookupCount;
        }
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 컨트롤러 계층  </summary>
    <div markdown="1">
  
    ```java
    @RestController
    @RequestMapping("posts")
    public class PostController {
    
        private final PostService postService;
    
        @Autowired
        public PostController(PostService postService) {
            this.postService = postService;
        }
    
    		/*
         * 5. 게시판에 속한 게시글 목록 조회 API
         * [GET] /posts/all/:boardId?pageIndex=?&size=?&keyword=?
         * @return BasicResponse<List<AllPostRes>>
         */
        @GetMapping("/all/{boardId}")
        public BasicResponse<List<AllPostRes>> getPosts(@PathVariable(value = "boardId") Long boardId,
                                                        @RequestParam(required = false, defaultValue = "") String searchWord,
                                                        @RequestParam(required = false) Integer pageIndex,
                                                        @RequestParam(required = false) Integer size) {
    
            Sort sort = Sort.by(Sort.Direction.DESC, "id");   //정렬할 필드를 updatedAt로 변경해야 합니다.
            Pageable pageable = PageRequest.of(pageIndex, size, sort);
    
            //post 테이블에서 해당 게시판에 해당되는 게시글 모두 조회
            List<AllPostRes> allPostRes = postService.getPosts(boardId, searchWord, pageable);
    
            return new BasicResponse<>(allPostRes);
        }
    
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 서비스 계층  </summary>
    <div markdown="1">
  
    ```java
    @Service
    public class PostService {
    
        private final PostRepository postRepository;
        private final UserRepository userRepository;
        private final BoardRepository boardRepository;
    
        @Autowired
        public PostService(PostRepository postRepository, UserRepository userRepository, BoardRepository boardRepository){
            this.postRepository = postRepository;
            this.userRepository = userRepository;
            this.boardRepository = boardRepository;
        }
    
    
    		/* 5. 게시판에 속한 게시글 목록 조회 API*/
        public List<AllPostRes> getPosts(Long boardId, String searchWord, Pageable pageable) {
    
            //게시글 조회
            List<Post> posts = postRepository.findByBoardId(boardId, searchWord, pageable).orElseThrow(
                    () -> new BasicException(DB_SERVER_ERROR)
            );
    
            List<AllPostRes> allPostRes = new ArrayList<>();
            for(Post post : posts){  
                allPostRes.add(AllPostRes.builder()
                        .boardName(post.getBoard().getName())
                        .postId(post.getId())
                        .postTitle(post.getTitle())
                        .userName(post.getUser().getName())
                        .postUpdateAt(post.getUpdatedAt())
                        .postLookupCount(post.getLookupCount())
                        .build());
            }
    
            return allPostRes;
        }
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 레포지토리 계층  </summary>
    <div markdown="1">
  
    ```java
    public interface PostRepository extends JpaRepository<Post, Long> {
    
    		/* 5. 게시판에 속한 게시글 목록 조회 */
        @Query(value="select p from Post p \n" +
                "join fetch p.user u \n" +
                "join fetch p.board b\n" +
                "where b.id =:boardId \n" +
                "and p.status = 'ACTIVE' \n" +
                "and p.title LIKE CONCAT('%', :searchWord , '%') \n" +
                "order by p.id DESC", nativeQuery = false)
        Optional<List<Post>> findByBoardId(@Param("boardId")Long boardId, @Param("searchWord") String searchWord, Pageable pageable); 
    
    }
     ```
  
    </div>
  </details>


  <details>
    <summary> 실행 결과  </summary>  
    <img src="https://github.com/gusdn7142/homework/assets/62496215/edb153bb-caca-416d-8a34-61ba0e9f66c5" alt="이미지를 찾을 수 없습니다.">
  </details>

- 테스트 코드 & 실행 결과

  <details>
    <summary> 컨트롤러 계층의 게시글 목록 조회 메서드 테스트 코드  </summary>
    <div markdown="1">
  
    ```java
    @Transactional     //before() 메서드 롤백을 위해 필요
    @SpringBootTest
    class PostControllerTest {
    
        @Autowired
        private PostController postController;
    
        @Autowired
        private PostService postService;
    
        @Autowired
        private PostRepository postRepository;
    
        @Autowired
        private UserRepository userRepository;
    
        @Autowired
        private BoardRepository boardRepository;
    
        private Board boardCreation;
        private User userCreation;
        private List<Post> postCreations;		
    
    
        @Test
        @DisplayName("게시판에 속한 게시글 목록 조회 API 테스트 ")   
        void getPosts(){
    
            //given
            String searchWord = "메일";
            Integer pageIndex = 0;
            Integer size = 3;
            Long boardId = boardCreation.getId();
    
            //when
            BasicResponse<List<AllPostRes>> response = postController.getPosts(boardId, searchWord, pageIndex, size);
    
            //then
            assertThat(response.getResult()).isNotNull();                   //response 값이 null이 아닌지 확인
    
            int postCreationIndex = postCreations.size()-1;
            for(int i=0; i<size; i++) {       
                AllPostRes allPostRes = response.getResult().get(i);
                Post postCreation = postCreations.get(postCreationIndex--);  //페이징 조회이기 때문에 순서 반대
    
                assertThat(allPostRes.getBoardName()).isEqualTo(boardCreation.getName());             //게시판 이름 검증
                assertThat(allPostRes.getPostId()).isEqualTo(postCreation.getId());                    //게시글 id 검증
                assertThat(allPostRes.getPostTitle()).isEqualTo(postCreation.getTitle());              //게시글 제목 검증
                assertThat(allPostRes.getUserName()).isEqualTo(userCreation.getName());                 //사용자 이름 검증
                assertThat(allPostRes.getPostUpdateAt()).isEqualTo(convertToDate(postCreation.getUpdatedAt()));  //게시글 수정일 검증
                assertThat(allPostRes.getPostLookupCount()).isEqualTo(postCreation.getLookupCount());   //게시글 조회수 검증
            }
        }
    
    
    		@BeforeEach
        void before(TestInfo testInfo) {
            if (testInfo.getTags().contains("단일_실행_테스트")) {
                return;
            }
    
            //DB에 게시판 등록
            boardCreation = boardRepository.save(Board.builder()
                    .name("자유 게시판")
                    .status(DataStatus.ACTIVE)
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build());
    
            //DB에 사용자 등록
            userCreation = userRepository.save(User.builder()
                    .name("Depth")
                    .status(DataStatus.ACTIVE)//기본 값 필요
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build());
    
            //DB에 게시글 등록
            postCreations = new ArrayList<>();
            for(int i=0; i<5; i++){
                postCreations.add(postRepository.save(Post.builder()
                    .title("메일플러그 공지")
                    .content("메일플러그에 오신것을 환영합니다.")
                    .user(userCreation)
                    .board(boardCreation)
                    .lookupCount(0L)              
                    .status(DataStatus.ACTIVE)    
                    .createdAt(LocalDateTime.now())
                    .updatedAt(LocalDateTime.now())
                    .build()));
            }
    }
     ```
  
    </div>
  </details>

  <details>
    <summary> 실행 결과  </summary>  
    <img src="https://github.com/gusdn7142/homework/assets/62496215/30190b90-489f-4514-8ff8-b5f4d0a9c8f9" alt="이미지를 찾을 수 없습니다.">
  </details>


- API 명세
  - Link : https://fir-lancer-6bb.notion.site/API-79e33ebf31f14ab39ad8da3d4ea3d7ec?pvs=4



</br>

## 💡 서버 실행시 주의사항

### 환경변수 설정
applicaiton.yml 파일에 애플리케이션 정보, DB 정보를 기입해 주시면 됩니다.

```
# Application
server:
  port: 10000

# MYSQL DB
spring:
  datasource:
    url:
    username: 
    password:
  jpa:
    hibernate:
      ddl-auto: validate
``` 
  
### 빌드 및 실행 방법  
```
# 프로젝트 빌드 
$ ./gradlew clean build

# Jar 파일 실행
$ java -jar build/libs/homework-0.0.1-SNAPSHOT.jar
``` 
  

