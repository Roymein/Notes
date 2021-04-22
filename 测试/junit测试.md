# Java  Junit4 测试框架

## 一 Junit4 特性

- 简单的注解，提供书写Junit测试的基本特性。
- 断言方法(Assert Methods)比较测试的方法执行结果值和期望值。
- @Ignore 注解，忽略测试方法或者测试类的执行。
- 期望异常测试。
- 超时测试 , 测试方法的执行时间。
- 测试组件，一起运行一些测试类。
- 参数化测试， 以不同的输入参数值测试方法。
- 多个测试，优先级测试

## 二 基础注解

| 注解         | 说明                                                         | 作用                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| @Before      | junit测试类中的任意一个测试方法执行前都会执行此方法          | 用来为测试方法初始化所需的资源                               |
| @BeforeClass | 只被执行一次<br />  运行junit测试类时第一个被执行的方法      | 如打开数据库连接，@BeforeClass 注解的方法应该是静态的        |
| @After       | junit测试类中的任意一个测试方法执行后 都会执行此方法, 即使被@Test 或 @Before修饰的测试方法抛出异常 | 关闭由@Before注解修饰的测试方法打开的资源                    |
| @AfterClass  | 只被执行一次<br />  运行junit测试类是最后一个被执行的方法    | 被用作执行类似关闭数据库连接的任务。被@AfterClass 注解的方法应该是静态的 |
| @Test        | 包含了真正的测试代码，并且会被Junit应用为要测试的方法        | @Test注解有两个可选的参数<br />**expected** 表示此测试方法执行后应该抛出的异常<br />timeout 检测测试方法的执行时间 |



## 三 参数化测试

> 允许通过变化范围的参数值来测试方法

1. 对**测试类**添加注解 @RunWith(Parameterized.class)。
2. 将需要使用变化范围参数值测试的参数定义为私有变量。
3. 使用上一步骤声明的私有变量作为入参，创建构造函数。
4. .创建一个使用@Parameters注解的公共静态方法，它将需要测试的各种变量值通过集合的形式返回。
5. 使用定义的私有变量定义测试方法。

demo类

```java
public class EvenNumberChecker {

    public boolean isEven(int i) {
        if (i % 2 == 0) {
            return true;
        } else {
            return false;
        }
    }
}
```

测试类

```java
@RunWith(Parameterized.class)
public class EvenNumberCheckerTest {
    private int inputNumber;
    private boolean isEven;

    public EvenNumberCheckerTest(int inputNumber, boolean isEven) {
        this.inputNumber = inputNumber;
        this.isEven = isEven;
    }

    @Parameterized.Parameters
    public static Collection<Object[]> data() {
        Object[][] data = new Object[][] {
                { 2, true },
                { 5, false },
                { 10, true }
        };
        return Arrays.asList(data);
    }

    @Test
    public void testIsEven() {
        System.out.println("inputNumber: " + inputNumber + "; isEven: " + isEven);
        EvenNumberChecker evenNumberChecker = new EvenNumberChecker();
        boolean actualResult = evenNumberChecker.isEven(inputNumber);
        assertEquals(isEven, actualResult);
    }
}
```



## 四 套件测试

> Junit 4允许通过使用测试套件类批量运行测试类。为一套测试类创建一个测试套件，要为测试类添加以下注解：
>
> 1. @RunWith(Suite.class)
> 2. @SuiteClasses(TestClass1.class, TestClass2.class)
>
> 当运行时，所有包含在@SuiteClasses注解内的所有测试类都会被执行



Geometry.class

```java
public class Geometry {

    public int squareArea(int length) {
        return length * length;
    }
}
```

Arithmetic.class

```java
public class Arithmetic {

    public int add(int i, int j) {
        return i + j;
    }

}
```

利用套件测试对以上两个方法进行测试

ArithmeticTest.class

```java
public class ArithmeticTest {

    @Test
    public void testAdd() {
        Arithmetic arithmetic = new Arithmetic();
        int actualResult = arithmetic.add(1 , 2);
        int expectedResult = 3;
        assertEquals(expectedResult, actualResult);

    }
}
```

GeometryTest.class

```java
public class GeometryTest {

    @Test
    public void squareArea() {
        Geometry geometry = new Geometry();
        int actualResult = geometry.squareArea(2);
        int expectedResult = 4;
        assertEquals(expectedResult, actualResult);
    }
}
```



## 五 超时测试

> Junit 4超时测试（Timeout test）可以被用来测试方法的执行时间。 Junit 4 超时测试可以被用在：
>
> - 在测试类的方法上使用 @Timeout 注解
> - 测试类的所有方法应用 Timeout 规则

```java
 @Rule
 public Timeout timeout = new Timeout(1000);
```



## 六 期望异常测试

> 开发人员常常使用单元测试来验证的一段儿代码的操作，很多时候单元测试可以检查抛出预期异常( expected exceptions)的代码。

1、@Test（expected …）

```java
    @Test(expected = NullPointerException.class)
    public void test() {
        
    }
```



2、ExpectedException

```java
@Rule
public ExpectedException thrown= ExpectedException.none();
```



```java

@Test
public void canVote_throws_IllegalArgumentException_for_zero_age() {
    Student student = new Student();
    thrown.expect(NullPointerException.class);
    thrown.expectMessage("age should be +ve");
    student.canVote(0);
}
```

除了可以设置异常的属性信息之外，这种方法还有一个优点，它可以更加精确的找到异常抛出的位置。在上面的例子中，在构造函数中抛出的未预期的(unexpected) IllegalArgumentException 异常将会引起测试失败，我们希望它在canVote()方法中抛出



3、 Try/catch with assert/fail

```java
@Test
public void canVote_throws_IllegalArgumentException_for_zero_age() {
    Student student = new Student();
    try {
        student.canVote(0);
    } catch (IllegalArgumentException ex) {
        assertThat(ex.getMessage(), containsString("age should be +ve"));
    }
    fail("expected IllegalArgumentException for non +ve age");
}
```



## 七、优先级测试

> @FixMethodOrder的顺序也并不一定是方法在代码中定义的顺序，这与JVM的实现有关，我猜在class中方法名是保存在一个map中，不同JVM对map的实现不同，导致并不一定是按代码定义顺序的。
>
> 我们在写JUnit测试用例时，有时候需要按照定义顺序执行我们的单元测试方法，比如如在测试数据库相关的用例时候要按照测试插入、查询、删除的顺序测试。如果不按照这个顺序测试可能会出现问题，比如删除方法在前面执行，后面的方法就都不能通过测试，因为数据已经被清空了。而JUnit测试时默认的顺序是随机的。所以这时就需要有办法要求JUnit在执行测试方法时按照我们指定的顺序来执行。

@FixMethodOrder 注解参数

| 参数                                                         |
| ------------------------------------------------------------ |
| MethodSorters.JVM（按照JVM得到的方法顺序，也就是代码中定义的方法顺序） |
| MethodSorters.DEFAULT(默认的顺序)                            |
| MethodSorters.NAME_ASCENDING（按方法名字母顺序执行）         |





# Android Junit 单元测试

## 单元测试目的及测试内容

为什么要进行单元测试？

- 提高稳定性，能够明确地了解是否正确的完成开发；
- 快速反馈bug，跑一遍单元测试用例，定位bug；
- 在开发周期中尽早通过单元测试检查bug，最小化技术债，越往后可能修复bug的代价会越大，严重的情况下会影响项目进度；
- 为代码重构提供安全保障，在优化代码时不用担心回归问题，在重构后跑一遍测试用例，没通过说明重构可能是有问题的，更加易于维护。

单元测试要测什么？

- 列出想要测试覆盖的正常、异常情况，进行测试验证;
- 性能测试，例如某个算法的耗时等等。



## 单元测试分类

1. 本地测试(Local tests): 只在本地机器JVM上运行，以最小化执行时间，这种单元测试不依赖于Android框架，或者即使有依赖，也很方便使用模拟框架来模拟依赖，以达到隔离Android依赖的目的，模拟框架如google推荐的[Mockito](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fmockito%2Fmockito)；

   添加依赖：

   ```java
   dependencies {
       // Required -- JUnit 4 framework
       testImplementation 'junit:junit:4.12'
       // Optional -- Mockito framework（可选，用于模拟一些依赖对象，以达到隔离依赖的效果）
       testImplementation 'org.mockito:mockito-core:2.19.0'
   }
   ```

   单元测试代码存储位置

   ```java
   app/src
        ├── androidTestjava (仪器化单元测试、UI测试)
        ├── main/java (业务代码)
        └── test/java  (本地单元测试)
   ```

   

2. 仪器化测试(Instrumented tests): 在真机或模拟器上运行的单元测试，由于需要跑到设备上，比较慢，这些测试可以访问仪器（Android系统）信息，比如被测应用程序的上下文，一般地，依赖不太方便通过模拟框架模拟时采用这种方式。

   











