# 设计模式

- [创建型模式 _Creational Patterns_](#creational-patterns)
  - [工厂模式 _Factory Pattern_](#factory-pattern)
  - [抽象工厂模式 _Abstract Factory Pattern_](#abstract-factory-pattern)
  - [单例模式 _Singleton Pattern_](#singleton-pattern)
  - [建造者模式 _Builder Pattern_](#builder-pattern)
  - 原型模式 _Prototype Pattern_
- 结构型模式 _Structural Patterns_
  - 适配器模式 _Adapter Pattern_
  - 桥接模式 _Bridge Pattern_
  - 装饰器模式 _Decorator Pattern_
  - 组合模式 _Composite Pattern_
  - 外观模式 _Facade Pattern_
  - 享元模式 _Flyweight Pattern_
  - 代理模式 _Proxy Pattern_
- 行为型模式 _Behavioral Patterns_
  - 策略模式 _Strategy Pattern_
  - 观察者模式 _Observer Pattern_
  - 命令模式 _Command Pattern_
  - 模板方法模式 _Template Method Pattern_
  - 迭代器模式 _Iterator Pattern_
  - 解释器模式 _Interpreter Pattern_
  - 状态模式 _State Pattern_
  - 责任链模式 _Chain of Responsibility Pattern_
  - 中介者模式 _Mediator Pattern_
  - 访问者模式 _Visitor Pattern_
  - 备忘录模式 _Memento Pattern_

## Creational Patterns

### Factory Pattern

> 工厂方法模式定义了一个创建对象的接口,但是由子类决定要实例化的类是哪一个.可以将`对象的创建和使用分离`,使得系统更加灵活

```javascript
class Animal {
  speak() {
    throw new Error("This method must be implemented.");
  }
}

class Dog extends Animal {
  speak() {
    return "Woof!";
  }
}

class Cat extends Animal {
  speak() {
    return "Meow!";
  }
}

class AnimalFactory {
  createAnimal(animalType) {
    switch (animalType) {
      case "dog":
        return new Dog();
      case "cat":
        return new Cat();
      default:
        throw new Error(`Invalid animal type: ${animalType}`);
    }
  }
}

const animalFactory = new AnimalFactory();
const dog = animalFactory.createAnimal("dog");
dog.speak();
const cat = animalFactory.createAnimal("cat");
cat.speak();
```

### Abstract Factory Pattern

> 抽象工厂模式提供了一种封装一组具有相同主题的单个工厂的方式.它有一个接口,`用于创建相关或依赖对象的家族,而不需要指定实际实现的类`.

```javascript
class AnimalFood {
  provide() {
    throw new Error("This method must be implemented.");
  }
}
class AnimalToy {
  provide() {
    throw new Error("This method must be implemented.");
  }
}
class HighQualityDogFood extends AnimalFood {
  provide() {
    return "High quality dog food";
  }
}
class HighQualityDogToy extends AnimalToy {
  provide() {
    return "High quality dog toy";
  }
}
class CheapCatFood extends AnimalFood {
  provide() {
    return "cheap cat food";
  }
}
class CheapCatToy extends AnimalToy {
  provide() {
    return "cheap cat toy";
  }
}
class AnimalProductsAbstractFactory {
  createFood() {
    throw new Error("This method must be implemented");
  }
  createToy() {
    throw new Error("This method must be implemented");
  }
}
class HighQualityAnimalProductsFactory extends AnimalProductsFactory {
  createFood() {
    return new HighQualityDogFood();
  }
  createToy() {
    return new HighQualityDogToy();
  }
}
class CheapAnimalProductsFactory extends AnimalProductsFactory {
  createFood() {
    return new CheapCatFood();
  }
  createToy() {
    return new CheapCatToy();
  }
}

const highQualityAnimalProductsFactory = new HighQualityAnimalProductsFactory();
highQualityAnimalProductsFactory.createFood().provide();
highQualityAnimalProductsFactory.createToy().provide();

const cheapQualityAnimalProductsFactory = new CheapAnimalProductsFactory();
cheapQualityAnimalProductsFactory.createFood().provide();
cheapQualityAnimalProductsFactory.createToy().provide();
```

### Singleton Pattern

> 单例模式的目的是`确保一个类只有一个实例,并为该实例提供全局访问点`.

```javascript
class Logger {
  constructor() {
    if (!Logger.instance) {
      this.logs = [];
      Logger.instance = this;
    }
    return Logger.instance;
  }
  log(message) {
    this.logs.push(message);
    console.log(`Logger:${message}`);
  }
  printLogCount() {
    console.log(`Number of logs: ${this.logs.length}`);
  }
}

const logger = new Logger();

logger.log("First message");
logger.printLogCount();

const anotherLogger = new Logger();
anotherLogger.log("Second message");
anotherLogger.printLogCount();
```

### Builder Pattern

> 建造者模式是一种对象创建设计模式,它旨在`通过一步步的构建流程来创建复杂对象`.

```javascript
class Sandwich(){
  constructor(){
    this.ingredients=[]
  }
  addIngredient(ingredient){
    this.ingredients.push(ingredient)
  }
  toString(){
    return this.ingredients.join(", ")
  }
}
class SandwichBuilder{
  constructor(){
    this.sandwich=new Sandwich()
  }
  reset(){
    this.sandwich=new Sandwich()
  }
  putMeat(meat){
    this.sandwich.addIngredient(meat)
  }
  putCheese(cheese){
    this.sandwich.addIngredient(cheese)
  }
  putVegetables(vegetables){
    this.sandwich.addIngredient(vegetables)
  }
  get result(){
    return this.sandwich
  }
}
class SandwichMaker{
  constructor(){
    this.builder=new SandwichBuilder()
  }

  createCheeseSteakSandwich(){
    this.builder.reset()
    this.builder.putMeat('ribeye steak')
  }
}
```
