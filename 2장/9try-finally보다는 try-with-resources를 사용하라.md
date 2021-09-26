### ITEM 9

## try-fimally보다는 try-with-resources를 사용하라

자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원들이 많다.   
Ex: `InputStream`, `OutputStream`, `java.sql.Connection`
클라이언트가 자원 반납을 안 하거나 놓쳐 성능 문제로 이어진다.   

try finally 구문을 생각해 볼 수 있다.

~~~
public class  Main {
	
	public static void main(String[] args) throws Exception {
		MyResource myResource = new MyResource();
		
		try {
			myResource.doSomething();
		} finally {
			myResource.close();
		}
	}
}
~~~

~~~
public class MyResource implements AutoCloseable {

	
	public void doSomething() {
		System.out.println("Do something");
		throw new FirstError();
	}
	
	@Override
	public void close() throws Exception {
		System.out.println("Close My Resouce");
		throw new SecondError();
	}
}

class FirstError extends RuntimeException {}

class SecondError extends RuntimeException{}
~~~

코드의 결과는 어떻게 될까? 에러를 두 번 던지니 두 개의 에러를 표시해 줘야 되지 않을까?

![try-with-resources](https://user-images.githubusercontent.com/82176176/134820436-8505b473-8c9b-4fd0-8561-0d4ccfb9ea51.JPG)

SecondError만 표시되었다.
try-finally 문의 결점이다. 
예외가 발생하면 두 번째 예외가 첫 번째 예외를 집어삼켜 디버깅을 어렵게 한다.

이런 문제를 개선하기 위하여 try-with-resource를 사용합니다.

~~~
public class  Main {
	
	public static void main(String[] args) throws Exception {
		
		try (MyResource myResource = new MyResource()) {
			myResource.doSomething();
		}
	}
}
~~~

![try-with-resources](https://user-images.githubusercontent.com/82176176/134820810-6d944f3e-fac8-4853-808b-cfd6d3372789.JPG)

두 가지의 에러를 다 표시해 준다.

두 번째 발생한 에러에 suppressed라고 꼬리표가 달려있는 것을 확인할 수 있다. 
이는 Throwable의 getSupressd 메서드를 사용하여 코딩으로 활용할 수 있다.

try-with-resources에서 catch 절이 사용 가능하다.


