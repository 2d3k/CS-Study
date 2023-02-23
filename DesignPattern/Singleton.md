```java
public class Singleton {
    private static Singleton instance;
    
    private Singleton() {
        // private constructor to prevent instantiation from outside
    }
    
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
    
    public void doSomething() {
        System.out.println("Singleton instance is doing something.");
    }
}
```

위 예제에서는 **`Singleton`**클래스를 정의하며, **`instance`**변수를 통해 클래스의 유일한 인스턴스를 유지합니다. **`getInstance()`**메서드를 호출하여 클래스의 유일한 인스턴스를 가져올 수 있습니다. 이 메서드는 **`instance`**가 **`null`**인 경우에만 인스턴스를 만들고 반환합니다. 따라서, 프로그램 실행 도중 **`getInstance()`**메서드를 여러 번 호출해도 항상 동일한 인스턴스가 반환됩니다. 마지막으로, **`doSomething()`** 메서드는 인스턴스에서 작업을 수행하는 예시 메서드입니다.
