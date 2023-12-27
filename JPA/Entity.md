## 객체와 테이블 매핑
```
@Entity
@Table(name = "orders")
@Getter @Setter
public class Order {

```
- @Table 미 지정시 클래스명으로 매핑

## 기본 키 매핑
```
@Id @GeneratedValue
@Column(name = "member_id")
private Long id;
```
- 어노테이션 설명 
   - @Id : PK 키임을 명시합니다. PK 키임을 명시합니다.
   - @GeneratedValue : 자동 생성
   - @Column : 객체 필드를 테이블의 컬럼에 매핑시켜주는 어노테이션
- 엔티티의 식별자는 `id` 를 사용하고 PK 컬럼명은 `테이블명_id` 를 사용 (테이블은 타입이 없으므로 구분이 어렵다)

## Enum 
```
@Enumerated(EnumType.STRING)
private OrderStatus status; //주문상태 [ORDER, CANCEL]
```
- @Enumerated의 default는 EnumType.ORDINAL인데 순서으로 Enum파일의 값 순서가 변경됐을때 문제가 발생
- String 값을 이용하는 EnumType.STRING 권장

## 상속관계 매핑 전략
```
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "dtype")
@Getter @Setter
public abstract class Item {
...
}
```
```
@Entity
@DiscriminatorValue("B")
@Getter @Setter
public class Book extends Item {
...
}
```
```
@Entity
@DiscriminatorValue("A")
@Getter @Setter
public class Album extends Item {
 ...
}
```

- @Inheritance(strategy=InheritanceType.XXX)의 stategy를 설정해주면 된다.
   - default 전략은 SINGLE_TABLE(단일 테이블 전략)이다.
   - InheritanceType 종류
      - JOINED: 조인 전략
      - SINGLE_TABLE: 단일 테이블 전략
      - TABLE_PER_CLASS: 구현 클래스마다 테이블 전략
- @DiscriminatorColumn : 구분자 컬럼 / @DiscriminatorValue : 구분값 셋팅

## 값 타입
```
public class Delivery {
   ...
     @Embedded
     private Address address;
  ...
}
```
```
@Embeddable
 @Getter
 public class Address {
     private String city;
     private String street;
     private String zipcode;
     protected Address() {
     }
     public Address(String city, String street, String zipcode) {
         this.city = city;
         this.street = street;
         this.zipcode = zipcode;
    }
}
```
-  값 타입은 변경 불가능하게 설계해야 한다.(Setter 생성 X)


## 연관관계 매핑

```
public class Member {
  ...
    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;

    @OneToMany(mappedBy = "member")
     private List<Order> orders = new ArrayList<>();
  ...
}
```
```
public class Order {
 ...
  @ManyToOne(fetch = FetchType.LAZY)   
  @JoinColumn(name = "member_id") 
  private Member member; //주문 회원
 ...
}
```
- 연관관계의 주인은 외래 키의 위치를 기준으로 정해야함
   - order가 외래키를 가지므로 주인
- 연관관계는 지연로딩(LAZY)으로 설정
   - @XToOne(OneToOne, ManyToOne) 관계는 기본이 즉시로딩이므로 직접 지연로딩으로 설정
- 일대다(1:N)
   - @OneToMany
   - @ManyToOne
- 일대일(1:1)
   - @OneToOne
- 다대다(N:N)
   - @ManyToMany
   - 실무사용 x
   - 중간 테이블에 외래 키 외에 다른 정보가 들어가는 경우가 많기 때문에 다대다를 일대다, 다대일로 풀어서 만드는 것(중간 테이블을 Entity로 만드는 것)이 추후 변경에도 유연하게 대처할 수 있습니다.
     
##  엔티티 설계시 주의점
- 실무에서는 가급적 Getter는 열어두고, Setter는 꼭 필요한 경우에만 사용하는 것을 추천
   -  Setter 대신에 변경 지점이 명확하도록 변경을 위한 비즈니 스 메서드를 별도로 제공해야 한다.
- 연관관계는 지연로딩(LAZY)으로 설정
   - 즉시로딩(EAGER)은 엔티티 실행시점에서 연관된 다른 엔티티 모두 로딩되기때문에 문제가 발생
   - @XToOne(OneToOne, ManyToOne) 관계는 기본이 즉시로딩이므로 직접 지연로딩으로 설정
  ```
  @ManyToOne(fetch = FetchType.LAZY)
  @JoinColumn(name = "item_id")
  private Item item;
  ```
  
