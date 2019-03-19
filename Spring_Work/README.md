# Spring
Spring work

*DI(Dependency Injection)_Java
	*하드코딩 된 dependencies를 제거하고 어플리케이션 간 결합, 확장, 유지보수를 용이하게 한다
	*dependency분석을 compile-time에서 runtime으로 이동한다

*하드 코딩	

```java
public class EmailService {

	public void sendEmail(String message, String receiver){
		//logic to send email
		System.out.println("Email sent to "+receiver+ " with Message="+message);
	}
}
```	
	*서비스 파일 생성
		
```java	
public class MyApplication {

	private EmailService email = new EmailService();
	
	public void processMessages(String msg, String rec){
		//do some msg validation, manipulation logic etc
		this.email.sendEmail(msg, rec);
	}
}	
```		
	*하드 코딩의 예시이다. 서비스를 확장하고자 하는 경우 코드의 변경이 불가피 하다.
	
```java	
public class MyLegacyTest {

	public static void main(String[] args) {
		MyApplication app = new MyApplication();
		app.processMessages("Hi Pankaj", "pankaj@abc.com");
	}

}	
```		
---------------------------------------

*인터페이스를 통한 의존성 주입	- Service


```java
public interface MessageService {

	void sendMessage(String msg, String rec);
}
```	
	*서비스 인터페이스 생성
	
	
```java
public class EmailServiceImpl implements MessageService {

	@Override
	public void sendMessage(String msg, String rec) {
		//logic to send email
		System.out.println("Email sent to "+rec+ " with Message="+msg);
	}

}	
```	
	*implement 실행
	
	
```java
public class SMSServiceImpl implements MessageService {

	@Override
	public void sendMessage(String msg, String rec) {
		//logic to send SMS
		System.out.println("SMS sent to "+rec+ " with Message="+msg);
	}

}
```	
	*implement 실행
	
*인터페이스를 통한 의존성 주입	- Customer
	
```java
public interface Consumer {

	void processMessages(String msg, String rec);
}
```	
	*서비스 소비자 인터페이스 생성
	
	
```java
public class MyDIApplication implements Consumer{

	private MessageService service;
	
	public MyDIApplication(MessageService svc){
		this.service=svc;
	}
	
	@Override
	public void processMessages(String msg, String rec){
		//do some msg validation, manipulation logic etc
		this.service.sendMessage(msg, rec);
	}

}
```
	*서비스 소비자 implement 그리고 service를 생성자에서에 붙여준다
	

*인터페이스를 통한 의존성 주입	- Injector

```java
public interface MessageServiceInjector {

	public Consumer getConsumer();
}
```	
	*소비자를 반환용 인터페이스

```java
public class SMSServiceInjector implements MessageServiceInjector {

	@Override
	public Consumer getConsumer() {
		return new MyDIApplication(new SMSServiceImpl());
	}

}
```	
	*소비자 객체를 반환 


```java
public class MyMessageDITest {

	public static void main(String[] args) {
		String msg = "Hi Pankaj";
		String email = "pankaj@abc.com";
		String phone = "4088888888";
		MessageServiceInjector injector = null;
		Consumer app = null;
		
		//Send email
		injector = new EmailServiceInjector();
		app = injector.getConsumer();
		app.processMessages(msg, email);
		
		//Send SMS
		injector = new SMSServiceInjector();
		app = injector.getConsumer();
		app.processMessages(msg, phone);
	}

}
```	

---------------------------------------

*DI(Dependency Injection)_Java
	*장점
		*분리에 대한 걱정 해소
		*코드의 감소(di로 핸들링하기 때문)
		*변경 가능한 요소들을 통한 확장성 상승
		*unit test가 용이
	*단점
		*과도한 사용시 유지보수에 대한 문제 (runtime에서 확인이 가능하기 때문)
		*compile-time에서 잡힐 수 있는 (runtime에러를 일으키는 서비스 클래스의 )dependency가 DI에 의해 숨겨져 버림 즉, 안잡히고 넘어가버림

