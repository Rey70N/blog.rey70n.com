本教程是关于JBoss的`Drools Fusion`。在继续本文之前，我们假设读者具有JBoss Drools的基本知识和Java n层应用程序的工作。简而言之，`Drools`是一个业务规则管理系统（BRMS）解决方案。它提供了核心业务规则引擎（BRE），Web创作和规则管理应用程序（Drools Workbench）以及用于核心开发的Eclipse IDE插件。有JBoss的更多的了解`Drools`，请参阅文章  [在这里](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-best-practices-tutorial/)。



### 目录

### [1. Drools Fusion简介](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#introduction)

- [2.什么是事件驱动架构](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#eventDrivenArchitecture)

  [2.1。事件处理样式](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#eventProcessingStyles)

- [3.复杂事件处理](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#complexEventProcessing)

- [4. Drools Fusion](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#droolsFusion)

  [4.1声明基于CEP的规则](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#cepBasedRules)[4.2欺诈保护](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#fraudProtection)[4.2.1问题描述](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#problemDesc)[4.2.2设计欺诈检测系统](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#DesigningFraudDetection)[4.2.3欺诈检测规则](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#FraudDetectionRules)[4.2.4通知](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#Notification)[4.2.5测试配置设置](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#TestConfigurationSetup)[4.2.6测试通知规则](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#TestingNotificationRule)

- [5.参考文献](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-fusion-tutorial/#references)



## 1. Drools Fusion简介

`Drools Fusion`基于事件驱动的体系结构，下面将详细解释。`Drools Fusion`包括复杂的事件处理功能。复杂事件处理（CEP）用于处理大量信息，可用于实时事件监视或关联。事件可以通过两种方式处理，即“流”模式或“云”模式。

云模式在以下情况下非常有用：用户行为，市场数据和活动监控。

流模式在应用程序中最有用，例如实时事件监视，事件关联和传感器网络。

## 2.什么是事件驱动架构

事件代表了一种重大的国家变化。正如我们上面所说，`Drools Fusion`基于复杂事件处理（CEP），事件驱动架构是一个非常容易与CEP绑定的概念，因为它定义了一个简单的架构来促进生产，检测，消费和反应。事件。该体系结构的概念是关注应用程序组件。

例如，当消费者购买汽车时，汽车的状态从“待售”变为“已售出”。汽车经销商的系统架构可以将该状态变化视为可以使架构内的其他应用程序知道其发生的事件。从形式的角度来看，生成，发布，传播，检测或消费的内容是称为事件通知的（通常是异步的）消息，而不是事件本身，即触发消息发送的状态更改。

事件不会发生，它们只会发生。事件驱动系统通常由事件发射器（或代理），事件消费者（或接收器）和事件通道组成。

**事件发射器**有责任检测，收集和转移事件。事件发射器不知道事件的消费者，它甚至不知道消费者是否存在，并且如果它存在，它不知道如何使用或进一步处理事件。

**事件消费者或接收者**有责任在事件发生后立即应用。水槽本身可能会或可能不会完全提供反应。例如，接收器可能只负责过滤，转换和转发事件到另一个组件，或者它可能对此类事件提供自包含的反应。

**事件通道**是将事件从事件发射器传输到事件使用者的管道。事件可以以消息的形式传播，或者可以存在基本上需要特定框架工作的点对点通信。

### 2.1事件处理方式

事件处理有三种常规样式：简单，流和复杂。这三种样式通常在成熟的事件驱动架构中一起使用。

**简单事件处理：** 简单事件处理涉及与特定的，可测量的条件变化直接相关的事件。在简单事件处理中，发生了引发下游动作的值得注意的事件。例如，传感器检测轮胎压力的变化。

**事件流处理：** 在事件流处理（ESP）中，发生普通事件和值得注意的事件。ESP更多的是关于大量事件的实时处理。例如，计算实时平均交易量随时间的变化。

**复杂事件处理：** 复杂事件处理（CEP）处理复杂事件。复杂事件是一组简单事件。例如，一系列大额提款可能引发可疑交易事件。简单事件被认为是推断出发生了复杂事件。

## 3.复杂事件处理

正如我们之前已经提到的，这`Drools Fusion`是基于复杂事件处理，让我们在这里详细了解CEP。**复杂事件处理（CEP）**是一种技术，其中有关正在发生的事件（事件数据）的传入数据在其到达时或多或少地被处理以生成更高级别，更有用的摘要信息（复杂事件）。事件处理平台具有内置功能，用于过滤传入数据，存储事件数据窗口，计算聚合和检测模式。

在正式术语中，CEP软件是可以生成，读取，丢弃或执行复杂事件计算的任何计算机程序。复杂事件是一个或多个基础（输入）事件的抽象。复杂事件可能表示需要业务响应的威胁或机会。一个复杂事件可能是对来自一个或多个事件源的几个或数百万个基本事件执行的计算的结果。

CEP正在快速发展，因为从技术意义上讲，CEP是实时或近实时从事件流中获取信息的唯一方式。系统必须在到达时或多或少地处理事件数据，以便可以快速采取适当的措施。

**事件流处理（ESP）**专注于（近实时**）处理事件流**的功能，其中**复杂事件处理（CEP）**的主要焦点是原子事件与复杂（复合）事件的相关性和组合。

简而言之，CEP是关于从事件云中检测和选择有趣事件（并且只有它们），找到它们之间的关系并从它们及其关系中推断出新数据。

## 4. Drools Fusion

`Drools Fusion`是Drools模块，是Business Logic Integration Platform的一部分。它是Drools事件处理引擎，涵盖CEP和ESP。每个事件都有一个类型，一个发生的时间，可能还有一个持续时间。支持时间点（零持续时间）和基于间隔的事件。事件还可以包含其他数据，例如具有名称和类型的任何其他事实属性。所有事件都是事实，但不是所有事实都是事件。不应更改事件的状态。但是，填充未填充的值是有效的。事件具有明确的生命周期窗口，并且可以在生命周期窗口到期后透明地进行垃圾收集（例如，我们可能仅对过去24小时内发生的事务感兴趣）。规则可以处理事件之间的时间关系。

到目前为止，在我们[之前的Drools文章中](https://examples.javacodegeeks.com/enterprise-java/jboss-drools/jboss-drools-spreadsheet-example/)，我们已经处理了我们插入的事实`KieSession`以及它们如何匹配特定规则。事实与事件非常相似，除了事件有一个额外的特征：发生的时间。事件只是关于任何域（表示为Java对象）的数据，以及有关此信息为真的时间的信息。

我们在特定时间录制的任何内容都可以是一个事件。

### 4.1声明基于CEP的规则

我们知道规则应该是原子的，当合作必须能够实现复杂场景的管理时，这种方法也很好地与CEP保持一致，因为每个不同的规则可以处理其他规则的聚合，组合或抽象的一个方面。事件。他们可以一起工作，以实现非常复杂的事件情况的实时解决。

在以下部分中，我们将了解如何定义事件。在此之前，我们可以深入了解两种主要类型的事件 - 准时和间隔事件。

**准时事件：**  它们是在特定时刻发生的事件。它们可能代表我们的领域模型的现实变化出现的确切时刻，或者它们的寿命可能太短而无法考虑。准时事件的一个示例是传感器读数，其将来自传感器的特定值与读取的特定时间相关联。交易也可以被视为准时事件，如果它们如此短暂，我们可以忽略它们的持续时间。

**区间事件：**  它们是具有两个不同时刻的事件：它们开始的那一刻和它们结束的那一刻。这使得间隔事件比准时事件更复杂一些。如果你有两个准时事件，你只能比较它们是在同一时间，在彼此之前还是之后发生的。另一方面，对于区间事件，您可以比较一个事件在另一个事件开始和结束期间的情况，只是为了命名一个场景。

### 4.2欺诈检测

为了更好地理解概念`CEP`和`Drools Fusion`，让我们看看欺诈检测系统的一个例子。银行系统中的欺诈正成为一个主要问题。在线交易量每天都在增加。需要一种用于欺诈检测的自动系统。系统应分析银行中发生的各种事件，并根据一组规则提出适当的警报。

标准`Drools`规则引擎无法解决此问题。事件量巨大且异步发生。如果我们只是将它们插入知识会话中，我们很快就会耗尽内存。虽然后面的Rete算法`Drools`对会话中的对象数没有任何理论限制，但我们可以更明智地使用处理能力。`Drools Fusion`是这类任务的合适人选。

#### 4.2.1问题描述：

让我们考虑以下欺诈检测系统的业务要求：

1. 如果收到客户关于被盗卡的通知，请阻止此帐户以及此帐户的任何提款。
2. 根据帐号黑名单检查每笔交易。如果交易正在从这样的帐户转移资金，则将此交易标记为具有最大严重性的可疑。
3. 如果在90秒内有两个来自同一帐户的大额借记交易且每笔交易提取超过平均每月（30天）提款金额的300％，则将这些交易标记为可疑且严重程度较轻。
4. 如果在三分钟内有来自同一帐户的三个连续增加的借记交易序列，并且这些交易在30天内一起提取超过帐户平均余额的90％，则将这些交易标记为可疑的主要交易严重性并暂停帐户。
5. 如果一天内的提款数量比30天内的平均提款数量高500％，并且该帐户在一个月（30天）内的平均余额不到10％，则将该帐户标记为可疑的严重程度较轻。
6. 重复事务检查 - 如果两个事务在15秒的时间窗口中发生，具有相同的源/目标帐号，具有相同的数量，并且只是事务ID不同，则将这些事务标记为重复。

**监控**：

1. 监控所有账户的平均提款金额30天。
2. 监控所有帐户的平均余额。

#### 4.2.2设计欺诈检测系统：

我们已经有了这些要求，现在我们需要一种方法来将交易标记为**可疑**。

此状态可以添加到现有  `Transaction` 类型，或者我们可以将此状态外部化为新的事件类型。我们会做后者。将定义以下新事件：

- `TransactionCreatedEvent` - 创建新事务时触发的事件。它包含事务标识符，源帐号，目标帐号和传输的实际金额。
- `TransactionCompletedEvent` - 处理现有事务时触发的事件。它包含与`TransactionCreatedEvent` 类相同的字段  。
- `AccountUpdatedEvent` - 帐户更新后触发的事件。它包含帐户号，当前余额以及启动此更新的事务的事务标识符。
- `SuspiciousAccount` - 当帐户周围存在某种怀疑时触发的事件。它包含帐号和怀疑的严重程度。它是一个枚举，可以有两个值  `MINOR` 和  `MAJOR`。此事件的实现显示在以下代码中。
- `SuspiciousTransaction`- 类似于  `SuspiciousAccount`，这是一个将事务标记为可疑的事件。它包含事务标识符和严重性级别。
- `LostCardEvent` - 表示卡丢失的事件。它包含一个帐号。

所描述的事件之一`SuspiciousAccount`- 如以下代码所示。它还定义了  `SuspiciousAccountSeverity` 枚举，该枚举封装了事件可以表示的各种严重性级别。该事件将定义两个属性。已经提到其中一个，  `severity` 另一个将识别帐户`accountNumber`。

*SuspiciousAccount.java*

```java
`package` `com.drools.myexample;` `import` `java.io.Serializable;` `import` `org.apache.commons.lang.builder.ToStringBuilder;` `/**`` ``* Marks an account as suspicious`` ``*/``public` `class` `SuspiciousAccount ``implements` `Serializable {``    ``public` `enum` `SuspiciousAccountSeverity {``        ``MINOR, MAJOR``    ``}` `    ``private` `final` `Long accountNumber;``    ``private` `final` `SuspiciousAccountSeverity severity;` `    ``public` `SuspiciousAccount(Long accountNumber, SuspiciousAccountSeverity severity) {``        ``this``.accountNumber = accountNumber;``        ``this``.severity = severity;``    ``}` `    ``private` `transient` `String toString;` `    ``@Override``    ``public` `String toString() {``        ``if` `(toString == ``null``) {``            ``toString = ``new` `ToStringBuilder(``this``).appendSuper(``super``.toString()).append(``"accountNumber"``, accountNumber)``                    ``.append(``"severity"``, severity).toString();``        ``}``        ``return` `toString;``    ``}``}`
```

事件表示活动实体，这意味着每个实例都是唯一的。因此，我们不必重写`equals()`和`hashcode()`方法。上面列出的事件类是轻量级的，它们不引用任何其他域对象。它们还实现了`Serializable`接口，便于在JVM之间进行传输。作为最佳实践，此事件是不可变的。上面（`accountNumber` 和`severity`）的两个属性被标记为final。它们只能通过构造函数设置。

事件本身并没有发生时间 - 时间戳。当事件被插入知识会话时，规则引擎分配这样的时间戳。有一个特殊的`FactHandle`被调用实现，`EventFactHandle`它被返回`session.insert()`。它扩展`DefaultFactHandle`并添加了一些额外的字段， - `startTimestamp`和`duration`。两者都包含毫秒值并且类型为long。

所以，现在我们有了我们的事件类，我们知道有一个特殊`FactHandle`的事件。但我们仍然没有看到一种方法来告诉`Drools`我们班级代表一个事件。有类型声明`Drools`，有助于`Drools`了解我们代表事件的类。它可以定义新类型并增强现有类型。让我们看一个例子：

*事件角色声明（cep.drl）*

```java
`declare` `TransactionCreatedEvent``   ``@role( event )``end`
```

此代码可以驻留在普通  `.drl` 文件中。如果我们的事件有时间戳属性或持续时间属性，我们可以使用以下映射将其映射到  `startTimestamp` 或持续时间属性`EventFactHandle`：

```java
`@duration``( durationProperty )`
```

括号中的名称是我们的事件的属性的实际名称，它将映射到的duration属性  `EventFactHandle`。对于`startTimestamp`财产，这可以类似地完成  。

在以下示例中，我们还将了解如何定义新类型声明。

#### 4.2.3欺诈检测规则

让我们假设系统在任何给定时间处理数千个事务。这在时间和内存消耗方面可能具有挑战性，因为无法将所有数据保存在内存中。对此的可能解决方案是将所有帐户保留在内存中，因为帐户的数量将少于交易数量，并且仅在特定时间段内保留交易。

`Drools Fusion`通过将其声明`Transaction`为事件，可以实现这一点。该事务将通过入口点插入知识会话中。每个入口点在输入数据存储中定义一个分区，减少匹配空间并允许模式以特定分区为目标。匹配分区中的数据需要在模式声明中进行显式引用。这是有道理的，特别是如果有大量数据并且只有一些规则对它们感兴趣。我们将在以下示例中查看入口点。

#### 4.2.4通知

我们要在这里实施的要求主要`LostCardEvent` 是在收到帐户时阻止帐户  。此规则将匹配两个事实：

a）类型为Account和

b）LostCardEvent类型之一。

然后，该规则会将此帐户的状态设置为已阻止。该规则的实施如下：

*阻止帐户的通知规则（cep.drl）*

```java
`rule notification``  ``when``     ``$account : Account( status != Account.Status.BLOCKED )``     ``LostCardEvent( accountNumber == $account.number )``       ``from entry-point LostCardStream``  ``then``     ``modify($account) {``         ``setStatus(Account.Status.BLOCKED)``     ``};``end`
```

我们知道，帐户是知识会话中的一个普通事实，第二个事实`LostCardEvent`- 来自被称为入口点的事件`LostCardStream`。每当创建新事件并通过入口点时`LostCardStream`，此规则将尝试匹配（检查是否可以满足其条件）。如果知识会话中的帐户尚未与此事件匹配，并且满足所有条件，则激活规则。结果将帐户的状态设置为在修改块中被阻止。

由于我们在结果中更新帐户并在条件中对其进行匹配，因此我们必须添加仅与非阻止帐户匹配的约束以防止循环（参见上文:)  `status != Account.Status.BLOCKED`。

#### 4.2.5测试配置设置

在本节中，我们将设置一个单元测试类。所有规则都将写在一个名为的文件中  `cep.drl`。创建此文件时，只需确保它在类路径上。创建`KnowledgeBase`将与我之前的文章第4.4节中显示的相同。我们只需稍微更改默认知识库配置：

*在知识库配置上启用STREAM事件处理模式。*

```java
`KnowledgeBaseConfiguration config = KnowledgeBaseFactory``     ``.newKnowledgeBaseConfiguration();``config.setOption( EventProcessingOption.STREAM );`
```

上面的代码将启用  `STREAM` 事件处理模式。

`KnowledgeBaseConfiguration` 然后在创建知识库时使用上面的代码，如下所示：

```java
`KnowledgeBaseFactory.newKnowledgeBase(config).`
```

接下来我们将设置点击初始化。我们已经知道每个事件都有时间戳。此时间戳来自知识会话内的时钟。`Drools`支持多种时钟类型，例如实时时钟或伪时钟。

实时时钟是默认值，应该在正常情况下使用。伪时钟对测试非常有用，因为我们可以完全控制时间。

如果我们看到以下代码，则其`initialize()`方法设置伪时钟。这是通过设置时钟类型`KnowledgeSessionConfiguration`并将此对象传递给`newStatefulKnowledgeSession`方法来完成的`KnowledgeBase`。然后，initialize方法在调用时将此时钟作为名为clock的测试实例变量提供`session.getSessionClock()`。

*单元测试设置（CepTest.java）*

```java
`public` `class` `CepTest {``  ``static` `KnowledgeBase knowledgeBase;``  ``StatefulKnowledgeSession session;``  ``Account account;``  ``FactHandle accountHandle;``  ``SessionPseudoClock clock;``  ``TrackingAgendaEventListener trackingAgendaEventListener;``  ``WorkingMemoryEntryPoint entry;` `  ``@Before``  ``public` `void` `initialize() ``throws` `Exception {``    ``KnowledgeSessionConfiguration conf = ``                         ``KnowledgeBaseFactory.newKnowledgeSessionConfiguration();``    ``conf.setOption( ClockTypeOption.get( ``"pseudo"` `) );``    ``session = knowledgeBase.newStatefulKnowledgeSession(conf, ``null``);``    ``clock = (SessionPseudoClock) session.getSessionClock();``    ``trackingAgendaEventListener = ``new` `TrackingAgendaEventListener();``    ``session.addEventListener(trackingAgendaEventListener);``    ``account = ``new` `Account();``    ``account.setNumber(123456l);``    ``account.setBalance(BigDecimal.valueOf(``1000.00``));``    ``accountHandle = session.insert(account);`
```

如我们所见，前面的`initialize()`方法还创建了一个事件监听器并将其传递给会话。调用事件监听器`TrackingAgendaEventListener`。它只是跟踪所有规则执行。对于单元测试来说，验证规则是否被触发是很有用的。其实施如下：

*TrackingAgendaEventListener.java*

```java
`public` `class` `TrackingAgendaEventListener ``extends``    ``DefaultAgendaEventListener {``  ``List rulesFiredList = ``new` `ArrayList();``  ``@Override``  ``public` `void` `afterActivationFired(``      ``AfterActivationFiredEvent event) {``    ``rulesFiredList.add(event.getActivation().getRule()``        ``.getName());``  ``}``  ``public` `boolean` `isRuleFired(String ruleName) {``    ``for` `(String firedRuleName : rulesFiredList) {``      ``if` `(firedRuleName.equals(ruleName)) {``        ``return` `true``;``       ``}``      ``}``      ``return` `false``;``     ``}``     ``public` `void` `reset() {``       ``rulesFiredList.clear();``     ``}``   ``}`
```

`DefaultAgendaEventListener`来自org.drools.event.rule包，它是`drools-api.jar`文件的一部分，而不是作为`org.drools.event`旧API的一部分的包`drools-core.jar`。

所有`Drools`议程事件侦听器都必须实现该`AgendaEventListener`接口。在这里，在上面的例子中，`TrackingAgendaEventListener`延伸`DefaultAgendaEventListener`，使我们没有实现所有的在定义的方法`AgendaEventListener`接口。

我们的监听器只会覆盖每次执行规则后果时`afterActivationFired()`将调用的方法`Drools`。我们对此方法的实现将已触发的规则名称添加到已触发的规则列表中`rulesFiredList`。然后，便捷方法`isRuleFired()`将ruleName作为参数，并检查此规则是否已执行/触发。该`reset()`方法用于清除此侦听器的状态，例如，在`session.fireAllRules()`调用之后。

再次，回到测试配置设置（`CepTest.java`）。该`initialize` 方法的最后一部分  是帐户对象创建（`account = new Account();` ...）。这是为了方便起见，因此每个测试都不必创建一个。帐户余额设置为  *1000*。该帐户被插入知识会话中并被  `FactHandle` 存储，以便可以容易地更新帐户对象。

#### 4.2.6测试通知规则

设置测试基础架构后，我们可以为*通知*  规则编写测试  。

*CepTest.java*

```java
`@Test``  ``public` `void` `notification() ``throws` `Exception {``    ``session.fireAllRules();``    ``assertNotSame(Account.Status.BLOCKED,account.getStatus());``    ``entry = session``        ``.getWorkingMemoryEntryPoint(``"LostCardStream"``);``    ``entry.insert(``new` `LostCardEvent(account.getNumber()));``    ``session.fireAllRules();``    ``assertSame(Account.Status.BLOCKED, account.getStatus());``   ``}`
```

该测试验证该帐户未被阻止。然后`LostCardStream` 通过调用：从会话中获取  入口点  `session.getWorkingMemoryEntryPoint(“LostCardStream”)`。然后代码清单演示了如何通过入口点将事件插入到知识会话中`entry.insert(new LostCardEvent(…))`。

## 参考

1. http://planet.jboss.org/post/getting_started_with_drools_fusion
2. https://docs.jboss.org/drools/release/5.5.0.CR1/drools-fusion-docs/pdf/drools-fusion-docs.pdf
3. https://docs.jboss.org/drools/release/5.3.0.Final/drools-fusion-docs/html_single/
4. https://www.packtpub.com/mapt/book/networking_and_servers/9781783288625/6/ch06lvl1sec41/running-cep-based-scenarios