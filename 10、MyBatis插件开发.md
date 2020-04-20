###  1、Mybatis插件开发

#### 1.1、Mybatis插件说明

> 这些类中方法的细节可以通过查看每个方法的签名来发现，或者直接查看 MyBatis 发行包中的源代码。 如果你想做的不仅仅是监控方法的调用，那么你最好相当了解要重写的方法的行为。 因为如果在试图修改或重写已有方法的行为的时候，你很可能在破坏 MyBatis 的核心模块。 这些都是更低层的类和方法，所以使用插件的时候要特别当心。

MyBatis 允许用户在已映射语句执行过程中的某一点进行拦截调用。默认情况下，MyBatis 允许使用插件来拦截的方法调用包括：

- Executor (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed)
- ParameterHandler (getParameterObject, setParameters)
- ResultSetHandler (handleResultSets, handleOutputParameters)
- StatementHandler (prepare, parameterize, batch, update, query)

#### 1.2、自定义插件

> Mybatis中使用的拦截器都需要实现Interceptor接口。Interceptor接口是Mybatis实现Plugin的核心。

##### 1.2.1、Interceptor接口

```java
public interface Interceptor {
    //执行拦截逻辑的方法
    Object intercept(Invocation var1) throws Throwable;
    //决定是否触发interceptor()方法
    Object plugin(Object var1);
    //根据配置初始化Interceptor对象
    void setProperties(Properties var1);
}
```

##### 1.2.2、@Intercepts和@Signature注解

- `@Intercepts`:指定一个@Signature注解列表

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
public @interface Intercepts {
    //@Signature 列表
    Signature[] value();
}
```

- `@Signature`:标识了该插件需要拦截的方法信息

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({})
public @interface Signature {
    //指定需要拦截的类型
    Class<?> type();
	//指定需要拦截的方法
    String method();
	//指定被拦截的方法的参数列表
    Class<?>[] args();
}
```

通过这三个属性可以确定一个唯一的方法

- `ExamplePlugin`

```java
@Intercepts({@Signature(
  type= Executor.class,
  method = "update",
  args = {MappedStatement.class,Object.class})})
public class ExamplePlugin implements Interceptor {
  private String someProperty;
  public Object intercept(Invocation invocation) throws Throwable {
    return invocation.proceed();
  }
  public Object plugin(Object target) {
    return Plugin.wrap(target, this);
  }
  public void setProperties(Properties properties) {
  }
}
```

插件将会拦截在 `Executor `实例中所有的 `update` 方法调用

- `mybatis-config.xml`

```xml
<plugins>
  <plugin interceptor="org.mybatis.example.ExamplePlugin">
    <property name="someProperty" value="100"/>
  </plugin>
</plugins>
```

