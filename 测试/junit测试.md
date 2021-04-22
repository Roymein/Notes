# Junit 测试框架

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





