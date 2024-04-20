![image.png](https://cdn.nlark.com/yuque/0/2024/png/38674938/1709707261090-48b5b6e5-05db-487a-94d0-feaafd31eccf.png#averageHue=%23f8f6f6&clientId=ubdb2a4a8-bec4-4&from=paste&height=229&id=u905cb38f&originHeight=227&originWidth=757&originalType=binary&ratio=1.375&rotation=0&showTitle=false&size=63615&status=done&style=none&taskId=uc24874be-fddc-4b52-b313-2986458d9fe&title=&width=763)

| 学号 | 121052021086 | 专业 | 软件工程 |
| --- | --- | --- | --- |
| 姓名 | 王志鸿 | 实验日期 | 2024.3.6 |

# 一、实验目的
加深对适配器模式和桥接设计模式原理的理解
# 二、实验环境

- 操作系统：windows10
- IDEA2023
- JDK11
# 三、实验内容
## 3.1 任务一
现有一个接口DataOperation定义了排序方法Sort(int[])和查找方法search(int[],int)，已知类QuickSort的quickSort(int[])方法实现了快速排序，类BinarySearch的binarySearch(int[],int)方法实现了二分查找算法，现使用适配器模式设计一个系统，在不修改源码的情况下将类QuickSort和类BinarySearch的方法适配到DataOperation接口中。绘制类图并编程实现。
## 3.2 任务二
修改实例仿生机器人，使得机器人可以像鸟一样叫，并像狗一样的跑，请绘制类图并编程实现。
## 3.3 任务三
如果系统中某对象有三个维度，如某日志记录器既可以支持不同的操作系统，还可以支持多种编程语言，并且可以使用不同的输出方式。请使用桥接模式设计该系统。
# 四、实验记录
## 4.1 任务一
### 4.1.1 **构建实现场景，画出UML类图**
![实验七-task1.drawio.png](https://cdn.nlark.com/yuque/0/2024/png/38674938/1713529395828-56a86b7a-722b-4ee5-ade2-bc4d7e938bf8.png#averageHue=%23f6f6f6&clientId=uc3bfb7ff-7c59-4&from=ui&id=ua0cb6b9c&originHeight=364&originWidth=571&originalType=binary&ratio=1.375&rotation=0&showTitle=false&size=78379&status=done&style=none&taskId=ud5901d1f-b959-4ab4-a1ae-2a195657f3d&title=)
为了能够让接口DataOperation调用Quicksort和BinarySearch的对应方法，并且不改变对应的调用方式。我们采用了对象层面的适配器模型对该情况进行结构设计。
对于每一个Adapter而言，它实现了DataOperation的接口，并且其内部有QuickSort和BinarySearch的私有实例对象。因此，再重写DataOperation的两个抽象方法时，我们就可以调用委派给其内部持有这两个实例对象。这样，适配器类就能够提供一个与DataOperation接口兼容的统一接口，使得客户端代码可以透明地使用这些算法，而无需关心它们的具体实现细节。
### 4.1.2 **实现代码**
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<config>
  <Adapter>
    com.exp7.task1.Adapter
  </Adapter>
</config>
```
```java
public class XMLUitl {
    public static Object getBean(){
        File f = new File("Experiment/src/main/java/com/exp7/task1/myconfig.xml");
        try{
            DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
            DocumentBuilder db = dbf.newDocumentBuilder();
            Document doc = db.parse(f);

            String className = doc.getElementsByTagName("Adapter").item(0).getTextContent().trim();
            // 找到第一个 Adapter 标签，并获取它的文本内容，是找到该节点，而不是对应的内容
            // System.out.println("jhhh " + className);
            Class<?> cls = Class.forName(className);
            return cls.getDeclaredConstructor().newInstance();
        }catch (Exception e){
            e.printStackTrace();
            return null;
        }
    }
}
```
```java
public class BinarySearch {
    public int binarySearch(int[] arr, int target){
        return Arrays.binarySearch(arr, target);
    }
}
```
```java
public class QuickSort {
    public void sort(int[] arr){
        Arrays.sort(arr);
    }
}
```
```java
public abstract class DataOperation {
    public abstract void sort(int[] arr);
    public abstract int binarySearch(int[] arr, int target);
}
```
```java
public class Adapter extends DataOperation {
    private BinarySearch cSearch;
    private QuickSort cSort;
    public Adapter(){
        cSearch = new BinarySearch();
        cSort = new QuickSort();
    }

    public void sort(int[] arr){
        cSort.sort(arr);
    }
    public int binarySearch(int[] arr, int target){
        return cSearch.binarySearch(arr, target);
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        DataOperation op = (DataOperation) XMLUitl.getBean();
        int[] arr = new int[]{1, 5, 2, 9};
        op.sort(arr);
        System.out.print("arr : ");
        for (int i : arr) {
            System.out.print(i + ", ");
        }
        System.out.println();
        System.out.println("index of 2 is " + op.binarySearch(arr, 2));
    }
}
```
上述代码，除了xml以及对应的辅助代码，其余的部分均是按照UML图设计而来的。XMLUtil类会解析这个XML文件，并根据配置创建DataOperationAdapter实例，以及它依赖的QuickSortImpl和BinarySearchImpl实例。这样，客户端代码就可以通过XMLUtil来获取适配器实例，并且透明地使用排序和搜索功能，而无需关心适配器的具体实现细节。
并且当客户端为了使用DataOperation的具体实例，会通过辅助代码XMLUtil获得xml文件内的对应的适配器，从而来模拟适配器对模块的一个适配过程。
### 4.1.3 **实验讨论**
通过运行上述代码，我们的到了如下的结果：
![运行结果](https://cdn.nlark.com/yuque/0/2024/png/38674938/1713530185259-997d8739-dbc4-4982-bc36-7f010c5a6c40.png#averageHue=%23f4f3f1&clientId=uc3bfb7ff-7c59-4&from=paste&height=57&id=u0e43de0f&originHeight=78&originWidth=314&originalType=binary&ratio=1.375&rotation=0&showTitle=true&size=6294&status=done&style=none&taskId=u6333625d-44a5-4744-b9e9-21ed509cb73&title=%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C&width=228.36363636363637 "运行结果")
通过上述的设计和实现，我们成功地应用了适配器模式，使得客户端代码能够通过DataOperati on接口透明地使用不同的排序和搜索算法。在实际的运行过程中，当我们调用DataOperation的sort方法对数组进行排序，以及调用search方法在排序后的数组中查找特定元素时，我们能够得到预期的结果。
## 4.2 任务二
### 4.2.1 **构建实现场景，画出UML类图**
![实验七-task2.drawio.png](https://cdn.nlark.com/yuque/0/2024/png/38674938/1713530360069-bf2f3529-56d5-45a6-b9ec-7a870da94f83.png#averageHue=%23f5f5f5&clientId=uc3bfb7ff-7c59-4&from=ui&id=u090ba02a&originHeight=300&originWidth=581&originalType=binary&ratio=1.375&rotation=0&showTitle=false&size=76009&status=done&style=none&taskId=u7d6ba881-3ae1-435f-93c2-407046e0e4f&title=)
该UML图展示了一个适配器的模式，其中包含三个类：Robot、Adapter和Dog以及Bird。在Adapter类中，它包含了两个属性：bird和dog，它们都是对象类型。通过将这两个类设置为属性来满足对象层面的适配器模式。
此外，Adapter类还具体实现了Robot类的move()方法和cry()方法，并重写了这两个方法。在Dog类中，它有一个叫wang的方法和一个叫run的方法。在Bird类中，它有一个叫sweelld的方法和一个叫fly的方法。
### 4.2.2 **实现代码**
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<config>
  <Adapter>
    com.exp7.task2.Adapter
  </Adapter>
</config>
```
```java
public class XMLUitl {
    public static Object getBean(){
        File f = new File("Experiment/src/main/java/com/exp7/task2/myconfig.xml");
        try{
            DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
            DocumentBuilder db = dbf.newDocumentBuilder();
            Document doc = db.parse(f);

            String className = doc.getElementsByTagName("Adapter").item(0).getTextContent().trim();
            // System.out.println("jhhh " + className);
            Class<?> cls = Class.forName(className);
            return cls.getDeclaredConstructor().newInstance();
        }catch (Exception e){
            e.printStackTrace();
            return null;
        }
    }
}

```
```java
public class Bird {
    public void sweeld(){
        System.out.println("bird sweeld");
    }
    public void fly(){
        System.out.println("bird fly");
    }
}
```
```java
public class Dog {
    public void wang(){
        System.out.println("dog wang");
    }
    void run(){
        System.out.println("dog run");
    }
}
```
```java
public interface Robot {
    void move();
    void cry();
}
```
```java
public class Adapter implements Robot{
    private Dog dog;
    private Bird bird;
    public Adapter(){
        dog=new Dog();
        bird=new Bird();
    }
    public void move(){
        System.out.print("robot imitate: ");
        dog.run();
    }
    public void cry(){
        System.out.print("robot imitate: ");
        bird.sweeld();
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        Robot r = (Robot)XMLUitl.getBean();
        r.move();
        r.cry();
    }
}
```
上述代码，除了xml以及对应的辅助代码，其余的部分均是按照UML图设计而来的。并且用xml文件以及XMLUtil作为辅助代码。XMLUtil会根据配置文件内对应的适配器标签的具体的内容，生成并返回对应的适配器类。
### 4.2.3 **实验讨论**
通过运行上述程序，我们的到了如下的结果。
![运行结果](https://cdn.nlark.com/yuque/0/2024/png/38674938/1713530826907-60f0f45b-5e84-4558-ac28-c67bc508ab67.png#averageHue=%23f5f3f2&clientId=uc3bfb7ff-7c59-4&from=paste&height=80&id=u6893276f&originHeight=110&originWidth=499&originalType=binary&ratio=1.375&rotation=0&showTitle=true&size=9022&status=done&style=none&taskId=uf97a234b-bb25-4ff4-ac97-70ee26dc56f&title=%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C&width=362.90909090909093 "运行结果")
在客户端代码中，我们创建了Robot接口的实现类Adapter的实例，并调用它的move()和cry()方法。由于Adapter类重写了这两个方法，调用时会调用对应动物的行为方法，并输出对应的结果。
由于Adapter类内部持有了Dog和Bird的实例，并在重写的move()和cry()方法中调用了它们对应的行为方法，所以输出结果分别是"Dog runs"和"Bird sweeld"。这样的输出结果验证了我们的桥梁适配器模式设计和实现的正确性。
## 4.3 任务三
### 4.3.1 **构建实现场景，画出UML类图**
![实验七-task3.drawio (1).png](https://cdn.nlark.com/yuque/0/2024/png/38674938/1713574762742-e16cb571-e3f1-4ba3-9f0f-022a7dc307c9.png#averageHue=%23f8f8f8&clientId=ue0e50bd5-1cfa-4&from=ui&id=u585ba8c4&originHeight=525&originWidth=1104&originalType=binary&ratio=1.375&rotation=0&showTitle=false&size=96997&status=done&style=none&taskId=u3f9ffbc3-f46d-4139-a8f6-0f3aecd5292&title=)
在日志记录器的场景中，我们可以看到有多个维度：

1. **操作系统**：日志记录器可能需要在不同的操作系统上运行，如Windows、Linux、macOS等。
2. **输出**：日志可以被输出到不同的目的地，如控制台、文件、数据库、网络等。
3. **编程语言**：日志记录器可能需要支持多种编程语言，如Java、C、Python等。

由于存在多个独立变化的维度，我们使用桥接模式可以将这些维度分离，使得每个维度可以独立地进行扩展和修改。因此我们选择了Log为抽象类，并且将lang、OS、output设置为接口。在桥接模式中，Log将持有实现接口OS、Output和Language的具体实例对象。这样，抽象类可以通过调用这些实例对象的方法来实现自己的功能。同时，抽象类还可以提供set方法，以便在运行时动态地修改持有的实例对象，从而改变日志记录器的行为。
### 4.3.2 **实现代码**
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<config>
  <OS>
    com.exp7.task3.os.Linux
  </OS>
  <Lang>
    com.exp7.task3.lang.Python
  </Lang>
  <Output>
    com.exp7.task3.output.FileOutput
  </Output>
</config>
```
```java
public class XMLUtil {
    public static Object getBean(String type){
        File f = new File("Experiment/src/main/java/com/exp7/task3/myconfig.xml");
        try{
            DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
            DocumentBuilder db = dbf.newDocumentBuilder();
            Document doc = db.parse(f);

            String className = doc.getElementsByTagName(type).item(0).getTextContent().trim();
            Class<?> cls = Class.forName(className);
            return cls.getDeclaredConstructor().newInstance();
        }catch (Exception e){
            e.printStackTrace();
            return null;
        }
    }
}
```
```java
public interface Lang {
    public void with();
}

public class Java implements Lang{
    public void with(){
        System.out.println("with java");
    }
}

public class C implements Lang{
    public void with(){
        System.out.println("with C");
    }
}

public class Python implements Lang{
    public void with(){
        System.out.println("with python");
    }
}
```
```java
public interface OS {
    public void in();
}

public class Linux implements OS {
    public void in(){
        System.out.println("in Linux");
    }
}

public class Windows implements OS {
    public void in(){
        System.out.println("in Windows");
    }
}
```
```java
public interface Output {
    public void output();
}

public class ConsoleOutput implements Output{
    public void output(){
        System.out.println("console output");
    }
}

public class FileOutput implements Output{
    public void output(){
        System.out.println("file output");
    }
}
```
```java
public abstract class AbsLog {
    private OS os;
    private Output output;
    private Lang lang;
    public abstract void setOS(OS os);
    public abstract void setOutput(Output output);
    public abstract void setLang(Lang lang);
    public abstract void run();
}
```
```java
public class Log extends AbsLog {
    private Lang lang;
    private Output output = null;
    private OS os;
    public Log(){
        super();
        // 默认情况
        lang = new Java();
        output = new ConsoleOutput();
        os = new Linux();
    }

    public void setOS(OS os){this.os = os;}
    public void setOutput(Output output){
        this.output = output;
    }

    public void setLang(Lang lang){
        this.lang = lang;
    }
    public void run(){
        System.out.print("log is running... ");
        os.in();
        lang.with();
        System.out.print("and ");
        output.output();
    }
}
```
```java
public class Clinet {
    public static void main(String[] args) {
        Log log = new Log();
        log.setOS((OS) XMLUtil.getBean("OS"));
        log.setLang((Lang) XMLUtil.getBean("Lang"));
        log.setOutput((Output) XMLUtil.getBean("Output"));
        log.run();
    }
}
```
根据上述的UML图，我们编写了对应的类实现类之间的桥接模式并进行测试。同时，我们配置了xml文件以及对应的XMLUtil工具类进行辅助测试，减少因为需求导致代码的修改。
### 4.3.3 **实验讨论**
运行上述代码，我们得到了如下结果。
![运行结果](https://cdn.nlark.com/yuque/0/2024/png/38674938/1713582993938-112b6f10-50a1-431a-bae7-e10c8317714d.png#averageHue=%23f9f8f7&clientId=uc33baf40-11d9-4&from=paste&height=127&id=u37404333&originHeight=175&originWidth=537&originalType=binary&ratio=1.375&rotation=0&showTitle=true&size=13460&status=done&style=none&taskId=ud6c5cbb5-72ca-47c1-bf49-272892a5101&title=%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C&width=390.54545454545456 "运行结果")
该结果表明我们得到log程序是在linux环境下用python语言进行编写，并且使用文件作为输出。
当我们将xml文件改成如下格式之后，再次运行程序，得到如下结果。
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<config>
  <OS>
    com.exp7.task3.os.Windows
  </OS>
  <Lang>
    com.exp7.task3.lang.C
  </Lang>
  <Output>
    com.exp7.task3.output.ConsoleOutput
  </Output>
</config>
```
![修改xml之后的运行结果](https://cdn.nlark.com/yuque/0/2024/png/38674938/1713583038998-da2f1e2e-5273-41fd-8233-382b1d4a60e2.png#averageHue=%23f9f7f6&clientId=uc33baf40-11d9-4&from=paste&height=117&id=u625ac903&originHeight=161&originWidth=543&originalType=binary&ratio=1.375&rotation=0&showTitle=true&size=11130&status=done&style=none&taskId=ud91e8384-e890-4a2a-8055-b0ce459df45&title=%E4%BF%AE%E6%94%B9xml%E4%B9%8B%E5%90%8E%E7%9A%84%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C&width=394.90909090909093 "修改xml之后的运行结果")
通过结果我们发现，当我们修改了xml文件之后，log程序则是在windows环境下，用c语言进行编写并且使用控制台作为输出的。这与预期的一致。
在软件开发中，使用桥接模式可以有效地将抽象部分与实现部分分离，使得它们可以独立地变化。这种模式特别适合于处理具有多个独立变化维度的系统。在我们的日志记录器场景中，我们使用了桥接模式来处理操作系统的差异、输出的差异以及编程语言的差异。
# 五、实验总结
在本次实验中，我们通过三个任务的实践深入了解了构造型设计模式中的适配器模式和桥接模式。
通过前两个任务，我们为了保证不改变原本的程序调用，但是又为了是他能够适应新的环境，我们从对象的角度为其构建适配器。
适配器模式是一种结构型设计模式，它允许将一个类的接口转换成客户期望的另一个接口。这种模式通过创建一个中间层（适配器），使得原本因为接口不兼容而不能一起工作的类可以一起工作。在前两个任务中，我们使用了对象适配器的方式，通过继承和组合的方式，将适配器类与目标接口和适配者类关联起来。
通过前两个任务的实践，我们深入理解了适配器模式的工作原理和适用场景。适配器模式使得我们可以在不改变原有代码的情况下，将新的类或方法集成到现有的系统中。这种模式在处理遗留系统和新系统之间的接口不兼容问题时非常有用。
而第三个任务则是对桥接模式的一次实践。针对问题三中的日志管理器，由于该管理器受到了操作系统、编程语言以及输出这三个维度的影响。为了应对日志管理器受到操作系统、编程语言以及输出这三个维度的影响，我们采用了桥接模式来设计日志管理器。在桥接模式中，我们将抽象部分和实现部分分离，使得它们可以独立地变化。
具体来说，我们定义了一个日志管理器的抽象接口，用于定义日志记录的基本操作。然后，我们为每个维度创建了具体的实现类，用于处理特定操作系统、编程语言和输出方式的日志记录。最后，我们创建了一个桥接类，用于持有这些具体实现类的实例，并提供了统一的接口来调用它们。
 适配器模式和桥接模式都是构造型设计模式中的重要成员，它们各自在软件设计中发挥着关键的作用。适配器模式用于解决接口不兼容的问题，而桥接模式用于解决多个独立变化维度的问题。这两个模式虽然侧重点不同，但它们可以相互结合，共同解决更复杂的问题。
