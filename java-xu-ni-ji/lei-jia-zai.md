---
description: 从类加载机制开始吧！
---

# 类加载

## 类加载机制

在Java中，类加载机制是指将Java源代码编译后的字节码文件（.class文件）加载到Java虚拟机（JVM）中，并进行链接、初始化等操作的过程。类加载机制负责加载类的字节码，并将其转换为可以在JVM中执行的对象。

类加载机制的过程通常分为以下几个步骤：

1. 加载（Loading）：

* 加载是类加载机制的第一步，它将字节码文件加载到内存中。
* 类加载器会根据类的全限定名（包括包名和类名）查找并读取对应的字节码文件。
* 加载过程可以来自本地文件系统、网络、Jar文件等不同的来源。

2. 链接（Linking）：

* 链接是类加载机制的第二步，它将加载的字节码进行链接处理。
* 链接分为三个阶段：验证、准备和解析。
  * 验证（Verification）：验证字节码文件的正确性，检查类的结构、依赖关系等。
  * 准备（Preparation）：为类的静态变量分配内存，并设置默认初始值。
  * 解析（Resolution）：将符号引用转换为直接引用，解析类与其他类的关系。

3. 初始化（Initialization）：

* 初始化是类加载机制的最后一步，它对类进行初始化操作。
* 在初始化阶段，会执行类的静态变量赋值和静态代码块的初始化。
* 初始化过程是按照顺序执行的，保证了类的正确初始化。

## 类加载器有哪些

Java中的类加载器可以分为以下几种类型：

1. 启动类加载器（Bootstrap Class Loader）：

* 它是Java虚拟机的一部分，负责加载Java的核心类库，如rt.jar等。
* 启动类加载器是用本地代码实现的，通常无法直接获取对其的引用。

2. 扩展类加载器（Extension Class Loader）：

* 它是加载Java扩展库的类加载器。
* 扩展类加载器的父类加载器是启动类加载器。
* 它负责加载位于%JAVA\_HOME%/jre/lib/ext目录下的JAR包和类。

3. 应用程序类加载器（Application Class Loader）：

* 它也被称为系统类加载器，负责加载应用程序的类。
* 应用程序类加载器的父类加载器是扩展类加载器。
* 它加载的类包括应用程序的类路径（Classpath）下的类。

除了这三种主要的类加载器，Java还提供了一些特殊的类加载器：

1. 自定义类加载器（Custom Class Loader）：

* Java允许用户通过继承ClassLoader类来创建自定义的类加载器。
* 自定义类加载器可以实现特定的加载逻辑，例如从特定位置加载类、解密加密的类等。

## 双亲委派机制

首先，类加载器的层次结构是树状的，除了启动类加载器没有父类加载器，其它每个类加载器都有一个父类加载器。

当一个类加载器需要加载某个类时，它首先将加载请求委派给其父类加载器。父类加载器也会按照相同的方式将加载请求传递给它的父类加载器，一直向上委派，直到顶层的启动类加载器。只有当父类加载器无法加载该类时，子类加载器才会尝试自己加载。

具体实现：

1. 当一个类加载器收到加载请求时，首先检查该类是否已经被加载过了。如果已经加载过，则直接返回已加载的类。
2. 如果该类尚未被加载过，则将加载请求委派给父类加载器。父类加载器会按照相同的方式进行检查和委派。
3. 如果父类加载器也没有加载该类，则子类加载器会尝试加载该类。如果子类加载器能够成功加载，则将该类返回给请求的加载器，并将其缓存起来。
4. 如果子类加载器无法加载该类，则再次委派给父类加载器。这个过程将一直持续，直到顶层的启动类加载器。

该模型的关键思想是层级委派和逆向委派，它的优势在于：

1. 安全性：双亲委派模型可以确保类的加载是由上层到下层的，上层的类加载器不会加载下层的类。这样可以避免恶意代码替换核心类库，保护Java运行环境的安全性。
2. 避免重复加载：当父类加载器已经加载了一个类时，子类加载器再次加载同一个类时会直接使用父类加载器已加载的类，避免了重复加载，提高了加载的效率。
3. 统一性：由于双亲委派模型的存在，Java类库和用户自定义类都使用相同的类加载机制。这种统一性使得Java的类库和用户自定义类能够共享同一个类的定义，避免了类的重复定义和冲突。

## 如何破坏双亲委派机制

以下是一些可能的方法来破坏双亲委派机制：

1. 自定义类加载器：继承ClassLoader类，重写它的加载方法（如findClass()），在加载类时自定义加载逻辑。这样可以绕过父类加载器的委派，自己实现类的加载。
2. 重写loadClass()方法：在自定义类加载器中重写loadClass()方法，根据特定的条件决定是否委派给父类加载器加载类。可以在loadClass()方法中判断类的名字，如果满足特定条件，不委派给父类加载器，自己加载类。
3. 系统属性设置：通过设置系统属性来修改类加载机制的行为。例如，可以通过设置java.system.class.loader属性来指定一个自定义的类加载器，替代默认的系统类加载器。
4. 需要注意的是，破坏双亲委派机制可能会导致一些安全性问题和类的冲突。因此，如果没有充分的理解和明确的需求，最好遵循双亲委派模型，使用默认的类加载机制。自定义类加载器应该谨慎使用，并且需要清楚其影响和潜在的风险。

