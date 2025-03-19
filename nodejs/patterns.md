# 深入设计模式

## 对象之间的关系

### 依赖关系（Dependency）
是类之间最基础的、也是最弱的临时关系。表示一个类（称为客户端类）在某一时刻使用另一个类（称为供应类）的对象。关键特征是如果供应类发生变化，可能会影响到依赖它的客户端类。  
![依赖](https://github.com/BigbangBang/learningNotes/blob/main/picture/patterns-design/%E5%85%B3%E8%81%94.png)

### 关联关系（Association）
是一种较强的、持久的关系。表示一个类直接与另一个类相连。通常是一个类对象包含了另一个类对象的引用。  
![关联](https://github.com/BigbangBang/learningNotes/blob/main/picture/patterns-design/%E5%85%B3%E8%81%94.png)

### 聚合关系（Aggregation）
是一种”较弱“的拥有关系，子对象可以独立于父对象存在。  
![聚合](https://github.com/BigbangBang/learningNotes/blob/main/picture/patterns-design/%E8%81%9A%E5%90%88.png)

### 组合关系（Composition）
是一种“较强”的拥有关系，也成为部分与整体的关系。  
![组合](https://github.com/BigbangBang/learningNotes/blob/main/picture/patterns-design/%E7%BB%84%E5%90%88.png)

类之间的关系还有**继承**和**实现**。

## 代码设计原则   
### 优秀代码的特征   
- 代码复用   
- 扩展性   
  
###    
### 设计原则   
### 封装变化的内容。   
    找到程序中变化的内容将其与不变的内容区分开。   
### 面向接口进行开发，而不是面向实现。   
    面向接口进行开发，而不是面向实现；依赖于抽象类型，而不是具体类。   
### 组合优于继承。   
###    
## SOLID原则   
- **单一职责原则。**   
- **开闭原则。**对于扩展类应该是“开放的”；对于修改类应该是“封闭”的。   
- **里氏替换原则。**当扩展一个类的时候，应该能在不修改客户端代码的情况下将子类的对象作为父类的对象进行传递。   
- **接口隔离原则。**客户端不应该被强迫依赖于其不使用的方法。   
- **依赖倒置原则。**高层次的类不应该依赖低层次的类，两者都应该依赖于抽象接口。抽象接口不应该依赖于具体实现。具体实现应该依赖于抽象接口。   
  
   
## 创建型模式   
- abstract factory（抽象工厂）   
- builder（生成器）   
- factory method（工厂方法）   
- prototype（原型）   
- singleton（单例）   
  
   
### FACTORY METHOD（工厂方法）   
定义一个用于创建对象的接口，让子类决定实例化哪一个类。Factory Method使一个类的实例化延迟到其子类。

### ABSTRACT FACTORY（抽象工厂）   
提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。 
![抽象工厂](https://github.com/BigbangBang/learningNotes/blob/main/picture/patterns-design/%E6%8A%BD%E8%B1%A1%E5%B7%A5%E5%8E%82.png)

优缺点：   
- **它分离了具体的类。** 客户只通过抽象接口操作工厂实例。   
- **它使得易于交换产品系列。**   
- **它利于产品的一致性。**   
- **难以支持新种类的产品。** 添加新种类的产品需求修改AbstractFactory及其子类。 

### BUILDER（生成器）   
使你能够分步骤创建复杂对象。该模式允许你使用相同的创建代码生成不同类型和形式的对象。   

和工厂模式相比。   
*创建什么样的产品和创建什么产品*   
#### 适用场景   
- 当创建复杂子对象的算法应该独立于该对象的组成部分已经他们的装配方式时。   
- 当构造的过程必须允许被构造的对象有不同的表示时。   
  
   
### PROTOTYPE（原型）   
使你能够复制已有对象，而又无需使代码依赖他们所属的类。   
基于已有的对象创建新的对象。   
*注意现实时使用深拷贝还是浅拷贝*   
![原型.png](https://github.com/BigbangBang/learningNotes/blob/main/picture/patterns-design/%E5%8E%9F%E5%9E%8B.png)


### SINGLETON（单利模式） 
让你能够保证一个类只有一个实例，并提供一个访问该实例的全局节点。   

### ADAPTER（适配器）
Adapter模式是的原本由于接口不兼容而不能一起动作的那些类可以一起工作。将一个类的接口转换为客户端希望的另一个接口。   

让汽车可以在火车的轨道上行驶。一个类通过一个中间层可以使用另一个类的方法。是在开发过程中为了弥补设计上的缺陷使用该模式   

**结构：**   
  类适配器可以使用多重继承来处理。仅适用于支持多重继承的语言中。   
  对象适配器使用对象组合。   

### BRIDGE（桥接）  
将抽象部分与它的实现部分分离，使得他们都可以独立地变化。   
将一个功能的使用和实现分为两个部分，抽象部分提供功能接口，实现部分提供真正实现功能的接口。   

优点：   
- 分离接口及其实现，当实现发生变化时未必需要修改接口部分。   
- 提高可扩充性   
- 实现细节对客户端透明   
  
   
### COMPOSITE（组合）  
将对象组合成树形结构以表示“部分-整体”的层次结构。适合树状结构的业务模式。   

### DECORATPR（装饰）
通过将对象放入包含行为的特殊封装对象中来为原对象绑定新的行为。   

### FACADE（外观）  
能为程序库、框架或其他复杂类提供一个简单的接口。降低客户类与系统类交互的复杂度。   

**外观**为现有对象定义了一个新的接口，**适配器**则会试图运用已有的接口。适配器通常只封装一个对象，外观通常会作用于整个对象子系统上。**代理与其服务对象遵循同一接口，使得自己和服务器对象可以互换。**   

### FLYWEIGHT（享元） 
运用共享技术有效地支持大量细粒度的对象。摒弃了在每个对象中保存所有数据的方式，通过共享多个对象共有的相同状态，让你能在有限的内存容量中载入更对对象。   

### PROXY（代理）  
让你能提供对象的替代品或其占位符。代理控制着对于原对象的访问，并允许在将请求提交给对象前进行一些处理。   

相对于外观模式，代理对象与其服务器对象遵循同一个接口，使得自己可以互换。   

## 行为模式   
### CHAIN OF RESPONSIBILITY（责任链）
允许将请求沿着处理者链接进行发送。收到请求后，每个处理者均可对请求进行处理，或将其传递给链上的下个处理者。

### 命令（Command）
它可将请求转换为一个包含与请求相关的所有信息的独立对象。该转换可以让你能根据不同的请求将方法参数化、延迟请求执行或者将其放入队列中，并且可以实现撤销操作。

### 迭代器（Iterator）
让你在不暴露集合底层表现形式（树、队列、栈）的情况下遍历集合中的所有元素。

### 中介者(Intermediary)
能够减少对象之间混乱无序的依赖关系。该模式会限制对象之间的直接交互，迫使它们通过一个中介者对象进行合作。

### 备忘录（Snapshot）
允许在不暴露对象实现细节的情况下保存和恢复对象之前的状态。

### 观察者（Observe）
允许你定义一种订阅机制，可在对象事件发生时通知多个“观察”该对象的其他对象。

### State(状态)
让你能在一个对象的内部状态变化时改变其行为，使其看上去就像改变了自己的所属的类。

根据具体的状态抽象出State类，在需要使用状态的上下文环境Context类中持有状态类。在状态State发生变成时可以修改Context中State实例的指向。
```c++
class TCPConnection {
public:
    TCPConnection();

private:
    friend class TCPState;
    void changeState(TCPState* state) {
        this.state = state;
    }

private:
    TCPState* state;
}

class TCPState {
protected:
    void changeState(TCPConnection *connection, TCPState *state) {
        connection->changeState(state);
    }
}

class TCPClosed: public TCPState {
    // ...
}
```

### 策略（Strategy）
定义一系列算法，把它们一个个封装起来，并且是他们可以相互替换。该模式使得算法可独立于使用他们的客户而变化。

策略类实现一个对外暴露的通用接口，客户端将具体的策略类Strategy对象传递给上下文环境类Context，Context只能通过Strategy暴露的接口进行交互。

### 模板方法（Template Method）
定义一个操作中的算法的骨架，将一些步骤延迟到子类中。 TemplateMethod使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

**适用性**  
* 一次性实现一个算法不变的部分，并将可变的部分留给子类来实现。
* 各自类的公共行为应该被提取出来集中到一个公共父类中以避免重复。
* 控制子类的扩展。 

### 访问者（Visitor）
它能将算法与其所作用的对象隔离开来。

_将原始对象作为参数传递给访问者对象成员方法，以便访问者在实现算法时可以获取到原始对象所包含的一切必要数据_

```c++
class Equipment {
public:
    virutal ~Equipment();

    const char* Name() {return this._name;}
    
    virtual Watt Power();
    virtual Currency NetPrice();
    virtual Currency DiscountPrice();

    virtual void accept(EquipmentVisitor&);
protected:
    Equipment(const char*);
private:
    const char* _name;
}

// 双分流 编译器可以知道访问目标对象具体类型
void Equipment::accept(EquipmentVisitor& visitor) {
    visitor.visitorFloppyDisk(this);
}

class EquipmentVisitor {
public:
    virtual ~EquipmentVisitor();

    virtual void visitFloppyDisk(FloppyDisk*);
    virtual void visitCard(Card *);
    virtual void visitChassis(Chassis*);
    virtual void visitBus(Bus*);

protected:
    // 需要通过派生类构造来实例化对象
    EquipmentVisitor();
}


class InventoryVisitor : public EquipmentVisitor {
public:
    InventoryVisitor();

    Inventory& GetInventory();

    virtual void visitFloppyDisk(FloppyDisk*);
    virtual void visitCard(Card *);
    virtual void visitChassis(Chassis*);
    virtual void visitBus(Bus*);

private: 
    Inventory _inventory;
}

void InventoryVisitor::visitFloppyDisk(FloppyDisk* e) {
    this._inventory.Accumulate(e);
}

void InventoryVisitor::VisitChassis(Chassis* e) {
    this._inbentory.Accumulate(e);
}

```
