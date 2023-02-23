![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b200a78c-05ce-41e3-9a93-381619b3e8f7/Untitled.png)

하나의 프로젝트를 구성할 때 Model, View, Controller의 역할로 구성요소를 구분한 패턴

사용 예시

```java
1. 사용자가 웹 사이트 접속
2. Controller는 사용자가 요청한 웹 페이지를 위해 모델 호출
3. Model은 DB같은 데이터 소스를 제어한 후 return
4. Controller는 Model이 return한 겨로가를 View에 반영
5. View가 사용자에게 보여짐
```

Model

애플리케이션의 정보와 비즈니스 로직을 나타낸다. 

데이터베이스, 변수 등을 뜻하는데, 이러한 데이터의 가공을 책임지는 컴포넌트이다.

JPA에서 모델은 DB 테이블을 나타내주는 엔티티이다.

규칙

1) 사용자가 수정하기 위한 모든 데이터를 가지고 있어야 된다.

2) View, Controller에 대한 정보를 몰라야 된다.

3) 변경이 생긴다면, 그 사실을 알리는 방법을 구현해야 된다. 모델은 재사용이 가능해야하므로 다른 인터페이스에서도 변하면 안된다.

```java
public class Employee {
    private String firstName;
    private String lastName;
    private String department;
    
    public String getFirstName() {
        return firstName;
    }
    
    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }
    
    public String getLastName() {
        return lastName;
    }
    
    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
    
    public String getDepartment() {
        return department;
    }
    
    public void setDepartment(String department) {
        this.department = department;
    }
}
```

View

사용자 인터페이스 요소를 나타낸다.

데이터 및 객체의 입력 그리고 출력을 담당한다.

JPA에서 뷰는 서버와 클라이언트에 의해 HTML 템플릿으로 만들어진다.

규칙

1) Model이 가지고 있는 정보를 따로 저장하면 안된다.

2) Model, Controller와 같은 달느 구성요소들을 알면 안 된다.

3) 변경이 생긴다면, 그 사실을 알리는 방법을 구현해야 된다.

```java
public class EmployeeView {
    public void printEmployeeDetails(String firstName, String lastName, String department) {
        System.out.println("Employee:");
        System.out.println("First Name: " + firstName);
        System.out.println("Last Name: " + lastName);
        System.out.println("Department: " + department);
    }
}
```

Controller

데이터(Model)와 사용자 인터페이스(View)의 요소들의 다리역할을 한다.

사용자의 요청을 다루고, 모델의 데이터를 수정하고, 뷰로 만들기 위해 보낸다.

스프링 부트에서는 REST controller로 HTTP request를 요청한다.

규칙

1) Model, View에 대해 알고있어야 된다.

2) Model, View의 변경을 모니터링 해야 된다.

특징

`@controller` : 전통적인 Spring MVC 컨트롤러는 View를 반환하기 위해 사용한다. `@ResponseBody`어노테이션과 같이 사용하면 RestController와 똑같은 기능을 수행

`@RestController` : *`@Controller와 @ResponseBody`의 기능을 묶은 것이다.* **JSON/XML** 형태로 객체 데이터 반환을 목적으로 한다.

```java
public class EmployeeController {
    private Employee model;
    private EmployeeView view;
    
    public EmployeeController(Employee model, EmployeeView view) {
        this.model = model;
        this.view = view;
    }
    
    public void setEmployeeFirstName(String firstName) {
        model.setFirstName(firstName);
    }
    
    public void setEmployeeLastName(String lastName) {
        model.setLastName(lastName);
    }
    
    public void setEmployeeDepartment(String department) {
        model.setDepartment(department);
    }
    
    public void updateView() {
        view.printEmployeeDetails(model.getFirstName(), model.getLastName(), model.getDepartment());
    }
}
```

Main method

```java
public class Main {
    public static void main(String[] args) {
        // Create model, view, and controller
        Employee model = new Employee();
        EmployeeView view = new EmployeeView();
        EmployeeController controller = new EmployeeController(model, view);
        
        // Update model and view
        controller.setEmployeeFirstName("John");
        controller.setEmployeeLastName("Doe");
        controller.setEmployeeDepartment("Engineering");
        controller.updateView();
    }
}
```

---

사용하는 이유

인터페이스, 데이터처리 그리고 이를 제어하는 컨트롤로 구성되는 하나의 애플리케이션을 만들면 각자 맡은 역할에 집중할 수 있다.

분리되어 역할을 수행하므로 유지보수 용이해지고, 확장성, 유연성이 증가하고, 코드 중복의 문제점이 사라진다.

한계

Massive-View-Controller

복잡한 프로그램의 경우 다수의 뷰와 모델이 컨트롤러를 통해 연결되기 때문에 컨트롤러가 불필요하게 커지고,

복잡한 화면을 구성하는 경우에도 동일한 현생이 발생

보완

- MVP
- MVVM
- Flux
- Redux
- RxMVVM

---

스프링 부트 → JPA

MVC 패턴을 사용한 스프링부트와 JPA는 애플리케이션 관심사의 분리한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/37bbcb5b-7177-41da-9f36-740d9af78c48/Untitled.png)

Service

데이터를 가공하는 과정 == 비즈니스 로직 수행

```java
1. Client가 Request 전송
2. Request URL에 해당되는 Controller가 수신
3. Controller는 넘어온 요청을 처리하기 위해 Service 를 호출
4. Service는 정보를 가공하여 Controller에 데이터를 넘김
5. Controller는 Service의 결과물을 Client에게 전달
```

JPA와 MVC의 차이

JPA

- 자바에서 관계형 데이터베이스를 작동하게 하는 표준 인터페이스
- ORM 프레임워크를 제공하기 때문에, 자바 객체를 데이터베이스의 테이블과 매핑시킬 수 있다.

MVC

- 디자인패턴으로 모델을 테이터와 비즈니스 로직으로 표현한다.

웹 어플리케이션을 빌드하기 위해 함께 사용된다.
