---
title: framework
date: 2026-01-13 20:09:38
tags: 
  - java
  - framework
categories:
  - java
  - framework
---
# spring-framework
## bean
org.springframework.beans.factory.config.BeanDefinition

+ org.springframework.beans.factory.support.AbstractBeanDefinition, 提供具体实现
    - org.springframework.beans.factory.support.GenericBeanDefinition , private @Nullable String parentName; 设置父类bean ，使用广泛 (Generic 通用的，一般的)
    - org.springframework.beans.factory.support.RootBeanDefinition， 和  ChildBeanDefinition组合使用
    - org.springframework.beans.factory.support.ChildBeanDefinition
+ org.springframework.beans.factory.annotation.AnnotatedBeanDefinition， 支持通过asm获取bean class上注解信息
    - org.springframework.context.annotation.ConfigurationClassBeanDefinitionReader.ConfigurationClassBeanDefinition，提供@bean 注解的bean 的解析， 生成ConfigurationClassBeanDefinition 对象

org.springframework.context.annotation.ScannedGenericBeanDefinition ->  extends GenericBeanDefinition implements AnnotatedBeanDefinition,  通过扫描asm来获取带有Component, Service,Controller 等注解的bean

org.springframework.beans.factory.annotation.AnnotatedGenericBeanDefinition -> extends GenericBeanDefinition implements AnnotatedBeanDefinition



BeanDefinition

+ bean class name
+ autoWire method
+ lazy method

AbstractBeanDefinition

+ String scope
+ Boolean lazyInit
+ boolean primary

RootBeanDefinition

GenericBeanDefinition



BeanDefinitionRegistry

+ map<beanname, BeanDefinition>

BeanDefinitionHolder

+ BeanDefinition
+ beanName
+ alias names



BeanDefinitionReader

+ BeanDefinitionRegistry getRegistry();

AbstractBeanDefinitionReader

XmlBeanDefinitionReader

ClassPathBeanDefinitionScanner, resources 目录下文件在打包后会在类路径的根目录下，和com 同一层级



Introspector

BeanWrapper

BeanWrapperImpl， 操作javaBean



ResolvableType， 运行时获取类型信息

+ forClass(@Nullable Class<?> clazz)
+ forConstructorParameter(Constructor<?> constructor, int parameterIndex)
+ forField(Field field)

....



TypeDescriptor, 包含 resolvableType 以及基础的一些类型的类型信息。

```java
private static final Map<Class<?>, TypeDescriptor> commonTypesCache = new HashMap<>(32);

private static final Class<?>[] CACHED_COMMON_TYPES = {
    boolean.class, Boolean.class, byte.class, Byte.class, char.class, Character.class,
    double.class, Double.class, float.class, Float.class, int.class, Integer.class,
    long.class, Long.class, short.class, Short.class, String.class, Object.class};

static {
    for (Class<?> preCachedClass : CACHED_COMMON_TYPES) {
        commonTypesCache.put(preCachedClass, valueOf(preCachedClass));
    }
}


private final Class<?> type;

private final ResolvableType resolvableType;

// 类上的注解信息
private final AnnotatedElementSupplier annotatedElementSupplier;
```



ObjectProvider

getIfAvailable()



ConversionService

```java
public static void addCollectionConverters(ConverterRegistry converterRegistry) {
ConversionService conversionService = (ConversionService) converterRegistry;

converterRegistry.addConverter(new ArrayToCollectionConverter(conversionService));
converterRegistry.addConverter(new CollectionToArrayConverter(conversionService));

converterRegistry.addConverter(new ArrayToArrayConverter(conversionService));
converterRegistry.addConverter(new CollectionToCollectionConverter(conversionService));
converterRegistry.addConverter(new MapToMapConverter(conversionService));

converterRegistry.addConverter(new ArrayToStringConverter(conversionService));
converterRegistry.addConverter(new StringToArrayConverter(conversionService));

converterRegistry.addConverter(new ArrayToObjectConverter(conversionService));
converterRegistry.addConverter(new ObjectToArrayConverter(conversionService));

converterRegistry.addConverter(new CollectionToStringConverter(conversionService));
converterRegistry.addConverter(new StringToCollectionConverter(conversionService));

converterRegistry.addConverter(new CollectionToObjectConverter(conversionService));
converterRegistry.addConverter(new ObjectToCollectionConverter(conversionService));

converterRegistry.addConverter(new StreamConverter(conversionService));
}
```

GenericConversionService

+ getClassHierarchy 找到有该class以及父类



## Resource


org.springframework.core.io.Resource

org.springframework.core.io.ClassPathResource



ResourceLoader

DefaultResourceLoader

PathMatchingResourcePatternResolver, 扩展  DefaultResourceLoader 支持 classpath*:  ,  war: ,

classpath:

当前jar包下寻找

classpath*:

所有jar包下寻找



profile

properties

@PropertySource



PropertyResolver

Environment

AbstractEnvironment

StandardEnvironment

+ systemEnvironment， 系统环境
+ systemProperties, JVM 信息



PropertyResolver

ConfigurablePropertyResolver

AbstractPropertyResolver

PropertySourcesPropertyResolver

PropertySources propertySources;



PropertySources extends Iterable<PropertySource<?>>

MutablePropertySources

private final List<PropertySource<?>> propertySourceList = new CopyOnWriteArrayList<>();



PropertySource

MapPropertySource



## event
发布订阅， 观察者设计模式的扩展



GenericApplicationListenerAdapter 适配器模式

```java
// 缓存
private static final Map<Class<?>, ResolvableType> eventTypeCache = new ConcurrentReferenceHashMap<>();

// 原对象
private final ApplicationListener<ApplicationEvent> delegate;
// event 类型
private final @Nullable ResolvableType declaredEventType;

	public GenericApplicationListenerAdapter(ApplicationListener<?> delegate) {
		Assert.notNull(delegate, "Delegate listener must not be null");
		this.delegate = (ApplicationListener<ApplicationEvent>) delegate;
		this.declaredEventType = resolveDeclaredEventType(this.delegate);
	}
```



AbstractApplicationEventMulticaster

+ DefaultListenerRetriever, 存储listener

SimpleApplicationEventMulticaster

+ org.springframework.context.event.AbstractApplicationEventMulticaster#retrieveApplicationListeners ， 核心找listener方法



## 国际化


MessageSource

HierarchicalMessageSource , getParentMessageSource, 父子关系相关Hierarchical

AbstractMessageSource

ResourceBundleMessageSource

ApplicationContext



## Spel


ExpressionParser

SpelExpressionParser



## 容器和上下文
BeanFactory

+ <T> T getBean(Class<T> requiredType) throws BeansException;

ListableBeanFactory, 可枚举能力

HierarchicalBeanFactory , 父子BeanFactory

ConfigurableBeanFactory ,可配置的

** AbstractBeanFactory

AutowireCapableBeanFactory, 自动装配能力

```java
public interface AutowireCapableBeanFactory extends BeanFactory {

int AUTOWIRE_BY_NAME = 1;

int AUTOWIRE_BY_TYPE = 2;

int AUTOWIRE_CONSTRUCTOR = 3;

String ORIGINAL_INSTANCE_SUFFIX = ".ORIGINAL";

    //创建bean
	<T> T createBean(Class<T> beanClass) throws BeansException;
    
    // 填充bean
    void applyBeanPropertyValues(Object existingBean, String beanName) throws BeansException;

    void autowireBean(Object existingBean) throws BeansException;
    
    ...
}
```

ConfigurableListableBeanFactory

       extends ListableBeanFactory, AutowireCapableBeanFactory, ConfigurableBeanFactory

DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory

       implements ConfigurableListableBeanFactory, BeanDefinitionRegistry



public abstract class AbstractAutowireCapableBeanFactory extends AbstractBeanFactory  implements AutowireCapableBeanFactory



ApplicationContext ->   EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory, MessageSource (i18n),   ApplicationEventPublisher, ResourcePatternResolver(资源匹配)

ConfigurableApplicationContext -> ApplicationContext, Lifecycle, Closeable, setParent()

AbstractApplicationContext ,  refresh(), public abstract ConfigurableListableBeanFactory getBeanFactory() throws IllegalStateException; 此时还没有实现beanFactory功能

-> 其子类实现了beanFactory功能 ， private DefaultListableBeanFactory beanFactory;

AbstractRefreshableApplicationContext, 在创建时传入资源，调用refresh(),  进行 beanFactory 注册beanDefinition

ClassPathXmlApplicationContext, 旧版本

FileSystemXmlApplicationContext, 旧版本

GenericApplicationContext, 新增可以主动调用load()进行扫描资源，注册beanDefinition

GenericXmlApplicationContext, @since 3.0

AnnotationConfigApplicationContext, @since 3.0



WebApplicationContext

AnnotationConfigWebApplicationContext



InstantiationStrategy

Object instantiate(RootBeanDefinition bd, @Nullable String beanName, BeanFactory owner)

+ SimpleInstantiationStrategy, 使用构造器初始化
+ CglibSubclassingInstantiationStrategy, cglib 子类



DefaultSingletonBeanRegistry



## 扩展点
BeanFactoryPostProcessor

BeanDefinitionRegistryPostProcessor



BeanPostProcessor

MergedBeanDefinitionPostProcessor



AbstractBeanFactory refresh() **



org.springframework.context.support.AbstractApplicationContext#onRefresh

org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext#onRefresh

createWebServer()



registerShutdownHook



FactoryBean

SqlSessionFactoryBean



循环引用 A->B B->A ， 一般是设计问题， 可以C-> A, B

单例bean初始化

getSingleton() -> beforeSingletionBeanCreation

createBean -> doCreateBean

实例化

放入三级缓存

populateBeean

initializeBean -> 返回代理对象

Object earlySingletonReference = getSingleton(beanName, false);

addSingleton() - >  放入1级缓存，2，3级缓存进行移除。

```java
protected void addSingleton(String beanName, Object singletonObject) {
    Object oldObject = this.singletonObjects.putIfAbsent(beanName, singletonObject);
    if (oldObject != null) {
        throw new IllegalStateException("Could not register object [" + singletonObject +
                                        "] under bean name '" + beanName + "': there is already object [" + oldObject + "] bound");
    }
    this.singletonFactories.remove(beanName);
    this.earlySingletonObjects.remove(beanName);
    this.registeredSingletons.add(beanName);

    Consumer<Object> callback = this.singletonCallbacks.get(beanName);
    if (callback != null) {
        callback.accept(singletonObject);
    }
}
```



## 循环依赖：
单例bean初始化

getSingleton() -> beforeSingletionBeanCreation

createBean -> doCreateBean

实例化

放入三级缓存

populateBeean -》

需要B

实例化

放入三级缓存

populateBeean -》 需要a , 生成的代理对象，将a从3级缓存中取出放入二级缓存

initializeBean -> 返回代理对象

addSingleton() - >  放入1级缓存，2，3级缓存进行移除。

initializeBean -> 进行后置处理生成代理对象时检查缓存，此时二级缓存已有A, 则直接返回a的代理对象, 将A从二级缓存移除。

Object earlySingletonReference = getSingleton(beanName, false);

addSingleton() - >  放入1级缓存，2，3级缓存进行移除。



## AOP


3级缓存的作用是当出现循环引用时，将 a 的原始bean 放入3级循环依赖，如果需要代理则生成代理bean 放入二级话缓存中， 后面 a 在创建完成返回时从二级缓存中获取代理bean 1 , 保证 a 单例性。

BeanPostProcessor

InstantiationAwareBeanPostProcessor

SmartInstantiationAwareBeanPostProcessor

AbstractAutoProxyCreator

AnnotationAwareAspectJAutoProxyCreator

1. 用于循环引用时生成代理bean (直接调用wrapIfNecessary)

2. 初始化后生成代理bean (调用 postProcessAfterInitialization -> wrapIfNecessary ->  AbstractAutoProxyCreator. 使用 ProxyFactory 创建代理)

AutowiredAnnotationBeanPostProcessor -> 用于进行属性填充



ProxyCreatorSupport

private AopProxyFactory aopProxyFactory; -> DefaultAopProxyFactory ( 选择使用 ObjenesisCglibAopProxy还是  JdkDynamicAopProxy)

ProxyFactory



aspectJ

编译器织入

加载时织入  load time weaving, 类文件转化器转化成新的 class 字节码文件 (二进制文件) , arthas , skywalking

运行时织入

EnableAspectJAutoProxy, exposeProxy @Import(AspectJAutoProxyRegistrar.class) 注入  AnnotationAwareAspectJAutoProxyCreator



DefaultAopProxyFactory

JdkDynamicAopProxy

private final AdvisedSupport advised;

TargetSource targetSource

Object getTarget() throws Exception;

ObjenesisCglibAopProxy



## 事务

mysql 不支持嵌套事务, spring 使用多个session 解决， 前面session 挂起， 后面session 执行完再继续执行前面session

TransactionManager ， 标记接口

PlatformTransactionManager

```java
TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException;
void commit(TransactionStatus status) throws TransactionException;
void rollback(TransactionStatus status) throws TransactionException;
```

AbstractPlatformTransactionManager

getTransaction**

DataSourceTransactionManager



TransactionDefinition , 隔离级别， 传播行为定义

DefaultTransactionDefinition



TransactionStatus

DefaultTransactionStatus

private final @Nullable Object suspendedResources;



TransactionSynchronization

```java
default void suspend() {
}

default void resume() {
}

default void beforeCommit(boolean readOnly) {
}

default void beforeCompletion() {
}

default void afterCommit() {
}

default void afterCompletion(int status) {
}
```

TransactionSynchronizationManager



EnableTransactionManagement ,  @Import(TransactionManagementConfigurationSelector.class)

TransactionInterceptor



事务失效场景： 1. 代理方法private , final , 2. this调用本身方法，即不是调用代理对象的代理方法， 3. 异常被捕获



## exception
BeanDefinitionValidationException 创建bean definition 异常



# Spring Web
## WebApplicationType
WebApplicationType deduceFromClasspath()

### WebServerFactory
ServletWebServerFactory

ReactiveWebServerFactory



## HttpMessageConverter
AbstractHttpMessageConverter

### StringHttpMessageConverter




# Spring MVC


ServletWebServerApplicationContext onRefresh**

createWebServer  
getBeanFactory().registerSingleton("webServerGracefulShutdown",new WebServerGracefulShutdownLifecycle(this.webServer))



ServletWebServerFactory , servlet 容器

AbstractServletWebServerFactory

TomcatServletWebServerFactory



@EnableConfigurationProperties({ServerProperties.class}) ， 将该类作为bean实例注入

@Import({BeanPostProcessorsRegistrar.class, ServletWebServerFactoryConfiguration.EmbeddedTomcat.class, ServletWebServerFactoryConfiguration.EmbeddedJetty.class, ServletWebServerFactoryConfiguration.EmbeddedUndertow.class})

ServletWebServerFactoryAutoConfiguration



BeanPostProcessorsRegistrar -> implements ImportBeanDefinitionRegistrar, BeanFactoryAware

WebServerFactoryCustomizerBeanPostProcessor

-> postProcessBeforeInitialization -> WebServerFactoryCustomizer



WebServerFactoryCustomizer

TomcatWebSocketServletWebServerCustomizer -> WsSci

ServletWebServerFactoryCustomizer ， serlvet是一种规范 ， 使用 PropertyMapper 进行配置

TomcatWebServerFactoryCustomizer， 文件目录， 线程池配置等

TomcatServletWebServerFactoryCustomizer，没啥用

LocaleCharsetMappingsCustomizer，设置编码格式



tomcat 接受请求，包装请求交给 DispatcherServlet -> HandlerMapping(RequestMappingHandlerMapping） -> HandlerAdapter （HttpRequestHandlerAdapter）

-> HandlerMethodReturnValueHandler

HttpServlet

HttpServletBean

FrameworkServlet

ApplicationListener<ContextRefreshedEvent>

DispatcherServlet -> DispatcherServletAutoConfiguration

HandlerExecutionChain mappedHandler = getHandler(processedRequest);

mappedHandler.applyPreHandle(processedRequest, response)   // interceptorList

HandlerAdapter -> mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

mappedHandler.applyPostHandle(processedRequest, response, mv); // interceptorList

processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException); // 处理结果，exceptionHandler处理





@ExceptionHandler



HandlerExceptionResolver

AbstractHandlerExceptionResolver

AbstractHandlerMethodExceptionResolver

ExceptionHandlerExceptionResolver

ExceptionHandlerMethodResolver

```java
private final Map<ControllerAdviceBean, ExceptionHandlerMethodResolver> exceptionHandlerAdviceCache =
new LinkedHashMap<>();

private void initExceptionHandlerAdviceCache() {
		if (getApplicationContext() == null) {
			return;
		}

        // @ControllerAdvice -> @Component
		List<ControllerAdviceBean> adviceBeans = ControllerAdviceBean.findAnnotatedBeans(getApplicationContext());
		for (ControllerAdviceBean adviceBean : adviceBeans) {
			Class<?> beanType = adviceBean.getBeanType();
			if (beanType == null) {
				throw new IllegalStateException("Unresolvable type for ControllerAdviceBean: " + adviceBean);
			}
			ExceptionHandlerMethodResolver resolver = new ExceptionHandlerMethodResolver(beanType);
			if (resolver.hasExceptionMappings()) {
				this.exceptionHandlerAdviceCache.put(adviceBean, resolver);
			}
			if (ResponseBodyAdvice.class.isAssignableFrom(beanType)) {
				this.responseBodyAdvice.add(adviceBean);
			}
		}

		if (logger.isDebugEnabled()) {
			int handlerSize = this.exceptionHandlerAdviceCache.size();
			int adviceSize = this.responseBodyAdvice.size();
			if (handlerSize == 0 && adviceSize == 0) {
				logger.debug("ControllerAdvice beans: none");
			}
			else {
				logger.debug("ControllerAdvice beans: " +
						handlerSize + " @ExceptionHandler, " + adviceSize + " ResponseBodyAdvice");
			}
		}
	}
```

```java
private static final MethodFilter EXCEPTION_HANDLER_METHODS = method ->
AnnotatedElementUtils.hasAnnotation(method, ExceptionHandler.class);

private final Map<ExceptionMapping, ExceptionHandlerMappingInfo> mappedMethods = new HashMap<>(16);

private final ConcurrentLruCache<ExceptionMapping,ExceptionHandlerMappingInfo> lookupCache = new ConcurrentLruCache<>(24,cacheKey -> getMappedMethod(cacheKey.exceptionType(), cacheKey.mediaType()))
```



ExceptionHandlerMappingInfo





org.springframework.web.servlet.DispatcherServlet#handlerExceptionResolvers

DefaultErrorAttributes

request.setAttribute(ERROR_INTERNAL_ATTRIBUTE, （Exception) ex);

HandlerExceptionResolverComposite

ExceptionHandlerExceptionResolver

ResponseStatusExceptionResolver

ResponseStatusException || @ResponseStatus

DefaultHandlerExceptionResolver



HandlerMapping

AbstractHandlerMapping

AbstractHandlerMethodMapping implements InitializingBean

private final Map<T, MappingRegistration<T>> registry = new HashMap<>();

or MappingRegistry mappingRegistry

RequestMappingInfoHandlerMapping

RequestMappingHandlerMapping



endpoint:

AbstractWebMvcEndpointHandlerMapping

EndpointMapping endpointMapping

Collection<ExposableWebEndpoint> endpoints

WebMvcEndpointHandlerMapping

@ManagementContextConfiguration

WebMvcEndpointManagementContextConfiguration



```plain
配置时
management:
  server:
    port: 18088

ManagementContextAutoConfiguration
    -> 
    (配置 时注入 DifferentManagementContextConfiguration, 
    即ManagementPortType.DIFFERENT时才会走自动装配逻辑，并且装配到子applicatonContext)
@Configuration
@ConditionalOnManagementPort(ManagementPortType.DIFFERENT)
DifferentManagementContextConfiguration
    ->
    ConfigurableWebServerApplicationContext managementContext = this.managementContextFactory
						.createManagementContext(this.applicationContext,
								EnableChildManagementContextConfiguration.class,
								PropertyPlaceholderAutoConfiguration.class);

// 在子applicationContext加载
@Configuration
@EnableManagementContext(ManagementContextType.CHILD)
class EnableChildManagementContextConfiguration

@EnableManagementContext -> @Import(ManagementContextConfigurationImportSelector.class) 
-> spi 方式 加载带@ManagementContextConfiguration的bean -> WebMvcEndpointManagementContextConfiguration

@@Conditional(OnManagementPortCondition.class)
public @interface ConditionalOnManagementPort{}

org.springframework.context.annotation.Condition
org.springframework.boot.autoconfigure.condition.SpringBootCondition
org.springframework.boot.actuate.autoconfigure.web.server.OnManagementPortCondition


http://localhost:18088/actuator
tomcat.ThreadPoolExecutor@8804
DispatcherServlet@8668
this.handlerMappings = {Collections$SingletonList@8670}  size = 1
 0 = {CompositeHandlerMapping@8718} -> WebMvcEndpointHandlerMapping

http://localhost:18081/midWare-learn/hello
tomcat.ThreadPoolExecutor@9020
DispatcherServlet@8992
this.handlerMappings = {ArrayList@9243}  size = 5
 0 = {SimpleUrlHandlerMapping@9522} 
 1 = {RequestMappingHandlerMapping@9523} 
 2 = {BeanNameUrlHandlerMapping@9524} 
 3 = {SimpleUrlHandlerMapping@9525} 
 4 = {WelcomePageHandlerMapping@9526} 

不配置时
ManagementContextAutoConfiguration
  afterSingletonsInstantiated() 
    -> 
@Configuration
@ConditionalOnManagementPort(ManagementPortType.SAME)
static class SameManagementContextConfiguration {
  // 在主applicationContext加载
  @Configuration
  @EnableManagementContext(ManagementContextType.SAME)
  static class EnableSameManagementContextConfiguration
}

@EnableManagementContext -> @Import(ManagementContextConfigurationImportSelector.class) 
-> spi 方式 加载带@ManagementContextConfiguration的bean -> WebMvcEndpointManagementContextConfiguration

http://localhost:18081/midWare-learn/actuator
tomcat.ThreadPoolExecutor@11072
DispatcherServlet@8631
 handlerMappings = {ArrayList@8635}  size = 7
 0 = {SimpleUrlHandlerMapping@11060} 
 1 = {WebMvcEndpointHandlerMapping@11061} 
 2 = {ControllerEndpointHandlerMapping@11062} 
 3 = {RequestMappingHandlerMapping@11063} 
 4 = {BeanNameUrlHandlerMapping@11064} 
 5 = {SimpleUrlHandlerMapping@11065} 
 6 = {WelcomePageHandlerMapping@11066} 
 
```

handlerMappings

org.springframework.web.servlet.handler.AbstractHandlerMethodMapping#lookupHandlerMethod

MultiValueMap<String, T> pathLookup = new LinkedMultiValueMap<>();

org.springframework.web.servlet.mvc.method.RequestMappingInfo#getMatchingCondition



PathPattern -> matches



PathElement

CaptureVariablePathElement -> {}



HandlerAdapter

AbstractHandlerMethodAdapter

RequestMappingHandlerAdapter -> handle() -> method.invoke(getBean(), args);



HandlerExecutionChain

```java
private final Object handler; // controller 方法 HandlerMethod
private final List<HandlerInterceptor> interceptorList; 执行前后拦截器
```





DispatcherServletAutoConfiguration



ServletContextInitializer

RegistrationBean

DynamicRegistrationBean

ServletRegistrationBean

DispatcherServletRegistrationBean , servletMappings ->  DispatcherServlet name



StandardContext，

org.apache.catalina.core.ApplicationContext context， web上下文， 外部使用 private final ServletContext facade = new ApplicationContextFacade(this); 门面模式

servletMappings

TomcatEmbeddedContext servlet 上下文



UrlPathHelper

AntPathMatcher



AbstractNamedValueMethodArgumentResolver

PathVariableMethodArgumentResolver -> 解析匹配 @PathVariable



ContentNegotiationStrategy

ContentNegotiationManager

HeaderContentNegotiationStrategy



## WebServer
default void shutDownGracefully(GracefulShutdownCallback callback)

### TomcatWebServer
GracefulShutdown gracefulShutdown

#### LRU
org.apache.tomcat.util.res.StringManager#getManager(java.lang.String, java.util.Locale)



### UndertowWebServer
GracefulShutdownHandler gracefulShutdown



Lifecycle

SmartLifecycle  extends Lifecycle, Phased

### WebServerGracefulShutdownLifecycle
stop()

this.webServer.shutDownGracefully()



#### SmartLifecycle
NacosWatch





### DefaultLifecycleProcessor
onClose()

stopBeans()

#### LifecycleGroup
int phase

Map<String, ? extends Lifecycle> lifecycleBeans

List<LifecycleGroupMember> members

int smartMemberCount

### SpringApplicationShutdownHook
addRuntimeShutdownHook

Runtime.getRuntime().addShutdownHook(new Thread(this, "SpringApplicationShutdownHook"))



# Spring WebFlux
## ReactiveWebServerFactory
TomcatReactiveWebServerFactory

NettyReactiveWebServerFactory

UndertowReactiveWebServerFactory

### ReactiveWebServerApplicationContext
onRefresh()

createWebServer()



AnnotationConfigReactiveWebServerApplicationContext



#### WebServerManager
WebServer webServer



TomcatWebServer

NettyWebServer

## reactor
### Mono
public static <T> Mono<T> defer(Supplier<? extends Mono<? extends T>> supplier)

public @Nullable T block()

await()

#### BlockingMonoSubscriber
extends CountDownLatch

T blockingGet()

### Flux
public static <T> Flux<T> fromIterable(Iterable<? extends T> it)



### Subscriber
onSubscribe(Subscription s)

onNext(T t)

onError(Throwable t)

onComplete()

CoreSubscriber

InnerConsumer

BlockingSingleSubscriber

onComplete()

countDown()

BlockingMonoSubscriber

### Schedulers
### Sinks
#### Empty
Mono<T> asMono()

#### One
extends Empty<T>

EmitResult tryEmitValue(@Nullable T value)

##### SinkOneSerialized
extends SinkEmptySerialized<T> implements InternalOneSink<T>, ContextHolder

SinkEmptySerialized

extends SinksSpecs.AbstractSerializedSink implements InternalEmptySink<T>, ContextHolder

SinksSpecs





##### SinkOneMulticast
extends SinkEmptyMulticast<O> implements InternalOneSink<O>

EmitResult tryEmitValue(@Nullable O value)



SinkEmptyMulticast

extends Mono<T> implements InternalEmptySink<T>



#### Many
EmitResult tryEmitNext(T t)

Flux<T> asFlux()



# Spring data redis


RedisOperations

RedisTemplate



AbstractOperations

ListOperations

DefaultListOperations<K, V> extends AbstractOperations<K, V> implements ListOperations<K, V>

# Spring Boot


spi 注入

BootstrapRegistryInitializer

ApplicationContextInitializer

ApplicationListener



DefaultBootstrapContext 启动引导上下文 -> BootstrapRegistry, BootstrapContext



CachingMetadataReaderFactoryPostProcessor -> 注入 beanDefinition： ConcurrentReferenceCachingMetadataReaderFactory  （private final Map<Resource, MetadataReader> cache = new ConcurrentReferenceHashMap<>(); 资源元数据缓存）

ConfigurationWarningsPostProcessor -> 校验beanDefinition

ConfigurationClassPostProcessor -> 解析 @Configuration 配置类

@PropertySource

@ComponentScan

@Import

@Bean

default methods on interfaces

superclass



ConfigurationPropertiesBindingPostProcessor

ApplicationContextAwareProcessor

WebServerFactoryCustomizerBeanPostProcessor



org.slf4j.LoggerFactory

ILoggerFactory ？？



LoggingApplicationListener ， 日志系统初始化

onApplicationStartingEvent， 获取 LoggingSystem

onApplicationEnvironmentPreparedEvent , 根据  Environment 进行配置

onApplicationPreparedEvent, 注册一些单例bean



LoggingSystemFactory ， auto configure, spring spi 方式

DelegatingLoggingSystemFactory 获取日志框架的实现 LoggingSystem ,    LogbackLoggingSystem



@Import(AutoConfigurationImportSelector.class)

@EnableAutoConfiguration

AutoConfigurationImportSelector -> DeferredImportSelector -> ImportSelector

spi 方式 找到所有带 EnableAutoConfiguration  注解的类 ， 即 META-INF/spring.factories 里 org.springframework.boot.autoconfigure.EnableAutoConfiguration =\...的类 （此为 spring boot 2 , 而spring boot 3 改为 META-INForg.springframework.boot.autoconfigure.EnableAutoConfiguration.imports文件 ）

ConfigurationClassParser



stater



spring-boot-starter-data-redis

dependencies {

	api(project(":starter:spring-boot-starter"))



	api(project(":module:spring-boot-data-redis"))

}



spring-boot-starter

dependencies {

	api(project(":starter:spring-boot-starter-logging"))



	api(project(":core:spring-boot-autoconfigure"))



	api("jakarta.annotation:jakarta.annotation-api")

	api("org.yaml:snakeyaml")

}



spring-boot-autoconfigure

imports:

org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration

org.springframework.boot.autoconfigure.aop.AopAutoConfiguration

org.springframework.boot.autoconfigure.availability.ApplicationAvailabilityAutoConfiguration

org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration

org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration

org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration

org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration

org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration

org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration

org.springframework.boot.autoconfigure.ssl.SslAutoConfiguration

org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration

org.springframework.boot.autoconfigure.task.TaskSchedulingAutoConfiguration

dependencies {

	api(project(":core:spring-boot"))



	optional("com.github.ben-manes.caffeine:caffeine")

	optional("org.aspectj:aspectjweaver")

	optional("jakarta.persistence:jakarta.persistence-api")

	optional("jakarta.servlet:jakarta.servlet-api")

	optional("javax.money:money-api")

	optional("org.springframework:spring-web")

	optional("org.springframework.data:spring-data-commons")



	testFixturesCompileOnly(project(":core:spring-boot-test"))

	testFixturesCompileOnly(project(":test-support:spring-boot-test-support"))

	testFixturesCompileOnly("javax.cache:cache-api")

	testFixturesImplementation(testFixtures(project(":core:spring-boot")))



	testImplementation(project(":core:spring-boot-test"))

	testImplementation(project(":test-support:spring-boot-test-support"))

	testImplementation(testFixtures(project(":core:spring-boot")))

	testImplementation("ch.qos.logback:logback-classic")

	testImplementation("io.projectreactor:reactor-core")

	testImplementation("org.springframework:spring-context-support")

	testImplementation("org.springframework.security:spring-security-config")

	testImplementation("tools.jackson.core:jackson-databind")



	testRuntimeOnly("com.github.ben-manes.caffeine:caffeine")

	testRuntimeOnly("org.springframework:spring-webflux")

}



springboot

dependencies {

	api("org.springframework:spring-core")

	api("org.springframework:spring-context")

...

}



spring-boot-data-redis

dependencies {

	api(project(":core:spring-boot"))

	api(project(":module:spring-boot-data-commons"))

	api(project(":module:spring-boot-tx"))

	api("io.lettuce:lettuce-core")

	api("org.springframework.data:spring-data-redis")

...

}

imports

org.springframework.boot.data.redis.autoconfigure.LettuceMetricsAutoConfiguration

org.springframework.boot.data.redis.autoconfigure.RedisAutoConfiguration

org.springframework.boot.data.redis.autoconfigure.RedisReactiveAutoConfiguration

org.springframework.boot.data.redis.autoconfigure.RedisRepositoriesAutoConfiguration

org.springframework.boot.data.redis.autoconfigure.health.RedisHealthContributorAutoConfiguration

org.springframework.boot.data.redis.autoconfigure.health.RedisReactiveHealthContributorAutoConfiguration

```java
@AutoConfiguration
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public final class RedisAutoConfiguration {

...
	@Bean
	@ConditionalOnMissingBean(name = "redisTemplate")
	@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
	RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
		RedisTemplate<Object, Object> template = new RedisTemplate<>();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}
...
}
```



spring-data-redis

dependencies {

spring-data-keyvalue

...

}



spring-boot-data-commons

dependencies {

	api(project(":core:spring-boot"))

	api("org.springframework.data:spring-data-commons"

...

)



# Spring Boot Actuator


## Observation
io.micrometer.observation.Observation



ObservationHandler



## metrics
## loggers
### LoggersEndpointAutoConfiguration
### LoggingSystem
JavaLoggingSystem

Log4J2LoggingSystem

LogbackLoggingSystem



## tracing
### otel
#### concept
##### otel collector
otelcol-contrib.exe --config**=**customconfig.yaml

```java
// https://opentelemetry.io/zh/docs/collector/configuration/#location
receivers:
  otlp:
    protocols:
      http:
        endpoint: 0.0.0.0:14318
exporters: 
   zipkin:
    endpoint: http://localhost:9411/api/v2/spans
service:
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [zipkin]
```





#### OpenTelemetryTracingAutoConfiguration
BatchSpanProcessor

Worker  
ArrayBlockingQueue -> span data



SpanExporters

CompositeSpanExporter

OtlpHttpSpanExporter

OTLP_HTTP_SPAN_EXPORTER -》 OTLP_HTTP_JSON_SPAN_EXPORTER

OkHttpHttpSender

DEFAULT_ENDPOINT = "http://localhost:4318/v1/traces" （[http://localhost:4318/v1/logs](http://localhost:4318/v1/logs) , [http://localhost:4318/v1/metrics](http://localhost:4318/v1/metrics)）



OtlpGrpcSpanExporter

OTLP_GRPC_SPAN_EXPORTER

OkHttpGrpcSender (uses OkHttp instead of grpc-java)



io.opentelemetry.sdk.internal.StandardComponentId.ExporterType

io.opentelemetry.sdk.internal.StandardComponentId.ExporterType#OTLP_HTTP_JSON_SPAN_EXPORTER



#### ObservationAutoConfiguration
ObservationRegistry

ObservationRegistryPostProcessor

MetricsWithTracingConfiguration

TracingObservationHandler

MeterObservationHandler

DefaultMeterObservationHandler

TracingAwareMeterObservationHandler





#### MicrometerTracingAutoConfiguration
DefaultTracingObservationHandler

PropagatingSenderTracingObservationHandler

PropagatingReceiverTracingObservationHandler



ServerHttpObservationFilter

SimpleObservation

ServerRequestObservationContext





#### micrometer-tracing-bridge-otel
io.micrometer.tracing.otel.bridge.OtelScopedSpan

io.opentelemetry.api.trace.Span



#### opentelemetry-exporter-zipkin
io.opentelemetry.exporter.zipkin.ZipkinSpanExporter

DEFAULT_ENDPOINT = "http://localhost:9411/api/v2/spans"

zipkin2.reporter.okhttp3.OkHttpSender



#### exmples
##### spring ai
ChatModelOutputObservationHandler

```java
@Override
public void onStop(ChatModelObservationContext context) {
TracingObservationHandler.TracingContext tracingContext = context
.get(TracingObservationHandler.TracingContext.class);
Span otelSpan = OpenTelemetrySpanBridge.retrieveOtelSpan(tracingContext);

if (otelSpan != null) {
    String outputMessages = getOutputMessages(context);
    if (outputMessages != null) {
        // 设置输出信息 
        otelSpan.setAttribute(outputMessagesKey, outputMessages);
    }
}
}
```



# Spring boot starter data redis


RedisAutoConfiguration

# Spring Cloud


## starter
### context
#### Bootstrap
##### BootstrapApplicationListener
implements ApplicationListener<ApplicationEnvironmentPreparedEvent>





#### PropertySource
##### PropertySourceBootstrapConfiguration
implements ApplicationListener<ContextRefreshedEvent>,

      ApplicationContextInitializer<ConfigurableApplicationContext>, Ordered

@Autowired(required = false)

private List<PropertySourceLocator> propertySourceLocators = new ArrayList<>();



prepareEnvironment -> SpringApplicationRunListeners.environmentPrepared() ->


BootstrapApplicationListener implements ApplicationListener<ApplicationEnvironmentPreparedEvent>

onApplicationEvent -> ConfigurableApplicationContext context = new SpringApplicationBuilder().run() -> prepareEnvironment() -> ConfigFileApplicationListener ->  getSearchNames() -> load bootstrap.yml only



org.springframework.cloud.bootstrap.BootstrapApplicationListener

public static final String CONFIG_NAME_PROPERTY = "spring.config.name";

public static final String BOOTSTRAP_PROPERTY_SOURCE_NAME = "bootstrap";



org.springframework.boot.context.config.ConfigFileApplicationListener

private static final String DEFAULT_NAMES = "application";



Scope

GenericScope

RereshScope



ConfigurationPropertiesBinder



post http://localhost:8080/acutuator/env

body : {"name":"server.port", "value":"9090"}



# Spring Cloud Open Feign


SynchronousMethodHandler

FeignBlockingLoadBalancerClient client

feign.SynchronousMethodHandler#invoke

feign.SynchronousMethodHandler#executeAndDecode



org.springframework.cloud.openfeign.loadbalancer.FeignBlockingLoadBalancerClient#execute

feign.Request -> org.springframework.cloud.client.loadbalancer.DefaultRequest

ServiceInstance instance = loadBalancerClient.choose(serviceId, lbRequest);

```java
@Override
public <T> ServiceInstance choose(String serviceId, Request<T> request) {
    ReactiveLoadBalancer<ServiceInstance> loadBalancer = loadBalancerClientFactory.getInstance(serviceId);
    if (loadBalancer == null) {
        return null;
    }
    Response<ServiceInstance> loadBalancerResponse = Mono.from(loadBalancer.choose(request)).block();
    if (loadBalancerResponse == null) {
        return null;
    }
    return loadBalancerResponse.getServer();
}
```



NamedContextFactory

private final Map<String/* serviceId */, GenericApplicationContext> contexts = new ConcurrentHashMap<>();



# Spring Cloud Netflix Zuul
## EnableZuulProxy
ZuulHandlerMapping extends AbstractUrlHandlerMapping

ZuulController extends ServletWrappingController

LocationRewriteFilter extends ZuulFilter

DiscoveryClientRouteLocator extends SimpleRouteLocator



## EnableZuulServer


# Spring Cloud Sleuth
## core
## instruments
## report
### zipkin


# Spring Cloud Gateway


## webmvc
### core


AbstractHandlerMapping

org.springframework.web.servlet.function.support.RouterFunctionMapping



HandlerAdapter

org.springframework.web.servlet.function.support.HandlerFunctionAdapter



HandlerFilterFunction



org.springframework.cloud.gateway.server.mvc.handler.ProxyExchangeHandlerFunction



ProxyExchangeHandlerFunction



### auto configure
GatewayServerMvcAutoConfiguration

RouterFunctionHolderFactory



## webflux


# springdoc-openapi


springdoc-openapi-starter-common

org.springdoc.core.service.GenericResponseService#buildGenericResponse

@ControllerAdvice



springdoc-openapi-starter-webmvc-api

springdoc-openapi-starter-webmvc-scalar

springdoc-openapi-starter-webmvc-ui



# Spring AI


## Document
org.springframework.ai.document.Document

### DocumentTransformer
org.springframework.ai.transformer.splitter.TextSplitter

org.springframework.ai.transformer.splitter.TokenTextSplitter

## VectorStore
org.springframework.ai.vectorstore.VectorStore

org.springframework.ai.vectorstore.observation.AbstractObservationVectorStore

implements org.springframework.ai.vectorstore.VectorStoreRetriever

List<Document> similaritySearch(SearchRequest request);

protected final EmbeddingModel embeddingModel;

#### org.springframework.ai.vectorstore.SimpleVectorStore
Map<String, SimpleVectorStoreContent> store = new ConcurrentHashMap<>();

#### com.alibaba.cloud.ai.vectorstore.analyticdb.AnalyticDbVectorStore
okhttp request server

## DocumentRetriever
org.springframework.ai.rag.retrieval.search.DocumentRetriever

List<Document> retrieve(Query query);

org.springframework.ai.rag.retrieval.search.VectorStoreDocumentRetriever



## EmbeddingModel
org.springframework.ai.embedding.EmbeddingModel

### DashScopeEmbeddingModel
### OpenAiEmbeddingModel


## PromptTemplateActions & PromptTemplateMessageActions
### PromptTemplate
stringtemplate

system

user

role play

context

samples before promt

step by step

think step by step and explain why.

Explain your reasoning step by step, considering: 1... 2...3...

Tree of Thoughts , multi promts

problem : Design a system to recommend movies to users based on their viewing history

// Step 1: Generate multiple solution approaches

// Step 2: Evaluate approaches and select the most promising

// Step 3: Refine and elaborate on the selected approach

[spring-ai-examples/prompt-engineering/prompt-engineering-patterns/src/main/java/org/springframework/ai/example/prompt_engineering/PromptEngineeringApplication.java at main · spring-projects/spring-ai-examples](https://github.com/spring-projects/spring-ai-examples/blob/main/prompt-engineering/prompt-engineering-patterns/src/main/java/org/springframework/ai/example/prompt_engineering/PromptEngineeringApplication.java#L139)

### examples
#### extractKeywords promt
```plain
question-to-keywords -> 将下述问题的关键语料抽取出来，直接以list形式输出，不要分析。
示例如下：
【问题】
查询2024年8月在北京，一级标签为"未成单"的人数。
【关键语料】
["2024年8月", "北京", "一级标签", "未成单", "人数"]

【问题】
Name movie titles released in year 1945. Sort the listing by the descending order of movie popularity. released in the year 1945 refers to movie_release_year = 1945;
【关键语料】
["movie titles", "released in year 1945", "movie popularity", "movie_release_year = 1945"]

【问题】
List all product name from Australia Bike Retailer order by product ID. Australia Bike Retailer is name of vendor
【关键语料】
["product name", "Australia Bike Retailer", "product ID", "name of vendor"]

【问题】
山东省济南市各车型（牵引车、载货车、自卸车、搅拌车）销量占比的月趋势
【关键语料】
["山东省", "济南市", "各车型", "牵引车", "载货车", "自卸车", "搅拌车", "销量占比", "月趋势"]

【问题】
{question}
【关键语料】
```

#### select table sql promt
```plain
你现在是一位数据分析师，你的任务是分析用户的问题和数据库schema，
数据库schema包括表名、表描述、表之间的外键依赖，每张表中包含多个列的列名、列描述和主键信息，
现在你需要根据提供的数据库信息和用户的问题，分析与用户问题相关的table，给出相关table的名称。
[Instruction]:
1. 排除与用户问题完全不相关的table
2. 保留可能对回答用户问题有帮助的表
3. 结果以json形式输出，用```json和```包围起来
4. 直接输出结果，不要做多余的分析

以下样例供你参考：

【DB_ID】 station_weather
# Table: train
[
(id:TEXT, 火车编号, Primary Key.),
(train_number:TEXT, 火车车次, Examples: [56701]),
(name:TEXT, 火车的名称),
(origin:TEXT, 出发站, Examples: [Kanniyakumari, Chennai, Trivandrum]),
(destination:TEXT, 到达站, Examples: [Kanniyakumari, Chennai, Trivandrum]),
(time:TEXT, 发车时间, Examples: [4:49, 22:10, 21:49]),
(interval:TEXT, 火车的运行频率, Examples: [Daily])
]
# Table: station
[
(id:TEXT, 车站编号, Primary Key),
(network_name:TEXT, 车站所属网络的名称, Examples: [Croxley, Chorleywood, Cheshunt]),
(services:TEXT, 提供的服务),
(local_authority:TEXT, 负责该车站区域的地方当局, Examples: [Three Rivers, Chiltern, Broxbourne])
]
# Table: route
[
(train_id:TEXT, 火车编号),
(station_id:TEXT, 车站编号)
]
# Table: weekly_weather
[
(station_id::TEXT, 车站编号),
(day_of_week:TEXT, 星期, Examples: [Tuesday, Monday, Wednesday]),
(high_temperature:INT, 最高气温, Examples: [59, 55, 58]),
(low_temperature:INT, 最低气温, Examples: [54, 52, 55]),
(precipitation:DOUBLE, 降水量, Examples: [50.0, 90.0, 70.0]),
(wind_speed_mph:INT, 风速, Examples: [22, 14, 13])
]
【Foreign keys】
route.station_id=station.id
route.train_id=train.id
weekly_weather.station_id=station.id

【问题】
How many different services are provided by all stations?
【Answer】
```json
["station"]
```

===============
【DB_ID】 hr_1
# Table: regions
[
(REGION_ID:TEXT, Primary Key),
(REGION_NAME:TEXT)
]
# Table: countries
[
(COUNTRY_ID:TEXT, Primary Key),
(COUNTRY_NAME:TEXT),
(REGION_ID:TEXT)
]
# Table: departments
[
(DEPARTMENT_ID:TEXT, Primary Key),
(DEPARTMENT_NAME:TEXT, department name. Examples: [Treasury, Shipping, Shareholder Services]),
(MANAGER_ID:TEXT),
(LOCATION_ID:TEXT)
]
# Table: jobs
[
(JOB_ID:TEXT, Primary Key),
(JOB_TITLE:TEXT),
(MIN_SALARY:INT, min salary. Examples: [4000, 8200, 4200]),
(MAX_SALARY:INT, max salary. Examples: [9000, 16000, 15000])
]
# Table: employees
[
(EMPLOYEE_ID:TEXT),
(FIRST_NAME:TEXT, Examples: [Peter, John, David]),
(LAST_NAME:TEXT, Examples: [Taylor, Smith, King]),
(EMAIL:TEXT),
(PHONE_NUMBER:TEXT, Examples: [650.509.4876]),
(HIRE_DATE:DATE, Examples: [1987-10-01]),
(JOB_ID:TEXT),
(SALARY:TEXT, Examples: [2500, 10000, 9000]),
(COMMISSION_PCT:DOUBLE, Examples: [0, 0.3, 0.25]),
(MANAGER_ID:TEXT),
(DEPARTMENT_ID:TEXT)
]
# Table: job_history
[
(EMPLOYEE_ID:TEXT),
(START_DATE:DATE, Examples: [1999-01-01]),
(END_DATE:DATE, Examples: [1999-12-31]),
(JOB_ID:TEXT),
(DEPARTMENT_ID:TEXT)
]
# Table: locations
[
(LOCATION_ID:TEXT),
(STREET_ADDRESS:TEXT),
(POSTAL_CODE:TEXT, Examples: [YSW 9T2, M5V 2L7, 99236]),
(CITY:TEXT, Examples: [Whitehorse, Venice, Utrecht]),
(STATE_PROVINCE:TEXT, Examples: [Yukon, Washington, Utrecht]),
(COUNTRY_ID:TEXT)
]
【Foreign keys】
countries.REGION_ID=regions.REGION_ID
employees.JOB_ID=jobs.JOB_ID
employees.DEPARTMENT_ID=departments.DEPARTMENT_ID
job_history.JOB_ID=jobs.JOB_ID
job_history.DEPARTMENT_ID=departments.DEPARTMENT_ID
job_history.EMPLOYEE_ID=employees.EMPLOYEE_ID
locations.COUNTRY_ID=countries.COUNTRY_ID

【问题】
display the full name (first and last name ) of employee with ID and name of the country presently where (s)he is working.
【Answer】
```json
["employees", "departments", "countries", "locations"]
```

===============
{schema_info}

【问题】
{question}

【参考信息】
{evidence}

【Answer】
```



#### extract date promt
```plain
给你一个用户的问题，请你提取出该用户所提问的时间，时间维度包括：年、月、日，结果以list格式输出。形如：["今年", "本月", "今日"]
，如果用户的问题不涉及时间，直接返回给我[]，不需要多余的内容！不要回答多余的内容！
用户问题：{question}
回答：
```

#### generate sql promt
##### system
```plain
现在你是一个{dialect}生成师，需要阅读一个客户的问题，参考的数据库schema，根据参考信息的提示，生成一句可执行的SQL。
注意：
1、不要select多余的列。
2、生成的SQL用```sql 和```包围起来。
3、不要在SQL语句中加入注释！！！

【数据库schema】
{schema_info}

【参考信息】
{evidence}
```

```plain
schema_info
【DB_ID】 a
# Table: orders, 订单表
[
(user_id:NUMBER, 下单用户ID),
(id:NUMBER, 订单ID，主键自增, Primary Key),
(order_date:DATETIME, 下单时间),
(total_amount:NUMBER, 订单总金额),
(status:TEXT, 订单状态（pending/completed/cancelled等）)
]
# Table: users, 用户表
[
(email:TEXT, 用户邮箱),
(username:TEXT, 用户名),
(id:NUMBER, 用户ID，主键自增, Primary Key),
(created_at:DATETIME, 用户注册时间)
]
【Foreign keys】
order_items.order_id=orders.id
orders.user_id=users.id
product_categories.category_id=categories.id
product_categories.product_id=products.id
order_items.product_id=products.id
```

##### user
```plain
【问题】
{question}

【SQL】
```



## StructuredOutput
StructuredOutputConverter

BeanOutputConverter

```plain
@Override
	public String getFormat() {
		String template = """
				Your response should be in JSON format.
				Do not include any explanations, only provide a RFC8259 compliant JSON response following this format without deviation.
				Do not include markdown code blocks in your response.
				Remove the ```json markdown from the output.
				Here is the JSON Schema instance your output must adhere to:
				```%s```
				""";
		return String.format(template, this.jsonSchema);
	}
```

## ChatModel
org.springframework.ai.chat.model.ChatModel

org.springframework.ai.openai.OpenAiChatModel

private final OpenAiChatOptions defaultOptions;

private final RetryTemplate retryTemplate;

private final OpenAiApi openAiApi;

private final ObservationRegistry observationRegistry;

private final ToolCallingManager toolCallingManager;

com.alibaba.cloud.ai.dashscope.chat.DashScopeChatModel

## ChatOptions
org.springframework.ai.chat.prompt.ChatOptions

org.springframework.ai.openai.OpenAiChatOptions

private @JsonProperty("max_tokens") Integer maxTokens;

private @JsonProperty("presence_penalty") Double presencePenalty;

...

com.alibaba.cloud.ai.dashscope.chat.DashScopeChatOptions

private @JsonProperty("temperature") Double temperature;

private @JsonProperty("top_k") Integer topK;

private @JsonProperty("max_tokens") Integer maxTokens;

private @JsonProperty("repetition_penalty") Double repetitionPenalty;

...



## ChatClient
Builder

defaultAdvisors

defaultUser

defaultSystem

defaultToolNames



### DefaultChatClient
chatClient.prompt()..call()



### Advisor
CallAdvisor

BaseAdvisor

BaseChatMemoryAdvisor

VectorStoreChatMemoryAdvisor

MessageChatMemoryAdvisor

PromptChatMemoryAdvisor

QuestionAnswerAdvisor

RetrievalAugmentationAdvisor

ChatModelCallAdvisor

StructuredOutputValidationAdvisor

SimpleLoggerAdvisor

SafeGuardAdvisor

## mcp
### modelcontextprotocol
io.modelcontextprotocol.client.McpSyncClient

private final McpAsyncClient delegate;



### @Bean ToolCallbackProvider


### @Tool


### server
#### McpServerProperties
#### McpServerSseProperties
#### McpServerStreamableHttpProperties


### client


## ChatMemory
invoke get()

org.springframework.ai.chat.client.advisor.MessageChatMemoryAdvisor#before

org.springframework.ai.chat.client.advisor.PromptChatMemoryAdvisor#before

### MessageWindowChatMemory
final int DEFAULT_MAX_MESSAGES = 20

ChatMemoryRepository chatMemoryRepository

int maxMessages

### ChatMemoryRepository
InMemoryChatMemoryRepository

Map<String/* ConversationId */, List<Message>> chatMemoryStore = new ConcurrentHashMap<>()

RedisChatMemoryRepository

MongoChatMemoryRepository

CosmosDBChatMemoryRepository

JdbcChatMemoryRepository

Neo4jChatMemoryRepository

CassandraChatMemoryRepository

AdvancedRedisChatMemoryRepository

### Message
AbstractMessage

AssistantMessage

UserMessage

SystemMessage

ToolResponseMessage



## spring ai alibaba
### spring-ai-alibaba-agent-framework
#### BaseAgent
protected String name;

protected String description;

protected String outputKey;

protected CompileConfig compileConfig;

protected volatile CompiledGraph compiledGraph;

protected volatile StateGraph graph;

FlowAgent

protected String inputKey;

protected List<BaseAgent> subAgents;

LlmRoutingAgent

private ChatModel chatModel;

FlowAgentBuilder

name

model

state

description  
inputKey

outputKey

subAgents

#### A2aRemoteAgent
private final AgentCardWrapper agentCard;

private A2aNodeWithConfig a2aNode;



A2aNodeWithConfig

createStreamingGenerator

#### ReactAgent


#### AsyncGenerator
##### AsyncGeneratorQueue
final BlockingQueue<Data<E>> queue;

##### WithEmbed


### spring-ai-alibaba-starter-a2a-nacos
#### A2aService
getAgentCard(String agentName)

releaseAgentCard(AgentCard agentCard, String registrationType, boolean setAsLatest)

registerAgentEndpoint(String agentName, AgentEndpoint endpoint)

### spring-ai-alibaba-graph-core
#### StateGraph
public static final String END = "__END__";

public static final String START = "__START__";

       public static final String ERROR = "__ERROR__";

       public static final String NODE_BEFORE = "__NODE_BEFORE__";

       public static final String NODE_AFTER = "__NODE_AFTER__";

final Nodes nodes = new Nodes();

final Edges edges = new Edges();

#### OverAllState
private final Map<String, Object> data;



#### CompiledGraph
#### private final ProcessedNodesEdgesAndConfig processedData;
#### 
com.alibaba.cloud.ai.graph.ProcessedNodesEdgesAndConfig



#### NodeAction
com.alibaba.cloud.ai.graph.node.HttpNode

com.alibaba.cloud.ai.graph.node.KnowledgeRetrievalNode

com.alibaba.cloud.ai.graph.node.ListOperatorNode

com.alibaba.cloud.ai.graph.node.LlmNode

com.alibaba.cloud.ai.graph.node.ParameterParsingNode

com.alibaba.cloud.ai.graph.node.QuestionClassifierNode

com.alibaba.cloud.ai.graph.node.ToolNode



#### Node
com.alibaba.cloud.ai.graph.internal.node.CommandNode

com.alibaba.cloud.ai.graph.internal.node.ParallelNode

com.alibaba.cloud.ai.graph.internal.node.SubStateGraphNode

private final StateGraph subGraph;

com.alibaba.cloud.ai.graph.internal.node.SubCompiledGraphNode

private final CompiledGraph subGraph;



#### GraphRunner
private final CompiledGraph compiledGraph;

private final OverAllState initialState;

private final MainGraphExecutor mainGraphExecutor;



### spring-ai-alibaba-autoconfigure-mcp-registry
#### NacosMcpRegister
createMcpServer

updateMcpServer

subscribeNacosMcpServer

registerService

### spring-ai-alibaba-autoconfigure-mcp-distributed
##### DistributedAsyncMcpClient
callTool

listTools

init

subscribe

SseWebFluxDistributedAsyncMcpClient

StreamWebFluxDistributedAsyncMcpClient





# LangChain4j


## ChatModel
OpenAiChatModel

OpenAiClient client

HttpClient httpClient

String baseUrl

Map<String, String> defaultHeaders

Map<String, String> customQueryParams

List<ChatModelListener> listeners

Integer maxRetries

OpenAiChatRequestParameters defaultRequestParameters



### StreamingChatModel
OpenAiStreamingChatModel



### ChatRequest
List<ChatMessage> messages

ChatRequestParameters parameters



#### ChatRequestParameters
DefaultChatRequestParameters

String modelName

Double temperature

List<ToolSpecification> toolSpecifications

ToolChoice toolChoice

...

OpenAiChatRequestParameters



### ChatResponse
AiMessage aiMessage

ChatResponseMetadata metadata

String id  
String modelName

TokenUsage tokenUsage

FinishReason finishReason

### ChatMessage
AiMessage

String text

List<ToolExecutionRequest> toolExecutionRequests

UserMessage

String name

List<Content> contents

SystemMessage

String text

ToolExecutionResultMessage

String id

String toolName

String text



### Content
TextContent

ImageContent

AudioContent

VideoContent

PdfFileContent



### ChatModelListener
MetricsChatModelListener

SpanChatModelListener



### ChatMemory
TokenWindowChatMemory

Object id

Function<Object, Integer> maxTokensProvider

TokenCountEstimator tokenCountEstimator

ChatMemoryStore store

MessageWindowChatMemory



#### TokenCountEstimator
OpenAiTokenCountEstimator

String modelName

Encoding encoding

#### ChatMemoryStore
InMemoryChatMemoryStore

Map<Object, List<ChatMessage>> messagesByMemoryId = new ConcurrentHashMap()

RedisChatMemoryStore

JedisPooled client

SingleSlotChatMemoryStore

List<ChatMessage> messages = new ArrayList<>()

Object memoryId

YugabyteDBChatMemoryStore

YugabyteDBEngine engine

YugabyteDBChatMemorySchema schema

Duration ttl



## AiServices
AiServiceContext context



create(Class<T> aiService, ChatModel chatModel)

create(Class<T> aiService, StreamingChatModel streamingChatModel)

DefaultAiServices

dev.langchain4j.service.DefaultAiServices#build

Proxy.newProxyInstance

InvocationHandler.inoke()



### AiServiceContext
Class<?> aiServiceClass

ChatLanguageModel chatModel

StreamingChatLanguageModel streamingChatModel



Map<Object, ChatMemory> chatMemories

ChatMemoryProvider chatMemoryProvider



ModerationModel moderationModel



List<ToolSpecification> toolSpecifications

Map<String, ToolExecutor> toolExecutors

ToolProvider toolProvider



RetrievalAugmentor retrievalAugmentor



Function<Object, Optional<String>> systemMessageProvider



AtomicReference<GuardrailService> guardrailService



### RetrievalAugmentor
DefaultRetrievalAugmentor

QueryTransformer queryTransformer

QueryRouter queryRouter

ContentAggregator contentAggregator

ContentInjector contentInjector

Executor executor

#### ContentRetriever
SqlDatabaseContentRetriever

DataSource dataSource

String sqlDialect

String databaseStructure

PromptTemplate promptTemplate

ChatModel chatModel

int maxRetries

WebSearchContentRetriever

WebSearchEngine webSearchEngine

int maxResults

EmbeddingStoreContentRetriever

EmbeddingStore<TextSegment> embeddingStore

EmbeddingModel embeddingModel



#### EmbeddingStore
OpenSearchEmbeddingStore

PgVectorEmbeddingStore

<details class="lake-collapse"><summary id="uacbfde63"><span class="ne-text">others</span></summary><p id="u2842d8d5" class="ne-p"><span class="ne-text">QdrantEmbeddingStore</span></p><p id="uae6561a9" class="ne-p"><span class="ne-text">PineconeEmbeddingStore</span></p><p id="u0c3c7f8b" class="ne-p"><span class="ne-text">MongoDbEmbeddingStore</span></p><p id="u094e688c" class="ne-p"><span class="ne-text">InMemoryEmbeddingStore</span></p><p id="u5613ea73" class="ne-p"><span class="ne-text">CoherenceEmbeddingStore</span></p><p id="u24afae70" class="ne-p"><span class="ne-text">ChromaEmbeddingStore</span></p><p id="u32fba9c3" class="ne-p"><span class="ne-text">AzureCosmosDbNoSqlEmbeddingStore</span></p><p id="u8eaf2202" class="ne-p"><span class="ne-text">AzureCosmosDbMongoVCoreEmbeddingStore</span></p><p id="u0f097bce" class="ne-p"><span class="ne-text">ElasticsearchEmbeddingStore</span></p><p id="ua4b22588" class="ne-p"><span class="ne-text">TablestoreEmbeddingStore</span></p><p id="u536a72e0" class="ne-p"><span class="ne-text">InfinispanEmbeddingStore</span></p><p id="ua7052a36" class="ne-p"><span class="ne-text">VespaEmbeddingStore</span></p><p id="uf7d27fe0" class="ne-p"><span class="ne-text">AzureAiSearchEmbeddingStore</span></p><p id="u85d143ae" class="ne-p"><span class="ne-text">AbstractAzureCosmosDBNoSqlEmbeddingStore</span></p><p id="u4f355c1c" class="ne-p"><span class="ne-text">MariaDbEmbeddingStore</span></p><p id="u5faf5571" class="ne-p"><span class="ne-text">WeaviateEmbeddingStore</span></p><p id="u2ec3da49" class="ne-p"><span class="ne-text">CassandraEmbeddingStore</span></p><p id="u45e00bad" class="ne-p"><span class="ne-text">OracleEmbeddingStore</span></p><p id="u6ec9fdd2" class="ne-p"><span class="ne-text">CouchbaseEmbeddingStore</span></p><p id="u1496cc04" class="ne-p"><span class="ne-text">AstraDbEmbeddingStore</span></p><p id="u6882f5ec" class="ne-p"><span class="ne-text">MilvusEmbeddingStore</span></p><p id="u9113c7fc" class="ne-p"><span class="ne-text"></span></p><p id="u892029e0" class="ne-p"><span class="ne-text">这是一个非常好的问题，但答案取决于你的具体需求。</span><strong><span class="ne-text">没有一个“最好”的数据库，只有最适合你特定场景的数据库。</span></strong></p><p id="u621cc699" class="ne-p"><span class="ne-text">这些 </span><code class="ne-code"><span class="ne-text">EmbeddingStore</span></code><span class="ne-text"> 实现都有各自的优势和最佳适用场景。下面我将它们分类并进行对比，以帮助你做出决策。</span></p><hr id="wLQQc" class="ne-hr"><h3 id="o43o4"><span class="ne-text">1. 专用向量数据库 (Vector Databases)</span></h3><p id="u8a0aae9a" class="ne-p"><span class="ne-text">这类数据库是为向量搜索和相似性查询专门设计的，通常性能最高，功能最丰富。</span></p><ul class="ne-ul"><li id="ue5c319d0" data-lake-index-type="0"><strong><span class="ne-text">QdrantEmbeddingStore</span></strong></li></ul><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u6dc14bba" data-lake-index-type="0"><strong><span class="ne-text">特点</span></strong><span class="ne-text">: 开源，性能极高，支持多种量化方法（如PQ, HNSW），有完善的过滤功能。</span></li><li id="u14d664ac" data-lake-index-type="0"><strong><span class="ne-text">优势</span></strong><span class="ne-text">: 部署简单（有 Docker 镜像），社区活跃，功能强大。</span></li><li id="ube6ac18c" data-lake-index-type="0"><strong><span class="ne-text">适用场景</span></strong><span class="ne-text">: 绝大多数生产环境，特别是对性能和成本敏感的应用。</span></li></ul></ul><ul class="ne-ul"><li id="u508bf033" data-lake-index-type="0"><strong><span class="ne-text">PineconeEmbeddingStore</span></strong></li></ul><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u36970972" data-lake-index-type="0"><strong><span class="ne-text">特点</span></strong><span class="ne-text">: 全托管的 SaaS 服务，无需关心基础设施。</span></li><li id="u05adc991" data-lake-index-type="0"><strong><span class="ne-text">优势</span></strong><span class="ne-text">: 开箱即用，扩展性好，维护成本极低。</span></li><li id="u3add10e7" data-lake-index-type="0"><strong><span class="ne-text">适用场景</span></strong><span class="ne-text">: 快速原型开发、不想管理数据库的团队、需要快速扩展的业务。</span></li></ul></ul><ul class="ne-ul"><li id="u4d762c80" data-lake-index-type="0"><strong><span class="ne-text">WeaviateEmbeddingStore</span></strong></li></ul><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u6ef6fe91" data-lake-index-type="0"><strong><span class="ne-text">特点</span></strong><span class="ne-text">: 开源，不仅是向量数据库，还融合了图数据库的特性，支持 GraphQL API。</span></li><li id="u4e9762cc" data-lake-index-type="0"><strong><span class="ne-text">优势</span></strong><span class="ne-text">: 可以轻松构建知识图谱和复杂的语义搜索应用。</span></li><li id="u0923a9dc" data-lake-index-type="0"><strong><span class="ne-text">适用场景</span></strong><span class="ne-text">: 需要将向量搜索与结构化数据关系结合的复杂应用。</span></li></ul></ul><ul class="ne-ul"><li id="ucd8d5482" data-lake-index-type="0"><strong><span class="ne-text">MilvusEmbeddingStore</span></strong></li></ul><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="ud98defc0" data-lake-index-type="0"><strong><span class="ne-text">特点</span></strong><span class="ne-text">: 开源，专为大规模向量数据设计，架构非常灵活，支持分布式部署。</span></li><li id="u77919f15" data-lake-index-type="0"><strong><span class="ne-text">优势</span></strong><span class="ne-text">: 性能优异，扩展性极强，适合海量数据。</span></li><li id="u85ead028" data-lake-index-type="0"><strong><span class="ne-text">适用场景</span></strong><span class="ne-text">: 大型企业级应用，处理亿级甚至十亿级别的向量数据。</span></li></ul></ul><ul class="ne-ul"><li id="u847eff3b" data-lake-index-type="0"><strong><span class="ne-text">ChromaEmbeddingStore</span></strong></li></ul><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="ub4569be8" data-lake-index-type="0"><strong><span class="ne-text">特点</span></strong><span class="ne-text">: 开源，轻量级，非常易于上手。</span></li><li id="u03698110" data-lake-index-type="0"><strong><span class="ne-text">优势</span></strong><span class="ne-text">: API 简单，适合快速集成和本地开发。</span></li><li id="u671f284d" data-lake-index-type="0"><strong><span class="ne-text">适用场景</span></strong><span class="ne-text">: 快速原型开发、个人项目、本地开发环境。</span></li></ul></ul><hr id="thTaB" class="ne-hr"><h3 id="Oncoa"><span class="ne-text">2. 通用数据库的向量扩展</span></h3><p id="u9b365090" class="ne-p"><span class="ne-text">这类数据库本身是成熟的通用数据库，通过插件或内置功能增加了向量存储和搜索能力。</span></p><ul class="ne-ul"><li id="ua1c84098" data-lake-index-type="0"><strong><span class="ne-text">PgVectorEmbeddingStore</span></strong><span class="ne-text"> (PostgreSQL)</span></li></ul><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="ue64efab8" data-lake-index-type="0"><strong><span class="ne-text">特点</span></strong><span class="ne-text">: 在强大的 PostgreSQL 数据库上通过 </span><code class="ne-code"><span class="ne-text">pgvector</span></code><span class="ne-text"> 扩展实现。</span></li><li id="ud41b2b6e" data-lake-index-type="0"><strong><span class="ne-text">优势</span></strong><span class="ne-text">: 可以将向量数据与你的业务数据（如用户信息、订单）存储在同一个数据库中，方便管理和关联查询。</span></li><li id="u6958f60f" data-lake-index-type="0"><strong><span class="ne-text">适用场景</span></strong><span class="ne-text">: 已经在使用 PostgreSQL 的团队，希望统一数据存储，且向量查询性能要求不是极致的场景。</span></li></ul></ul><ul class="ne-ul"><li id="uc8e44c4f" data-lake-index-type="0"><strong><span class="ne-text">MongoDbEmbeddingStore</span></strong></li></ul><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u26b4770c" data-lake-index-type="0"><strong><span class="ne-text">特点</span></strong><span class="ne-text">: MongoDB 6.0+ 版本内置了对向量的支持。</span></li><li id="u35091e25" data-lake-index-type="0"><strong><span class="ne-text">优势</span></strong><span class="ne-text">: 同样可以将向量数据与 JSON 格式的业务数据共存，利用 MongoDB 的灵活文档模型。</span></li><li id="u0a84a177" data-lake-index-type="0"><strong><span class="ne-text">适用场景</span></strong><span class="ne-text">: 已经在使用 MongoDB 的团队，追求开发灵活性。</span></li></ul></ul><ul class="ne-ul"><li id="u2018c8a3" data-lake-index-type="0"><strong><span class="ne-text">ElasticsearchEmbeddingStore</span></strong><span class="ne-text"> / </span><strong><span class="ne-text">OpenSearchEmbeddingStore</span></strong></li></ul><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u0898dba4" data-lake-index-type="0"><strong><span class="ne-text">特点</span></strong><span class="ne-text">: 基于 Lucene 的搜索引擎，通过插件支持向量搜索。</span></li><li id="u74fa0942" data-lake-index-type="0"><strong><span class="ne-text">优势</span></strong><span class="ne-text">: 可以结合全文搜索和向量搜索，实现混合查询（例如，先通过关键词过滤，再进行语义相似度排序）。</span></li><li id="u27bb29bf" data-lake-index-type="0"><strong><span class="ne-text">适用场景</span></strong><span class="ne-text">: 需要同时进行全文检索和语义检索的应用，如智能客服、文档搜索。</span></li></ul></ul><hr id="U7Iof" class="ne-hr"><h3 id="v7MU3"><span class="ne-text">3. 云厂商提供的服务</span></h3><p id="u60f66b87" class="ne-p"><span class="ne-text">这些是各大云厂商推出的集成了向量搜索能力的托管服务。</span></p><ul class="ne-ul"><li id="u204803a2" data-lake-index-type="0"><strong><span class="ne-text">AzureCosmosDbNoSqlEmbeddingStore</span></strong></li><li id="u54cb2536" data-lake-index-type="0"><strong><span class="ne-text">AzureCosmosDbMongoVCoreEmbeddingStore</span></strong></li><li id="ufc9b743a" data-lake-index-type="0"><strong><span class="ne-text">AzureAiSearchEmbeddingStore</span></strong></li><li id="u8d71e5d7" data-lake-index-type="0"><strong><span class="ne-text">TablestoreEmbeddingStore</span></strong><span class="ne-text"> (阿里云)</span></li></ul><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u8f4b1b27" data-lake-index-type="0"><strong><span class="ne-text">特点</span></strong><span class="ne-text">: 与各自云平台深度集成的托管服务。</span></li><li id="u8f3e2dc5" data-lake-index-type="0"><strong><span class="ne-text">优势</span></strong><span class="ne-text">: 高可用、高扩展、无需运维，并且可以方便地与同平台的其他服务（如 AI 模型服务、计算服务）联动。</span></li><li id="u325b8d0a" data-lake-index-type="0"><strong><span class="ne-text">适用场景</span></strong><span class="ne-text">: 已经深度使用特定云平台（如 Azure、阿里云）的企业，希望构建一体化的云原生应用。</span></li></ul></ul><hr id="ti1cu" class="ne-hr"><h3 id="d3rz2"><span class="ne-text">4. 内存存储 (仅用于开发和测试)</span></h3><ul class="ne-ul"><li id="u89986cb1" data-lake-index-type="0"><strong><span class="ne-text">InMemoryEmbeddingStore</span></strong></li></ul><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u93dabe2c" data-lake-index-type="0"><strong><span class="ne-text">特点</span></strong><span class="ne-text">: 数据仅存储在内存中。</span></li><li id="udd3b9a43" data-lake-index-type="0"><strong><span class="ne-text">优势</span></strong><span class="ne-text">: 速度极快，无需任何外部依赖。</span></li><li id="uc3667c43" data-lake-index-type="0"><strong><span class="ne-text">适用场景</span></strong><span class="ne-text">: </span><strong><span class="ne-text">仅限开发、测试和演示</span></strong><span class="ne-text">。程序重启后数据会全部丢失，</span><strong><span class="ne-text">绝对不能用于生产环境</span></strong><span class="ne-text">。</span></li></ul></ul><hr id="KAy2b" class="ne-hr"><h3 id="ZVJnE"><span class="ne-text">如何选择？一个简单的决策框架</span></h3><ol class="ne-ol"><li id="u92a2318f" data-lake-index-type="0"><strong><span class="ne-text">你处于什么阶段？</span></strong></li></ol><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u46321073" data-lake-index-type="0"><strong><span class="ne-text">快速原型/本地开发</span></strong><span class="ne-text">: 选择 </span><strong><span class="ne-text">Chroma</span></strong><span class="ne-text"> 或 </span><strong><span class="ne-text">InMemoryEmbeddingStore</span></strong><span class="ne-text">。</span></li><li id="ufa555836" data-lake-index-type="0"><strong><span class="ne-text">生产环境</span></strong><span class="ne-text">: 从专用向量数据库或云服务中选择。</span></li></ul></ul><ol start="2" class="ne-ol"><li id="u11bfbf36" data-lake-index-type="0"><strong><span class="ne-text">你的团队技术栈是什么？</span></strong></li></ol><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u679ce41e" data-lake-index-type="0"><strong><span class="ne-text">已经在用 PostgreSQL</span></strong><span class="ne-text">: 强烈考虑 </span><strong><span class="ne-text">PgVector</span></strong><span class="ne-text">。</span></li><li id="u14a613fd" data-lake-index-type="0"><strong><span class="ne-text">已经在用 MongoDB</span></strong><span class="ne-text">: 考虑 </span><strong><span class="ne-text">MongoDbEmbeddingStore</span></strong><span class="ne-text">。</span></li><li id="uafd382cf" data-lake-index-type="0"><strong><span class="ne-text">需要全文检索 + 向量搜索</span></strong><span class="ne-text">: 考虑 </span><strong><span class="ne-text">Elasticsearch</span></strong><span class="ne-text"> 或 </span><strong><span class="ne-text">OpenSearch</span></strong><span class="ne-text">。</span></li><li id="ubf3b24e0" data-lake-index-type="0"><strong><span class="ne-text">深度绑定某个云平台</span></strong><span class="ne-text">: 优先考虑该平台的服务（如 Azure AI Search）。</span></li></ul></ul><ol start="3" class="ne-ol"><li id="u6b6f8e5b" data-lake-index-type="0"><strong><span class="ne-text">你的性能和规模要求是什么？</span></strong></li></ol><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u13aebb2c" data-lake-index-type="0"><strong><span class="ne-text">亿级以上海量数据</span></strong><span class="ne-text">: </span><strong><span class="ne-text">Milvus</span></strong><span class="ne-text"> 是一个非常强的竞争者。</span></li><li id="u3473a2ca" data-lake-index-type="0"><strong><span class="ne-text">极致的查询性能</span></strong><span class="ne-text">: </span><strong><span class="ne-text">Qdrant</span></strong><span class="ne-text"> 和 </span><strong><span class="ne-text">Pinecone</span></strong><span class="ne-text"> 都表现出色。</span></li></ul></ul><ol start="4" class="ne-ol"><li id="u98edd92a" data-lake-index-type="0"><strong><span class="ne-text">你愿意管理基础设施吗？</span></strong></li></ol><ul class="ne-list-wrap"><ul ne-level="1" class="ne-ul"><li id="u70a43770" data-lake-index-type="0"><strong><span class="ne-text">不愿意，想要开箱即用</span></strong><span class="ne-text">: 选择 </span><strong><span class="ne-text">Pinecone</span></strong><span class="ne-text"> 或其他云厂商的托管服务。</span></li><li id="u066251c2" data-lake-index-type="0"><strong><span class="ne-text">愿意，追求更高的控制力和成本优化</span></strong><span class="ne-text">: 选择开源的 </span><strong><span class="ne-text">Qdrant</span></strong><span class="ne-text"> 或 </span><strong><span class="ne-text">Weaviate</span></strong><span class="ne-text">，自己部署在云服务器或 Kubernetes 上。</span></li></ul></ul><h3 id="wFGO3"><span class="ne-text">总结建议</span></h3><ul class="ne-ul"><li id="u4be48b74" data-lake-index-type="0"><strong><span class="ne-text">新手入门/快速原型</span></strong><span class="ne-text">: </span><strong><span class="ne-text">Chroma</span></strong><span class="ne-text"> 或 </span><strong><span class="ne-text">Qdrant</span></strong><span class="ne-text"> (使用 Docker 快速部署)。</span></li><li id="ub678818d" data-lake-index-type="0"><strong><span class="ne-text">追求简单，不想运维</span></strong><span class="ne-text">: </span><strong><span class="ne-text">Pinecone</span></strong><span class="ne-text">。</span></li><li id="uf576c00f" data-lake-index-type="0"><strong><span class="ne-text">已在使用 PostgreSQL 或 MongoDB</span></strong><span class="ne-text">: 优先考虑 </span><strong><span class="ne-text">PgVector</span></strong><span class="ne-text"> 或 </span><strong><span class="ne-text">MongoDbEmbeddingStore</span></strong><span class="ne-text">，实现数据统一。</span></li><li id="u7185697e" data-lake-index-type="0"><strong><span class="ne-text">需要混合搜索 (全文 + 语义)</span></strong><span class="ne-text">: </span><strong><span class="ne-text">Elasticsearch</span></strong><span class="ne-text">。</span></li><li id="u221aa215" data-lake-index-type="0"><strong><span class="ne-text">大规模生产环境，追求极致性能和扩展性</span></strong><span class="ne-text">: </span><strong><span class="ne-text">Qdrant</span></strong><span class="ne-text"> 或 </span><strong><span class="ne-text">Milvus</span></strong><span class="ne-text">。</span></li><li id="uf6c70722" data-lake-index-type="0"><strong><span class="ne-text">云原生应用，深度绑定云平台</span></strong><span class="ne-text">: 选择对应云厂商的服务。</span></li></ul></details>


#### EmbeddingModel
OpenAiEmbeddingModel



## AgenticServices


### AgentBuilder
Class<T> agentServiceClass

String name

ChatModel model

ChatMemory chatMemory

ContentRetriever contentRetriever

RetrievalAugmentor retrievalAugmentor

Map<ToolSpecification, ToolExecutor> toolsMap

Executor concurrentToolsExecutor

InputGuardrail[] inputGuardrails

OutputGuardrail[] outputGuardrails

String outputKey



build(DefaultAgenticScope agenticScope)

Proxy.newProxyInstance(new AgentInvocationHandler())



### AgentInvocationHandler
AiServiceContext context

AgentBuilder<?> builder

Object agent (aiServices.build())

UserMessageRecorder messageRecorder

String agentId

AgentInstance parent



### AgenticService
subAgents(Object... agents)

beforeCall(Consumer<AgenticScope> beforeCall)

outputKey(String outputKey)

outputKey(Class<? extends TypedKey<?>> outputKey)

errorHandler(Function<ErrorContext, ErrorRecoveryResult> errorHandler)

ConditionalAgentService

ParallelAgentService

LoopAgentService

SequentialAgentService

PlannerBasedService



#### PlannerBasedInvocationHandler
DefaultAgenticScope agenticScope

AbstractServiceBuilder<?, ?> service

Supplier<Planner> plannerSupplier

Planner defaultPlannerInstance

Type outputType

String outputKey

Function<AgenticScope, Object> output

List<AgentInstance> subagents

String agentId

AgentInstance parent

AtomicReference<AgenticScopeRegistry> agenticScopeRegistry

#### Planner
void init(InitPlanningContext initPlanningContext);

Action firstAction(PlanningContext planningContext);

AgenticSystemTopology topology();

Action nextAction(PlanningContext planningContext);

Action noOp();

Action call(AgentInstance... agents);

Action call(List<AgentInstance> agents);

Action done();

Action done(Object result);

SequentialPlanner

LoopPlanner

ParallelPlanner

SupervisorPlanner

ConditionalPlanner



##### AbstractServiceBuilder
String name

String outputKey

Function<AgenticScope, Object> output

Class<T> agentServiceClass

AgentListener agentListener

List<AgentExecutor> subagents = new ArrayList<>()

Executor executor

Function<ErrorContext, ErrorRecoveryResult> errorHandler

SequentialAgentServiceImpl

...



#### HumanInTheLoop
String inputKey

String outputKey

Consumer<?> requestWriter

Supplier<?> responseReader

AgentListener listener

boolean async



#### SupervisorAgentService
SupervisorAgentServiceImpl

ChatModel chatModel

ChatMemoryProvider chatMemoryProvider

SupervisorContextStrategy contextStrategy = SupervisorContextStrategy.CHAT_MEMORY

SupervisorResponseStrategy responseStrategy = SupervisorResponseStrategy.LAST

Function<AgenticScope, String> requestGenerator

String supervisorContext

SupervisorContextStrategy

```java
/**
 * Strategy for providing context to the supervisor agent.
 */
public enum SupervisorContextStrategy {
    /**
     * Use only the supervisors {@link ChatMemory} (default).
     */
    CHAT_MEMORY,
    /**
     * Use only a summarization of the interaction of the supervisor with its sub-agents.
     */
    SUMMARIZATION,
    /**
     * Use both the supervisor's {@link ChatMemory} and a summarization of the interaction of the supervisor with its sub-agents.
     */
    CHAT_MEMORY_AND_SUMMARIZATION
}
```

SupervisorResponseStrategy

```java
/**
 * Strategy to decide which response the supervisor agent should return.
 */
public enum SupervisorResponseStrategy {
    /**
     * Use an internal LLM to score the last response and the summarization of the interaction of the supervisor
     * with its sub-agents against the original user request, and return the one with the higher score.
     */
    SCORED,
    /**
     * Return a summarization of the interaction of the supervisor with its sub-agents.
     */
    SUMMARY,
    /**
     * Return only the final response of the last invoked sub-agent (default).
     */
    LAST
}
```



#### A2AService


##### A2AClientBuilder
DefaultA2AClientBuilder

Class<T> agentServiceClass

AgentCard agentCard

Client a2aClient

String agentId

AgentInstance parent

String[] inputKeys

String outputKey

boolean async

AgentListener agentListener





### AgenticScope
DefaultAgenticScope

Object memoryId

Map<String, Object> state = new ConcurrentHashMap<>()

transient Map<String, Object> agents = new ConcurrentHashMap<>()

List<AgentInvocation> agentInvocations = Collections.synchronizedList(new ArrayList<>())

List<AgentMessage> context = Collections.synchronizedList(new ArrayList<>())

Kind kind  (EPHEMERAL, REGISTERED, PERSISTENT)

transient ReadWriteLock lock****



#### AgenticScopeRegistry
String agentId

AgenticScopeStore store

#### AgenticScopeStore




## GuardrailService
AbstractGuardrailService

DefaultGuardrailService

#### InputGuardrail
MessageModeratorInputGuardrail

ModerationModel moderationModel

##### ModerationModel
OpenAiModerationModel

OpenAiClient client

String modelName

Integer maxRetries

#### OutputGuardrail
JsonExtractorOutputGuardrail



# agentScope


## Agent
### AgentBase
String agentId

String name

String description

AtomicBoolean running = new AtomicBoolean(false)

boolean checkRunning

List<Hook> hooks

List<Hook> systemHooks = new CopyOnWriteArrayList<>()

Map<String, List<AgentBase>> hubSubscribers = new ConcurrentHashMap<>()

AtomicBoolean interruptFlag = new AtomicBoolean(false)

AtomicReference<Msg> userInterruptMessage = new AtomicReference<>(null)



Mono<Void> doObserve(Msg msg)



Mono<Msg> call(List<Msg> msgs)

running.compareAndSet(false, true)

notifyPreCall(msgs)

PreCallEvent hooks::onEvent

doCall

notifyPostCall

PostCallEvent hooks::onEvent

broadcastToSubscribers(msg)

onErrorResume(createErrorHandler(msgs.toArray(new Msg[0])))

doFinally(signalType -> running.set(false))



### ReActAgent
Memory memory

String sysPrompt

String sysPrompt

Toolkit toolkit

PlanNotebook planNotebook

int maxIters

ExecutionConfig modelExecutionConfig

ExecutionConfig toolExecutionConfig

StructuredOutputReminder structuredOutputReminder

ToolExecutionContext toolExecutionContext

HookNotifier hookNotifier

MessagePreparer messagePreparer

AtomicReference<StructuredOutputHandler> currentStructuredOutputHandler



executeIteration

reasoning

actingOrFinish



Mono<Void> doObserve(Msg msg)

memory.addMessage(msg)



#### ReasoningPipeline
prepareMessageList: system prompt + user prompt

getToolSchemas: toolRegistry.getAllRegisteredTools() : local tools + mcp tools

streamWithHttpClient()

processFinalMessage()

List<ToolUseBlock> toolBlocks = reasoningMsg.getContentBlocks(ToolUseBlock.class)

memory add toolBlocks

#### ActingPipeline
acting()

MessageUtils.extractRecentToolCalls(memory.getMessages(), agentName)

toolkit execute tool call if present





#### PlanNotebook
Tool Functions: PlanNotebook provides 10 tool functions:

createPlan - Create a new plan

updatePlanInfo - Update current plan's name, description, or expected outcome

reviseCurrentPlan - Add, revise, or delete subtasks

updateSubtaskState - Update subtask state

finishSubtask - Mark subtask as done

viewSubtasks - View subtask details

getSubtaskCount - Get the number of subtasks in current plan

finishPlan - Finish or abandon plan

viewHistoricalPlans - View historical plans

recoverHistoricalPlan - Recover a historical plan





### A2aAgent
AgentCardResolver agentCardResolver

A2aAgentConfig a2aAgentConfig

Memory memory

ClientEventHandlerRouter clientEventHandlerRouter

Client a2aClient

String currentRequestId

ClientEventContext clientEventContext



### UserAgent
UserInputBase inputMethod



#### UserInputBase
Mono<UserInputData> handleInput(

        String agentId, String agentName, List<Msg> contextMessages, Class<?> structuredModel)

## Model
ChatModelBase

### DashScopeChatModel
DashScopeHttpClient httpClient

DashScopeChatFormatter<DashScopeMessage, DashScopeResponse, DashScopeRequest> formatter

OpenAIChatModel



### Formatter
AbstractBaseFormatter

#### DashScopeChatFormatter
DashScopeMessageConverter messageConverter

DashScopeResponseParser responseParser

DashScopeToolsHelper toolsHelper

applyOptions

applyTools

applyToolChoice



## Toolkit
### ParallelToolExecutor


### ToolRegistry
Map<String, AgentTool> tools = new ConcurrentHashMap<>()

Map<String, RegisteredToolFunction> registeredTools = new ConcurrentHashMap<>()



#### AgentTool
McpTool

McpClientWrapper clientWrapper



## Pipeline
### SequentialPipeline
### FanoutPipeline
### ComposedSequentialPipeline

## MsgHub
implements AutoCloseable

String name

List<AgentBase> participants

List<Msg> announcement

boolean enableAutoBroadcast



# Quarkus
## core
### BuildItem
#### BuildProducer


## extensions


### faulttolerance
#### SmallRyeFaultToleranceProcessor
@BuildStep

build()



@BuildStep

processFaultToleranceAnnotations()

#### FaultToleranceScanner
##### FaultToleranceMethod
Class<?> beanClass

MethodDescriptor method

RateLimit rateLimit

...

MethodDescriptor fallbackMethod

###### MethodDescriptor
Class<?> declaringClass

String name

Class<?>[] parameterTypes

Class<?> returnType

Method reflect()

declaringClass.getDeclaredMethod(name, parameterTypes)



#### QuarkusFaultToleranceOperationProvider
implements FaultToleranceOperationProvider



#### FaultToleranceInterceptor
@AroundInvoke

Object intercept(InvocationContext invocationContext)

##### Timeout
FaultToleranceStrategy<V> delegate

long timeoutInMillis

Timer timer

##### RateLimit
FaultToleranceStrategy<V> delegate

TimeWindow timeWindow

FixedWindow

##### Bulkhead
FaultToleranceStrategy<V> delegate

Deque<BulkheadTask> queue

Semaphore capacitySemaphore

Semaphore workSemaphore

##### CircuitBreaker




# shenyu
## plugins
plugins = {ArrayList@10522}  size = 12

0 = {GlobalPlugin@10370}

1 = {ContextPathPlugin@10371}

2 = {CryptorRequestPlugin@10372}

3 = {RedirectPlugin@10523}

4 = {GeneralContextPlugin@10373}

5 = {DividePlugin@10374}

6 = {WebSocketPlugin@10375}

7 = {URIPlugin@10376}

8 = {NettyHttpClientPlugin@10377}

9 = {RpcParamTransformPlugin@10378}

10 = {CryptorResponsePlugin@10379}

11 = {ResponsePlugin@10380}

# mybatis


XPathParser - xml 解析工具



org.apache.ibatis.session.Configuration - 核心配置类



Ognl ->

//todo 操作方式



TypeAliasRegistry



TypeHandler

BaseTypeHandler

StringTypeHandler

TypeHandlerRegistry



MetaObject -> 反射工具

ObjectWrapper objectWrapper;

objectFactory

objectWrapperFactory

reflectorFactory



ErrorContext -> 错误格式化打印



MappedStatement -> xml配的一个sql 语句块

DynamicSqlSource sqlSource



DynamicSqlSource -> 动态生成sql



DynamicContext -> 拼接sql



SqlNode -> xml标签

IfSqlNode



XMLLanguageDriver



VFS -> 虚拟文件系统, 加载文件，jar包

DefaultVFS



SqlSessionFactoryBuilder -> SqlSessionFactory 生成





XMLConfigBuilder -> 解析xml 生成



```java
private void parseConfiguration(XNode root) {
    try {
      // issue #117 read properties first
      propertiesElement(root.evalNode("properties"));
      Properties settings = settingsAsProperties(root.evalNode("settings"));
      loadCustomVfsImpl(settings);
      loadCustomLogImpl(settings);
      typeAliasesElement(root.evalNode("typeAliases"));
      pluginsElement(root.evalNode("plugins"));
      objectFactoryElement(root.evalNode("objectFactory"));
      objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
      reflectorFactoryElement(root.evalNode("reflectorFactory"));
      settingsElement(settings);
      // read it after objectFactory and objectWrapperFactory issue #631
      environmentsElement(root.evalNode("environments"));
      databaseIdProviderElement(root.evalNode("databaseIdProvider"));
      typeHandlersElement(root.evalNode("typeHandlers"));

    // 
      mappersElement(root.evalNode("mappers"));
    } catch (Exception e) {
      throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
    }
  }


```



UnpooledDataSource -> 每次新建连接

PooledDataSource -> 连接池

private final PoolState state

popConnection() 获取连接

pingConnection() 监测连接是否正常

pushConnection() 释放连接



TransactionFactory

JdbcTransactionFactory



Transaction

JdbcTransaction

SpringManagedTransaction



DatabaseIdProvider -> id



CacheBuilder

Cache

LruCache

ScheduledCache

SerializedCache

LoggingCache

SynchronizedCache

BlockingCache



XMLMapperBuilder

```java

private void configurationElement(XNode context) {
    try {
      String namespace = context.getStringAttribute("namespace");
      if (namespace == null || namespace.isEmpty()) {
        throw new BuilderException("Mapper's namespace cannot be empty");
      }
      builderAssistant.setCurrentNamespace(namespace);
      cacheRefElement(context.evalNode("cache-ref"));
      cacheElement(context.evalNode("cache"));
      parameterMapElement(context.evalNodes("/mapper/parameterMap"));
      resultMapElements(context.evalNodes("/mapper/resultMap"));
      sqlElement(context.evalNodes("/mapper/sql"));
      buildStatementFromContext(context.evalNodes("select|insert|update|delete"));
    } catch (Exception e) {
      throw new BuilderException("Error parsing Mapper XML. The XML location is '" + resource + "'. Cause: " + e, e);
    }
  }

public void parse() {
    if (!configuration.isResourceLoaded(resource)) {
      configurationElement(parser.evalNode("/mapper"));
      configuration.addLoadedResource(resource);
    // 解析接口文件
      bindMapperForNamespace();
    }
    // 解析挂起的解析
    configuration.parsePendingResultMaps(false);
    configuration.parsePendingCacheRefs(false);
    configuration.parsePendingStatements(false);
  }
```



XMLStatementBuilder



预编译



SqlSessionFactory

DefaultSqlSessionFactory



```java
private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level,
      boolean autoCommit) {
    Transaction tx = null;
    try {
      final Environment environment = configuration.getEnvironment();
      final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);
      tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);
      final Executor executor = configuration.newExecutor(tx, execType);
      return createSqlSession(configuration, executor, autoCommit);
    } catch (Exception e) {
      closeTransaction(tx); // may have fetched a connection so lets call close()
      throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
    } finally {
      ErrorContext.instance().reset();
    }
  }
```



SqlSession

DefaultSqlSession



Executor

BaseExecutor

SimpleExecutor

ReuseExecutor

BatchExecutor

CachingExecutor



StatementHandler

BaseStatementHandler

CallableStatementHandler

PreparedStatementHandler

SimpleStatementHandler



ParameterHandler

DefaultParameterHandler



ResultSetHandler

DefaultResultSetHandler



ResultLoader -> 懒加载

org.apache.ibatis.executor.loader.ProxyFactory

JavassistProxyFactory



MethodHandler -> 反射api, todo



InvocationHandler

MapperProxy

MapperProxyFactory



MethodHandle



Interceptor



PageHelper

```java
@Intercepts(
    {
        @Signature(type = Executor.class, method = "query", args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class}),
        @Signature(type = Executor.class, method = "query", args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class, CacheKey.class, BoundSql.class}),
    }
)
public class PageInterceptor implements Interceptor {
```



# Mybatis Plus


DynamicDataSourceContextHolder

```java
// stack servieA -> servieB -> servieC 
  // 为了支持嵌套切换，如ABC三个service都是不同的数据源
  // 其中A的某个业务要调B的方法，B的方法需要调用C的方法。一级一级调用切换，形成了链。
  // 传统的只设置当前线程的方式不能满足此业务需求，必须使用栈，后进先出。
private static final ThreadLocal<Deque<String>> LOOKUP_KEY_HOLDER = new NamedThreadLocal<Deque<String>>("dynamic-datasource") {
    @Override
    protected Deque<String> initialValue() {
        return new ArrayDeque<>();
    }
};
```



DynamicRoutingDataSource



InvocationHandler

MybatisMapperProxy



MybatisMapperProxy



MybatisMapperMethod



## plugins
### PaginationInnerInterceptor
com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor

-> page.optimizeJoinOfCountSql 进行 left join 优化



com.github.pagehelper.PageInterceptor

-> 无优化

# netty


HashedWheelTimer -> 时间轮



# nacos
## client


NacosServiceDiscovery



NacosServiceRegistry



## server


### naming
#### Request
InstanceRequest



#### RequestHandler
InstanceRequestHandler

private final EphemeralClientOperationServiceImpl clientOperationService;



#### DistroFilter
DistroMapper

serverIndex = Math.abs(serviceName.hashCode() % Integer.MAX_VALUE)% servers.size()

@CanDistro

InstanceController



### config


### consistency


#### ClientOperationService
instance.isEphemeral() ? EphemeralClientOperationServiceImpl : PersistentClientOperationServiceImpl

EphemeralClientOperationServiceImpl

ClientRegisterServiceEvent

DistroClientDataProcessor

PersistentClientOperationServiceImpl





#### ConsistencyProtocol
void init(T config);

void addRequestProcessors(Collection<P> processors);

ProtocolMetaData protocolMetaData();

Response getData(ReadRequest request);

CompletableFuture<Response> aGetData(ReadRequest request);

Response write(WriteRequest request);

void memberChange(Set<String> addresses);

CompletableFuture<Response> writeAsync(WriteRequest request);



##### AbstractConsistencyProtocol


##### CPProtocol
JRaftProtocol

private JRaftServer raftServer;

       // **Constants.NAMING_PERSISTENT_SERVICE_GROUP & Constants.INSTANCE_METADATA**

private Map<String, RaftGroupTuple> multiRaftGroup = new ConcurrentHashMap<>();

NamingMetadataOperateService

private final CPProtocol cpProtocol;

EmbeddedDumpService

private final CPProtocol cpProtocol;



##### APProtocol
DistroProtocol

DistroClientDataProcessor

private final DistroProtocol distroProtocol;



#### ProtocolManager
private CPProtocol cpProtocol;

private APProtocol apProtocol;

private final Object cpLock = new Object();

private final Object apLock = new Object();





#### Member


# Dubbo


## common
### **HashedWheelTimer**




## LoadBalance
AbstractLoadBalance

RandomLoadBalance

```java
random=org.apache.dubbo.rpc.cluster.loadbalance.RandomLoadBalance
roundrobin=org.apache.dubbo.rpc.cluster.loadbalance.RoundRobinLoadBalance
leastactive=org.apache.dubbo.rpc.cluster.loadbalance.LeastActiveLoadBalance
consistenthash=org.apache.dubbo.rpc.cluster.loadbalance.ConsistentHashLoadBalance
shortestresponse=org.apache.dubbo.rpc.cluster.loadbalance.ShortestResponseLoadBalance
adaptive=org.apache.dubbo.rpc.cluster.loadbalance.AdaptiveLoadBalance
```



## Protocol
AbstractProtocol

DubboProtocol



```java
package org.apache.dubbo.rpc;
import org.apache.dubbo.common.extension.ExtensionLoader;
public class Protocol$Adaptive implements org.apache.dubbo.rpc.Protocol {
    public void destroy()  {
        throw new UnsupportedOperationException("The method public abstract void org.apache.dubbo.rpc.Protocol.destroy() of interface org.apache.dubbo.rpc.Protocol is not adaptive method!");
    }
    public int getDefaultPort()  {
        throw new UnsupportedOperationException("The method public abstract int org.apache.dubbo.rpc.Protocol.getDefaultPort() of interface org.apache.dubbo.rpc.Protocol is not adaptive method!");
    }
    public org.apache.dubbo.rpc.Invoker refer(java.lang.Class arg0, org.apache.dubbo.common.URL arg1) throws org.apache.dubbo.rpc.RpcException {
        if (arg1 == null) throw new IllegalArgumentException("url == null");
        org.apache.dubbo.common.URL url = arg1;
        String extName = ( url.getProtocol() == null ? "dubbo" : url.getProtocol() );
        if(extName == null) throw new IllegalStateException("Failed to get extension (org.apache.dubbo.rpc.Protocol) name from url (" + url.toString() + ") use keys([protocol])");
        org.apache.dubbo.rpc.Protocol extension = (org.apache.dubbo.rpc.Protocol)ExtensionLoader.getExtensionLoader(org.apache.dubbo.rpc.Protocol.class).getExtension(extName);
        return extension.refer(arg0, arg1);
    }
    public org.apache.dubbo.rpc.Exporter export(org.apache.dubbo.rpc.Invoker arg0) throws org.apache.dubbo.rpc.RpcException {
        if (arg0 == null) throw new IllegalArgumentException("org.apache.dubbo.rpc.Invoker argument == null");
        if (arg0.getUrl() == null) throw new IllegalArgumentException("org.apache.dubbo.rpc.Invoker argument getUrl() == null");
        org.apache.dubbo.common.URL url = arg0.getUrl();
        String extName = ( url.getProtocol() == null ? "dubbo" : url.getProtocol() );
        if(extName == null) throw new IllegalStateException("Failed to get extension (org.apache.dubbo.rpc.Protocol) name from url (" + url.toString() + ") use keys([protocol])");
        org.apache.dubbo.rpc.Protocol extension = (org.apache.dubbo.rpc.Protocol)ExtensionLoader.getExtensionLoader(org.apache.dubbo.rpc.Protocol.class).getExtension(extName);
        return extension.export(arg0);
    }
}
```



## RegistryService
Registry  extends Node, RegistryService

AbstractRegistry

FailbackRegistry

MulticastRegistry



ZookeeperRegistry



# RocketMq
## core
### message store
#### MessageStore
DefaultMessageStore



getMessage()

check messageFilter.isMatchedByConsumeQueue

bloomFilter.isHit()

check messageFilter.isMatchedByCommitLog



CommitLogDispatcher

CommitLogDispatcherCalcBitMap

ConsumerFilterManager consumerFilterManager;

BloomFilter bloomFilter;

dispatch()

evaluate true then bloomFilter.hashTo()

#### CommitLog
asyncPutMessages

#### 
### pull & push messsage


DefaultMQPushConsumer

private long consumeTimeout = 15; // minutes

DefaultMQPushConsumerImpl defaultMQPushConsumerImpl;

private MQClientInstance mQClientFactory;

private ConsumeMessageService consumeMessageService;

private ConsumeMessageService consumeMessagePopService;



consumeMessageService:

MessageListenerConcurrently

ConsumeMessageConcurrentlyService

private final ThreadPoolExecutor consumeExecutor;

this.consumeExecutor.submit(consumeRequest)



consumeMessagePopService:

MessageListenerConcurrently

ConsumeMessagePopConcurrentlyService



MQClientInstance

PullMessageService

private final LinkedBlockingQueue<MessageRequest> messageRequestQueue

RebalanceService



MessageRequest

PullRequest

private String consumerGroup;

private MessageQueue messageQueue;

private ProcessQueue processQueue;

private long nextOffset;

private boolean previouslyLocked = false;



PullResult

private final long nextBeginOffset;



ProcessQueue

private final ReadWriteLock treeMapLock = new ReentrantReadWriteLock();

private final TreeMap<Long, MessageExt> msgTreeMap = new TreeMap<>();



DefaultLitePullConsumer

DefaultLitePullConsumerImpl defaultLitePullConsumerImpl

org.apache.rocketmq.client.consumer.DefaultLitePullConsumer#poll() 区别



DefaultMQProducerImpl

org.apache.rocketmq.client.impl.producer.DefaultMQProducerImpl#sendMessageInTransaction



DefaultMQPushConsumerImpl



pull message:

client:

DefaultMQPushConsumerImpl

MQClientAPIImpl

NettyRemotingClient

PullMessageRequestHeader



server:

PullMessageProcessor

org.apache.rocketmq.store.MessageStore#getMessageAsync(java.lang.String, java.lang.String, int, long, int, org.apache.rocketmq.store.MessageFilter)

ReferredIterator<CqUnit> bufferConsumeQueue = consumeQueue.iterateFrom(nextBeginOffset);

DefaultPullMessageResultHandler







### namesrv
org.apache.rocketmq.common.namesrv.DefaultTopAddressing

## spring
DefaultRocketMQListenerContainer

DefaultMQPushConsumer

RocketMQMessageConverter

fastjson jackson

# MqCloud
producer 监控， 基于 SohuSendMessageHook implements SendMessageHook -> com.sohu.tv.mq.cloud.web.controller.ClusterController#report , 60s job

consumer 监控， 基于com.sohu.tv.mq.rocketmq.consumer.AbstractMessageConsumer -> com.sohu.tv.mq.cloud.web.controller.ClusterController#consumerReport , 60s job





# Sentinel


## dashboard publish rule
com.alibaba.csp.sentinel.dashboard.controller.DegradeController#apiAddRule

1. InMemoryRuleRepositoryAdapter( update )
2. SentinelApiClient (http invoke all clients )

-> client

com.alibaba.csp.sentinel.transport.command.http.HttpEventTask



SimpleHttpCommandCenter

ServerThread -> run(）-> socket = this.serverSocket.accept(); -> new HttpEventTask(socket); -> @CommandMapping(name = "setRules") -> handle()



CommandHandler

ModifyRulesCommandHandler

## heartbeat
com.alibaba.csp.sentinel.transport.heartbeat.SimpleHttpHeartbeatSender

## config loader


com.alibaba.csp.sentinel.config.SentinelConfigLoader



## spring initializer
com.alibaba.cloud.sentinel.custom.context.SentinelApplicationContextInitializer

```plain
SentinelProperties sentinelProperties = Binder.get(environment)
       .bindOrCreate(SentinelConstants.PROPERTY_PREFIX, SentinelProperties.class);
```

## core


DegradeRuleManager



com.alibaba.csp.sentinel.slotchain.ProcessorSlot

com.alibaba.csp.sentinel.slots.block.flow.FlowSlot



ProcessorSlotChain:

NodeSelectorSlot

ClusterBuilderSlot

LogSlot

StatisticSlot

AuthoritySlot

SystemSlot

FlowSlot

DegradeSlot



SystemStatusListener



com.alibaba.csp.sentinel.init.InitFunc



Node

StatisticNode

Metric rollingCounterInSecond = new ArrayMetric(SampleCountProperty.SAMPLE_COUNT,

    IntervalProperty.INTERVAL); // 1s 两个 window

OccupiableBucketLeapArray



## adapter
sentinel-spring-webmvc-adapter

org.springframework.web.servlet.AsyncHandlerInterceptor

resourceName:

String BEST_MATCHING_PATTERN_ATTRIBUTE = HandlerMapping.class.getName() + ".bestMatchingPattern";



cluster mode:

client

DefaultClusterTokenClient ->



## server
NettyTransportServer  
TokenServerHandler

DefaultTokenService

ClusterFlowChecker

ClusterFlowRuleManager



## extension
sentinel-datasource-nacos



## transport
sentinel-transport-simple-http



# ElasticJob


ShardingService

JobShardingStrategy

AverageAllocationJobShardingStrategy (default=true)

OdevitySortByNameJobShardingStrategy

...

regCenter.persistEphemeral

regCenter.executeInTransaction(getShardingResultTransactionOperations(shardingResults))



ElasticJobExecutor -> .. ->



JobTracingEventBus



# Apollo


client -> read config-> metaServer-> Eureka (service discoery & registriy) -> configService -> configDB

write config -> portalService -> adminService -> configDB



spring boot



com.ctrip.framework.apollo.spring.boot.ApolloApplicationContextInitializer



org.springframework.core.env.CompositePropertySource





# JetCache


AbstractCacheAutoInit -> process CacheBuilder

ExternalCacheAutoInit

RedisAutoConfiguration



CacheBuilder

AbstractCacheBuilder

EmbeddedCacheBuilder

CaffeineCacheBuilder



ConfigProvider

SpringConfigProvider



CacheManager

SimpleCacheManager



Cache

AbstractCache

AbstractEmbeddedCache

CaffeineCache

RefreshCache

LoadingCache



Cache

AbstractCache

MultiLevelCache



# RateLimiter
## guava - com.google.common.util.concurrent.RateLimiter
micros /qps  = micros for per request

com.google.common.util.concurrent.SmoothRateLimiter#coolDownIntervalMicros

warn up

com.google.common.util.concurrent.SmoothRateLimiter.SmoothWarmingUp#storedPermitsToWaitTime



# skywalking
## java agent


### SkyWalkingAgent
```plain
Manifest-Version: 1.0
Created-By: Apache Maven 3.6.3
Built-By: Administrator
Build-Jdk: 17.0.15
Specification-Title: apm-agent
Specification-Version: 9.5
Specification-Vendor: The Apache Software Foundation
Implementation-Title: apm-agent
Implementation-Version: 9.5.0
Implementation-Vendor-Id: org.apache.skywalking
Implementation-Vendor: The Apache Software Foundation
Implementation-URL: http://maven.apache.org
Can-Redefine-Classes: true
Can-Retransform-Classes: true
Premain-Class: org.apache.skywalking.apm.agent.SkyWalkingAgent
```

#### PluginBootstrap
(AbstractClassEnhancePluginDefine) Class.forName(pluginDefine.getDefineClass(), true, AgentClassLoader

    .getDefault()).newInstance()



##### PluginResourcesResolver
urls = AgentClassLoader.getDefault().getResources("skywalking-plugin.def");



##### PluginDefine
private String name;

private String defineClass;



##### DynamicPluginLoader
ServiceLoader.load(InstrumentationLoader.class, classLoader)

###### InstrumentationLoader
List<AbstractClassEnhancePluginDefine> load(AgentClassLoader classLoader);

CustomizeInstrumentationLoader



#### AgentBuilder
##### BootstrapInstrumentBoost
AgentBuilder agentBuilder = newAgentBuilder().ignore(

    nameStartsWith("net.bytebuddy.")

        .or(nameStartsWith("org.slf4j."))

        .or(nameStartsWith("org.groovy."))

        .or(nameContains("javassist"))

        .or(nameContains(".asm."))

        .or(nameContains(".reflectasm."))

        .or(nameStartsWith("sun.reflect"))

        .or(allSkyWalkingAgentExcludeToolkit())

        .or(ElementMatchers.isSynthetic()));



net.bytebuddy.agent.builder.AgentBuilder.Default

SWAgentBuilderDefault extends AgentBuilder.Default



generateDelegator

new ByteBuddy().redefine(TypeDescription type, ClassFileLocator classFileLocator)

.name(internalInterceptorName)

.field(named("PLUGIN_NAME"))

.value(pluginName)

.field(named("TARGET_INTERCEPTOR"))

.value(methodsInterceptor)

.make();



build enhance class with interceptor Delegators

agentBuilder.type(pluginFinder.buildMatch())

            .transform(new Transformer(pluginFinder))

            .with(AgentBuilder.RedefinitionStrategy.RETRANSFORMATION)

            .with(new RedefinitionListener())

            .with(new Listener())

            .installOn(instrumentation);



java.lang.instrument.Instrumentation

addTransformer(ClassFileTransformer transformer);



#### ServiceManager
##### BootService
void prepare() throws Throwable;

void boot() throws Throwable;

void onComplete() throws Throwable;

void shutdown() throws Throwable;

default int priority() {return 0;}

JVMService

JVMMetricsSender sender

private LinkedBlockingQueue<JVMMetric> queue;

CommandExecutorService

private Map<String, CommandExecutor> commandExecutorMap;

ProfileTaskCommandExecutor

ConfigurationDiscoveryCommandExecutor

AsyncProfilerCommandExecutor

ProfileTaskExecutionService

MeterService

private MeterSender sender;

private final ConcurrentHashMap<MeterId, BaseMeter> meterMap = new ConcurrentHashMap<>();

scheduleWithFixedDelay 20s

TraceSegmentServiceClient

private volatile DataCarrier<TraceSegment> carrier;





### AbstractSpan


### AbstractClassEnhancePluginDefine
public abstract ConstructorInterceptPoint[] getConstructorsInterceptPoints();

protected abstract ClassMatch enhanceClass();

public abstract InstanceMethodsInterceptPoint[] getInstanceMethodsInterceptPoints();

ClassEnhancePluginDefine

ClassInstanceMethodsEnhancePluginDefine



SentinelCtEntryInstrumentation



### EnhancedInstance
Object getSkyWalkingDynamicField();

void setSkyWalkingDynamicField(Object value);

### InstanceConstructorInterceptor
void onConstruct(EnhancedInstance objInst, Object[] allArguments) throws Throwable;



### InstanceMethodsInterceptPoint
ElementMatcher<MethodDescription> getMethodsMatcher();

String getMethodsInterceptor();

boolean isOverrideArgs();



#### ElementMatcher
net.bytebuddy.matcher.ElementMatcher



##### samples
```java
package org.apache.skywalking.apm.plugin.mybatis;

import net.bytebuddy.description.method.MethodDescription;
import net.bytebuddy.matcher.ElementMatcher;

import static net.bytebuddy.matcher.ElementMatchers.named;
import static net.bytebuddy.matcher.ElementMatchers.takesArguments;

public enum MyBatisMethodMatch {
    INSTANCE;

    public ElementMatcher<MethodDescription> getMyBatisMethodMatcher() {
        return named("select").and(takesArguments(4))
        .or(named("selectList").and(takesArguments(3)))
        .or(named("update").and(takesArguments(2)));
    }

    public ElementMatcher<MethodDescription> getMyBatisShellMethodMatcher() {
        return named("selectOne").or(named("selectMap"))
        .or(named("insert"))
        .or(named("delete"))
        .or(named("select").and(takesArguments(2)))
        .or(named("select").and(takesArguments(3)))
        .or(named("selectList").and(takesArguments(1)))
        .or(named("selectList").and(takesArguments(2)))
        .or(named("update").and(takesArguments(1)));
    }
}
```



### InstanceMethodsAroundInterceptor
```java
/**
 * A interceptor, which intercept method's invocation. The target methods will be defined in {@link
 * ClassEnhancePluginDefine}'s subclass, most likely in {@link ClassInstanceMethodsEnhancePluginDefine}
 */
public interface InstanceMethodsAroundInterceptor {
    /**
     * called before target method invocation.
     *
     * @param result change this result, if you want to truncate the method.
     */
    void beforeMethod(EnhancedInstance objInst, Method method, Object[] allArguments, Class<?>[] argumentsTypes,
                      MethodInterceptResult result) throws Throwable;

    /**
     * called after target method invocation. Even method's invocation triggers an exception.
     *
     * @param ret the method's original return value. May be null if the method triggers an exception.
     * @return the method's actual return value.
     */
    Object afterMethod(EnhancedInstance objInst, Method method, Object[] allArguments, Class<?>[] argumentsTypes,
                       Object ret) throws Throwable;

    /**
     * called when occur exception.
     *
     * @param t the exception occur.
     */
    void handleMethodException(EnhancedInstance objInst, Method method, Object[] allArguments,
                               Class<?>[] argumentsTypes, Throwable t);
}
```



### resources: skywalking-plugin.def
```plain
sentinel-1.x=org.apache.skywalking.apm.plugin.sentinel.v1.define.SentinelCtEntryInstrumentation
sentinel-1.x=org.apache.skywalking.apm.plugin.sentinel.v1.define.SentinelAsyncEntryInstrumentation
sentinel-1.x=org.apache.skywalking.apm.plugin.sentinel.v1.define.SentinelCtSphInstrumentation
```

## oap server


# opentelemtry
## core
### OpenTelemetry
TracerBuilder tracerBuilder(String instrumentationScopeName)

MeterBuilder meterBuilder(String instrumentationScopeName)

#### GlobalOpenTelemetry
private static volatile OpenTelemetry globalOpenTelemetry;



### Context
Context current()

Context root()

#### ContextStorage
ThreadLocalContextStorage

ThreadLocal<Context> THREAD_LOCAL_STORAGE = new ThreadLocal()

### Tracer
#### TracerBuilder
##### SpanBuilder
SpanBuilder setParent(Context context)

SdkSpanBuilder



##### RandomIdGenerator
String generateTraceId()

longToBase16String(ThreadLocalRandom.nextLong()) + longToBase16String(ThreadLocalRandom.nextLong())

String generateSpanId()

longToBase16String(ThreadLocalRandom.nextLong())

### Logger
#### LoggerBuilder
ExportLogsServiceRequest

## agent
## OpenTelemetryAgent
```plain
Main-Class: io.opentelemetry.javaagent.OpenTelemetryAgent
Agent-Class: io.opentelemetry.javaagent.OpenTelemetryAgent
Premain-Class: io.opentelemetry.javaagent.OpenTelemetryAgent
```

## InstrumentationModule
public List<TypeInstrumentation> typeInstrumentations()

### SpanNameExtractor<REQUEST>
String extract(REQUEST request);



#### DbClientSpanNameExtractor<REQUEST>
```java
public static <REQUEST> SpanNameExtractor<REQUEST> create(
    DbClientAttributesGetter<REQUEST, ?> getter) {
    return new GenericDbClientSpanNameExtractor<>(getter);
}
```

GenericDbClientSpanNameExtractor<REQUEST>

##### DbClientCommonAttributesGetter<REQUEST, RESPONSE>
String getDbSystem(REQUEST request);

String getUser(REQUEST request);

String getConnectionString(REQUEST request)

String getResponseStatus(@Nullable RESPONSE response, @Nullable Throwable error)

String getDbNamespace(REQUEST request);

DbClientAttributesGetter<REQUEST, RESPONSE>

String getStatement(REQUEST request);

String getOperation(REQUEST request)

String getDbQueryText(InfluxDbRequest request)

...

InfluxDbAttributesGetter



### SpanKindExtractor<REQUEST>
```java
public enum SpanKind {
    /** Default value. Indicates that the span is used internally. */
    INTERNAL,

    /** Indicates that the span covers server-side handling of an RPC or other remote request. */
    SERVER,

    /**
   * Indicates that the span covers the client-side wrapper around an RPC or other remote request.
   */
    CLIENT,

    /**
   * Indicates that the span describes producer sending a message to a broker. Unlike client and
   * server, there is no direct critical path latency relationship between producer and consumer
   * spans.
   */
    PRODUCER,

    /**
   * Indicates that the span describes consumer receiving a message from a broker. Unlike client and
   * server, there is no direct critical path latency relationship between producer and consumer
   * spans.
   */
    CONSUMER
}
```



### AttributesExtractor<REQUEST, RESPONSE>
DbClientAttributesExtractor

ServerAttributesExtractor



### OperationMetrics
OperationListener create(Meter meter);

#### OperationListener
onStart

onEnd

### Instrumenter
```java
public class Instrumenter<REQUEST, RESPONSE> {
    
    private final String instrumentationName;
    private final Tracer tracer;
    private final SpanNameExtractor<? super REQUEST> spanNameExtractor;
    private final SpanKindExtractor<? super REQUEST> spanKindExtractor;
    private final AttributesExtractor<? super REQUEST, ? super RESPONSE>[] attributesExtractors;
    private final OperationListener[] operationListeners;
    private final SpanSuppressor spanSuppressor;
    ...
    
    /**
   * Starts a new instrumented operation. The returned {@link Context} should be propagated along
   * with the operation and passed to the {@link #end(Context, Object, Object, Throwable)} method
   * when it is finished.
   *
   * <p>The {@code parentContext} is the parent of the resulting instrumented operation and should
   * usually be {@link Context#current() Context.current()}. The {@code request} is the request
   * object of this operation.
   */
    public Context start(Context parentContext, REQUEST request) {
        return doStart(parentContext, request, null);
    }

    /**
   * Ends an instrumented operation. It is of extreme importance for this method to be always called
   * after {@link #start(Context, Object) start()}. Calling {@code start()} without later {@code
   * end()} will result in inaccurate or wrong telemetry and context leaks.
   *
   * <p>The {@code context} must be the same value that was returned from {@link #start(Context,
   * Object)}. The {@code request} parameter is the request object of the operation, {@code
   * response} is the response object of the operation, and {@code error} is an exception that was
   * thrown by the operation or {@code null} if no error occurred.
   */
    public void end(
        Context context, REQUEST request, @Nullable RESPONSE response, @Nullable Throwable error) {
        doEnd(context, request, response, error, null);
    }
    ...  
}
```



### TypeInstrumentation
ElementMatcher<TypeDescription> typeMatcher();

void transform(TypeTransformer transformer);

### TypeTransformer
```java
public interface TypeTransformer {
    default void applyAdviceToMethod(
        ElementMatcher<? super MethodDescription> methodMatcher, String adviceClassName) {
        applyAdviceToMethod(methodMatcher, Function.identity(), adviceClassName);
    }
    
    void applyAdviceToMethod(
        ElementMatcher<? super MethodDescription> methodMatcher,
        Function<Advice.WithCustomMapping, Advice.WithCustomMapping> mappingCustomizer,
        String adviceClassName);
    ...
}
```

#### advice
adviceClassName: XxxTypeInstrumentationx.class.getName() + "$XxxAdvice"

XxxAdvice

+ @Advice.OnMethodEnter
+ @Advice.OnMethodExit
+ @Advice.AssignReturned.ToAllArguments
+ @Advice.This : typeMatcher class
+ @Advice.AllArguments
+ @Advice.Argument(0) : the index of the mapped parameter
+ @Advice.FieldValue
+ @Advice.Origin



## spring
SpringBootServiceNameDetector



# Conductor
## model
WorkflowDef

WorkflowTask



TaskDef

Task

TaskType

## net
### grpc
grpc-netty

grpc-services

grpc-protobuf

### rest
spring-boot-starter-web



## persistence
### metadata
Cassandra

ConcurrentMapCache + Cassandra

MySQL

Postgres

Redis

Sqlite

### external payload
AzureBlob

Dummy(local file storage ，used when no external payload is configured)

Postgres

Amazon S3

### index data
workFlow

task

ExecutionLog

EventExecution

Message



ElasticSearch

OpenSearch

Postgres

Sqlite



# Lucene
## IndexFiles


## IndexWriter
addDocument(Iterable<? extends IndexableField> doc)

DocumentsWriter docWriter



### DocumentsWriter
DocumentsWriterFlushControl flushControl

#### DocumentsWriterFlushControl
DocumentsWriterPerThreadPool perThreadPool



##### DocumentsWriterPerThread
LockableConcurrentApproximatePriorityQueue<DocumentsWriterPerThread> freeList =

    new LockableConcurrentApproximatePriorityQueue<>()

Supplier<DocumentsWriterPerThread> dwptFactory

int numDocsInRAM

IndexingChain indexingChain

###### LockableConcurrentApproximatePriorityQueue
ConcurrentApproximatePriorityQueue<T> queue

AtomicInteger addAndUnlockCounter = new AtomicInteger()

ConcurrentApproximatePriorityQueue

Lock[] locks

ApproximatePriorityQueue<T>[] queues

ApproximatePriorityQueue

List<T> slots = new ArrayList<>(Long.SIZE)

##### BKDWriter
int maxPointsInLeafNode

byte[] maxPackedValue

###### OneDimensionBKDWriter
byte[] leafValues = new byte[config.maxPointsInLeafNode() * config.packedBytesLength()]



## IndexSearcher
QueryCache queryCache

QueryCachingPolicy DEFAULT_CACHING_POLICY = new UsageTrackingQueryCachingPolicy()

TaskExecutor taskExecutor

List<LeafReaderContext> leafContexts

IndexReader reader

IndexReaderContext context

volatile LeafSlice[] leafSlices

QueryTimeout queryTimeout



### QueryTimeout
QueryTimeoutImpl

Long timeoutAt

### LRUQueryCache


### LeafSlice
LeafReaderContextPartition[] partitions

int maxDocs

#### LeafReaderContextPartition
int minDocId

int maxDocId

LeafReaderContext ctx

int maxDocs

### TopScoreDocCollectorManager
int numHits

reduce(Collection<TopScoreDocCollector> collectors)

#### TopDocs
merge(int start, int topN, TopDocs[] shardHits)

K-way Merge 算法  nlogn -> nlogk

#### TopScoreDocCollector
extends TopDocsCollector<ScoreDoc>



TopDocsCollector implements Collector

### TermQuery
Term term

TermStates perReaderTermState

ScorerSupplier scorerSupplier(LeafReaderContext context)

Term

String field

BytesRef bytes

BulkScorer

BatchScoreBulkScorer

score(LeafCollector collector, Bits acceptDocs, int min, int max)

ConstantScoreScorer



### Weight


### IndexReader
CompositeReader

CompositeReaderContext readerContext = null

BaseCompositeReader

DirectoryReader

LeafReader



### IndexReaderContext


### IntersectVisitor
grow(int count)

### PointTree




## Document
List<IndexableField> fields = new ArrayList<>()



### IndexableField
Field

IndexableFieldType type

String name

Object fieldsData

#### StringField
BytesRef binaryValue

StoredValue storedValue

#### IntField
StoredValue storedValue

#### KnnFloatVectorField
#### IndexableFieldType
FieldType

boolean tokenized

#### StoredValue
private final Type type;

private int intValue;

private long longValue;

private float floatValue;

private double doubleValue;

private StoredFieldDataInput dataInput;

private BytesRef binaryValue;

private String stringValue;

##### Type
INTEGER,

LONG,

FLOAT

DOUBLE,

BINARY,

DATA_INPUT,

STRING;

# OpenSearch
## server
### Node
NodeService nodeService

Injector injector



### ActionModule


RestController restController

Map<String, ActionHandler<?, ?>> actions



actions.register(BulkAction.INSTANCE, TransportBulkAction.class, TransportShardBulkAction.class);



#### RestController
dispatchRequest(RestRequest request, RestChannel channel, RestHandler handler)



#### RestHandler


### NodeService
Settings settings

ThreadPool threadPool

TransportService transportService

IndicesService indicesService

ClusterService clusterService

HttpServerTransport httpServerTransport

SearchPipelineService searchPipelineService



### HttpServerTransport
AbstractHttpServerTransport

ReactorNetty4HttpServerTransport

Netty4HttpServerTransport



TransportAction

String actionName

#### HandledTransportAction
##### TransportSearchAction
SearchService searchService

##### SearchRequest
extends ActionRequest



##### SearchResponse
extends ActionResponse



##### TransportBulkAction
ThreadPool threadPool

TransportShardBulkAction shardBulkAction



##### TransportReplicationAction
IndicesService indicesService

##### TransportShardBulkAction




#### ReactorNetty4HttpServerTransport


### ThreadPool
Map<String, ExecutorHolder> executors

key: Names.SEARCH , value: core size: ((node.processors * 3) / 2) + 1



### ClusterService


### IndicesService
volatile Map<Integer, IndexShard> shards = emptyMap()

### SearchService
ThreadPool threadPool

### SearchPipelineService


### store


#### IndexShard
AtomicReference<Engine> currentEngineReference = new AtomicReference<>()



#### InternalEngine
IndexResult index(Index index)

#### Store




#### IndexService




## client




## data prepper




# ElasticSearch


# cassandra




# maven


Mojo

void execute() throws MojoExecutionException, MojoFailureException;

AbstractMojo



@Mojo



@Parameter

## maven-antrun-plugin


@Mojo(name = "run", threadSafe = true, requiresDependencyResolution = ResolutionScope.TEST)

AntRunMojo extends AbstractMojo

@Parameter(property = "maven.antrun.skip", defaultValue = "false")

private boolean skip;

@Parameter

private PlexusConfiguration target;



# debezium
## core
### org.apache.kafka.connect.transforms.Transformation
FieldToEmbedding



### org.apache.kafka.connect.source.SourceConnector
RelationalBaseSourceConnector

BinlogConnector<T extends BinlogConnectorConfig>

MySqlConnector extends BinlogConnector<MySqlConnectorConfig>



## DebeziumServer
### DebeziumEngine
AsyncEmbeddedEngine



### io.debezium.engine.source.EngineSourceTask
SourceTask connectTask



### io.debezium.embedded.async.RecordProcessor


# flinkcdc
## CliFrontend


### CliExecutor




### YamlPipelineDefinitionParser
PipelineDef parse(Path pipelineDefPath, Configuration globalPipelineConfig)



#### PipelineDef
SourceDef source

SinkDef sink

List<RouteDef> routes

List<TransformDef> transforms

List<UdfDef> udfs

List<ModelDef> models



### StreamExecutionEnvironment
#### LocalStreamEnvironment
#### RemoteStreamEnvironment


### FlinkPipelineComposer
PipelineExecution compose(PipelineDef pipelineDef)

translate(StreamExecutionEnvironment env, PipelineDef pipelineDef)



#### FlinkPipelineExecution
ExecutionInfo execute()

### DataSourceTranslator
createDataSource



#### DataSource
##### MySqlDataSource


#### Source
##### MySqlSource




### TransformTranslator


### PartitioningTranslator


### DataSinkTranslator
#### DataSink
##### DorisDataSink
# EventMesh
## Connector
extends ComponentLifeCycle

start()

stop()

init(Config config)

commit(ConnectRecord record)

### source
List<ConnectRecord> poll()

#### SourceConnector
SourceConfig sourceConfig

##### JdbcSourceConnector
CdcEngine cdcEngine

JdbcSourceConfig sourceConfig

EventDispatcher dispatcher

SourceJdbcTaskManager sourceJdbcTaskManager

SnapshotEngine<?> snapshotEngine

TaskManagerCoordinator taskManagerCoordinator

###### MysqlCdcEngine
BinaryLogClient client

BlockingQueue<Event> eventQueue = new LinkedBlockingQueue<>(10000)

EnumMap<EventType, Consumer<Event>> eventHandlers = new EnumMap<>(EventType.class)

Map<Long/* table number */, TableId> tableIdMap = new HashMap<>(64)

MysqlJdbcContext context

List<EventConsumer> consumers = new ArrayList<>(16)

MysqlJdbcConnection connection

MysqlAntlr4DdlParser ddlParser

com.github.shyiko.mysql.binlog.GtidSet localGtidSet

### sink
#### JdbcSinkConnector
JdbcSinkConfig sinkConfig

SessionFactory sessionFactory

SinkRecordHandler sinkRecordHandler

## server


### Application
run(Class<? extends Connector> clazz)



### ConnectorWorker
init()

start()

stop()

#### SourceWorker
Source source

SourceConfig config

int MAX_RETRY_TIMES = 3



BlockingQueue<ConnectRecord> queue

EventMeshTCPClient<CloudEvent> eventMeshTCPClient



OffsetStorageWriterImpl offsetStorageWriter

OffsetStorageReaderImpl offsetStorageReader

OffsetManagementService offsetManagementService

RecordOffsetManagement offsetManagement

volatile RecordOffsetManagement.CommittableOffsets committableOffsets

##### SourceConfig
PubSubConfig pubSubConfig

OffsetStorageConfig offsetStorageConfig

PollConfig pollConfig = new PollConfig()



##### EventMeshTCPClient
CloudEventTCPClient

CloudEventTCPPubClient cloudEventTCPPubClient

CloudEventTCPSubClient cloudEventTCPSubClient

CloudEventTCPPubClient

extends TcpClient implements EventMeshTCPPubClient<CloudEvent>

publish(ProtocolMessage event, long timeout)

TcpClient

Bootstrap bootstrap = new Bootstrap()

EventLoopGroup workers = new NioEventLoopGroup()

Channel channel



##### CloudEvent


#### SinkWorker
Sink sink

SinkConfig config

EventMeshTCPClient<CloudEvent> eventMeshTCPClient



### JdbcConnectorServer
new Application().run(JdbcSourceConnector.class)

new Application().run(JdbcSinkConnector.class)

## EventMeshStartup
### EventMeshServer
init()

start()

EventMeshTCPServer eventMeshTCPServer

EventMeshHTTPServer eventMeshHTTPServer

EventMeshGrpcServer eventMeshGrpcServer

EventMeshAdminServer eventMeshAdminServer

ProducerTopicManager producerTopicManager

ServiceState serviceState

MetaStorage metaStorage



### EventMeshBootstrap
#### EventMeshTcpBootstrap
EventMeshTCPServer eventMeshTcpServer

EventMeshTCPConfiguration eventMeshTcpConfiguration

EventMeshServer eventMeshServer

### MetaStorage
MetaService metaService

#### MetaService
NacosMetaService

ZookeeperMetaService



## RuntimeInstanceStarter
### RuntimeInstance
RegistryService registryService

Runtime runtime



### Runtime
#### ConnectorRuntime
Source sourceConnector

Sink sinkConnector

BlockingQueue<ConnectRecord> queue

### RegistryService
NacosDiscoveryService

# Zookeeper


## server
### boostrap
org.apache.zookeeper.server.quorum.QuorumPeerMain



#### Command
CommandBase

##### MonitorCommand




#### CommandResponse
String command

String error

Map<String, Object> data



#### State
INITIAL

RUNNING

SHUTDOWN

ERROR



Request

long sessionId

int cxid

int type

ByteBuffer request

ServerCnxn cnxn

TxnHeader hdr

Record txn

long zxid





#### ZooKeeperServer
RequestProcessor firstProcessor;

volatile State state

ZKDatabase zkDb

AtomicLong hzxid = new AtomicLong(0);



processPacket()



QuorumZooKeeperServer

QuorumPeer self

UpgradeableSessionTracker upgradeableSessionTracker

##### LeaderZooKeeperServer
CommitProcessor commitProcessor

SyncRequestProcessor syncProcessor



LeaderRequestProcessor ->

PrepRequestProcessor ->

ProposalRequestProcessor ->

LearnerSyncRequest -> org.apache.zookeeper.server.quorum.Leader#processSync

else ->

CommitProcessor ->

Leader.ToBeAppliedRequestProcessor ->

FinalRequestProcessor



SyncRequestProcessor

AckRequestProcessor



QuorumZooKeeperServer

LearnerZooKeeperServer

CommitProcessor commitProcessor

SyncRequestProcessor syncProcessor

##### FollowerZooKeeperServer
LinkedBlockingQueue<Request> submittedRequests = new LinkedBlockingQueue<Request>();

ConcurrentLinkedQueue<Request> pendingSyncs

LinkedBlockingQueue<Request> pendingTxns = new LinkedBlockingQueue<Request>();



FollowerRequestProcessor ->

CommitProcessor ->

FinalRequestProcessor



SyncRequestProcessor -> SendAckRequestProcessor



##### ObserverZooKeeperServer
private boolean syncRequestProcessorEnabled = this.self.getSyncEnabled();

ConcurrentLinkedQueue<Request> pendingSyncs = new ConcurrentLinkedQueue<Request>();



ObserverRequestProcessor ->

CommitProcessor ->

FinalRequestProcessor



SyncRequestProcessor -> null



Thread

ZooKeeperThread

ZooKeeperCriticalThread

##### RequestThrottler
private static volatile int maxRequests = Integer.getInteger("zookeeper.request_throttle_max_requests", 0);

private static volatile int stallTime = Integer.getInteger("zookeeper.request_throttle_stall_time", 100);



##### RequestProcessor


PrepRequestProcessor

RequestProcessor nextProcessor

LinkedBlockingQueue<Request> submittedRequests = new LinkedBlockingQueue<Request>();

pRequest2Txn()

SyncRequestProcessor



FinalRequestProcessor

ZooKeeperServer zks

RequestPathMetricsCollector requestPathMetricsCollector



### store
#### DataTree
createNode()

#### ZKDatabase
FileTxnSnapLog snapLog

DataTree dataTree



##### FileTxnSnapLog
File dataDir

File snapDir



#### Watcher
```plain
Disconnected(0),
SyncConnected(3),
AuthFailed(4),
ConnectedReadOnly(5),
SaslAuthenticated(6),
Expired(-112),
Closed(7);
```

```plain
None(-1),
NodeCreated(1),
NodeDeleted(2),
NodeDataChanged(3),
NodeChildrenChanged(4),
DataWatchRemoved(5),
ChildWatchRemoved(6),
PersistentWatchRemoved (7);
```

```plain
Children(1),
Data(2),
Any(3);
```



#### ServerCnxn implements Stats, Watcher
NIOServerCnxn

NettyServerCnxn

NIOServerCnxnFactory factory

SocketChannel sock

SelectorThread selectorThread

SelectionKey sk

receiveMessage()

### metrics
ServerMetrics



DefaultMetricsProvider

MonitorCommand extends CommandBase

response::put

MonitorCommand extends AbstractFourLetterCommand

this::print



## client
org.apache.zookeeper.ZooKeeperMain



### ZooKeeper
ClientCnxn cnxn

HostProvider hostProvider

ZKClientConfig clientConfig



create

delete

...



#### HostProvider
StaticHostProvider



#### ClientCnxn
Queue<Packet> pendingQueue = new ArrayDeque<>()

LinkedBlockingDeque<Packet> outgoingQueue = new LinkedBlockingDeque<Packet>()



int connectTimeout

volatile int negotiatedSessionTimeout

int readTimeout

final int sessionTimeout

long requestTimeout

long sessionId

ZKClientConfig clientConfig



final ZKWatchManager watchManager



SendThread sendThread

EventThread eventThread



#### Packet
        RequestHeader requestHeader;

        ReplyHeader replyHeader;

        Record request;

        Record response;

        ByteBuffer bb;

        /** Client's view of the path (may differ due to chroot) **/

        String clientPath;

        /** Servers's view of the path (may differ due to chroot) **/

        String serverPath;

        boolean finished;

        AsyncCallback cb;

        Object ctx;

        WatchRegistration watchRegistration;

        public boolean readOnly;

        WatchDeregistration watchDeregistration;



packet.wait() -> outgoingQueue -> pendingQueue -> packet.notifyAll()



##### RequestHeader
int xid

int type



ZooDefs

OpCode

```plain
public interface OpCode {

    int notification = 0;

    int create = 1;

    int delete = 2;

    int exists = 3;

    int getData = 4;

    int setData = 5;

    int getACL = 6;

    int setACL = 7;

    int getChildren = 8;

    int sync = 9;

    int ping = 11;

    int getChildren2 = 12;

    int check = 13;

    int multi = 14;

    int create2 = 15;

    int reconfig = 16;

    int checkWatches = 17;

    int removeWatches = 18;

    int createContainer = 19;

    int deleteContainer = 20;

    int createTTL = 21;

    int multiRead = 22;

    int auth = 100;

    int setWatches = 101;

    int sasl = 102;

    int getEphemerals = 103;

    int getAllChildrenNumber = 104;

    int setWatches2 = 105;

    int addWatch = 106;

    int whoAmI = 107;

    int createSession = -10;

    int closeSession = -11;

    int error = -1;

}
```

Perms

```plain
public interface Perms {

    int READ = 1 << 0;

    int WRITE = 1 << 1;

    int CREATE = 1 << 2;

    int DELETE = 1 << 3;

    int ADMIN = 1 << 4;

    int ALL = READ | WRITE | CREATE | DELETE | ADMIN;

}
```

Ids

```plain
public interface Ids {

    /**
     * This Id represents anyone.
     */
    Id ANYONE_ID_UNSAFE = new Id("world", "anyone");

    /**
     * This Id is only usable to set ACLs. It will get substituted with the
     * Id's the client authenticated with.
     */
    Id AUTH_IDS = new Id("auth", "");

    /**
     * This is a completely open ACL .
     */
    @SuppressFBWarnings(value = "MS_MUTABLE_COLLECTION", justification = "Cannot break API")
    ArrayList<ACL> OPEN_ACL_UNSAFE = new ArrayList<ACL>(Collections.singletonList(new ACL(Perms.ALL, ANYONE_ID_UNSAFE)));

    /**
     * This ACL gives the creators authentication id's all permissions.
     */
    @SuppressFBWarnings(value = "MS_MUTABLE_COLLECTION", justification = "Cannot break API")
    ArrayList<ACL> CREATOR_ALL_ACL = new ArrayList<ACL>(Collections.singletonList(new ACL(Perms.ALL, AUTH_IDS)));

    /**
     * This ACL gives the world the ability to read.
     */
    @SuppressFBWarnings(value = "MS_MUTABLE_COLLECTION", justification = "Cannot break API")
    ArrayList<ACL> READ_ACL_UNSAFE = new ArrayList<ACL>(Collections.singletonList(new ACL(Perms.READ, ANYONE_ID_UNSAFE)));

}
```





#### ClientCnxnSocket
doTransport()

##### ClientCnxnSocketNIO
Selector selector = Selector.open()

SelectionKey sockKey

SocketAddress localSocketAddress

SocketAddress remoteSocketAddress

##### ClientCnxnSocketNetty
private final EventLoopGroup eventLoopGroup;

private Channel channel;

private CountDownLatch firstConnect;

private ChannelFuture connectFuture;

private final Lock connectLock = new ReentrantLock();

private final AtomicBoolean disconnected = new AtomicBoolean();

private final AtomicBoolean needSasl = new AtomicBoolean();

private final Semaphore waitSasl = new Semaphore(0);



<font style="background-color:#FFFFFF;"></font>



