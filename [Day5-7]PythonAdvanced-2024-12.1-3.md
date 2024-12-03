# [PythonAdvanced] 学习笔记- Day 5-7

## 概览

**Date**: 2024-12.1-12.3                                     **Time Spent**: 6 hours                    

**Topics**: Decorators                                  **Difficulty**: ⭐⭐⭐ (1-5 ⭐)

## 今日计划

- [x] 优化MTtranslator代码为异步运行
- [x] 常用设计模式（单例、工厂、观察者）
- [x] 单元测试（pytest）
- [x] 代码质量工具（pylint, black）

## 学习要点

1. [**Singleton 单例**]

   - **Main points**: **单例模式**是一种创建型设计模式， 让你能够保证一个类只有一个实例， 并提供一个访问该实例的全局节点。通常用来控制某些共享资源 （例如数据库或文件） 的访问权限。

     构建步骤：

     1. 将默认构造函数设为私有， 防止其他对象使用单例类的 `new`运算符。

     2. 新建一个静态构建方法作为构造函数。 该函数会 “偷偷” 调用私有构造函数来创建对象， 并将其保存在一个静态成员变量中。 此后所有对于该函数的调用都将返回这一缓存对象。

        ![image-20241203152510238](C:\Users\kawp0\AppData\Roaming\Typora\typora-user-images\image-20241203152510238.png)

        ###### 图1.单例运行示意图

   - **伪代码**

     ```
     // 数据库类会对`getInstance（获取实例）`方法进行定义以让客户端在程序各处
     // 都能访问相同的数据库连接实例。
     class Database is
         // 保存单例实例的成员变量必须被声明为静态类型。
         private static field instance: Database
     
         // 单例的构造函数必须永远是私有类型，以防止使用`new`运算符直接调用构
         // 造方法。
         private constructor Database() is
             // 部分初始化代码（例如到数据库服务器的实际连接）。
             // ……
     
         // 用于控制对单例实例的访问权限的静态方法。
         public static method getInstance() is
             if (Database.instance == null) then
                 acquireThreadLock() and then
                     // 确保在该线程等待解锁时，其他线程没有初始化该实例。
                     if (Database.instance == null) then
                         Database.instance = new Database()
             return Database.instance
     
         // 最后，任何单例都必须定义一些可在其实例上执行的业务逻辑。
         public method query(sql) is
             // 比如应用的所有数据库查询请求都需要通过该方法进行。因此，你可以
             // 在这里添加限流或缓冲逻辑。
             // ……
     
     class Application is
         method main() is
             Database foo = Database.getInstance()
             foo.query("SELECT ……")
             // ……
             Database bar = Database.getInstance()
             bar.query("SELECT ……")
             // 变量 `bar` 和 `foo` 中将包含同一个对象。
     ```

   - **Example Code**

     ```python
     #单例模式是通过使用元类（metaclass）SingletonMeta来实现的。元类在Python中是一个高级特性，它允许你控制类的创建过程。通过定义__call__方法，SingletonMeta元类能够拦截对类的实例化调用，并确保一个类只有一个实例被创建。
     class SingletonMeta(type):
         """
         The Singleton class can be implemented in different ways in Python. Some
         possible methods include: base class, decorator, metaclass. We will use the
         metaclass because it is best suited for this purpose.
         """
     
         _instances = {}
     
         def __call__(cls, *args, **kwargs):
             """
             Possible changes to the value of the `__init__` argument do not affect
             the returned instance.
             __call__方法在Python中是一个特殊方法，它允许类的实例像函数那样被调用。
             然而，在元类中重写__call__方法，当尝试创建类的实例时会调用这个__call__方法。
             """
             if cls not in cls._instances:
                 instance = super().__call__(*args, **kwargs)
                 cls._instances[cls] = instance
             return cls._instances[cls]
     
     
     class Singleton(metaclass=SingletonMeta):
         def some_business_logic(self):
             """
             Finally, any singleton should define some business logic, which can be
             executed on its instance.
             """
     
             # ...
     
     
     if __name__ == "__main__":
         # The client code.
     
         s1 = Singleton()
         s2 = Singleton()
     
         if id(s1) == id(s2):
             print("Singleton works, both variables contain the same instance.")
         else:
             print("Singleton failed, variables contain different instances.")
             
     >>>Outputs:
         Singleton works, both variables contain the same instance.
     ```

1. [**Factory 工厂代码**]

   - **Main points**: [工厂之间的区别](https://refactoringguru.cn/design-patterns/factory-comparison)

     **工厂**是一个含义模糊的术语， 表示可以创建一些东西的函数、 方法或类。**工厂方法模式**是一种创建型设计模式， 其在母类中提供一个创建对象的方法， 允许子类决定实例化对象的类型。 最常见的情况下， 工厂创建的是对象。 但是它们也可以创建文件和数据库记录等其他东西。

     **产品 （Product）** 将会对接口进行声明。 对于所有由创建者及其子类构建的对象， 这些接口都是通用的。

     **具体产品 （Concrete Products）** 是产品接口的不同实现。

     **创建者 （Creator）** 类声明返回产品对象的工厂方法。 该方法的返回对象类型必须与产品接口相匹配。

     你可以将工厂方法声明为抽象方法， 强制要求每个子类以不同方式实现该方法。 或者， 你也可以在基础工厂方法中返回默认产品类型。

     注意， 尽管它的名字是创建者， 但它最主要的职责并不是创建产品。 一般来说， 创建者类包含一些与产品相关的核心业务逻辑。 工厂方法将这些逻辑处理从具体产品类中分离出来。 打个比方， 大型软件开发公司拥有程序员培训部门。 但是， 这些公司的主要工作还是编写代码， 而非生产程序员。

     **具体创建者** **（Concrete Creators）** 将会重写基础工厂方法， 使其返回不同类型的产品。注意， 并不一定每次调用工厂方法都会**创建**新的实例。 工厂方法也可以返回缓存、 对象池或其他来源的已有对象。

     ![image-20241203011347246](C:\Users\kawp0\AppData\Roaming\Typora\typora-user-images\image-20241203011347246.png)

   - ######  图2.工厂方法跨平台对话框示例

     1. 基础对话框类使用不同的 UI 组件渲染窗口。 在不同的操作系统下， 这些组件外观或许略有不同， 但其功能保持一致。 Windows 系统中的按钮在 Linux 系统中仍然是按钮。
     2. 如果使用工厂方法， 就不需要为每种操作系统重写对话框逻辑。 如果我们声明了一个在基本对话框类中生成按钮的工厂方法， 那么我们就可以创建一个对话框子类， 并使其通过工厂方法返回 Windows 样式按钮。 子类将继承对话框基础类的大部分代码， 同时在屏幕上根据 Windows 样式渲染按钮。
     3. 如需该模式正常工作， 基础对话框类必须使用抽象按钮 （例如基类或接口）， 以便将其扩展为具体按钮。 这样一来， 无论对话框中使用何种类型的按钮， 其代码都可以正常工作。

   - **UI伪代码**

     ```python
     // 创建者类声明的工厂方法必须返回一个产品类的对象。创建者的子类通常会提供
     // 该方法的实现。
     class Dialog is
         // 创建者还可提供一些工厂方法的默认实现。
         abstract method createButton():Button
     
         // 请注意，创建者的主要职责并非是创建产品。其中通常会包含一些核心业务
         // 逻辑，这些逻辑依赖于由工厂方法返回的产品对象。子类可通过重写工厂方
         // 法并使其返回不同类型的产品来间接修改业务逻辑。
         method render() is
             // 调用工厂方法创建一个产品对象。
             Button okButton = createButton()
             // 现在使用产品。
             okButton.onClick(closeDialog)
             okButton.render()
     
     
     // 具体创建者将重写工厂方法以改变其所返回的产品类型。
     class WindowsDialog extends Dialog is
         method createButton():Button is
             return new WindowsButton()
     
     class WebDialog extends Dialog is
         method createButton():Button is
             return new HTMLButton()
     
     
     // 产品接口中将声明所有具体产品都必须实现的操作。
     interface Button is
         method render()
         method onClick(f)
     
     // 具体产品需提供产品接口的各种实现。
     class WindowsButton implements Button is
         method render(a, b) is
             // 根据 Windows 样式渲染按钮。
         method onClick(f) is
             // 绑定本地操作系统点击事件。
     
     class HTMLButton implements Button is
         method render(a, b) is
             // 返回一个按钮的 HTML 表述。
         method onClick(f) is
             // 绑定网络浏览器的点击事件。
     
     
     class Application is
         field dialog: Dialog
     
         // 程序根据当前配置或环境设定选择创建者的类型。
         method initialize() is
             config = readApplicationConfigFile()
     
             if (config.OS == "Windows") then
                 dialog = new WindowsDialog()
             else if (config.OS == "Web") then
                 dialog = new WebDialog()
             else
                 throw new Exception("错误！未知的操作系统。")
     
         // 当前客户端代码会与具体创建者的实例进行交互，但是必须通过其基本接口
         // 进行。只要客户端通过基本接口与创建者进行交互，你就可将任何创建者子
         // 类传递给客户端。
         method main() is
             this.initialize()
             dialog.render()
     ```

   - **Example code**

     ```python
     """
     抽象产品是一个类，包含未指定的operation操作，具体的产品以抽象产品为为基础，定义具体的operation。同理Creator可以类比为生产线，它包含工厂方法，和operation，opreation中使用工厂方法返回抽象产品。具体的ConcreteCreator以Creator为基础，提供了不同的工厂方法，并返回具体的产品。在使用中，客户提出对具体产线的需求，产线调用工厂方法返回具体产品。
     抽象产品定义了操作的接口，具体产品实现了这些操作；抽象创建者定义了如何生产产品的接口（通过工厂方法），具体创建者则实现了具体的生产逻辑（即返回具体的产品）；客户端代码与抽象创建者交互，以获取所需的产品。
     """
     from __future__ import annotations
     from abc import ABC, abstractmethod
     
     
     class Creator(ABC):
         """
         The Creator class declares the factory method that is supposed to return an
         object of a Product class. The Creator's subclasses usually provide the
         implementation of this method.
         Creator是一个抽象类，它声明了一个工厂方法factory_method，该方法应该返回一个Product对象。
         还提供了一个some_operation方法，该方法演示了如何使用由工厂方法创建的产品对象。
         """
     
         @abstractmethod
         def factory_method(self):
             """
             Note that the Creator may also provide some default implementation of
             the factory method.
             """
             pass
     
         def some_operation(self) -> str:
             """
             Also note that, despite its name, the Creator's primary responsibility
             is not creating products. Usually, it contains some core business logic
             that relies on Product objects, returned by the factory method.
             Subclasses can indirectly change that business logic by overriding the
             factory method and returning a different type of product from it.
             """
     
             # Call the factory method to create a Product object.
             product = self.factory_method()
     
             # Now, use the product.
             result = f"Creator: The same creator's code has just worked with {product.operation()}"
     
             return result
     
     
     """
     Concrete Creators override the factory method in order to change the resulting
     product's type.
     """
     
     
     class ConcreteCreator1(Creator):
         """
         Note that the signature of the method still uses the abstract product type,
         even though the concrete product is actually returned from the method. This
         way the Creator can stay independent of concrete product classes.
         """
     
         def factory_method(self) -> Product:
             return ConcreteProduct1()
     
     
     class ConcreteCreator2(Creator):
         def factory_method(self) -> Product:
             return ConcreteProduct2()
     
     
     class Product(ABC):
         """
         The Product interface declares the operations that all concrete products
         must implement.
         Product是一个抽象类，它定义了一个接口，该接口将由具体产品类来实现。。
         """
     
         @abstractmethod
         def operation(self) -> str:
             pass
     
     
     """
     Concrete Products provide various implementations of the Product interface.
     """
     
     
     class ConcreteProduct1(Product):
         def operation(self) -> str:
             return "{Result of the ConcreteProduct1}"
     
     
     class ConcreteProduct2(Product):
         def operation(self) -> str:
             return "{Result of the ConcreteProduct2}"
     
     
     def client_code(creator: Creator) -> None:
         """
         The client code works with an instance of a concrete creator, albeit through
         its base interface. As long as the client keeps working with the creator via
         the base interface, you can pass it any creator's subclass.
         """
     
         print(f"Client: I'm not aware of the creator's class, but it still works.\n"
               f"{creator.some_operation()}", end="")
     
     
     if __name__ == "__main__":
         print("App: Launched with the ConcreteCreator1.")
         client_code(ConcreteCreator1())
         print("\n")
     
         print("App: Launched with the ConcreteCreator2.")
         client_code(ConcreteCreator2())
         
     >>>Output:
     App: Launched with the ConcreteCreator1.
     Client: I'm not aware of the creator's class, but it still works.
     Creator: The same creator's code has just worked with {Result of the ConcreteProduct1}
     
     App: Launched with the ConcreteCreator2.
     Client: I'm not aware of the creator's class, but it still works.
     Creator: The same creator's code has just worked with {Result of the ConcreteProduct2}
     ```

1. [**Abstract Factory**]

   - **Main points**: 

     **抽象工厂**是一种创建型设计模式， 它能创建一系列相关的对象， 而无需指定其具体类。抽象工厂定义了用于创建不同产品的接口， 但将实际的创建工作留给了具体工厂类。 每个工厂类型都对应一个特定的产品变体。

     ![image-20241202221256107](C:\Users\kawp0\AppData\Roaming\Typora\typora-user-images\image-20241202221256107.png)

     ######                                                                     图3.抽象工厂图示

     - **抽象产品** （Abstract Product） 为构成系列产品的一组不同但相关的产品声明接口。

     - **具体产品** （Concrete Product） 是抽象产品的多种不同类型实现。 所有变体 （维多利亚/现代） 都必须实现相应的抽象产品 （椅子/沙发）。

     - **抽象工厂** （Abstract Factory） 接口声明了一组创建各种抽象产品的方法。

     - **具体工厂** （Concrete Factory） 实现抽象工厂的构建方法。 每个具体工厂都对应特定产品变体， 且仅创建此种产品变体。

     - 尽管具体工厂会对具体产品进行初始化， 其构建方法签名必须返回相应的*抽象*产品。 这样， 使用工厂类的客户端代码就不会与工厂创建的特定产品变体耦合。 **客户端** （Client） 只需通过抽象接口调用工厂和产品对象， 就能与任何具体工厂/产品变体交互。

       ![image-20241202221735694](C:\Users\kawp0\AppData\Roaming\Typora\typora-user-images\image-20241202221735694.png)

       ######                                                                图4. 跨平台UI实例

       **实现逻辑：**应用程序启动后检测当前操作系统。 根据该信息， 应用程序通过与该操作系统对应的类创建工厂对象。 其余代码使用该工厂对象创建 UI 元素。 这样可以避免生成错误类型的元素。

       使用这种方法， 客户端代码只需调用抽象接口， 而无需了解具体工厂类和 UI 元素。 此外， 客户端代码还支持未来添加新的工厂或 UI 元素。

       这样一来， 每次在应用程序中添加新的 UI 元素变体时， 你都无需修改客户端代码。 你只需创建一个能够生成这些 UI 元素的工厂类， 然后稍微修改应用程序的初始代码， 使其能够选择合适的工厂类即可。

   - **GUI Fake Example Code**

     ```python
     // 抽象工厂接口声明了一组能返回不同抽象产品的方法。这些产品属于同一个系列
     // 且在高层主题或概念上具有相关性。同系列的产品通常能相互搭配使用。系列产
     // 品可有多个变体，但不同变体的产品不能搭配使用。
     interface GUIFactory is
         method createButton():Button
         method createCheckbox():Checkbox
     
     
     // 具体工厂可生成属于同一变体的系列产品。工厂会确保其创建的产品能相互搭配
     // 使用。具体工厂方法签名会返回一个抽象产品，但在方法内部则会对具体产品进
     // 行实例化。
     class WinFactory implements GUIFactory is
         method createButton():Button is
             return new WinButton()
         method createCheckbox():Checkbox is
             return new WinCheckbox()
     
     // 每个具体工厂中都会包含一个相应的产品变体。
     class MacFactory implements GUIFactory is
         method createButton():Button is
             return new MacButton()
         method createCheckbox():Checkbox is
             return new MacCheckbox()
     
     
     // 系列产品中的特定产品必须有一个基础接口。所有产品变体都必须实现这个接口。
     interface Button is
         method paint()
     
     // 具体产品由相应的具体工厂创建。
     class WinButton implements Button is
         method paint() is
             // 根据 Windows 样式渲染按钮。
     
     class MacButton implements Button is
         method paint() is
             // 根据 macOS 样式渲染按钮
     
     // 这是另一个产品的基础接口。所有产品都可以互动，但是只有相同具体变体的产
     // 品之间才能够正确地进行交互。
     interface Checkbox is
         method paint()
     
     class WinCheckbox implements Checkbox is
         method paint() is
             // 根据 Windows 样式渲染复选框。
     
     class MacCheckbox implements Checkbox is
         method paint() is
             // 根据 macOS 样式渲染复选框。
     
     // 客户端代码仅通过抽象类型（GUIFactory、Button 和 Checkbox）使用工厂
     // 和产品。这让你无需修改任何工厂或产品子类就能将其传递给客户端代码。
     class Application is
         private field factory: GUIFactory
         private field button: Button
         constructor Application(factory: GUIFactory) is
             this.factory = factory
         method createUI() is
             this.button = factory.createButton()
         method paint() is
             button.paint()
     
     
     // 程序会根据当前配置或环境设定选择工厂类型，并在运行时创建工厂（通常在初
     // 始化阶段）。
     class ApplicationConfigurator is
         method main() is
             config = readApplicationConfigFile()
     
             if (config.OS == "Windows") then
                 factory = new WinFactory()
             else if (config.OS == "Mac") then
                 factory = new MacFactory()
             else
                 throw new Exception("错误！未知的操作系统。")
     
             Application app = new Application(factory)
     
     ```

   - **AF Example Code**

     ```python
     from __future__ import annotations
     from abc import ABC, abstractmethod
     
     
     class AbstractFactory(ABC):
         """
         The Abstract Factory interface declares a set of methods that return
         different abstract products. These products are called a family and are
         related by a high-level theme or concept. Products of one family are usually
         able to collaborate among themselves. A family of products may have several
         variants, but the products of one variant are incompatible with products of
         another.
         """
         @abstractmethod
         def create_product_a(self) -> AbstractProductA:
             pass
     
         @abstractmethod
         def create_product_b(self) -> AbstractProductB:
             pass
     
     
     class ConcreteFactory1(AbstractFactory):
         """
         Concrete Factories produce a family of products that belong to a single
         variant. The factory guarantees that resulting products are compatible. Note
         that signatures of the Concrete Factory's methods return an abstract
         product, while inside the method a concrete product is instantiated.
         """
     
         def create_product_a(self) -> AbstractProductA:
             return ConcreteProductA1()
     
         def create_product_b(self) -> AbstractProductB:
             return ConcreteProductB1()
     
     
     class ConcreteFactory2(AbstractFactory):
         """
         Each Concrete Factory has a corresponding product variant.
         """
     
         def create_product_a(self) -> AbstractProductA:
             return ConcreteProductA2()
     
         def create_product_b(self) -> AbstractProductB:
             return ConcreteProductB2()
     
     
     class AbstractProductA(ABC):
         """
         Each distinct product of a product family should have a base interface. All
         variants of the product must implement this interface.
         """
     
         @abstractmethod
         def useful_function_a(self) -> str:
             pass
     
     
     """
     Concrete Products are created by corresponding Concrete Factories.
     """
     
     
     class ConcreteProductA1(AbstractProductA):
         def useful_function_a(self) -> str:
             return "The result of the product A1."
     
     
     class ConcreteProductA2(AbstractProductA):
         def useful_function_a(self) -> str:
             return "The result of the product A2."
     
     
     class AbstractProductB(ABC):
         """
         Here's the the base interface of another product. All products can interact
         with each other, but proper interaction is possible only between products of
         the same concrete variant.
         """
         @abstractmethod
         def useful_function_b(self) -> None:
             """
             Product B is able to do its own thing...
             """
             pass
     
         @abstractmethod
         def another_useful_function_b(self, collaborator: AbstractProductA) -> None:
             """
             ...but it also can collaborate with the ProductA.
     
             The Abstract Factory makes sure that all products it creates are of the
             same variant and thus, compatible.
             """
             pass
     
     
     """
     Concrete Products are created by corresponding Concrete Factories.
     """
     
     
     class ConcreteProductB1(AbstractProductB):
         def useful_function_b(self) -> str:
             return "The result of the product B1."
     
         """
         The variant, Product B1, is only able to work correctly with the variant,
         Product A1. Nevertheless, it accepts any instance of AbstractProductA as an
         argument.
         """
     
         def another_useful_function_b(self, collaborator: AbstractProductA) -> str:
             result = collaborator.useful_function_a()
             return f"The result of the B1 collaborating with the ({result})"
     
     
     class ConcreteProductB2(AbstractProductB):
         def useful_function_b(self) -> str:
             return "The result of the product B2."
     
         def another_useful_function_b(self, collaborator: AbstractProductA):
             """
             The variant, Product B2, is only able to work correctly with the
             variant, Product A2. Nevertheless, it accepts any instance of
             AbstractProductA as an argument.
             """
             result = collaborator.useful_function_a()
             return f"The result of the B2 collaborating with the ({result})"
     
     
     def client_code(factory: AbstractFactory) -> None:
         """
         The client code works with factories and products only through abstract
         types: AbstractFactory and AbstractProduct. This lets you pass any factory
         or product subclass to the client code without breaking it.
         """
         product_a = factory.create_product_a()
         product_b = factory.create_product_b()
     
         print(f"{product_b.useful_function_b()}")
         print(f"{product_b.another_useful_function_b(product_a)}", end="")
     
     
     if __name__ == "__main__":
         """
         The client code can work with any concrete factory class.
         """
         print("Client: Testing client code with the first factory type:")
         client_code(ConcreteFactory1())
     
         print("\n")
     
         print("Client: Testing the same client code with the second factory type:")
         client_code(ConcreteFactory2())
         
     >>>Output:
     Client: Testing client code with the first factory type:
     The result of the product B1.
     The result of the B1 collaborating with the (The result of the product A1.)
     
     Client: Testing the same client code with the second factory type:
     The result of the product B2.
     The result of the B2 collaborating with the (The result of the product A2.)
     ```

1. [**Observer Pattern 观察者模式**]

   - **Main points**:观察者模式（Observer Pattern）也被称为发布-订阅模式（Publish-Subscribe Pattern），它定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个主题对象。这个主题对象在状态发生变化时，会通知所有观察者对象，使它们能够自动更新自己的状态或执行相应的操作。

     **核心组件**：

     1. **主题（Subject）**：主题是观察者模式中的核心组件，它负责维护一个观察者列表，并提供添加、删除和通知观察者的方法。当主题的状态发生变化时，它会遍历这个列表，通知所有的观察者。
     2. **观察者（Observer）**：观察者是依赖于主题状态的对象。它们实现了一个更新接口，以便在接收到主题的通知时能够执行相应的操作。

   - **Example Code**

     ```python
     # 观察者接口
     class Observer:
         def update(self, subject):
             raise NotImplementedError("Subclasses must implement this method.")
     
     # 被观察者基类
     class Subject:
         def __init__(self):
             self._observers = []  # 存储观察者
     
         def attach(self, observer):
             if observer not in self._observers:
                 self._observers.append(observer)
     
         def detach(self, observer):
             self._observers.remove(observer)
     
         def notify(self):
             for observer in self._observers:
                 observer.update(self)
     
     # 具体的被观察者类（例如股票价格）
     class Stock(Subject):
         def __init__(self, name, price):
             super().__init__()
             self.name = name
             self._price = price
     
         @property
         def price(self):
             return self._price
     
         @price.setter
         def price(self, new_price):
             if self._price != new_price:
                 self._price = new_price
                 self.notify()  # 通知观察者价格已变化
     
     # 具体的观察者类（例如投资者）
     class Investor(Observer):
         def __init__(self, name):
             self.name = name
     
         def update(self, subject):
             print(f"通知{self.name}:{subject.name}的价格已更新为{subject.price}.")
     
     # 使用观察者模式
     if __name__ == "__main__":
         # 创建股票对象
         google_stock = Stock("Google", 1500)
         
         # 创建投资者对象
         investor_a = Investor("Alice")
         investor_b = Investor("Bob")
         
         # 添加观察者
         google_stock.attach(investor_a)
         google_stock.attach(investor_b)
         
         # 修改价格并触发通知
         google_stock.price = 1550
         google_stock.price = 1600
         
         # 移除观察者
         google_stock.detach(investor_a)
         google_stock.price = 1650
     ```

1. [**pytest 单元测试**]

   - **Main point**：

     pytest是一个强大的Python测试框架，适用于简单和复杂的测试需求。它提供了丰富的功能，如断言重写、测试夹具（fixtures）、插件系统等，使得编写测试用例变得更加简单和灵活。

   - **Example Code**

     ```python
     # 安装pytest
     # pip install pytest
     
     """
     定义了一个简单的函数count_num，并编写了一个测试用例test_count来验证其正确性。使用pytest运行测试时，它会找到所有以test_开头的函数，并执行它们。
     """
     # test_example.py
     def count_num(a: list) -> int:
         return len(a)
     
     def test_count():
         assert count_num([1, 2, 3]) == 3
     
     # 运行测试
     # pytest test_example.py
     
     ```

1. [**代码质量工具 pylint **]

   - **Main point**:

     pylint是一个Python代码分析工具，它可以静态分析Python代码并发现潜在的问题。pylint基于PEP 8规范和其他最佳实践，提供了一系列的规则来评估代码的质量，并给出相应的建议和修复方法。它不仅可以检查代码的语法错误，还可以检查代码的风格、命名规范、代码复杂度等方面的问题。

   - **Example Code**

     ```bash
     # 安装pylint
     # pip install pylint
     
     # 运行pylint检查代码
     # pylint your_script.py
     # pylint会输出详细的报告，包括每个问题的描述、位置和建议的修复方法。
     ```

1. [**代码质量工具 black **]

   - **Main point**:

     pylint是一个Python代码分析工具，它可以静态分析Python代码并发现潜在的问题。pylint基于PEP 8规范和其他最佳实践，提供了一系列的规则来评估代码的质量，并给出相应的建议和修复方法。它不仅可以检查代码的语法错误，还可以检查代码的风格、命名规范、代码复杂度等方面的问题。

   - **Example Code**

     ```bash
     # 安装pylint
     # pip install pylint
     
     # 运行pylint检查代码
     # pylint your_script.py
     # pylint会输出详细的报告，包括每个问题的描述、位置和建议的修复方法。
     ```

## 代码练习

```python
"""单例"""
from joblib.externals.cloudpickle import instance

class SingletonMeta(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            instance = super().__call__(*args, **kwargs)
            cls._instances[cls] = instance
        return cls._instances[cls]

class Singleton(metaclass=SingletonMeta):
    pass

def main_s():
    s1=Singleton()
    s2=Singleton()
    if id(s1)==id(s2):
        print("singleton works")
    else:
        print("singleton fail")

'''
factory
'''
class Product:
    def operation(self):
        pass
class Creator:
    def factory_method(self):
        pass
    def some_operation(self):
        product = self.factory_method()
        result = f"just worked with {product.operation()}"
        return result

class CProduct1(Product):
    def operation(self):
        return "Product1"
class CProduct2(Product):
    def operation(self):
        return "Product2"

class CCreator1(Creator):
    def factory_method(self):
        return CProduct1()
class CCreator2(Creator):
    def factory_method(self):
        return CProduct2()

def client(creator: Creator):
    print(f"Client: I'm not aware of the creator's class, but it still works.\n"
          f"{creator.some_operation()}", end="")

def mainf():
    print("Launched with CCreator1")
    client(CCreator1())
    print("\n")
    print("Launched with CCreator2")
    client(CCreator2())


"""
观察者
"""
class Observer:
    def update(self,subject):
        raise NotImplemented("Subject is not implemented")
#被观察者
class Subject:
    def __init__(self):
        self.observer = [] # 观察者列表
    def attach(self, observer): #添加观察者
        if observer not in self.observer:
            self.observer.append(observer)
    def detach(self,observer):
        self.observer.remove(observer)
    def notify(self):
        for observer in self.observer:
            observer.update(self)
#被观察者
class Stock(Subject):
    def __init__(self, price, name):
        super().__init__()
        self._price = price  # 使用私有变量来存储价格
        self.name = name

    @property
    def price(self):
        return self._price
    #@property 装饰器来创建一个属性 price，
    #它有一个 getter 和一个 setter。
    #setter 方法会在价格改变时调用 notify。
    @price.setter
    def price(self, new_price):
        if self._price != new_price:
            self._price = new_price
            self.notify()
class Investor(Observer):
    def __init__(self, name):
        self.name = name
    def update(self,subject):
        print(f"{self.name}: {subject.name}的价格更新为{subject.price}")
def mainO():
    # 创建股票对象
    google_stock = Stock("Google", 1500)
    # 创建投资者对象
    investor_a = Investor("Alice")
    investor_b = Investor("Bob")
    # 添加观察者
    google_stock.attach(investor_a)
    google_stock.attach(investor_b)
    # 修改价格并触发通知
    google_stock.price = 1550
    google_stock.price = 1600

mainO()
```

遇见的挑战难及解决方法和笔记点见注释。

The challenges encountered, solutions, and key points for note-taking are indicated in the comments.

## 使用资源

- [design-patterns](https://refactoringguru.cn/design-patterns/factory-comparison) 
