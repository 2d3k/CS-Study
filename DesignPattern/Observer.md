# 1. observer pattern?

![image](https://user-images.githubusercontent.com/102038283/220955168-e319db2c-7d7a-4471-bc18-3fd1aaea93fe.png)

관찰자라는 뜻 그대로, 객체의 상태 변화를 관찰한다.

옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인패턴이다.

# 구현

옵저버(혹은 리스너)라 불리는 하나 이상의 객체를 관찰 대상이 되는 객체에 등록시킨다.

각 옵저버들은 관찰 대상인 객체가 발생시키는 이벤트를 받아 처리한다.

관찰 대상인 객체는 “이벤트를 발생시키는 주체”라는 의미에서 Subject로 표시되어 있다.

이벤트가 발생하면 각 옵저버는 콜백을 받는다. `notify` 함수는 관찰 대상이 발행한 메시지 이외에, 옵저버 자신이 생성한 인자값을 전달할 수도 있다.

각각의 파생 옵저버는 `notify` 함수를 구현함으로써 이벤트가 발생했을 때 처리할 각자의 동작을 정의해야 한다.

주체에는 일반적으로 **등록**(register), **제거**(unregister) 메서드가 있는데, 전자는 새로운 옵저버를 목록에 등록하고 후자는 목록에서 옵저버를 뺀다. 

등록과 제거 메서드 이외에도, 임시로 작동을 멈추거나 재개하는 메서드를 이용해 이벤트가 계속해서 있을 때 홍수같이 발생하는 요청을 제어할 수도 있다.

옵서버 패턴이 많이 쓰인 시스템에서는 순환 실행을 막는 메카니즘이 필요하다. 이벤트 X가 발생하면 옵저버A가 옵저버B를 갱신한다고 가정해보자. 그런데 옵저버B가 이 처리를 위해 옵저버A를 갱신한다면, 이는 다시 A로 하여금 이벤트 X를 발생하게 한다. 이같은 상황을 막기 위해 이벤트 X가 한번 처리된 후에는 A가 이벤트 X를 다시 발생시키지 않는 방법이 요구된다.

# 사용 예시

- 외부에서 발생한 이벤트(사용자 입력같은)에 대한 응답
- 객체의 속성 값 변화에 따른 응답. 종종 콜백은 속성 값 변화를 처리하기 위해 호출될 뿐 아니라 속성 값 또한 바꾼다. 때때로 이벤트 연쇄의 원인이 될 수 있다.

옵저버 패턴은 MVC에서 모델과 뷰 사이를 느슨히 연결하기 위해 사용된다. 대표적으로 모델에서 일어나는 이벤트를 통보받는 옵저버는 뷰의 내용을 바꾸는 스위치를 작동시킨다.

- 이벤트 기반 프로그래밍
    
    이벤트의 발생에 의해 프로그램 흐름이 결정되는 프로그래밍 패러다임
    

## **사례**

옵서버 패턴은 여러 프로그래밍 라이브러리와 시스템에서 구현되는데 대부분의 GUI 툴킷이 해당한다.

이 패턴이 쓰인 주목할 만한 사례 몇 가지:

- Java Swing 라이브러리는 이벤트 관리를 위해 옵저버 패턴을 광범위하게 사용했다.
- Boost.Signals, 시그널/슬롯(signal/slot) 모델을 제공하기 위해 C++ STL을 확장했다.

```java
import java.util.ArrayList;
import java.util.List;

interface Subject {
    void attach(Observer observer);
    void detach(Observer observer);
    void notifyObservers();
}

interface Observer {
    void update(String message);
}

class WeatherStation implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String weather;

    @Override
    public void attach(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void detach(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(weather);
        }
    }

    public void setWeather(String weather) {
        this.weather = weather;
        notifyObservers();
    }
}

class PhoneDisplay implements Observer {
    @Override
    public void update(String message) {
        System.out.println("Phone Display: " + message);
    }
}

class TVDisplay implements Observer {
    @Override
    public void update(String message) {
        System.out.println("TV Display: " + message);
    }
}

public class ObserverPatternExample {
    public static void main(String[] args) {
        WeatherStation weatherStation = new WeatherStation();

        PhoneDisplay phoneDisplay = new PhoneDisplay();
        TVDisplay tvDisplay = new TVDisplay();

        weatherStation.attach(phoneDisplay);
        weatherStation.attach(tvDisplay);

        weatherStation.setWeather("Sunny");

        weatherStation.detach(phoneDisplay);

        weatherStation.setWeather("Rainy");
    }
}
```

**`Subject`** 인터페이스가 정의되어 있으며 **`attach`**, **`detach`**, **`notifyObservers`** 메서드를 정의합니다. **`WeatherStation`** 클래스는 이 인터페이스를 구현하고, 날씨에 관심 있는 **`Observer`** 목록을 유지합니다.

**`Observer`** 인터페이스는 Subject의 상태가 변경될 때 호출되는 **`update`** 메서드를 정의합니다.

**`PhoneDisplay`**와 **`TVDisplay`** 클래스는 모두 **`Observer`**이며 업데이트를 수신하면 콘솔에 날씨 메시지를 출력합니다.

**`main`** 메서드에서는 **`WeatherStation`** 객체와 두 개의 **`Observer`**(**`PhoneDisplay`**와 **`TVDisplay`**)를 만듭니다. 두 **`Observer`** 모두를 **`WeatherStation`**에 추가한 다음 날씨를 "Sunny"로 설정합니다. 두 **`Observer`** 모두가 알림을 받고 메시지를 콘솔에 출력합니다. 그런 다음 **`PhoneDisplay`**를 분리하고 날씨를 "Rainy"로 설정합니다. **`TVDisplay`**만이 업데이트를 받고 콘솔에 메시지를 출력합니다.
