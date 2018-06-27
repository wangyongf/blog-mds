# 准备工作
# Getting Started

> 给 IntelliJ Platform 插件工程增加 Gradle 构建支持需要比较新版本的 Gradle 构建系统 和 IntelliJ IDEA (社区版 | 旗舰版）

## 1.0. 下载并安装 IntelliJ IDEA

下载并安装 IntelliJ IDEA 社区版（旗舰版）

## 1.1. 开启 IDE 的 Gradle 插件和 `Plugin DevKit` 插件

如果要查看上述两个插件是否被开启了，可以访问 `Settings | Plugins`

![](http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system/img/step0_gradle_enabled.png)

## 1.2. 从头开始创建 IntelliJ 插件项目

IntelliJ IDEA 支持自动初始化 Gradle 驱动的插件项目，只需要你稍微动动手指，项目初始化好以后，所有必要的工作，比如 build.gradle 配置都已经自动完成。你也可以将一个非 Gradle 插件项目迁移到 Gradle 上。当然，如果无法将现有插件项目自动迁移到 Gradle 上来，这种情况下，你就需要手动将项目下的代码等资源文件拷贝到新创建的 Gradle 插件项目中了。

在 IntelliJ IDEA 中，要创建一个新的插件项目，你应该选择 `File | New... | Project`，然后在对话框中选择左侧的 Gradle，选中之后，在 `Additional Libraries and Frameworks` 页面中，选中 `IntelliJ Platform Plugin`。

> 译者备注：
> 如果要使用 Kotlin 开发插件项目，再选中一下 `Kotlin (Java)`

![](http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system/img/step1_new_gradle_project.png)

项目创建向导会贯穿你 Gradle 项目创建的整个流程。在创建项目的时候，你得指定一个 `Group ID`，一个 `Artifact ID`，还有一个 `Version` 也就是版本号：

![](http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system/img/step2_group_artifact_version.png)

创建项目的时候，建议选择 `Use default gradle wrapper` 选项，这样 IntelliJ IDEA 会替你安装运行 Gradle  tasks 所需要的一切。

在创建步骤的最后一步，你需要指定项目 Gradle 所使用的 JVM 版本信息，默认应该是项目的 JDK 版本。当然，你也可以在项目创建好以后再指定这个路径，在这里配置：`Settings | Build, Execution, Deployment | Build Tools | Gradle`。

![](http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system/img/step3_gradle_config.png)

## 1.3. 给现有的插件项目添加 Gradle 构建支持

要给已有的插件项目添加 Gradle 构建支持，需要在项目根目录新建一个 `build.gradle` 文件，文件内容模板如下，但是各种插件的版本号最好是最新版本：

```groovy
buildscript {
    ext.kotlin_version = '1.2.31'

    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}

plugins {
    id 'java'
    id 'org.jetbrains.intellij' version '0.3.4'
}

group 'com.yongf.intellij'
version '1.0.0-SNAPSHOT'

apply plugin: 'kotlin'
apply plugin: 'java'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-jdk8:$kotlin_version"
    testCompile group: 'junit', name: 'junit', version: '4.12'
}

compileKotlin {
    kotlinOptions.jvmTarget = "1.8"
}
compileTestKotlin {
    kotlinOptions.jvmTarget = "1.8"
}
intellij {
    version '2018.1.4'
    plugins 'coverage' //Bundled plugin dependencies
}
patchPluginXml {
    changeNotes """
      Add change notes here.<br>
      <em>most HTML tags may be used</em>"""
}
```

然后，如果你将 Gradle 的可执行文件配置到了系统的环境变量中，直接在项目根目录中执行下列命令：

```shell
gradle cleanIdea idea
```

上述这条命令会清理并删除项目中所有的 IntelliJ IDEA 配置文件，然后生成一个新会被 IntelliJ IDEA 所识别的 Gradle 构建配置文件。然后，只要你 refresh 一下你的项目，应该就能在 `View | Tool Windows | Gradle` 看到 Gradle 工具窗口，这也就意味着 IntelliJ IDEA 已经配置好了对当前项目的 Gradle 构建支持。

## 1.4. 把这个简单的插件跑起来！

在项目中新建一个 `HelloAction` 类并在 `plugin.xml` 中配置之

![](http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system/img/gradle_directory_structure.png)

HelloAction.java
```java
import com.intellij.openapi.actionSystem.*;
import com.intellij.openapi.project.Project;
import com.intellij.openapi.ui.Messages;

public class HelloAction extends AnAction {
  public HelloAction() {
    super("Hello");
  }

  public void actionPerformed(AnActionEvent event) {
    Project project = event.getProject();
    Messages.showMessageDialog(project, "Hello world!", "Greeting", Messages.getInformationIcon());
  }
}
```

plugin.xml
```xml
<idea-plugin>
  <id>org.jetbrains</id>
  <name>my_plugin</name>
  <version>0.0.1</version>
  <vendor email="dummy" url="dummy">dummy</vendor>

  <description><![CDATA[
      Sample plugin.<br>
    ]]></description>

  <change-notes><![CDATA[
      Release 0.0.1: Initial release.<br>
    ]]>
  </change-notes>

  <!-- please see http://www.jetbrains.org/intellij/sdk/docs/basics/getting_started/build_number_ranges.html for description -->
  <idea-version since-build="162"/>

  <!-- please see http://www.jetbrains.org/intellij/sdk/docs/basics/getting_started/plugin_compatibility.html
       on how to target different products -->
  <!-- uncomment to enable plugin in all products
  <depends>com.intellij.modules.lang</depends>
  -->

  <extensions defaultExtensionNs="com.intellij">
  </extensions>

  <actions>
    <group id="MyPlugin.SampleMenu" text="Greeting" description="Greeting menu">
      <add-to-group group-id="MainMenu" anchor="last"/>
      <action id="Myplugin.Textboxes" class="HelloAction" text="Hello" description="Says hello"/>
    </group>
  </actions>

</idea-plugin>
```

然后，打开 Gradle 工具窗口，找到 `runIde` task 任务并双击运行之。如果没找到这个任务，点击顶部的 `Refresh` 按钮。

![](http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system/img/gradle_tasks_in_tool_window.png)

或者，你也可以自己配置一个新的 Gradle 运行配置，大致是下面这样的：

![](http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system/img/gradle_run_config.png)

应用你配置的新的 Gradle 运行配置并运行之。然后，在 Run Window 中，你就可以看到类似如下的输出了。

![](http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system/img/launched.png)

最后，当运行这个插件的时候，在打开的 IDE 中，你会在顶部 `Help` 菜单的右侧看到一个新的菜单。至此，你的这个插件项目已经配置好 Gradle 咯，庆祝一下🎉

# Q&A

## 1. 插件项目 Gradle Build 的时候无法下载 `ideaIC-2018.1.4.zip`

1. 先同步，同步出错，如果是因为下载不动，会报错，告诉你哪个资源下载不下来，获取这个地址
2. 手动下载 `ideaIC-2018.1.4.zip`，比如用迅雷神马的
3. 定位到目录 `~\.gradle\caches\modules-2\files-2.1\com.jetbrains.intellij.idea\ideaIC\2018.1.4`，此处的 `2018.1.4` 是根据你项目中使用的 `intellij | version` 来决定的
4. 在这个目录下找到一个很长的类似 hash 值的目录。如果没有，现在项目中 Gradle Build 一下
5. 将上面下载到的 zip 文件移动到这个目录下，然后解压之，得到目录 B
6. 下载对应的 ideaIC-Xxx-sources.jar 文件，链接形式类似于这样 `https://www.jetbrains.com/~urlswitch/intellij-repository/releases/com/jetbrains/intellij/idea/ideaIC/2018.1.4/ideaIC-2018.1.4-sources.jar`，这个文件放到目录 B 之下（解压后的目录）
7. 重新在项目根目录执行 `./gradlew` 即可
8. 如果项目下不能访问 Gradle 可执行文件，参考 Q&A-2
9. 如果上面的那个 hash 值目录有多个（我怀疑多次 build 可能产生多个不同的 hash 值目录），而你也不确定是哪个目录，那好办，把相关文件在每个 hash 值目录都放一个即可。当然，我这么做很蠢，如果你知道怎么弄，不要吝啬，来来来骚年，提个 MR，完善这篇文章呗~

关于 `intellij | version`
```groovy
intellij {
    version '2018.1.4'
    plugins 'coverage' //Bundled plugin dependencies
}
```

## 2. 没有配置 Gradle 可执行文件的环境变量肿么办

这里提供两个解法：

1. 没有配？配呗！自己动手丰衣足食。如何配？搜索一下你就知道
2. 如果新建的插件项目没有 gradlew 文件，可以去随便一个 Android 项目下面拷贝这个文件，或者也可以在[这里](https://github.com/fbsum/HelloWorldPlugin)下载

# 关于本文档

1. 原文地址：http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system/prerequisites.html
2. 如果翻译有不妥之处（肯定有啦），欢迎提交 MR
3. Q&A 部分是我自己加的，主要是把自己遇到的一些坑分享给大家作参考
