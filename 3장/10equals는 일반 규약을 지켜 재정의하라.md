### ITEM 10

## equals는 일반 규약을 지켜 재졍의하라

equals 메서드는 재정의 할 때 따져 봐야 할 것이 많다.   
꼭 필요한 경우가 아니면 재정의하지 않는 것이 바람직하다.   

재정의하지 않아도 되는 경우

### 각 인스턴스가 본질적으로 고유하다.
값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스들이다. ex) Thread
인스턴스가 고유하기 때문에 같을 일이 없다.

### 인스턴스의 '논리적 동치성'을 검사할 일이 없다.
논리적 동치성이란 무엇일까?

equals를 통해 논리적 동치성을 확인할 필요가 없다면 굳이 재정의 하지 않아도 되며   
Object의 기본 equals로 해결된다.

### 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.
상위 클래스의 equals를 문제없이 잘 사용한다면 재정의할 필요가 없다.

### 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.
클래스가 private으로 선언되어 있다면(내부 클래스?) equals를 사용할 일이 없다.

## equals를 재정의해야 할 때는 언제일까?

equals는 값을 가지는 클래스들끼리 비교해야 할 때이다.
객체가 같은지가 아니라 값이 같은지 이다. 

equals 메서드를 재정의 할 때는 다음과 같은 규약을 따라야 한다.

### 반사성
null이 아닌 모든 참조 값 x에 대하여 `x.equals(x)`는 true이다.
자기 자신은 자신과 같다? 나는 나다?라는 표현이 적절할거다.
일부러 틀리기 어려워 보인다.

### 대칭성
null이 아닌 모든 참조가 x, y에 대해 x.equals(y)가 true 면 y.equals(x)도 true이다.
책의 예시를 보자
~~~
public final class CaseInsensitiveString {
	private final String s;

	public CaseInsensitiveString(String s) {
		this.s = Objects.requireNonNull(s);
	}

	// 대칭성 위배!
	@Override public boolean equals(Object o) {
		if (o instanceof CaseInsensitiveString)
			return s.equalsIgnoreCase(
					((CaseInsensitiveString) o).s);
		if (o instanceof String)  // 한 방향으로만 작동한다!
			return s.equalsIgnoreCase((String) o);
		return false;
	}

	// 문제 시연 (55쪽)
	public static void main(String[] args) {
		CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
		String s = "polish";

		System.out.println(cis.equals(s));
		System.out.println(s.equals(cis));
	}
}
~~~
`cis.equals`는 true를 반환한다. 하지만 `s.equals(cis)`는 false 반환한다.
String의 equal 메서드는 같은 String 타입의 인스턴스의 문자열이 같을 때 true를 반환한다.
이를 해결하기 위한 방법은 String의 equals를 CaseInsensitiveString의 equals와 연동하겠다는 걸 포기해라
~~~
@Override public boolean equals(Object o) {
		return o instanceof CaseInsensitiveString &&
				((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
	}
~~~
equals를 다음과 같이 수정하여 둘 다 false를 출력하게 한다.

### 추이성 
null이 아닌 참조 값 x, y, z에 대해 x.equals(y)가 true이고 y.equals(z)가 true면 x.equals(z)도 true이다. 
첫 번째 객체와 두 번째 객체가 같고 두 번째 객체와 세 번째 객체가 같으면 첫 번째 객체와 세 번째 객체도 같아야 한다.

책의 예제를 보자
~~~
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override public boolean equals(Object o) {
        if (!(o instanceof Point))
            return false;
        Point p = (Point)o;
        return p.x == x && p.y == y;
    }

   
}

class ColorPoint extends Point{
	private final Color color;
	
	public ColorPoint(int x, int y, Color color) {
		super(x,y);
		this.color = color;
	}
	//추이성 위배
	@Override public boolean equals(Object o) {
		if(!(o instanceof Point))
			return false;
		
		if(!(o instanceof ColorPoint))
			return o.equals(this);
		
		return super.equals(o) && ((ColorPoint) o).color ==color;
	}
}
~~~
세 가지의 객체 인스턴스가 다음과 같이 있다.
ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
Point p2 = new Point(1,2);
ColorPoint p3 = new ColorPoint(1, 2, Color.BULE);

p1.equals(p2)와 p2.equals(p3)는 true를 반환하지만
p1.equals(p3)가 false를 반환한다.
추이성이 지켜지지 않았다.

구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다.
x, y 값을 비교하고 색깔을 비교하고 거기에 새로운 값을 비교하는 등 새로운 것들이 추가될 때 equals 규약을 만족시킬 수 없다.

책에서는 상속을 시키지말고 컴포지션을 사용하라고 한다.

### 일관성
null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.

두 객체가 같다면(수정이 되지 않는 한) 앞으로도 영원히 같아야 한다.
클래스가 불변이든 가변이든 equals의 판단에 신뢰할 수 없는 자원이 끼어들게 해서는 안 된다.

### null-아님
null이 아닌 모든 참조 값 xd에 대해, x.equals(null)은 false이다.
eqauls를 사용할 때 어떤 객체든 null과 비교되어서는 안 된다. 
이걸 방지하기 위해 명시적 null 검사를 하는 데 필요치 않은 방법이다.
~~~
@Override public boolean equals(Object o) {
	if (o==null)
		return false;
    ....~~
}
~~~
묵시적으로 변환하는 다음과 같은 방법을 사용하자
~~~
@Override public boolean equals(Object o) {
	if (!(o instanceof MyType))
		return false;
	MyType mt = (MyType) o;
	...
}
~~~

equals메서드 구현 방법을 단계별로 정리해 보자

###
* == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
자기 자신이면 true를 반환하는 것이 성능에 도움이 된다.
* instanceof 연산자로 입력이 올바른 타입인지 확인한다.
타입이 올바르지 않다면 false를 반환한다.
* 입력을 올바른 타입으로 형 변환한다.
매개변수가 Object 타입이어서 변환한다.
* 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 하나씩 검사한다.
