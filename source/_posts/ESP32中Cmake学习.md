---
title: ESP32中Cmake学习
date: 2025-04-07 15:27:01
categories:
  - 软件
  - ESP
tags:
---

# Cmake用途

在学STM32的时候用的是Keil软件，keil已经把编译链接集成在软件里了，所以操作起来非常简单。ESP32构建项目是一个复杂的过程，需要编译-连接-烧录，每一步都是用一个工具实现。

* CMake : 配置待构建的项目，告诉编译器.c/.cpp文件和.h文件的位置。
* Ninja : 用于构建项目
* esptool.py ：烧录.bin文件到芯片上

在比较复杂的项目中，文件目录很多，不同的源文件之间的依赖关系也很复杂。Cmake工具可以帮我们很好管理各文件之间的依赖关系。ESP中的Cmake与原Cmake语法大致一样，我这里没有系统学习过Cmake，仅在做项目的过程中进行记录。

## ESP项目结构

![image](https://github.com/maxiro-samurai/picx-images-hosting/raw/master/image.41y8l5sl3g.webp)

* 顶层项目 CMakeLists.txt 文件：顶层项目 CMakeLists.txt 文件会导入 /tools/cmake/project.cmake 文件，由它负责实现构建系统的其余部分。该文件最后会设置项目的名称，并定义该项目。

* sdkconfig配置文件：顶层项目 CMakeLists.txt 文件会导入 /tools/cmake/project.cmake 文件，由它负责实现构建系统的其余部分。该文件最后会设置项目的名称，并定义该项目。

* components目录：包含了项目的部分自定义组件，并不是每个项目都需要这种自定义组件，但它有助于构建可复用的代码或者导入第三方（不属于 ESP-IDF）的组件。（移植别人的项目时很重要）

* "main" 目录：它包含项目本身的源代码。"main" 是默认名称，CMake 变量 COMPONENT_DIRS 默认包含此组件，但你可以修改此变量。

* "build" 目录：存放构建输出的地方，如果没有此目录，idf.py 会自动创建。CMake 会配置项目，并在此目录下生成临时的构建文件。随后，在主构建进程的运行期间，该目录还会保存临时目标文件、库文件以及最终输出的二进制文件。此目录通常不会添加到项目的源码管理系统中，也不会随项目源码一同发布。

这里只列出了M-heat项目中目前需要的文件，还有一些文件没有用到，等到以后用到再学。


## 项目 CMakeLists 文件

每个项目都有一个顶层 CMakeLists.txt 文件，包含整个项目的构建设置。

    cmake_minimum_required(VERSION 3.16)
    include($ENV{IDF_PATH}/tools/cmake/project.cmake)
    project(myProject)

* cmake_minimum_required(VERSION 3.16)必须放在文件的第一行，它会告诉 CMake 构建该项目所需要的最小版本号。

* include($ENV{IDF_PATH}/tools/cmake/project.cmake) 会导入 CMake 的其余功能来完成配置项目、检索组件等任务。

* project(myProject) 会创建项目本身，并指定项目名称。该名称会作为最终输出的二进制文件的名字，即 myProject.elf 和 myProject.bin。每个 CMakeLists 文件只能定义一个项目。


### 可选项目变量

* COMPONENT_DIRS：组件的搜索目录，默认为 IDF_PATH/components、 PROJECT_DIR/components、和 EXTRA_COMPONENT_DIRS。

* EXTRA_COMPONENT_DIRS：用于搜索组件的其它可选目录列表。路径可以是相对于项目目录的相对路径，也可以是绝对路径。

* COMPONENTS：用于指定要构建到项目中的组件名称列表，默认为 COMPONENT_DIRS 目录下检索到的所有组件。使用此变量可以“精简”项目，从而缩短构建时间。请注意，如果一个组件通过 COMPONENT_REQUIRES 指定了它依赖的另一个组件，则会自动将其添加到 COMPONENTS 中，所以 COMPONENTS 列表可能会非常短。

使用 cmake 中的 set 命令 来设置这些变量，如 set(VARIABLE "VALUE")。请注意，set() 命令需放在 include(...) 之前，cmake_minimum(...) 之后。


## 组件 CMakeLists 文件

每个项目都包含一个或多个组件，这些组件可以是 ESP-IDF 的一部分，可以是项目自身组件目录的一部分，也可以从自定义组件目录添加。

组件是 COMPONENT_DIRS 列表中包含 CMakeLists.txt 文件的任何目录。


### 最小组件 CMakeLists 文件

最小组件 CMakeLists.txt 文件通过使用 idf_component_register 将组件添加到构建系统中。

    idf_component_register(SRCS "foo.c" "bar.c"
    INCLUDE_DIRS "include" REQUIRES mbedtls)

* SRCS 是源文件列表（*.c、*.cpp、*.cc、*.S），里面所有的源文件都将会编译进组件库中。 

* INCLUDE_DIRS 是目录列表，里面的路径会被添加到所有需要该组件的组件（包括 main 组件）全局 include 搜索路径中。

* REQUIRES 实际上并不是必需的，但通常需要它来声明该组件需要使用哪些其它组件

上述命令会构建生成与组件同名的库，并最终被链接到应用程序中。

上述目录通常设置为相对于 CMakeLists.txt 文件的相对路径，当然也可以设置为绝对路径。

### 组建依赖

* REQUIRES 需要包含所有在当前组件的 公共 头文件里 #include 的头文件所在的组件。

* PRIV_REQUIRES 需要包含被当前组件的源文件 #include 的头文件所在的组件（除非已经被设置在了 REQUIRES 中）。以及是当前组件正常工作必须要链接的组件。

* 如果当前组件除了 通用组件依赖项 中设置的通用组件（比如 RTOS、libc 等）外，并不依赖其它组件，那么对于上述两个 REQUIRES 变量，可以选择其中一个或是两个都不设置。





## 添加组件下所有源文件

1. 使用file(GLOB) 通配符

        # 组件目录下的所有 .c 文件
        file(GLOB component_sources "*.c")

        # 注册组件（包含源文件和头文件路径）
        idf_component_register(
            SRCS ${component_sources}
            INCLUDE_DIRS "include"
        )

2. 使用GLOB_RECURSE通配符

    * GLOB：仅匹配当前目录。
    * GLOB_RECURSE：递归匹配子目录。

          cmake_minimum_required(VERSION 3.5)
          project(your_project)

          # 包含 src 目录及其子目录下的所有 .c 文件
          file(GLOB_RECURSE SOURCES 
              "src/*.c"
              "src/utils/*.c"
          )

          # 添加可执行目标
          add_executable(your_project ${SOURCES})

          # 链接必要库（如 ESP32 的驱动库）
          target_link_libraries(your_project PRIVATE esp32)


至此已经可以成功编译所需项目组件。