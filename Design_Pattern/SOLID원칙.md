# Chap3 SOLID 원칙
## 단일 책임 원칙(SRP: Single Reponsible Principle)
SRP에서 말하는 책임의 기본 단위는 객체를 지칭한다. 즉 객체는 하나의 책임만 가져야 한다는 의미다. 

예를 들어 Student라는 클래스가 수강 과목 추가, 조회, 데이터베이스 객체 정보 저장, 정보 읽기 등의 여러 일을 실행한다고 생각해보자. 이 상태는 Student 클래스가 너무 과한 책임을 수행하는 상태가 된다. Student 클래스에서 가장 잘 할 수 있는 일은 수강 과목을 추가하고 조회하는 일이다. 따라서 수강 과목을 추가하고 조회하는 책임만 수행하도록 하는 것이 SRP를 따르는 설계이다.

Student 클래스에 변경이 일어났을 때 가능한 영향을 받는 부분을 줄여야 좋은 설계이다. 
Student 클래스의 경우 학생의 고유 정보, 데이터베이스 스키마, 출력 형식 변화 등 3가지에 변경 사유가 있다. Student는 학생 고유의 역할을 수행하게끔 변경해야 한다. 학생 클래스의 인스턴스를 데이터베이스에 저장하고 읽어들이는 역할을 담당하는 DAO 클래스, 출석부와 성적표의 출력을 담당하는 성적표 클래스와 출석부 클래스로 분리할 수 있다. 이렇게 개선된 설계는 데이터베이스의 스키마가 변화되면 학생 DAO 클래스나 이를 사용하는 클래스만 영향을 받는다.
![](https://velog.velcdn.com/images/yh_lee/post/4612d811-e1d3-46cb-ba0c-6925bee1f0c3/image.png)


## 개방-폐쇄 원칙(OCP: Open-Closed Principle)
기존 코드를 변경하지 않으면서 기능을 추가할 수 있도록 설계되어야 한다는 뜻이다. 확장에는 용이하고 변경에는 닫혀있어야 한다. 여기서 확장이란 의존관계를 쉽게 변경할 수 있는 것이고, 변경이란 클라이언트 코드를 변경하지 않아도 되는 것이다. 

## 리스코프 치환 원칙(LSP: Liskov Subsitution Principle)
LSP는 부모 클래스와 자식 클래스 사이의 행위가 일관성이 있어야 한다는 뜻이다. 일반화 관계에 대한 이야기 이며 "is a kind of" 관계라고도 한다. 예를 들어 포유류와 원숭이 사이에 "is a kind of" 관계가 성립한다. 따라서 부모 클래스는 포유류, 자식 클래스를 원숭이로 설정할 수 있다. 

부모 클래스의 인스턴스 대신에 자식 클래스의 인스턴스를 별다를 변경 없이 그대로 사용할 수 있을 때 관계가 성립한다.
```java
public class Bag{
	private int price;
    
    public void setPrice(int price){
    	this.price=price;
    }
    public int getPrice(){
    	return price;
    }
}
```
```java
public class DiscountedBag extends Bag{
	private double discountedBag=0;
    
    public void setDiscounted(double discountedRate){
    	this.discountedRate=discountedRate;
    }	
    public void applyDiscount(int price){
    	super.setPrice(price-(int)(discountedRate*price));
    }	
}	
```
DIscountedBag 클래스는 할인율을 설정해서 할인된 가격을 계산하는 기능이 추가되었다. Bag 클래스에 있던 가격을 설정하고 조회하는 기능은 변경없이 그대로 상속되었음을 알 수 있다. 
#### Bag와 DiscountedBag
|Bag|DiscountedBag|
|---|---|
|Bag b1 = new Bag();| DiscountedBag b2 = new DiscountedBag();
|b1.setPrice(1000);|b2.setPrice(1000);
|System.out.pring(b1.getPrice());|System.out.pring(b2.getPrice());
Bag 클래스의 setPrice와 getPrice가 재정의되지 않았으므로 왼쪽과 오른쪽 코드의 실행 결과는 동일하다. DiscountedBag 클래스와 Bag 클래스의 상속관계가 LSP를 위반하지 않음을 의미한다.

LSP를 만족시키는 간단한 방법은 재정의하지 않는 것이다. 

## 의존 역전 원칙(DIP: Dependency Inversion Principle)
DIP는 의존 관계를 맺을 때 변화하기 어려운 것, 거의 변화가 없는 것에 의존하라는 원칙이다. 이때 변하지 않는 것은 인터페이스이고 변하기 쉬운 것은 구체 클래스이다. 다시 말해 _**인터페이스에 의존하고 구체 클래스엔 의존하지 않아야 한다는 의미이다.**_

```java
public class Kid{
	private Toy toy;
    
    public void setToy(Toy toy){
    	this.toy=toy;
    }
    public void play{...}
}
```
아이가 가지고 노는 장남감 종류가 Lego와 Robot이 있다고 하자. 
```java
public class Lego extends Toy{
	public String toString(){
    	return "Lego";
    }
}
```
```java
public class Robot extends Toy{
	public String toString(){
    	return "Robot";
    }
}
```
레고를 가지고 놀다가 마음이 바뀌어 로봇으로 바꾸게 된다면 Kid, Lego, Robot 등의 기존 코드에 영향을 받지 않고 바꿀 수 있다. 
```java
public class Main{
	public static void main(String[] args){
    	Toy t = new Lego();//new Robot();
        Kid k = new Kid();
        k.setToy(t);
        k.play();
    }	
}
```

## 인터페이스 분리 원칙(ISP: Interface Segregation Principle)
ISP는 인터페이스를 클라이언트에 특화되도록 분리시키는 설계 원칙이다. 클라이언트 자신이 이용하지 않는 기능에는 영향을 받지 않아야 한다. 

복합기(복사+프린터+팩스) 기능을 제공하는 기기가 있다고 생각해보자. 프린터 기능만 이용하는 클라이언트가 팩스 기능의 변경으로 인해 발생하는 문제의 영향을 받지 않도록 해야한다. 클라이언트와 무관하게 발생한 변화로 클라이언트 자신이 영향을 받지 않으려면 **범용의 인터페이스보다는 클라이언트에 특화된 인터페이스를 사용해야 한다.** 말 그대로 인터페이스를 클라이언트에 특화시키도록 분리하라는 설계 원칙이다.