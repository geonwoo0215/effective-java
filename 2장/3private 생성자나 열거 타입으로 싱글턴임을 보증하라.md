### ITEM 3
# private 생성자나 열거 타입으로 싱글턴임을 보증하라
## 개요
싱글턴이란 무엇일까?   
* 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.   
싱클턴을 만드는 방법은 여러 방법이 있다.   
첫번째 방식은 생성자는 pricvate으로 선언하고 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 마련해 둔다.   
~~~
public class singleton{
	public static final singleton instance = new singleton();
	private singleton(){};
}
~~~
생성자는 singleton instance를 초기화할 때 딱 한번 호출된다.   
따라서 singleton instance는 전체 시스템에서 하나뿐임이 보장된다.   
위코드의 장점은 
* 싱글턴임이 API에 드러난다.
* 절대로 다른 객체를 참조할 수 없다.
* 간결하다.



두번째 방식은 위 방식에 정적 팩터리 메서드를 public static 멤버로 제공하여 인스턴스를 private static으로 설정한다.
~~~
class singleton{
	private static final singleton instance = new singleton();
	private singleton(){};
	public static singleton getInstance() {return instance;}
}
~~~

위코드의 장점은 
* 싱글턴이 아니게 변경할 수 가 있다.(API를 바꾸지 않고)
* 제네릭 싱글턴 팩터리로 만들 수 있다.
* 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다.

위의 두방식으로 만든 싱글턴 클래스를 직렬화하려면 Serializable을 구현하는것만으로는 부족하다.   
다음의 메서드를 추가하자
~~~
private Object readResolve() {
        return instance;
}
~~~

싱글턴을 만드는 가장 좋은 방법은 원소가 하나인 열거타입을 선언하는 것이다.

~~~
public enum singleton{
	instance;
}
~~~



