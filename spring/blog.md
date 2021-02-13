## Spring bean生命周期

#### 阶段1：Bean元信息配置阶段

API的方式、Xml文件方式、properties文件的方式、注解的方式

![](C:\Users\张玉虎\Desktop\spring笔记\pic\BeanDefinition.jpg)

AttributeAccessor接口：属性访问接口

BeanMetadataElement接口：getSource返回BeanDefinition定义的来源

RootBeanDefinition类：表示根bean定义信息，通常bean中没有父bean的就使用这种表示

ChildBeanDefinition类：表示子bean定义信息，如果需要指定父bean的，可以使用ChildBeanDefinition来定义子bean的配置信息，里面有个parentName 属性，用来指定父bean的名称。

GenericBeanDefinition类：通用的bean定义信息，既可以表示没有父bean的bean配置信息，也可以表示有父bean的子bean配置信息，这个类里面也有parentName属性，用来指定父bean的名称。

ConfigurationClassBeanDefinition类：表示通过配置类中@Bean方法定义bean信息，可以通过配置类中使用@Bean来标注一些方法，通过这些方法来定义bean，这些方法配置的bean信息最后会转换为ConfigurationClassBeanDefinition类型的对象

AnnotatedBeanDefinition接口：表示通过注解的方式定义的bean信息里面有个方法AnnotationMetadata getMetadata();用来获取定义这个bean的类上的所有注解信息。

BeanDefinitionBuilder：构建BeanDefinition的工具类，spring中为了方便操作BeanDefinition，提供了一个类： BeanDefinitionBuilder ，内部提供了很多静态方法，通过这些方法可以非常方便的组装BeanDefinition对象。

#### 阶段2：Bean元信息解析阶段

将各种方式定义的bean配置信息解析为BeanDefinition对象。

3种解析方式：xml文件定义bean的解析、properties文件定义bean的解析、注解方式定义bean的解析

XmlBeanDefinitionReader：将xml中定义的bean解析为BeanDefinition对象。

```java
//定义一个spring容器，这个容器默认实现了BeanDefinitionRegistry，所以本身就是一个bean注册器
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
//定义一个xml的BeanDefinition读取器，需要传递一个BeanDefinitionRegistry（bean注册器）对象
XmlBeanDefinitionReader xmlBeanDefinitionReader = new XmlBeanDefinitionReader(factory);
//指定bean xml配置文件的位置
String location = "classpath:/com/javacode2018/lesson002/demo2/beans.xml";
//通过XmlBeanDefinitionReader加载bean xml文件，然后将解析产生的BeanDefinition注册到容器容器中
int countBean = xmlBeanDefinitionReader.loadBeanDefinitions(location);
System.out.println(String.format("共注册了 %s 个bean", countBean));
//打印出注册的bean的配置信息
for (String beanName : factory.getBeanDefinitionNames()) {
    //通过名称从容器中获取对应的BeanDefinition信息
    BeanDefinition beanDefinition = factory.getBeanDefinition(beanName);
    //获取BeanDefinition具体使用的是哪个类
    String beanDefinitionClassName = beanDefinition.getClass().getName();
    //通过名称获取bean对象
    Object bean = factory.getBean(beanName);
    //打印输出
    System.out.println(beanName + ":");
    System.out.println(" beanDefinitionClassName：" + beanDefinitionClassName);
    System.out.println(" beanDefinition：" + beanDefinition);
    System.out.println(" bean：" + bean);
}
```

properties文件定义bean的解析：PropertiesBeanDefinitionReader，将properties文件中定义的bean解析为BeanDefinition对
象

```java
//定义一个spring容器，这个容器默认实现了BeanDefinitionRegistry，所以本身就是一个bea注册器
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
//定义一个properties的BeanDefinition读取器，需要传递一个BeanDefinitionRegistry（bean注册器）对象
PropertiesBeanDefinitionReader propertiesBeanDefinitionReader = new PropertiesBeanDefinitionReader(factory);
//指定bean xml配置文件的位置
String location = "classpath:/com/javacode2018/lesson002/demo2/beans.properties";
//通过PropertiesBeanDefinitionReader加载bean properties文件，然后将解析产生的BeanDefinition注册到容器容器中
int countBean = propertiesBeanDefinitionReader.loadBeanDefinitions(location);
System.out.println(String.format("共注册了 %s 个bean", countBean));
//打印出注册的bean的配置信息
for (String beanName : factory.getBeanDefinitionNames()) {
    //通过名称从容器中获取对应的BeanDefinition信息
    BeanDefinition beanDefinition = factory.getBeanDefinition(beanName);
    //获取BeanDefinition具体使用的是哪个类
    String beanDefinitionClassName = beanDefinition.getClass().getName();
    //通过名称获取bean对象
    Object bean = factory.getBean(beanName);
    //打印输出
    System.out.println(beanName + ":");
    System.out.println(" beanDefinitionClassName：" + beanDefinitionClassName);
    System.out.println(" beanDefinition：" + beanDefinition);
    System.out.println(" bean：" + bean);
}
```

```
beans.properties:
car.(class)=com.javacode2018.lesson002.demo1.Car
car.name=奥迪
car1.(class)=com.javacode2018.lesson002.demo1.Car
car1.name=保时捷
car2.(parent)=car1
user.(class)=com.javacode2018.lesson002.demo1.User
user.name=路人甲Java
user.car(ref)=car
```

PropertiesBeanDefinitionReader，注解的方式定义的bean，需要使用PropertiesBeanDefinitionReader这个类来进行解析

```java
//定义一个spring容器，这个容器默认实现了BeanDefinitionRegistry，所以本身就是一个bean
注册器
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
//定义一个注解方式的BeanDefinition读取器，需要传递一个BeanDefinitionRegistry（bean
注册器）对象
AnnotatedBeanDefinitionReader annotatedBeanDefinitionReader = new
AnnotatedBeanDefinitionReader(factory);
//通过PropertiesBeanDefinitionReader加载bean properties文件，然后将解析产生的
BeanDefinition注册到容器容器中
annotatedBeanDefinitionReader.register(Service1.class, Service2.class);
factory.getBeansOfType(BeanPostProcessor.class).values().forEach(factory::addBe
anPostProcessor); // 
//打印出注册的bean的配置信息
for (String beanName : new String[]{"service1", "service2"}) {
    //通过名称从容器中获取对应的BeanDefinition信息
    BeanDefinition beanDefinition = factory.getBeanDefinition(beanName);
    //获取BeanDefinition具体使用的是哪个类
    String beanDefinitionClassName = beanDefinition.getClass().getName();
    //通过名称获取bean对象
    Object bean = factory.getBean(beanName);
    //打印输出
    System.out.println(beanName + ":");
    System.out.println(" beanDefinitionClassName：" +
    beanDefinitionClassName);
    System.out.println(" beanDefinition：" + beanDefinition);
    System.out.println(" bean：" + bean);
}
```

#### 阶段3：将Bean注册到容器中

BeanDefinitionRegistry：唯一实现DefaultListableBeanFactory

AliasRegistry：

```java
//创建一个bean工厂，这个默认实现了BeanDefinitionRegistry接口，所以也是一个bean注册器
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
//定义一个bean
GenericBeanDefinition nameBdf = new GenericBeanDefinition();
nameBdf.setBeanClass(String.class);
nameBdf.getConstructorArgumentValues().addIndexedArgumentValue(0, "路人甲Java");
//将bean注册到容器中
factory.registerBeanDefinition("name", nameBdf);

//通过名称获取BeanDefinition
System.out.println(factory.getBeanDefinition("name"));
//通过名称判断是否注册过BeanDefinition
System.out.println(factory.containsBeanDefinition("name"));
//获取所有注册的名称
System.out.println(Arrays.asList(factory.getBeanDefinitionNames()));
//获取已注册的BeanDefinition的数量
System.out.println(factory.getBeanDefinitionCount());
//判断指定的name是否使用过
System.out.println(factory.isBeanNameInUse("name"));
                                                               
//别名相关方法
//为name注册2个别名
factory.registerAlias("name", "alias-name-1");
factory.registerAlias("name", "alias-name-2");
//判断alias-name-1是否已被作为别名使用
System.out.println(factory.isAlias("alias-name-1"));
//通过名称获取对应的所有别名
System.out.println(Arrays.asList(factory.getAliases("name")));
//最后我们再来获取一下这个bean
System.out.println(factory.getBean("name"));
```

#### 阶段4：BeanDefinition合并阶段

bean定义可能存在多级父子关系，合并的时候进进行递归合并，最终得到一个包含完整信息的RootBeanDefinition。

```java
org.springframework.beans.factory.support.AbstractBeanFactory#getMergedBeanDefinition
```

```java
//创建bean容器
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
//创建一个bean xml解析器
XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(factory);
//解析bean xml，将解析过程中产生的BeanDefinition注册到DefaultListableBeanFactory中
beanDefinitionReader.loadBeanDefinitions("com/javacode2018/lesson002/demo4/beans.xml");
//遍历容器中注册的所有bean信息
for (String beanName : factory.getBeanDefinitionNames()) {
    //通过bean名称获取原始的注册的BeanDefinition信息
    BeanDefinition beanDefinition = factory.getBeanDefinition(beanName);
    //获取合并之后的BeanDefinition信息
    BeanDefinition mergedBeanDefinition = factory.getMergedBeanDefinition(beanName);
    System.out.println(beanName);
    System.out.println("解析xml过程中注册的beanDefinition：" + beanDefinition);
    System.out.println("beanDefinition中的属性信息" + beanDefinition.getPropertyValues());
    System.out.println("合并之后得到的mergedBeanDefinition：" + mergedBeanDefinition);
    System.out.println("mergedBeanDefinition中的属性信息" + mergedBeanDefinition.getPropertyValues());
    System.out.println("---------------------------");
}
```

#### 阶段5：Bean Class加载阶段

将bean的class名称转换为Class类型的对象

BeanDefinition中有个Object类型的字段：private volatile Object beanClass；通常这个字段的值有2种类型，一种是bean对应的Class类型的对象，另一种是bean对应的Class的完整类名，第一种情况不需要解析，第二种情况：即这个字段是bean的类名的
时候，就需要通过类加载器将其转换为一个Class对象。
此时会对阶段4中合并产生的RootBeanDefinition 中的beanClass 进行解析，将bean的类名转换为Class对象，然后赋值给beanClass 字段。

```java
org.springframework.beans.factory.support.AbstractBeanFactory#resolveBeanClass
```

#### 阶段6：Bean实例化阶段（2个小阶段）

Bean实例化前阶段

DefaultListableBeanFactory中的

```java
private final List<BeanPostProcessor> beanPostProcessors = new CopyOnWriteArrayList<>();
```

Bean实例化之前会调用一段代码：

```java
@Nullable
protected Object applyBeanPostProcessorsBeforeInstantiation(Class<?> beanClass, String beanName) {
    for (BeanPostProcessor bp : getBeanPostProcessors()) {
        if (bp instanceof InstantiationAwareBeanPostProcessor) {
            InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
            Object result = ibp.postProcessBeforeInstantiation(beanClass, beanName);
            if (result != null) {
           		return result;
            }
        }
    }
    return null;
}
```

如果类型是InstantiationAwareBeanPostProcessor ， 尝试调用InstantiationAwareBeanPostProcessor#postProcessBeforeInstantiation 获取bean的实例对象，如果能够获取到，那么将返回值作为当前bean的实例，那么spring自带的实例化bean的过程就被跳过了。

```java
import org.junit.Test;
import org.springframework.beans.BeansException;
import
org.springframework.beans.factory.config.InstantiationAwareBeanPostProcessor;
import org.springframework.beans.factory.support.AbstractBeanDefinition;
import org.springframework.beans.factory.support.BeanDefinitionBuilder;
import org.springframework.beans.factory.support.DefaultListableBeanFactory;
import org.springframework.lang.Nullable;
/**
* bean初始化前阶段，会调用：{@link
org.springframework.beans.factory.config.InstantiationAwareBeanPostProcessor#pos
tProcessBeforeInitialization(Object, String)}
*/
public class InstantiationAwareBeanPostProcessorTest {
@Test
public void test1() {
    DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
        //添加一个BeanPostProcessor：InstantiationAwareBeanPostProcessor
        factory.addBeanPostProcessor(new InstantiationAwareBeanPostProcessor() {
            //@1
            @Nullable
            @Override
            public Object postProcessBeforeInstantiation(Class<?> beanClass, String beanName) throws BeansException {
                System.out.println("调用postProcessBeforeInstantiation()");
                //发现类型是Car类型的时候，硬编码创建一个Car对象返回
                if (beanClass == Car.class) {
                    Car car = new Car();
                    car.setName("保时捷");
                    return car;
                }
                return null;
                }
            });
        //定义一个car bean,车名为：奥迪
        AbstractBeanDefinition carBeanDefinition = BeanDefinitionBuilder.
            genericBeanDefinition(Car.class).
            addPropertyValue("name", "奥迪"). //@2
            getBeanDefinition();
        factory.registerBeanDefinition("car", carBeanDefinition);
        //从容器中获取car这个bean的实例，输出
        System.out.println(factory.getBean("car"));
    }
}
```

Bean实例化阶段

org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor可以将@Autowired 标注的方法作为候选构造器返回。

自定义构造器案例：

```java
@Target(ElementType.CONSTRUCTOR)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyAutowried {
}

public class MySmartInstantiationAwareBeanPostProcessor implements
SmartInstantiationAwareBeanPostProcessor {
    @Nullable
    @Override
    public Constructor<?>[] determineCandidateConstructors(Class<?> beanClass, String beanName) throws BeansException {
        System.out.println(beanClass);
        System.out.println("调用MySmartInstantiationAwareBeanPostProcessor.determineCandidateConstructors 方法");
        Constructor<?>[] declaredConstructors = beanClass.getDeclaredConstructors();
        if (declaredConstructors != null) {
            //获取有@MyAutowried注解的构造器列表
            List<Constructor<?>> collect = Arrays.stream(declaredConstructors)
                .filter(constructor -> constructor.isAnnotationPresent(MyAutowried.class))
                .collect(Collectors.toList());
            Constructor[] constructors = collect.toArray(new Constructor[collect.size()]);
            return constructors.length != 0 ? constructors : null;
        } else {
            return null;
        }
    }
}

```

#### 阶段7：合并后的BeanDefinition处理

```java
protected void applyMergedBeanDefinitionPostProcessors(RootBeanDefinition mbd,
Class<?> beanType, String beanName) {
for (BeanPostProcessor bp : getBeanPostProcessors()) {
if (bp instanceof MergedBeanDefinitionPostProcessor) {
MergedBeanDefinitionPostProcessor bdp =
(MergedBeanDefinitionPostProcessor) bp;
bdp.postProcessMergedBeanDefinition(mbd, beanType, beanName);
}
}
}
```

void postProcessMergedBeanDefinition(RootBeanDefinition beanDefinition, Class<?> beanType, String beanName);

spring会轮询BeanPostProcessor ，依次调用MergedBeanDefinitionPostProcessor#postProcessMergedBeanDefinition。第一个参数为beanDefinition，表示合并之后的RootBeanDefinition，我们可以在这个方法内部对合并之后的BeanDefinition 进行再次处理postProcessMergedBeanDefinition有2个实现类，

```java
org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor
    在 postProcessMergedBeanDefinition 方法中对 @Autowired、@Value 标注的方法、字段进行缓存。

org.springframework.context.annotation.CommonAnnotationBeanPostProcessor
    在 postProcessMergedBeanDefinition 方法中对 @Resource 标注的字段、@Resource 标注的方法、 @PostConstruct 标注的字段、 @PreDestroy标注的方法进行缓存。
```

#### 阶段8：属性赋值阶段（3个小阶段）

Bean实例化后阶段

会调用InstantiationAwareBeanPostProcessor 接口的postProcessAfterInstantiation 这个方法：

```java
for (BeanPostProcessor bp : getBeanPostProcessors()) {
    if (bp instanceof InstantiationAwareBeanPostProcessor) {
        InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
        if (!ibp.postProcessAfterInstantiation(bw.getWrappedInstance(), beanName)) {
        	return;
        }
    }
}
```

postProcessAfterInstantiation 方法返回false的时候，后续的Bean属性赋值前处理、Bean属性赋值都会被跳过。



Bean属性赋值前阶段

调用InstantiationAwareBeanPostProcessor 接口的postProcessProperties 方法

```java
for (BeanPostProcessor bp : getBeanPostProcessors()) {
    if (bp instanceof InstantiationAwareBeanPostProcessor) {
        InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
        PropertyValues pvsToUse = ibp.postProcessProperties(pvs, bw.getWrappedInstance(), beanName);
        if (pvsToUse == null) {
            if (filteredPds == null) {
            	filteredPds = filterPropertyDescriptorsForDependencyCheck(bw, mbd.allowCaching);
            }
            pvsToUse = ibp.postProcessPropertyValues(pvs, filteredPds, bw.getWrappedInstance(), beanName);
            if (pvsToUse == null) {
            	return;
            }
        }
        pvs = pvsToUse;
    }
}
```

如果InstantiationAwareBeanPostProcessor 中的postProcessProperties 和postProcessPropertyValues 都返回空的时候，表示这个bean不需要设置属性，直接返回了，直接进入下一个阶段。

案例：

```java
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
factory.addBeanPostProcessor(new InstantiationAwareBeanPostProcessor() { //

    @Nullable
    @Override
    public PropertyValues postProcessProperties(PropertyValues pvs, Object bean, String beanName) throws BeansException {
        if ("user1".equals(beanName)) {
            if (pvs == null) {
            	pvs = new MutablePropertyValues();
            }
            if (pvs instanceof MutablePropertyValues) {
                MutablePropertyValues mpvs = (MutablePropertyValues) pvs;
                //将姓名设置为：路人
                mpvs.add("name", "路人");
                //将年龄属性的值修改为18
                mpvs.add("age", 18);
            }
        }
        return null;
	}
 });
//注意 user1 这个没有给属性设置值
factory.registerBeanDefinition("user1", 
                               BeanDefinitionBuilder
                               .genericBeanDefinition(UserModel.class)
                               .getBeanDefinition()); 
factory.registerBeanDefinition("user2", 
                               BeanDefinitionBuilder
                               .genericBeanDefinition(UserModel.class)
                               .addPropertyValue("name", "刘德华")
                               .addPropertyValue("age", 50)
                               .getBeanDefinition());
for (String beanName : factory.getBeanDefinitionNames()) {
    System.out.println(String.format("%s->%s", beanName,
    factory.getBean(beanName)));
}
```



Bean属性赋值阶段

循环处理PropertyValues 中的属性值信息，通过反射调用set方法将属性的值设置到bean实例中。
PropertyValues中的值是通过bean xml中property元素配置的，或者调用MutablePropertyValues中add方法设置的值。

#### 阶段9：Bean初始化阶段（5个小阶段）

Bean Aware接口回调阶段

```java
private void invokeAwareMethods(final String beanName, final Object bean) {
    if (bean instanceof Aware) {
        if (bean instanceof BeanNameAware) {
            ((BeanNameAware) bean).setBeanName(beanName);
        }
        if (bean instanceof BeanClassLoaderAware) {
            ClassLoader bcl = getBeanClassLoader();
            if (bcl != null) {
            	((BeanClassLoaderAware) bean).setBeanClassLoader(bcl);
            }
        }
        if (bean instanceof BeanFactoryAware) {
            ((BeanFactoryAware)bean).setBeanFactory(AbstractAutowireCapableBeanFactory.this);
        }
    }
}
```

Bean初始化前阶段

```java
@Override
public Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName) throws BeansException {
    Object result = existingBean;
    for (BeanPostProcessor processor : getBeanPostProcessors()) {
        Object current = processor.postProcessBeforeInitialization(result, beanName);
        if (current == null) {
        	return result;
        }
        result = current;
    }
    return result;
}
```



会调用BeanPostProcessor的postProcessBeforeInitialization 方法，若返回null，当前方法将结束。
通常称postProcessBeforeInitialization这个方法为：bean初始化前操作。这个接口有2个实现类，比较重要：

```java
org.springframework.context.support.ApplicationContextAwareProcessor
org.springframework.context.annotation.CommonAnnotationBeanPostProcessor
```

ApplicationContextAwareProcessor注入6个Aware接口对象
如果bean实现了下面的接口，在ApplicationContextAwareProcessor#postProcessBeforeInitialization 中会依次调用下面接口
中的方法，将Aware 前缀对应的对象注入到bean实例中。

```java
EnvironmentAware：注入Environment对象
EmbeddedValueResolverAware：注入EmbeddedValueResolver对象
ResourceLoaderAware：注入ResourceLoader对象
ApplicationEventPublisherAware：注入ApplicationEventPublisher对象
MessageSourceAware：注入MessageSource对象
ApplicationContextAware：注入ApplicationContext对象
```

从名称上可以看出这个类以ApplicationContext 开头的，说明这个类只能在ApplicationContext环境中使用。
CommonAnnotationBeanPostProcessor调用@PostConstruct标注的方法

Bean初始化阶段

1) 调用InitializingBean接口的afterPropertiesSet方法

2) 调用定义bean的时候指定的初始化方法。



Bean初始化后阶段

```java
@Override
public Object applyBeanPostProcessorsAfterInitialization(Object existingBean, String beanName) throws BeansException {
    Object result = existingBean;
    for (BeanPostProcessor processor : getBeanPostProcessors()) {
        Object current = processor.postProcessAfterInitialization(result, beanName);
        if (current == null) {
        	return result;
        }
        result = current;
    }
    return result;
}
```

调用BeanPostProcessor接口的postProcessAfterInitialization方法，返回null的时候，会中断上面的操作。

案例

```java
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
//加入bean初始化后置处理器方法实现
factory.addBeanPostProcessor(new BeanPostProcessor() {
        @Nullable
        @Override
        public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException 		{
            System.out.println("postProcessAfterInitialization：" + beanName);
            return bean;
        }
	});
//下面注册2个String类型的bean
factory.registerBeanDefinition("name", BeanDefinitionBuilder
                               .genericBeanDefinition(String.class)
                               .addConstructorArgValue("公众号：【路人甲Java】")
                               .getBeanDefinition());
factory.registerBeanDefinition("personInformation",BeanDefinitionBuilder
                               .genericBeanDefinition(String.class)
                               .addConstructorArgValue("带领大家成为java高手！")
                               .getBeanDefinition());
System.out.println("-------输出bean信息---------");
for (String beanName : factory.getBeanDefinitionNames()) {
    System.out.println(String.format("%s->%s", beanName, factory.getBean(beanName)));
}
```

#### 阶段10：所有单例bean初始化完成后阶段

```java
/**
* 确保所有非lazy的单例都被实例化，同时考虑到FactoryBeans。如果需要，通常在工厂设置结束时调
用。
*/
org.springframework.beans.factory.support.DefaultListableBeanFactory#preInstantiateSingletons
```

这个方法内部会先触发所有非延迟加载的单例bean初始化，然后从容器中找到类型是SmartInitializingSingleton 的bean，调用他们的afterSingletonsInstantiated 方法。

方式1：实现SmartInitializingSingleton接口

方式2：通过api的方式让DefaultListableBeanFactory去回调SmartInitializingSingleton

```java
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
factory.registerBeanDefinition("service1",BeanDefinitionBuilder.genericBeanDefinition(Service1.class).getBeanDefinition());
factory.registerBeanDefinition("service2",BeanDefinitionBuilder.genericBeanDefinition(Service2.class).getBeanDefinition());
factory.registerBeanDefinition("mySmartInitializingSingleton",BeanDefinitionBuilder.genericBeanDefinition(MySmartInitializingSingleton.class).getBeanDefinition());
System.out.println("准备触发所有单例bean初始化");
//触发所有bean初始化，并且回调SmartInitializingSingleton#afterSingletonsInstantiated 方法
factory.preInstantiateSingletons();
```

最后调用factory.preInstantiateSingletons 触发所有非lazy单例bean初始化，所有bean装配完毕之后，会回调SmartInitializingSingleton接口。

#### 阶段11：Bean的使用阶段

#### 阶段12：Bean销毁阶段

触发销毁方式

1）调用org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#destroyBean。

2）调用org.springframework.beans.factory.config.ConfigurableBeanFactory#destroySingletons。

3）调用ApplicationContext中的close方法。

Bean销毁阶段会依次执行

1）轮询beanPostProcessors列表，如果是DestructionAwareBeanPostProcessor这种类型的，会调用其内部的postProcessBeforeDestruction方法。

2）如果bean实现了org.springframework.beans.factory.DisposableBean接口，会调用这个接口中的destroy方法

3）调用bean自定义的销毁方法

DestructionAwareBeanPostProcessor接口的实现类CommonAnnotationBeanPostProcessor#postProcessBeforeDestruction方法中会调用bean中所有标注了@PreDestroy的方法。

可以看出销毁方法调用的顺序：

1. @PreDestroy标注的所有方法
2. DisposableBean接口中的destroy()
3. 自定义的销毁方法

![](C:\Users\张玉虎\Desktop\spring笔记\pic\AbstractApplicationContext类.jpg)

BeanFactory接口：Bean工厂的顶层接口

DefaultListableBeanFactory类
实现了BeanFactory接口，可以说这个可以是BeanFactory接口真正的唯一实现，内部真正实现了bean生命周期中的所有代码。
其他的一些类都是依赖于DefaultListableBeanFactory类，将请求转发给DefaultListableBeanFactor进行bean的处理的。

其他3个类
我们经常用到的就是这3个类：AnnotationConfigApplicationContext/ ClassPathXmlApplicationContext/ FileSystemXmlApplicationContext这3个类，他们的主要内部的功能是依赖他的父类AbstractApplicationContext来实现的，所以大家主要看AbstractApplicationContext 这个类。

AbstractApplicationContext类
这个类中有2个比较重要的方法

```java
public abstract ConfigurableListableBeanFactory getBeanFactory() throws IllegalStateException;
protected void registerBeanPostProcessors(ConfigurableListableBeanFactory beanFactory)
```

第一个方法：getBeanFactory()
返回当前应用上下文中的ConfigurableListableBeanFactory ，这也是个接口类型的，这个接口有一
个唯一的实现类： DefaultListableBeanFactory 。
有没有很熟悉，上面说过：DefaultListableBeanFactory是BeanFactory真正的唯一实现。
应用上线文中就会使用这个ConfigurableListableBeanFactory 来操作spring容器。

第二个方法：registerBeanPostProcessors
说的通俗点：这个方法就是向ConfigurableListableBeanFactory中注册BeanPostProcessor，内容会从spring容器中获取所有类型的BeanPostProcessor，将其添加到DefaultListableBeanFactory#beanPostProcessors列表中

```java
protected void registerBeanPostProcessors(ConfigurableListableBeanFactory beanFactory) {
	PostProcessorRegistrationDelegate.registerBeanPostProcessors(beanFactory, this);
}
```

会将请求转发给PostProcessorRegistrationDelegate#registerBeanPostProcessors 。
内部比较长，大家可以去看一下源码，这个方法内部主要用到了4个BeanPostProcessor 类型的List集合。

```java
List<BeanPostProcessor> priorityOrderedPostProcessors = new ArrayList<>();
List<BeanPostProcessor> orderedPostProcessors
List<BeanPostProcessor> nonOrderedPostProcessors;
List<BeanPostProcessor> internalPostProcessors = new ArrayList<>();
```

先说一下：当到方法的时候，spring容器中已经完成了所有Bean的注册。
spring会从容器中找出所有类型的BeanPostProcessor列表，然后按照下面的规则将其分别放到上面的4个集合中，上面4个集合中的BeanPostProcessor 会被依次添加到DefaultListableBeanFactory#beanPostProcessors列表中，来看一下4个集合的分别放的是那些BeanPostProcessor：

priorityOrderedPostProcessors（指定优先级的BeanPostProcessor）实现org.springframework.core.PriorityOrdered接口的BeanPostProcessor，但是不包含MergedBeanDefinitionPostProcessor类型的

orderedPostProcessors（指定了顺序的BeanPostProcessor）实现了org.springframework.core.annotation.Order接口的BeanPostProcessor，但是不包含MergedBeanDefinitionPostProcessor类型的

nonOrderedPostProcessors（未指定顺序的BeanPostProcessor）上面2中类型置为以及MergedBeanDefinitionPostProcessor之外的

internalPostProcessors
MergedBeanDefinitionPostProcessor类型的BeanPostProcessor列表。
大家可以去看一下CommonAnnotationBeanPostProcessor 和AutowiredAnnotationBeanPostProcessor ，这两个类都实现了PriorityOrdered 接口，但是他们也实现了MergedBeanDefinitionPostProcessor 接口，所以最终他们会被丢到internalPostProcessors 这个集合中，会被放入BeanPostProcessor的最后面。



Bean生命周期流程图

![](.//pic//Spring生命周期.png)

