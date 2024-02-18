---
layout: post
title: Java-상속 > 업캐스팅, 다운캐스팅
date: 2024-02-17 14:12:20 +0900
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img:  # Add image post (optional)
fig-caption: mac.jpg
tags: [BackEnd]
---
# 업캐스팅
업캐스팅은 객체 지향 프로그래밍에서 중요한 개념 중 하나입니다.  
이를 통해 다형성(polymorphism)을 실현할 수 있으며, 코드의 유연성과 재사용성을 높일 수 있습니다. 

장점: 
1. 다형성: 하나의 변수가 여러 가지 형태의 객체를 참조할 수 있으므로 코드가 더 유연해진다.
2. 코드 재사용성 향상 
3. 유지보수성 향상(코드를 더 추상적으로 작성함으로써, 향후 변경이나 확장에 대응하기가 더 쉬워진다.  
    새로운 서브 클래스를 추가하거나 ㅜ정해도 기존 코드의 수정이 필요없을 수 있다.)

단점: 타입 변환 문제, 슈퍼 클래스의 메서드만 사용가능, 속성의 손실. 즉, 서브클래스의 고유 기능을 사용하지 못하고 사용을 하려면 다운 캐스팅을 해야 하는데 나름 복잡하다. 잘못하면 런타임 에러 발생.

# 다운캐스팅
업캐스팅된 객체를 다시 원래의 서브 클래스 타입으로 변환하는 것을 다운캐스팅이라고 합니다.  
다운캐스팅을 하려면 업캐스팅된 객체를 다운캐스팅하려는 서브 클래스 타입으로 형변환해야 합니다.  
이때 (서브클래스타입) 변수명 형식으로 형변환을 수행합니다.

# 업캐스팅 다운캐스팅 예제
```java
// 슈퍼 클래스
class Animal {
    void makeSound() {
        System.out.println("동물 소리를 내다.");
    }
}

// 서브 클래스
class Dog extends Animal {
    void makeSound() {
        System.out.println("멍멍!");
    }
    
    void fetch() {
        System.out.println("물건을 가져오다.");
    }
}

public class Main {
    public static void main(String[] args) {
        // 업캐스팅
        Animal animal = new Dog(); // Dog 객체를 Animal 타입으로 업캐스팅
        
        // 슈퍼 클래스의 메서드 호출
        animal.makeSound(); // "멍멍!"이 출력됨
        
        // 다운캐스팅
        Dog dog = (Dog) animal; // Animal 객체를 다시 Dog 타입으로 다운캐스팅
        
        // 서브 클래스의 메서드 호출
        dog.fetch(); // "물건을 가져옵니다!"가 출력됨
    }
}
```

# 업캐스팅의 다형성
```java
class Animal {
    public void sound() {
        System.out.println("Some sound...");
    }
}

class Dog extends Animal {
    public void sound() {
        System.out.println("Woof!");
    }
}

class Cat extends Animal {
    public void sound() {
        System.out.println("Meow!");
    }
}

class Bird extends Animal {
    public void sound() {
        System.out.println("Tweet!");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal dog = new Dog(); // 업캐스팅
        Animal cat = new Cat(); // 업캐스팅
        Animal bird = new Bird(); // 업캐스팅
        
        makeSound(dog); // 다형성을 통해 Dog의 sound() 호출
        makeSound(cat); // 다형성을 통해 Cat의 sound() 호출
        makeSound(bird); // 다형성을 통해 Bird의 sound() 호출
    }
    
    public static void makeSound(Animal animal) {
        animal.sound();
    }
}
```

위의 코드에서 makeSound 메서드는 Animal 객체를 매개변수로 받아서 해당 동물의 소리를 출력합니다.  
이때 makeSound 메서드는 실제로 어떤 종류의 동물인지 알 필요가 없습니다.  
이것이 다형성의 한 예시입니다.