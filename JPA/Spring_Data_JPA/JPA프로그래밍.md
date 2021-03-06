## 프로젝트 세팅

데이터베이스 실행
- Postgre SQL 도커 컨테이너 재사용
- docker start postgres_boot

스프링 부트
- 스프링 부트 v2.*
- 스프링 프레임워크 v5.*

스프링 부트 스타터 JPA
- JPA 프로그래밍에 필요한 의존성 추가
    - JPA v2.*
    - Hibernate v5.*
- 자동설정: HibernateJpaAutoConfiguration
    - 컨테이너가 관리하는 EntityManager(프록시) 빈설정
    - Platform TransactionManager 빈 설정

JDBC 설정
- jdbc:postgresql://localhost:5432/springdata
- soojung
- pass

application.properties
- spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
- spring.jpa.hibernate.ddl-auto=create
    - 운영시 validate 로 사용하는것이 좋음
    - update 사용시 편할 수 있으나 스키마가 더러워질수있음 (컬럼이 빠지거나 이름변경시 알수없음 자동으로 지워지지않음)
    
## 엔티티 맵핑
@Entity
- 엔티티는 객체 세상에서 부르는 이름
- 보통 클래스와 같은 이름을 사용하기때문에 값을 변경하지 않음
- 언티티의 이름은 JQL에서 쓰임
- User 클래스 이름 어떤 디비에서는 키워드, 이름을 변경해줘야함
    - @Entity(name="users")

@Table
- 릴레이션 세상에서 부르는 이름
- @Entity의 이름이 기본값
- 테이블의 이름은 SQL에서 쓰임

@Id
- 엔티티의 주키를 맵핑할때 사용
- 자바의 모든 primitive 타입과 그 랩퍼 타입을 사용 할 수 있음
    - Date랑 BigDecimal, BigInteger도 사용가능
- 복합키를 만드는 매핑하는 방법도 있지만 그건 논외로
- 주로 long(primitive) 대신에 Long(wrapper) 을 씀
    - Long 새로만든 것 구별해줌 null 이니

@GeneratedValue
- 주키의 생성방법을 맵핑하는 애노테이션
- 생성 전략과 생서기를 설정 할 수있음
    - 기본전략은 AUTO: 사용하는 DB에 따라 적절한 전략 선택
    - TABLE, SEQUENCE, IDENTITY 중 하나

@Column
- unique
- nullable
- length
- columnDefinition
- ...

@Enumerated
- EnumType.STRING 으로 사용하는 것이 좋다
- 기본값 @Enumerated(value = EnumType.ORDINAL) 좋지않음
    - 순서로 맵핑해주기 때문에 값이 변경될때 문제가됨 

@Enumerated(value = EnumType.STRING)
private CommentStatus commentStatus;

@Temporal
- 현재 JPA 2.1까지는 Date, Calendar만 지원

@Transient
- 컬럼으로 맵핑하고 싶지 않은 멤버 변수에 사용

application.properties
- spring.jpa.show-sql=true
- spring.jpa.properties.hibernate.format_sql=true

앤티티 맵핑 변경후 적용되지 않는 경우
- spring.jpa.hibernate.ddl-auto=update 일때 전에 만들어진 컬럼일 경우 적용안될수있음
- spring.jpa.hibernate.ddl-auto=create 개발시 귀찮긴 해도 create가 깔끔함


## value 타입 맵핑
엔티티 타입과 Value 타입 구분
- 식별자가 있어야 하는가.
- 독립적으로 존재해야 하는가.

Value 타입 종류
- 기본타입 (String,Date,Boolean,...)
- Composite Value 타입
- Collection Value 타입
    - 기본 타입의 콜렉션
    - 컴포짓 타입의 콜렉션

Composite Value 타입 맵핑
- @Embeddable
- @Embedded
- @AttributeOverrides
- @AttributeOverride

~~~ java
@Embeddable
public class Address{

    private String street;
    
    private String city;
    
    private String state;
    
    private String zipCode;

}
~~~

~~~java
@Embedded
@AttributeOverrides({ @AttributeOverride(name ="street", column = @Column(name ="home_street"))})
private Address address;
~~~

## JPA 프로그래밍: 1대다 맵핑
관계에는 항상 두엔티티가 존재합니다.
- 둘 주 하나는 그 관계의 주인(owning) 이고
- 다른 쪽은 종속된(non-owing)
- 해당 관계의 반대쪽 레퍼런스를 가지고 있는 쪽이 주인

단방향에서 관게의 주인은 명확
- 관계를 정의한 쪽이 그 관계의 주인

단방향 @ManyToOne
- 기본값은 FK 생성

단방향 @OneToMany
- 기본값은 조인 테이블 생성

양방향
- FK 가지고 있는 쪽이 오너 따라서 기본값은 @ManyToOne 가지고 있는 쪽이 주인
- 주인이 아닌쪽 (@OneToMany쪽) 에서 mappedBy 사용해서 관계를 맺고 있는 필드를 설정해야 함

양방향
- @ManyToOne(이쪽이 주인)
- @OneToMany(mappedBy)
- 주인한테 관계를 설정해야 DB에 반영됨

~~~java
   account.getStudies().add(study); // 주석처리해도 됨 옵셔널
   study.setOwner(account); // 이건 항상!!!!
~~~

보통은 묶어서 세트로 다님 Account.java
~~~java
    public void addStudy(Study study) {
        this.getStudies().add(study);
        study.setOwner(this);
    }
    
      public void removeStudy(Study study) {
        this.getStudies().remove(study);
        study.setOwner(null);
    }
~~~

## Cascade
- 엔티티의 상태 변화를 전파(전이)시키는 옵션

**엔티티의 상태**가 뭐지?
- Transient: JPA가 모르는 상태
- Persistent: JPA가 관리중인 상태(1차 캐시,Dirty Checking, Write Behind,...)
 - Dirty Checking : 객체를 계속 추적하는 상태
 - Write Behind: 객체의 상태 변화를 늦게 반영함
- Detached: JPA가 더이상 관리하지 않는 상태
- Removed: JPA가 관리하긴 하지만 삭제하기로 한 상태

~~~java
    @OneToMany(mappedBy = "post", cascade = CascadeType.PERSIST)
    private Set<Comment> comments = new HashSet<>();
~~~

-   여러개 줄수있음
~~~java
    @OneToMany(mappedBy = "post", cascade ={ CascadeType.PERSIST,CascadeType.REMOVE})
    private Set<Comment> comments = new HashSet<>();
~~~

- 보통은 다 걸어줌 CascadeType.All
~~~java
    // 여러개 줄수있음
    @OneToMany(mappedBy = "post", cascade = CascadeType.All)
    private Set<Comment> comments = new HashSet<>();
~~~

## Fetch
연관 관계의 엔티티를 어떻게 가져올것이냐?
- 지금 (Eager) ? 나중에 (Lazy)
- @OneToMany의 기본값은 Lazy
- @ManyToOne 기본값은 Eager

## Query
JPQL(HQL)
- Java Persistence Query Language / Hibernate Query Language
- 데이터베이스 테이블이 아닌 엔티티 객체 모델 기반으로 쿼리 작성.
- JPA 또는 하이버네이트가 해당 쿼리를 SQL로 변환해서 실행함
- 타입 세이프 하지않음
- [HQL and JPQL](https://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#hql)

~~~ java
 TypedQuery<Post> query = entityManager.createQuery("SELECT p FROM Post As p", Post.class);
 List<post> posts = query.getResultList();
~~~

Criteria
- 타입 세이프 쿼리
- [Criteria](https://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#criteria)

~~~ java
    CriteriaBuilder builder = entityManager.getCriteriaBuilder();
    CriteriaQuery<Post> criteria = builder.createQuery(Post.class);
    Root<Post> root = criteria.from(Post.class);
    criteria.select(root);
    List<Post> posts=entityManager.createQuery(criteria).getResultList();
~~~

Native Query
- SQL 쿼리 실행하기
- [Native SQL Queries](https://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#sql)

~~~java
List<Post> posts = entityManager
                    .createNativeQuery("SELECT * FROM Post",Post.class)
                    .getResultList();
~~~

## 스프링 데이터 JPA 소개 및 원리
JpaRepository<Entity, Id> 인터페이스
- 매직 인터페이스
- @Repository 가 없어도 빈으로 등록해줌.

@EnableJpaRepositories
- 매직의 시작은 여기서부터

매직은 어덯게 이뤄지나?
- 시작은 @Import(JpaRepositoriesRegistrar.class)
- 핵심은 ImportBeanDefinitionRegistrar 인터페이스 
    - 스프링 프레임워크에 일부
    - 빈을 프로그래밍을 통해서 등록해줌 

~~~java
public class SoojungRegistrar implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata annotationMetadata, BeanDefinitionRegistry beanDefinitionRegistry) {
        GenericBeanDefinition beanDefinition = new GenericBeanDefinition();
        beanDefinition.setBeanClass(Soojung.class);
        beanDefinition.getPropertyValues().add("name", "crystal");

        beanDefinitionRegistry.registerBeanDefinition("soojung", beanDefinition);
    }
}
~~~    

~~~java
@SpringBootApplication
@Import(SoojungRegistrar.class)
public class DemospringdataApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemospringdataApplication.class, args);
    }

}
~~~   

## 핵심개념 개념정리
데이터베이스와 자바   
패러다임 불일치  
ORM이란?  
JPA 사용법(엔티티,벨류타입,관계맵핑)  
JPA 특징(엔티티 상태변화,Cascade,Fetch,1차 캐시,...)    

주의할점  
- 반드시 발생하는 SQL을 확인할것  
- 팁: "?"에 들어있는 값 출력하기  
    - logging.level.org.hibernate.SQL=debug 
    - logging.level.org.hibernate.type.descriptor.sql=trace  
