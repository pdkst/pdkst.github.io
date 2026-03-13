# 设计模式实战

设计模式是解决软件设计中常见问题的最佳实践，本文介绍常用的设计模式及其在 Java 中的实现。

## 创建型模式

### 单例模式

```java
// 饿汉式
public class EagerSingleton {
    private static final EagerSingleton instance = new EagerSingleton();
    
    private EagerSingleton() {}
    
    public static EagerSingleton getInstance() {
        return instance;
    }
}

// 懒汉式（线程安全）
public class LazySingleton {
    private static volatile LazySingleton instance;
    
    private LazySingleton() {}
    
    public static LazySingleton getInstance() {
        if (instance == null) {
            synchronized (LazySingleton.class) {
                if (instance == null) {
                    instance = new LazySingleton();
                }
            }
        }
        return instance;
    }
}

// 静态内部类（推荐）
public class StaticInnerSingleton {
    private StaticInnerSingleton() {}
    
    private static class Holder {
        private static final StaticInnerSingleton INSTANCE = 
            new StaticInnerSingleton();
    }
    
    public static StaticInnerSingleton getInstance() {
        return Holder.INSTANCE;
    }
}
```

### 工厂模式

```java
// 简单工厂
public interface Shape {
    void draw();
}

public class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing Circle");
    }
}

public class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing Rectangle");
    }
}

public class ShapeFactory {
    public static Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        switch (shapeType.toUpperCase()) {
            case "CIRCLE":
                return new Circle();
            case "RECTANGLE":
                return new Rectangle();
            default:
                return null;
        }
    }
}

// 工厂方法
public interface ShapeFactory {
    Shape createShape();
}

public class CircleFactory implements ShapeFactory {
    @Override
    public Shape createShape() {
        return new Circle();
    }
}
```

### 建造者模式

```java
public class Computer {
    private String cpu;
    private String ram;
    private String storage;
    
    private Computer(Builder builder) {
        this.cpu = builder.cpu;
        this.ram = builder.ram;
        this.storage = builder.storage;
    }
    
    public static class Builder {
        private String cpu;
        private String ram;
        private String storage;
        
        public Builder cpu(String cpu) {
            this.cpu = cpu;
            return this;
        }
        
        public Builder ram(String ram) {
            this.ram = ram;
            return this;
        }
        
        public Builder storage(String storage) {
            this.storage = storage;
            return this;
        }
        
        public Computer build() {
            return new Computer(this);
        }
    }
}

// 使用
Computer computer = new Computer.Builder()
    .cpu("Intel i7")
    .ram("16GB")
    .storage("512GB SSD")
    .build();
```

## 结构型模式

### 适配器模式

```java
// 目标接口
public interface MediaPlayer {
    void play(String audioType, String fileName);
}

// 适配者
public interface AdvancedMediaPlayer {
    void playVlc(String fileName);
    void playMp4(String fileName);
}

public class Mp4Player implements AdvancedMediaPlayer {
    @Override
    public void playVlc(String fileName) {
        // 不支持
    }
    
    @Override
    public void playMp4(String fileName) {
        System.out.println("Playing mp4 file: " + fileName);
    }
}

// 适配器
public class MediaAdapter implements MediaPlayer {
    private AdvancedMediaPlayer advancedMusicPlayer;
    
    public MediaAdapter(String audioType) {
        if (audioType.equalsIgnoreCase("mp4")) {
            advancedMusicPlayer = new Mp4Player();
        }
    }
    
    @Override
    public void play(String audioType, String fileName) {
        if (audioType.equalsIgnoreCase("mp4")) {
            advancedMusicPlayer.playMp4(fileName);
        }
    }
}
```

### 装饰器模式

```java
// 组件接口
public interface Shape {
    void draw();
}

// 具体组件
public class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Shape: Circle");
    }
}

// 装饰器
public abstract class ShapeDecorator implements Shape {
    protected Shape decoratedShape;
    
    public ShapeDecorator(Shape decoratedShape) {
        this.decoratedShape = decoratedShape;
    }
    
    @Override
    public void draw() {
        decoratedShape.draw();
    }
}

// 具体装饰器
public class RedShapeDecorator extends ShapeDecorator {
    public RedShapeDecorator(Shape decoratedShape) {
        super(decoratedShape);
    }
    
    @Override
    public void draw() {
        decoratedShape.draw();
        setRedBorder(decoratedShape);
    }
    
    private void setRedBorder(Shape decoratedShape) {
        System.out.println("Border Color: Red");
    }
}

// 使用
Shape circle = new Circle();
Shape redCircle = new RedShapeDecorator(new Circle());
redCircle.draw();
```

## 行为型模式

### 观察者模式

```java
import java.util.ArrayList;
import java.util.List;

// 主题
public class Subject {
    private List<Observer> observers = new ArrayList<>();
    private int state;
    
    public int getState() {
        return state;
    }
    
    public void setState(int state) {
        this.state = state;
        notifyAllObservers();
    }
    
    public void attach(Observer observer) {
        observers.add(observer);
    }
    
    public void notifyAllObservers() {
        for (Observer observer : observers) {
            observer.update();
        }
    }
}

// 观察者
public abstract class Observer {
    protected Subject subject;
    
    public abstract void update();
}

public class BinaryObserver extends Observer {
    public BinaryObserver(Subject subject) {
        this.subject = subject;
        this.subject.attach(this);
    }
    
    @Override
    public void update() {
        System.out.println("Binary String: " + 
            Integer.toBinaryString(subject.getState()));
    }
}

public class OctalObserver extends Observer {
    public OctalObserver(Subject subject) {
        this.subject = subject;
        this.subject.attach(this);
    }
    
    @Override
    public void update() {
        System.out.println("Octal String: " + 
            Integer.toOctalString(subject.getState()));
    }
}
```

### 策略模式

```java
// 策略接口
public interface PaymentStrategy {
    void pay(int amount);
}

// 具体策略
public class CreditCardStrategy implements PaymentStrategy {
    private String name;
    private String cardNumber;
    
    public CreditCardStrategy(String name, String cardNumber) {
        this.name = name;
        this.cardNumber = cardNumber;
    }
    
    @Override
    public void pay(int amount) {
        System.out.println(amount + " paid with credit/debit card");
    }
}

public class PayPalStrategy implements PaymentStrategy {
    private String emailId;
    
    public PayPalStrategy(String email) {
        this.emailId = email;
    }
    
    @Override
    public void pay(int amount) {
        System.out.println(amount + " paid using PayPal");
    }
}

// 上下文
public class ShoppingCart {
    private List<String> items;
    private PaymentStrategy paymentStrategy;
    
    public ShoppingCart() {
        this.items = new ArrayList<>();
    }
    
    public void addItem(String item) {
        items.add(item);
    }
    
    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;
    }
    
    public void checkout() {
        int amount = items.size() * 100;
        paymentStrategy.pay(amount);
    }
}

// 使用
ShoppingCart cart = new ShoppingCart();
cart.addItem("Item1");
cart.addItem("Item2");

// 使用信用卡支付
cart.setPaymentStrategy(new CreditCardStrategy("pdkst", "123456"));
cart.checkout();

// 使用 PayPal 支付
cart.setPaymentStrategy(new PayPalStrategy("pdkst@example.com"));
cart.checkout();
```

### 模板方法模式

```java
public abstract class Game {
    abstract void initialize();
    abstract void startPlay();
    abstract void endPlay();
    
    // 模板方法
    public final void play() {
        initialize();
        startPlay();
        endPlay();
    }
}

public class Cricket extends Game {
    @Override
    void initialize() {
        System.out.println("Cricket Game Initialized!");
    }
    
    @Override
    void startPlay() {
        System.out.println("Cricket Game Started!");
    }
    
    @Override
    void endPlay() {
        System.out.println("Cricket Game Finished!");
    }
}

public class Football extends Game {
    @Override
    void initialize() {
        System.out.println("Football Game Initialized!");
    }
    
    @Override
    void startPlay() {
        System.out.println("Football Game Started!");
    }
    
    @Override
    void endPlay() {
        System.out.println("Football Game Finished!");
    }
}
```

## 设计原则

### SOLID 原则

1. **单一职责原则 (SRP)**
   - 一个类应该只有一个改变的理由

2. **开闭原则 (OCP)**
   - 对扩展开放，对修改关闭

3. **里氏替换原则 (LSP)**
   - 子类应该可以替换父类

4. **接口隔离原则 (ISP)**
   - 客户不应该依赖它不需要的接口

5. **依赖倒置原则 (DIP)**
   - 高层模块不应该依赖低层模块，都应该依赖于抽象

### 示例

```java
// 依赖倒置原则示例
// 高层模块
public class Switch {
    private Switchable device;
    
    public Switch(Switchable device) {
        this.device = device;
    }
    
    public void press() {
        device.toggle();
    }
}

// 抽象
public interface Switchable {
    void toggle();
}

// 低层模块
public class LightBulb implements Switchable {
    @Override
    public void toggle() {
        System.out.println("Light bulb toggled");
    }
}

public class Fan implements Switchable {
    @Override
    public void toggle() {
        System.out.println("Fan toggled");
    }
}
```

## 总结

设计模式是经验的总结，合理使用可以提高代码的可维护性和可扩展性。但在使用时要注意：

1. 不要过度设计
2. 根据实际情况选择合适的模式
3. 理解模式的适用场景和局限性

## 参考资料

- [设计模式：可复用面向对象软件的基础](https://refactoring.guru/design-patterns)
- [Java 设计模式教程](https://www.tutorialspoint.com/design_pattern/index.htm)

---

*最后更新时间: {docsify-updated}*