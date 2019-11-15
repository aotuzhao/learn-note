```xml

<dependency>    
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-batch</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.batch</groupId>
    <artifactId>spring-batch-test</artifactId>
    <scope>test</scope>
</dependency>

<!--        <dependency>-->
<!--            <groupId>com.h2database</groupId>-->
<!--            <artifactId>h2</artifactId>-->
<!--            <scope>runtime</scope>-->
<!--        </dependency>-->
```

 ![一文轻松搞定批处理框架 Spring Batch](http://image20.it168.com/201807_670x502/3220/e34434ec13efdbe0.jpg)



Job Launcher（作业调度器）：是Spring Batch框架基础设施层提供的运行Job的能力。通过给定的Job名称和作Job Parameters，可以通过Job Launcher执行Job。

通过Job Launcher可以在Java程序中调用批处理任务，也可以在通过命令行或者其它框架（如定时调度框架Quartz）中调用批处理任务。

Job Repository来存储Job执行期的元数据（这里的元数据是指Job Instance、Job Execution、Job Parameters、Step Execution、Execution Context等数据），并提供两种默认实现。

**一种是存放在内存（H2）中；另一种将元数据存放在数据库中**。通过将元数据存放在数据库中，可以随时监控批处理Job的执行状态。Job执行结果是成功还是失败，并且使得在Job失败的情况下重新启动Job成为可能。Step表示作业中的一个完整步骤，一个Job可以有一个或者多个Step组成。

![image-20191029163924413](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191029163924413.png)

>  BATCH_JOB_INSTANCE：作业实例表，用于存放Job的实例信息
>
> BATCH_JOB_EXECUTION_PARAMS：作业参数表，用于存放每个Job执行时候的参数信息，该参数实际对应Job实例的。
>
> BATCH_JOB_EXECUTION：作业执行器表，用于存放当前作业的执行信息，比如创建时间，执行开始时间，执行结束时间，执行的那个Job实例，执行状态等。
>
> BATCH_JOB_EXECUTION_CONTEXT：作业执行上下文表，用于存放作业执行器上下文的信息。
>
> BATCH_STEP_EXECUTION：作业步执行器表，用于存放每个Step执行器的信息，比如作业步开始执行时间，执行完成时间，执行状态，读写次数，跳过次数等信息。
>
> BATCH_STEP_EXECUTION_CONTEXT：作业步执行上下文表，用于存放每个作业步上下文的信息。



 ![一文轻松搞定批处理框架 Spring Batch](http://image20.it168.com/201807_0x0/3220/c7acb9c4f49c11e5.jpg) 



 ![img](https://ss1.baidu.com/6ONXsjip0QIZ8tyhnq/it/u=1367040793,1779482407&fm=173&s=48A21D7213534C615AC540DA0000C0B1&w=640&h=309&img.JPEG) 

Job Instance（作业实例）是一个运行期的概念，Job每执行一次都会涉及到一个Job Instance。

Job Instance来源可能有两种：一种是根据设置的Job Parameters从Job Repository（作业仓库）中获取一个；如果根据Job Parameters从Job Repository没有获取Job Instance，则新创建一个新的Job Instance。

Job Execution表示Job执行的句柄，一次Job的执行可能成功也可能失败。只有Job执行成功后，对应的Job Instance才会被完成。因此在Job执行失败的情况下，会有一个Job Instance对应多个Job Execution的场景发生。

总结下批处理的典型概念模型，其设计非常精简的十个概念，完整支撑了整个框架。

 ![img](https://ss2.baidu.com/6ONYsjip0QIZ8tyhnq/it/u=4267360415,323533652&fm=173&s=E13787741E784C011E7D05DA0300C0BA&w=640&h=278&img.JPEG) 





Job提供的核心能力包括作业的抽象与继承，类似面向对象中的概念。对于执行异常的作业，提供重启的能力。

![img](https://ss1.baidu.com/6ONXsjip0QIZ8tyhnq/it/u=3737037184,1788818169&fm=173&s=05A27532837248291E789CCA0000D0B1&w=640&h=297&img.JPEG)

框架在Job层面，同样提供了作业编排的概念，包括顺序、条件、并行作业编排。

在一个Job中配置多个Step。不同的Step间可以顺序执行，也可以按照不同的条件有选择的执行（条件通常使用Step的退出状态决定），通过next元素或者decision元素来定义跳转规则；

为了提高多个Step的执行效率，框架提供了Step并行执行的能力（使用split进行声明，通常该情况下需要Step之间没有任何的依赖关系，否则容易引起业务上的错误）。Step包含了一个实际运行的批处理任务中的所有必需的信息，其实现可以是非常简单的业务实现，也可以是非常复杂的业务处理，Step的复杂程度通常是业务决定的。

![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=3781305471,1592223826&fm=173&s=05A255320B40654B16C5D4CA0000C0B1&w=640&h=330&img.JPEG)

每个Step由ItemReader、ItemProcessor、ItemWriter组成，当然根据不同的业务需求，ItemProcessor可以做适当的精简。同时框架提供了大量的ItemReader、ItemWriter的实现，提供了对FlatFile、XML、Json、DataBase、Message等多种数据类型的支持。

框架还为Step提供了重启、事务、重启次数、并发数；以及提交间隔、异常跳过、重试、完成策略等能力。基于Step的灵活配置，可以完成常见的业务功能需求。其中三步走（Read、Processor、Writer）是批处理中的经典抽象。

 ![img](https://ss1.baidu.com/6ONXsjip0QIZ8tyhnq/it/u=2800785985,1029768912&fm=173&s=20827D330F09414B58DD21DE000050B0&w=640&h=349&img.JPEG) 

 在Chunk的操作中，可以通过属性commit-interval设置read多少条记录后进行一次提交。通过设置commit-interval的间隔值，减少提交频次，降低资源使用率。Step的每一次提交作为一个完整的事务存在。默认采用Spring提供的声明式事务管理模式，事务编排非常方便。 

框架对于事务的支持能力包括：

Chunk支持事务管理，通过commit-interval设置每次提交的记录数；

支持对每个Tasklet设置细粒度的事务配置：隔离界别、传播行为、超时；

支持rollback和no rollback，通过skippable-exception-classes和no-rollback-exception-classes进行支撑；

支持JMS Queue的事务级别配置；





#####  实现作业的健壮性与扩展性 

批处理要求Job必须有较强的健壮性，通常Job是批量处理数据、无人值守的，这要求在Job执行期间能够应对各种发生的异常、错误，并对Job执行进行有效的跟踪。

一个健壮的Job通常需要具备如下的几个特性：

\1. 容错性

在Job执行期间非致命的异常，Job执行框架应能够进行有效的容错处理，而不是让整个Job执行失败；通常只有致命的、导致业务不正确的异常才可以终止Job的执行。

\2. 可追踪性

Job执行期间任何发生错误的地方都需要进行有效的记录，方便后期对错误点进行有效的处理。例如在Job执行期间任何被忽略处理的记录行需要被有效的记录下来，应用程序维护人员可以针对被忽略的记录后续做有效的处理。

\3. 可重启性

Job执行期间如果因为异常导致失败，应该能够在失败的点重新启动Job；而不是从头开始重新执行Job。

![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=2413416561,1254693232&fm=173&s=C48A543897587DC84C7D40D40100C0B2&w=640&h=290&img.JPEG)

框架提供了支持上面所有能力的特性，包括Skip（跳过记录处理）、Retry（重试给定的操作）、Restart（从错误点开始重新启动失败的Job）：

Skip，在对数据处理期间，如果数据的某几条的格式不能满足要求，可以通过Skip跳过该行记录的处理，让Processor能够顺利的处理其余的记录行。

Retry，将给定的操作进行多次重试，在某些情况下操作因为短暂的异常导致执行失败，如网络连接异常、并发处理异常等，可以通过重试的方式避免单次的失败，下次执行操作时候网络恢复正常，不再有并发的异常，这样通过重试的能力可以有效的避免这类短暂的异常。

Restart，在Job执行失败后，可以通过重启功能来继续完成Job的执行。在重启时候，批处理框架允许在上次执行失败的点重新启动Job，而不是从头开始执行，这样可以大幅提高Job执行的效率。

对于扩展性，框架提供的扩展能力包括如下的四种模式 :

Multithreaded Step 多线程执行一个Step;

Parallel Step 通过多线程并行执行多个Step;

Remote Chunking 在远端节点上执行分布式Chunk操作;

Partitioning Step 对数据进行分区，并分开执行;

我们先来看第一种的实现Multithreaded Step：

![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=2578739736,1574940474&fm=173&s=8171CF301B9B70434EFD45DE000080B0&w=640&h=313&img.JPEG)

批处理框架在Job执行时默认使用单个线程完成任务的执行，同时框架提供了线程池的支持（Multithreaded Step模式），可以在Step执行时候进行并行处理，这里的并行是指同一个Step使用线程池进行执行，同一个Step被并行的执行。使用tasklet的属性task-executor可以非常容易的将普通的Step变成多线程Step。

Multithreaded Step的实现示例：

![img](https://ss1.baidu.com/6ONXsjip0QIZ8tyhnq/it/u=3307246360,1766705834&fm=173&s=4E52CC1A93E879095C6994DB0300D0B0&w=640&h=241&img.JPEG)

需要注意的是Spring Batch框架提供的大部分的ItemReader、ItemWriter等操作都是线程不安全的。

可以通过扩展的方式显现线程安全的Step。

下面为大家展示一个扩展的实现：

![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=2984997255,1075547816&fm=173&s=0D2A5D321B5B7049425504DA0000A0B2&w=640&h=311&img.JPEG)

需求：针对数据表的批量处理，实现线程安全的Step，并且支持重启能力，即在执行失败点可以记录批处理的状态。

对于示例中的数据库读取组件JdbcCursorItemReader，在设计数据库表时，在表中增加一个字段Flag，用于标识当前的记录是否已经读取并处理成功，如果处理成功则标识Flag=true，等下次重新读取的时候，对于已经成功读取且处理成功的记录直接跳过处理。

Multithreaded Step（多线程步）提供了多个线程执行一个Step的能力，但这种场景在实际的业务中使用的并不是非常多。

更多的业务场景是Job中不同的Step没有明确的先后顺序，可以 在执行期并行的执行。

Parallel Step：提供单个节点横向扩展的能力

![img](https://ss2.baidu.com/6ONYsjip0QIZ8tyhnq/it/u=2319499727,2579841956&fm=173&s=4822157203D24C690EDD94DE0000C0B2&w=640&h=330&img.JPEG)

使用场景：Step A、Step B两个作业步由不同的线程执行，两者均执行完毕后，Step C才会被执行。

框架提供了并行Step的能力。可以通过Split元素来定义并行的作业流，并制定使用的线程池。

Parallel Step模式的执行效果如下：

![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=2142439830,4262523868&fm=173&s=A370CD320B83484B166D50DE000080B0&w=640&h=327&img.JPEG)

每个作业步并行处理不同的记录，示例中三个作业步，处理同一张表中的不同数据。

并行Step提供了在一个节点上横向处理，但随着作业处理量的增加，有可能一台节点无法满足Job的处理，此时我们可以采用远程Step的方式将多个机器节点组合起来完成一个Job的处理。

Remote Chunking：远程Step技术本质上是将对Item读、写的处理逻辑进行分离；通常情况下读的逻辑放在一个节点进行操作，将写操作分发到另外的节点执行。

![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=3131862120,2985190729&fm=173&s=85B2453287F27C2B5ADDD1CB0000C0B2&w=640&h=268&img.JPEG)

远程分块是一个把step进行技术分割的工作，不需要对处理数据的结构有明确了解。

任何输入源能够使用单进程读取并在动态分割后作为"块"发送给远程的工作进程。

远程进程实现了监听者模式，反馈请求、处理数据最终将处理结果异步返回。请求和返回之间的传输会被确保在发送者和单个消费者之间。

在Master节点，作业步负责读取数据，并将读取的数据通过远程技术发送到指定的远端节点上，进行处理，处理完毕后Master负责回收Remote端执行的情况。

在Spring Batch框架中通过两个核心的接口来完成远程Step的任务，分别是ChunkProvider与ChunkProcessor。

ChunkProvider：根据给定的ItemReader操作产生批量的Chunk操作；

ChunkProcessor：负责获取ChunkProvider产生的Chunk操作，执行具体的写逻辑；

Spring Batch中对远程Step没有默认的实现，但我们可以借助SI或者AMQP实现来实现远程通讯能力。

基于SI实现Remote Chunking模式的示例：

![img](https://ss1.baidu.com/6ONXsjip0QIZ8tyhnq/it/u=3738530905,2213508743&fm=173&s=082355321B0F44491AF5C5CE000080B1&w=640&h=375&img.JPEG)

Step本地节点负责读取数据，并通过MessagingGateway将请求发送到远程Step上；远程Step提供了队列的监听器，当请求队列中有消息时候获取请求信息并交给ChunkHander负责处理。

接下来我们看下最后一种分区模式；Partitioning Step：分区模式需要对数据的结构有一定的了解，如主键的范围、待处理的文件的名字等。

![img](https://ss1.baidu.com/6ONXsjip0QIZ8tyhnq/it/u=3179086033,990812125&fm=173&s=05B245329BBB60090CDD98C60000C0B2&w=640&h=294&img.JPEG)

这种模式的优点在于分区中每一个元素的处理器都能够像一个普通Spring Batch任务的单步一样运行，也不必去实现任何特殊的或是新的模式，来让他们能够更容易配置与测试。

通过分区可以实现以下的优点：

分区实现了更细粒度的扩展；

基于分区可以实现高性能的数据切分；

分区比远程通常具有更高的扩展性；

分区后的处理逻辑，支持本地与远程两种模式；

分区作业典型的可以分成两个处理阶段，数据分区、分区处理；

数据分区：根据特殊的规则（例如：根据文件名称，数据的唯一性标识，或者哈希算法）将数据进行合理的数据切片，为不同的切片生成数据执行上下文Execution Context、作业步执行器Step Execution。可以通过接口Partitioner生成自定义的分区逻辑，Spring Batch批处理框架默认实现了对多文件的实现org.springframework.batch.core.partition.support.MultiResourcePartitioner；也可以自行扩展接口Partitioner来实现自定义的分区逻辑。

分区处理：通过数据分区后，不同的数据已经被分配到不同的作业步执行器中，接下来需要交给分区处理器进行作业，分区处理器可以本地执行也可以远程执行被划分的作业。接口PartitionHandler定义了分区处理的逻辑，Spring Batch批处理框架默认实现了本地多线程的分区处理org.springframework.batch.core.partition.support.TaskExecutorPartitionHandler；也可以自行扩展接口PartitionHandler来实现自定义的分区处理逻辑。

![img](https://ss1.baidu.com/6ONXsjip0QIZ8tyhnq/it/u=673682347,588571550&fm=173&s=A370EB2296C5910BB91CA1DA01008093&w=640&h=272&img.JPEG)

Spring Batch框架提供了对文件分区的支持，实现类org.springframework.batch.core.partition.support.MultiResourcePartitioner提供了对文件分区的默认支持，根据文件名将不同的文件处理进行分区，提升处理的速度和效率，适合有大量小文件需要处理的场景。

![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=119503380,2637945214&fm=173&s=4E50C51A019E45C818E511D00200C0B2&w=640&h=418&img.JPEG)

示例展示了将不同文件分配到不同的作业步中，使用MultiResourcePartitioner进行分区，意味着每个文件会被分配到一个不同的分区中。如果有其它的分区规则，可以通过实现接口Partitioner来进行自定义的扩展。有兴趣的TX，可以自己实现基于数据库的分区能力哦。

总结一下，批处理框架在扩展性上提供了4中不同能力，每种都是各自的使用场景，我们可以根据实际的业务需要进行选择。

![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=4087975617,2496974715&fm=173&s=C49AE43899D874C80CDD81DE010080B2&w=640&h=337&img.JPEG)

批处理框架的不足与增强

Spring Batch批处理框架虽然提供了4种不同的监控方式，但从目前的使用情况来看，都不是非常的友好。

![img](https://ss2.baidu.com/6ONYsjip0QIZ8tyhnq/it/u=2273080067,2516960486&fm=173&s=89A25D328F244C030E7C90CE0000E0B1&w=640&h=245&img.JPEG)

通过DB直接查看，对于管理人员来讲，真的不忍直视；

通过API实现自定义的查询，这是程序员的天堂，确实运维人员的地狱；

提供了Web控制台，进行Job的监控和操作，目前提供的功能太裸露，无法直接用于生产；

提供JMX查询方式，对于非开发人员太不友好；

但在企业级应用中面对批量数据处理，仅仅提供批处理框架仅能满足批处理作业的快速开发、执行能力。

企业需要统一的批处理平台来处理复杂的企业批处理应用，批处理平台需要解决作业的统一调度、批处理作业的集中管理和管控、批处理作业的统一监控等能力。

那完美的解决方案是什么呢？

企业级批处理平台需要在Spring Batch批处理框架的基础上，集成调度框架，通过调度框架可以将任务按照企业的需求进行任务的定期执行；

丰富目前Spring Batch Admin（Spring Batch的管理监控平台，目前能力比较薄弱）框架，提供对Job的统一管理功能，增强Job作业的监控、预警等能力；

通过与企业的组织机构、权限管理、认证系统进行合理的集成，增强平台对Job作业的权限控制、安全管理能力。



```java

@SpringBootApplication
@EnableBatchProcessing
public class SpringbatchApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbatchApplication.class, args);
    }

}

```

Step：

通过Tasklet实现；通过Chunk实现（reader,process,write）

![image-20191029175147085](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191029175147085.png)

![image-20191030153316490](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030153316490.png)

Job

顺序执行：

![image-20191029174819224](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191029174819224.png)



条件执行：

![image-20191029174627070](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191029174627070.png)

Flow构建和使用

![image-20191029175956209](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191029175956209.png)

![image-20191029181118894](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191029181118894.png)

使用Split并发执行

![image-20191029182445374](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191029182445374.png)

![image-20191029183438908](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191029183438908.png)

决策器使用：复杂条件

start（flow）--next（flow、Step、JobExecutionDecider）

![image-20191030110939283](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030110939283.png)

start（step）--next（Step、JobExecutionDecider）

![image-20191030111059316](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030111059316.png)

![image-20191030134629493](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030134629493.png)

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030134647224.png" alt="image-20191030134647224" style="zoom:50%;" />



Job的嵌套

把job拆解为step

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030140508695.png" alt="image-20191030140508695" style="zoom:50%;" />

![image-20191030140523422](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030140523422.png)

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030140915764.png" alt="image-20191030140915764" style="zoom:50%;" />



Job的监听

实现方式： 1.通过注解实现，2.通过实现监听接口实现

用来监听Job的执行情况

![image-20191030143013498](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030143013498.png)

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030153335317.png" alt="image-20191030153335317" style="zoom:50%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030154510179.png" alt="image-20191030154510179" style="zoom:50%;" />



Job参数

 <img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030162125246.png" alt="image-20191030162125246" style="zoom:50%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030162145812.png" alt="image-20191030162145812" style="zoom:50%;" />





ItemReader

![image-20191030164826495](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030164826495.png)

![image-20191030171318970](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030171318970.png)

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030171906846.png" alt="image-20191030171906846" style="zoom:50%;" />

从数据库中分页读取

设置sql语句

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030172916809.png" alt="image-20191030172916809" style="zoom:50%;" />

sortKeys:排序字段，selectxx:查询字段 fromxxx:表名 

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030173645690.png" alt="image-20191030173645690" style="zoom: 50%;" />

ItemProcessor

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030164854073.png" alt="image-20191030164854073" style="zoom:50%;" />

ItemWriter

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030164911002.png" alt="image-20191030164911002" style="zoom:50%;" />

ItemReader异常处理

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191030180910122.png" alt="image-20191030180910122" style="zoom:50%;" />

ItemWrite输出数据

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031145523849.png" alt="image-20191031145523849" style="zoom:50%;" />

ItemProcessor<i,o>

业务处理，过滤

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031160206940.png" alt="image-20191031160206940" style="zoom:50%;" />



错误处理

 https://baijiahao.baidu.com/s?id=1641902479096242727&wfr=spider&for=pc 

默认情况下当任务出现异常时，SpringBatch会结束任务，当相同的参数重启任务时，SpringBatch会去执行未执行的剩余任务

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031163108112.png" alt="image-20191031163108112" style="zoom:50%;" />

 noRetry() ： 每次调用将参数加到需要排除的异常列表里面。 

 retryPolicy() ： 一些简单的重试，可以通过 retry()，noRetry()和retryLimit() 配置来实现重试。Spring Batch 还支持通过设置RetryPolicy 来实现复杂逻辑的重试。RetryPolicy(重试策略)接口定义如下：  ![img](https://pics7.baidu.com/feed/95eef01f3a292df5165d3fec0d59166535a8731f.jpeg?token=01a76094d1f51105dfce00ed77be42eb&s=72B530C4DDC4AD7016D0DC0C0200F08B) 

 canRetry() 如何判断是否可以重试，open()在重试开始时执行，close()在重试结束是执行，每次重试调用一次registerThrowable()来初始化上下文。 



异常跳过

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031163716317.png" alt="image-20191031163716317" style="zoom:50%;" />

跳过xx异常和跳过次数

跳过监听

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031173213792.png" alt="image-20191031173213792" style="zoom:33%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031173102862.png" alt="image-20191031173102862" style="zoom:50%;" />

作业调度

JobLuncher

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031175904342.png" alt="image-20191031175904342" style="zoom:50%;" />

JobOperator

对JobLuncher的封装

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031185202284.png" alt="image-20191031185202284" style="zoom:50%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031185409207.png" alt="image-20191031185409207" style="zoom:50%;" />

​					<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031185900148.png" alt="image-20191031185900148" style="zoom:33%;" />

​					<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031185826328.png" alt="image-20191031185826328" style="zoom:33%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191031185748718.png" alt="image-20191031185748718" style="zoom:50%;" />

