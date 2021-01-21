# spring-redis

원문출처 : [https://spring.io/guides/gs/messaging-redis/](https://spring.io/guides/gs/messaging-redis/)

## spring redis 공식문서 가이드를 통해 redis에 대해 알아보자

### What we will build

StringRedisTemplate을 사용하는 앱을 만들어서 string 메시지를 발행하며, MessageListenerAdapter를 사용하는 POJO(Plain Old Java Object) subscriber를 제작해보자.

### Before we start

JDK 1.8 이후 버전, Gradle 4+ or Maven 3.2+, IDE를 준비하자

- redis server 설치 & 작동

    ```python
    brew install redis

    redis-server
    ```

### Create a Spring project

- spring initializer [사이트](https://start.spring.io/)를 이용해서 생성

  ![spring-redis%20e462e08cdf704789be9f3986f17a6263/Screen_Shot_2021-01-21_at_3.23.17_PM.png](https://user-images.githubusercontent.com/37807838/105318596-10202280-5c07-11eb-90c8-89645d683e3c.png)

### Create a Redis message reciever

- redis는 다른 메시징 기반의 앱처럼 message publisher와 messaging receiver가 있다.
- Receiver.java를 통해 메시지를 받는다.  Receiver를 message listener로 등록하면 메시지 처리 메소드 네이밍을 마음대로 정할 수 있다. 이번 예제에서는 메시지 카운팅을 하는 메소드를 등록했다. 메시지를 받으면 신호를 보낼 수 있다.

### Register the Listener and Send a message

- sprig data redis는 메시지를 주고받는 모든 컴포넌트를 제공한다. 해당 기능을 위해 아래 설정들이 필요하다.
    - Redis template
    - message listener container
    - connection factory
- Redis template을 사용해 메시지를 보내고 Receiver와 message lister container를 통해 메시지를 수신한다. connection factory는 redis server와 연결하여 메시징 수신/발신 기능을 컨트롤한다.
- 이번 예제에서는 Jedis 라이브러리의 JedisConnectionFactory의 구현체인 Spring boot의 기본 RedisConnectionFactory를 이용한다.
- listenerAdapter 메소드에 정의된 bean은 message listener container안에서 message listener로 등록되었고 'chat' 토픽을 주시할것이다.
- Receiver.java가 POJO이기 때문에 message listener adapter로 wrap되어야한다. → message listener인터페이스는 addMessageListener()를 통해 구현되었다.
- 또한, message listener는 recieveMessage()를 통해 메시지가 올 때마다 불려지도록 설정되었다.
- connection factory와 message listener container만을 사용해서 메시징 수신을 처리한다.
- RedisTemplate으로 메시징을 발신한다.
    - RedisTemplate의 구현체인 StringRedisTemplate은 일반적인 Redis 사용법이다.
    - key와 value 쌍은 String instance이다.
- main()메소드는 Spring application context를 생성하며 모든것을 시작한다.
    - message listener container를 작동시키고 message listener container는 메시지를 listening한다.
    - 그런 다음, main()메소드는 application context로부터 StringRedisTemplate bean을 회수하고, 'chat'토픽에서 "Hello from Redis!"를 보낸다. 그런다음 applicaiton은 종료된다.

### Build an executable JAR

- 실행가능한 Jar파일로 빌드해보자.

```
//run applicaion
./gradlew bootRun

//build application
./gradlew build

//start built application
java -jar build/libs/redis-0.0.1-SNAPSHOT
```

### Result

![spring-redis%20e462e08cdf704789be9f3986f17a6263/Screen_Shot_2021-01-21_at_4.33.24_PM.png](https://user-images.githubusercontent.com/37807838/105318487-ef57cd00-5c06-11eb-9b1f-52f40ac6cc49.png)