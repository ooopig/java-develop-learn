## 2020届秋招面试题总结——Spring篇

**1、讲讲Spring的加载流程。**

这个有点长，，，建议大体读一遍spring源码。可以参考这篇文章：[Spring初始化加载流程分析](https://blog.csdn.net/u011043551/article/details/79675363)

**BeanFactory和ApplicationContext有什么区别？

BeanFactory和ApplicationContext是Spring的两大核心接口，都可以当做Spring的容器。其中ApplicationContext是BeanFactory的子接口。

（1）BeanFactory：是Spring里面最底层的接口，包含了各种Bean的定义，读取bean配置文档，管理bean的加载、实例化，控制bean的生命周期，维护bean之间的依赖关系。ApplicationContext接口作为BeanFactory的派生，除了提供BeanFactory所具有的功能外，还提供了更完整的框架功能：

①继承MessageSource，因此支持国际化。

②统一的资源文件访问方式。

③提供在监听器中注册bean的事件。

④同时加载多个配置文件。

⑤载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层。

（2）①BeanFactroy采用的是延迟加载形式来注入Bean的，即只有在使用到某个Bean时(调用getBean())，才对该Bean进行加载实例化。这样，我们就不能发现一些存在的Spring的配置问题。如果Bean的某一个属性没有注入，BeanFacotry加载后，直至第一次使用调用getBean方法才会抛出异常。

        ②ApplicationContext，它是在容器启动时，一次性创建了所有的Bean。这样，在容器启动时，我们就可以发现Spring中存在的配置错误，这样有利于检查所依赖属性是否注入。 ApplicationContext启动后预载入所有的单实例Bean，通过预载入单实例bean ,确保当你需要的时候，你就不用等待，因为它们已经创建好了。

        ③相对于基本的BeanFactory，ApplicationContext 唯一的不足是占用内存空间。当应用程序配置Bean较多时，程序启动较慢。

（3）BeanFactory通常以编程的方式被创建，ApplicationContext还能以声明的方式创建，如使用ContextLoader。

（4）BeanFactory和ApplicationContext都支持BeanPostProcessor、BeanFactoryPostProcessor的使用，但两者之间的区别是：BeanFactory需要手动注册，而ApplicationContext则是自动注册。

**Spring IOC的实现原理

（1）IOC，控制反转，是指创建对象的控制权的转移，以前创建对象的主动权和时机是由自己把控的，而现在这种权力转移到Spring容器中，并由容器根据配置文件去创建实例和管理各个实例之间的依赖关系，对象与对象之间松散耦合，也利于功能的复用。
	 DI，依赖注入，和控制反转是同一个概念的不同角度的描述，即应用程序在运行时依赖IOC容器来动态注入对象需要的外部资源。

（2）最直观的表达就是，IOC让对象的创建不用去new了，可以由spring自动生产，使用java的反射机制，根据配置文件在运行时动态的去创建对象以及管理对象，并调用对象的方法的。

（3）Spring的IOC有三种注入方式：构造器注入、setter方法注入、根据注解注入。

**Spring bean的循环依赖

循环依赖其实就是循环引用，也就是两个或则两个以上的bean互相持有对方，最终形成闭环。比如A依赖于B，B依赖于C，C又依赖于A。

Spring中循环依赖场景有：
（1）构造器的循环依赖
（2）field属性的循环依赖。

检测循环依赖相对比较容易，Bean在创建的时候可以给该Bean打标，如果递归调用回来发现正在创建中的话，即说明了循环依赖了。

**Spring单例对象的初始化

（1）createBeanInstance：实例化，其实也就是调用对象的构造方法实例化对象

（2）populateBean：填充属性，这一步主要是多bean的依赖属性进行填充

（3）initializeBean：调用spring xml中的init 方法。

**2、Spring AOP的实现原理。**

AOP（面向切面）是一种编程范式，提供从另一个角度来考虑程序结构以完善面向对象编程（OOP）。
AOP为开发者提供了一种描述横切关注点的机制，并能够自动将横切关注点织入到面向对象的软件系统中，从而实现了横切关注点的模块化。
AOP能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任，例如事务处理、日志管理、权限控制等，封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护性。

Spring AOP的动态代理主要有两种方式实现，JDK动态代理和cglib动态代理。JDK动态代理通过反射来接收被代理的类，但是被代理的类必须实现接口，核心是InvocationHandler和Proxy类。
cglib动态代理的类一般是没有实现接口的类，cglib是一个代码生成的类库，可以在运行时动态生成某个类的子类，所以，CGLIB是通过继承的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的。

使用AOP的好处
	降低模块的耦合度
	使系统容易扩展
	提高代码复用性

**4、Spring如何管理事务的，怎么配置事务。**

Spring事务的本质其实就是数据库对事务的支持，没有数据库的事务支持，spring是无法提供事务功能的。真正的数据库层的事务提交和回滚是通过binlog或者redo log实现的。

（1）Spring事务的种类：

spring支持编程式事务管理和声明式事务管理两种方式：

①编程式事务管理使用TransactionTemplate。

②声明式事务管理建立在AOP之上的。其本质是通过AOP功能，对方法前后进行拦截，将事务处理的功能编织到拦截的方法中，也就是在目标方法开始之前加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。

声明式事务最大的优点就是不需要在业务逻辑代码中掺杂事务管理的代码，只需在配置文件中做相关的事务规则声明或通过@Transactional注解的方式，便可以将事务规则应用到业务逻辑中。

声明式事务管理要优于编程式事务管理，这正是spring倡导的非侵入式的开发方式，使业务代码不受污染，只要加上注解就可以获得完全的事务支持。唯一不足地方是，最细粒度只能作用到方法级别，无法做到像编程式事务那样可以作用到代码块级别。

（2）spring的事务传播行为：

spring事务的传播行为说的是，当多个事务同时存在的时候，spring如何处理这些事务的行为。

① PROPAGATION_REQUIRED：如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，该设置是最常用的设置。

② PROPAGATION_SUPPORTS：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。‘

③ PROPAGATION_MANDATORY：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。

④ PROPAGATION_REQUIRES_NEW：创建新事务，无论当前存不存在事务，都创建新事务。

⑤ PROPAGATION_NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。

⑥ PROPAGATION_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。

⑦ PROPAGATION_NESTED：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行。

（3）Spring中的隔离级别：

① ISOLATION_DEFAULT：这是个 PlatfromTransactionManager 默认的隔离级别，使用数据库默认的事务隔离级别。

② ISOLATION_READ_UNCOMMITTED：读未提交，允许另外一个事务可以看到这个事务未提交的数据。

③ ISOLATION_READ_COMMITTED：读已提交，保证一个事务修改的数据提交后才能被另一事务读取，而且能看到该事务对已有记录的更新。

④ ISOLATION_REPEATABLE_READ：可重复读，保证一个事务修改的数据提交后才能被另一事务读取，但是不能看到该事务对已有记录的更新。

⑤ ISOLATION_SERIALIZABLE：一个事务在执行的过程中完全看不到其他事务对数据库所做的更新。

**5、说说你对Spring的理解，非单例注入的原理？它的生命周期？循环注入的原理，aop的实现原理，说说aop中的几个术语，它们是怎么相互工作的。**

在bean的scope属性中，当值是singleton表示单例,当值是prototype表示多例。

单例：多次用factory.getBean("user",user.class)获取实体类，获得是同一类。

多例：多次用factory.getBean("user",user.class)获取实体类，获得是多个类。

**9、Spring中用到的设计模式。**

（1）工厂模式：BeanFactory就是简单工厂模式的体现，用来创建对象的实例；

（2）单例模式：Bean默认为单例模式。

（3）代理模式：Spring的AOP功能用到了JDK的动态代理和CGLIB字节码生成技术；

（4）模板方法：用来解决代码重复的问题。比如. RestTemplate, JmsTemplate, JpaTemplate。

（5）观察者模式：定义对象键一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都会得到通知被制动更新，如Spring中listener的实现--ApplicationListener。
**10、一个HTTP请求就是一个线程吗。**

一个HTTP请求就是一个线程。tomcat会维护一个线程池，每一个http请求，会从线程池中取出一个空闲线程。

在Tomcat中server.xml中连接器设置如下：

```xml
<Connector port="8080"  
                   maxThreads="150" minSpareThreads="25" maxSpareThreads="75"  
                   enableLookups="false" redirectPort="8443" acceptCount="100"  
                   debug="0" connectionTimeout="20000"   
                   disableUploadTimeout="true" />  
```

相关参数表示为：

- minProcessors：最小空闲连接线程数，用于提高系统处理性能，默认值为10，服务器启动时创建的处理请求的线程数。
- maxProcessors：最大连接线程数，即：并发处理的最大请求数，默认值为75。
- acceptCount：指定当所有可以使用的处理请求的线程数都被使用时，可以放到处理队列中的请求数，超过这个数的请求将不予处理。
- connectionTimeout：一个请求最多等待时间，超过则报错。举个例子，现在这个值设置为20000，则代表请求建立一个socket连接后，如果一直没有收到客户端的FIN，也没有数据过来，那么此连接也必须等到20s后，才能被超时释放。

这三个值的具体使用场景如下：

- 情况1：Tomcat启动时，会按照minProcessors数目创建线程。
- 情况2：接受一个请求，此时tomcat启动的线程数目没有到达maxThreads，tomcat会启动一个线程来处理此请求。
- 情况3：接受一个请求，此时tomcat启动的线程数已经到达maxThreads，tomcat会把此请求放入等待队列中，等待空闲线程。
- 情况4：接受一个请求，此时tomcat启动的线程数已经到达maxThreads，等待队列中的请求个数也达到了acceptCount，此时tomcat会直接拒绝此次请求，返回connect refused。

参考链接：[一个http请求就是一个线程吗，java的服务是每收到一个请求就新开一个线程来处理吗](https://blog.csdn.net/weixin_39833509/article/details/88603957)

**12、Mybaits框架的优缺点。**

优点如下：

- 与JDBC相比，减少了50%以上的代码量。
- MyBatis是最简单的持久化框架，小巧并且简单易学。
- MyBatis灵活，不会对应用程序或者数据库的现有设计强加任何影响，SQL写在XML里，从程序代码中彻底分离，降低了耦合度，便于统一管理和优化，可以重用。
- 提供XML标签，支持编写动态SQL语句（XML中使用if，else）。
- 提供映射标签，支持对象与数据库的ORM字段关系映射（在XML中配置映射关系，也可以使用注解）。

缺点如下：

- SQL语句的编写工作量大，尤其是字段多、关联表多时，更是如此，对开发人员编写SQL语句的功底有一定要求。
- SQL语句依赖数据库，导致数据库移植性差，不能随意更换数据库。

**13、Spring中Bean的生命周期。**

（1）实例化Bean：

对于BeanFactory容器，当客户向容器请求一个尚未初始化的bean时，或初始化bean的时候需要注入另一个尚未初始化的依赖时，容器就会调用createBean进行实例化。对于ApplicationContext容器，当容器启动结束后，通过获取BeanDefinition对象中的信息，实例化所有的bean。

（2）设置对象属性（依赖注入）：

实例化后的对象被封装在BeanWrapper对象中，紧接着，Spring根据BeanDefinition中的信息 以及 通过BeanWrapper提供的设置属性的接口完成依赖注入。

（3）处理Aware接口：

接着，Spring会检测该对象是否实现了xxxAware接口，并将相关的xxxAware实例注入给Bean：

①如果这个Bean已经实现了BeanNameAware接口，会调用它实现的setBeanName(String beanId)方法，此处传递的就是Spring配置文件中Bean的id值；

②如果这个Bean已经实现了BeanFactoryAware接口，会调用它实现的setBeanFactory()方法，传递的是Spring工厂自身。

③如果这个Bean已经实现了ApplicationContextAware接口，会调用setApplicationContext(ApplicationContext)方法，传入Spring上下文；

（4）BeanPostProcessor：

如果想对Bean进行一些自定义的处理，那么可以让Bean实现了BeanPostProcessor接口，那将会调用postProcessBeforeInitialization(Object obj, String s)方法。

（5）InitializingBean 与 init-method：

如果Bean在Spring配置文件中配置了 init-method 属性，则会自动调用其配置的初始化方法。

（6）如果这个Bean实现了BeanPostProcessor接口，将会调用postProcessAfterInitialization(Object obj, String s)方法；由于这个方法是在Bean初始化结束时调用的，所以可以被应用于内存或缓存技术；

以上几个步骤完成后，Bean就已经被正确创建了，之后就可以使用这个Bean了。

（7）DisposableBean：

当Bean不再需要时，会经过清理阶段，如果Bean实现了DisposableBean这个接口，会调用其实现的destroy()方法；

（8）destroy-method：

最后，如果这个Bean的Spring配置中配置了destroy-method属性，会自动调用其配置的销毁方法。

**14、Spring中事务失效的几种原因。**

Spring中通过注解@Transactional来实现事务，但在以下几种情况时，事务会失效。

- Spring中事务自调用会失效，如果A方法调用B方法，B方法上有事务注解，AB方法在同一个类中，那么B方法的事务就会失效，这是动态代理的原因。
- Spring的事务注解@Transactional只能作用在public修饰的方法上才起作用，如果放在非public（如private、protected）方法上，虽然不报错，但是事务不起作用。
- 如果MySQL用的引擎是MyISAM，则事务会不起作用，原因是MyISAM不支持事务，可以改成InnoDB引擎。
- Spring建议在具体的类（或类的方法）上使用@Transactional注解，而不要使用在类所实现的任何接口上。在接口上使用@Transactional注解，只能当你设置了基于接口的代理时他才会生效，因为注解是不能继承的，这就意味着如果正在使用基于类的代理时，那么事务的设置将不能被基于类的代理所识别，而且对象也将不会被事务代理所包装。
- 如果在业务代码中抛出RuntimeException异常，事务回滚；但是抛出Exception，事务不回滚。

需要注意的是，@Transactional也可以作用于类上，放在类级别上等同于该类的每个公有方法都放上了@Transactional。

**15、注解继承问题。**

对于使用元注解@Inherited修饰的自定义注解，作用在父类上的自定义注解可以被继承下来。作用在接口上自定义注解不能被实现它的类继承下来。

需要注意的是：使用Inherited声明出来的注解，只有在类上使用时才会有效，对方法，属性等其他无效，并且也对于接口无效。简单一点来说吧，就是写在父类的类上的注解可以被继承，卸载父类方法上的注解无法被继承！

所以一般来说，我们应当在实现方法上打注解。

回到上一题说的事务注解@Transactional，它也有@Inherited修饰，所以对它来说，如果父类级别使用@Transactional，那子类会继承；如果父类方法级别使用@Transactional，那么子类方法不会继承；如果接口实现@Transactional，子类不会继承。

**16、MyBatis的分页。**

MyBatis实现分页操作时，有逻辑分页和物理分页这两个区别。

- 逻辑分页：将数据一次性从数据库查出到内存中，在内存中进行逻辑上的分页。在实际应用方面，Mybaits自带分页RowBounds可以实现逻辑分页。
- 物理分页：直接特定的SQL语句，只从数据库中查询出需要的数据。在实际应用方面，Mybatis可以通过自写SQL或者通过分页插件PageHelper来实现物理分页。

逻辑分页内存开销比较大，物理分页内存开销比较小。在数据量比较小的情况下，逻辑分页效率比物理分页高；在数据量很大的情况下，建议使用物理分页，因为物理内存开销太大，容易造成内存溢出。

我习惯在项目中使用PageHelper来实现分页。

弥有，2019年9月