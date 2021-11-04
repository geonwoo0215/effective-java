### Item16

## public 클래스에서는 public 필드가 아닌 접급자 메서드를 사용하라

책에서 다음과 같은 코드는 퇴보(수준이 뒤떨어지다)라고 한다.
~~~
class Point {
	public double x;
	public double y;
}
~~~

왤까?
* 데이터 필드에 직접 접근하여 캡슐화의 이점을 제공하지 못한다.
* API를 수정하지 않고는 내부 표현을 바꿀 수 없다.
* 불변식을 보장할 수 없다.
* 외부에서 필드에 접근할 때 부수 작업을 수행할 수도 없다.

개선된 코드를 보자

~~~
class Point {
	private double x;
	private double y;
	
	public Point(double x, double y) {
		this.x = x;
		this.y = y;
	}
	
	public double getX() {return x;}
	public double getY() {return y;}
	
	public void setX(double x) { this.x = x;}
	public void setY(double y) { this.y = y;}
}
~~~

### getter와 setter를 사용하고 데이터 필드를 private으로 변경
캡슐화를 하고 접근자를 사용해 유연성을 제공합니다.

## 정리
public 클래스는 절대 가변 필드를 노출해서는 안 된다. 
하지만 package-private 클래스나 private 중첩 클래스에서는 종종 필드를 노출하는 편이 나을 때도 있다.
