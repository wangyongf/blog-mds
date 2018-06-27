# IntelliJ Platform 是什么？

IntelliJ Platform 不单单指某一款产品，而是指一个打造 IDE 的基础平台。JetBrains 的产品都是基于 IntelliJ Platform 构建的，比如 [IntelliJ IDEA](https://www.jetbrains.com/idea/), [WebStorm](https://www.jetbrains.com/webstorm/), [RubyMine](https://www.jetbrains.com/ruby/), [DataGrip](https://www.jetbrains.com/datagrip/), [Rider](https://www.jetbrains.com/rider/). 与此同时，IntelliJ Platform 还是开源的，第三方可以借助 IntelliJ Platform 来打造自己的 IDE 产品，比如由 Google 打造的 [Android Studio](https://developer.android.com/studio/index.html)。

IntelliJ Platform 为这些 IDE 丰富的语言工具链工具支持提供了所有的基础设施。IntelliJ Platform 是一个组件驱动的、跨平台的 JVM 应用程序，自带一个高级用户界面工具箱，支持创建工具窗口、树状视图和支持快速搜索的列表组件等，同时还支持弹出式菜单和对话框等。

此外，IntelliJ Platform 还提供了一个图片编辑器和一个高级文本编辑器，这个高级文本编辑器支持语法高亮、代码折叠、代码自动完成以及其他一些高级文本编辑特性。

不仅如此，IntelliJ Platform 还提供了一系列可扩展的 API，用了这些可扩展的 API，我们可以打造一些公共的 IDE 功能特性，例如一个新的项目模型，或者一个新的构建系统等。它也支持了丰富的调试功能，提供语言无关的高级断点功能，支持查看调用栈，变量观察窗口，以及表达式求值等高级特性。

说了这么多，其实 IntelliJ Platform 的所有能力都来源于 「程序结构化接口-the Program Structure Interface(PSI)」。PSI 是提供了文件解析和丰富的代码语法句法模型构建支持的一系列功能集合，还支持缓存解析后的数据。有了这种能力，IntelliJ Platform 能够提供大量的功能，从文件、类型、符号等的快速跳转，到代码自动完成窗口提示、find usages、代码审查、基于代码重写的代码快速修复抑或是重构，等等一系列的功能。

IntelliJ Platform 内建了代码解析器和 PSI 模型，覆盖了很多种不同类型的语言，IntelliJ Platform 的复合性意味着它的可扩展性，能够扩展支持其他的语言。

## 插件系统

基于 IntelliJ Platform 构建的 IDE 产品都是集成度很高的应用程序，因为 IntelliJ Platform 本身就支持了组件创建，类的依赖注入等。IntelliJ Platform 完全支持插件，而且 JetBrains 提供了一个[插件仓库](https://plugins.jetbrains.com/)，用户可以给这个插件仓库贡献插件，这些插件可以是只支持某一款 IntelliJ Platform 产品的，也可以是支持多款产品的。当然，你也可以自己托管一个插件仓库，只给自己的插件仓库贡献插件。

插件可以在很多方面扩展 IntelliJ Platform，可以简单到仅仅添加一个菜单项，也可以复杂到提供对某种特定语言、特定的构建系统或者调试器的支持。IntelliJ Platform 中许多已经的功能特性都是通过插件的形式提供的，因此，基于 IntelliJ Platform 打造的终端产品是否支持这些功能特性完全取决于产品的需要。你可以在[插件](https://www.jetbrains.org/intellij/sdk/docs/basics.html)章节查看更多关于插件的知识。

IntelliJ Platform 是一个 JVM 应用程序，基本使用 Java 和 Kotlin 打造而成。如果你想给基于 IntelliJ Platform 的 IDE 产品编写插件，那你得对这些语言和相关的工具链比较熟悉。截至目前，还不支持使用非 JVM 语言对 IntelliJ Platform 进行扩展。

## 关于开源

整个 IntelliJ Platform 是基于 [Apache license](https://github.com/JetBrains/intellij-community/blob/master/LICENSE.txt) 开源许可证进行开源的，它的源代码托管在 [Github](https://github.com/JetBrains/intellij-community) 上。

尽管在本系列文档中一直把 IntelliJ Platform 看做一个独立的实体，但实际上并没有 IntelliJ Platform 这样一个 GitHub 仓库。相反，IntelliJ Platform 基本可以看做是 IntelliJ IDEA 社区版。IntelliJ IDEA 社区版是 IntelliJ IDEA 旗舰版的免费开源版本，它的 GitHub 仓库是 [JetBrains/intellij-community](https://github.com/JetBrains/intellij-community)

IntelliJ IDEA 旗舰版是 IntelliJ IDEA 社区版的超集。它基于社区版打造，但是包含了很多闭源的插件（[社区版和旗舰版的特性比较](https://www.jetbrains.com/idea/features/editions_comparison_matrix.html)）。类似的，其他的一些 IDE 产品，比如 WebStorm 和 DataGrip 等也是基于 IntelliJ IDEA 社区版打造的，只不过内建的插件集不同。

插件可以面向不同的产品，因为每一个 IDE 产品都会包含 IntelliJ IDEA 社区版的基本功能以及一系列不同的插件。我们把这个称之为 IntelliJ Platform。

通常来说，基于 IntelliJ Platform 打造的 IDE 产品都会将 `intellij-community` 作为一个 Git submodule，然后内置一个配置，说明这个 IDE 产品包含了哪些插件，这些插件中哪些是来自 `intellij-community` 的，哪些又是专门定制的插件。IntelliJ IDEA 旗舰版的开发团队大概就是这么工作的：一边向定制的插件贡献代码，一边向 IntelliJ Platform 贡献代码。

当然，既然 IntelliJ Platform 是开源的，我们也会接受三方的 [pull requests](https://github.com/JetBrains/intellij-community/pulls)，我们开源的目的不仅仅是 show you the code，而是想和大家一起促进 IntelliJ Platform 的发展。IntelliJ Platform 使用 [YouTrack](https://youtrack.jetbrains.com/issues/IDEA) 进行项目的 issue 管理，如果你想给 IntelliJ Platform 的开发贡献自己的力量，你应该现在 YouTrack 上开一个 issue，描述一下你想做些什么，而不是一上来就开始撸代码，通过 issue 的方式，开发团队可以在你贡献代码之前给你一些反馈和建议。有关贡献代码的更多细节参见 [向 IntelliJ Platform 贡献代码](https://www.jetbrains.org/intellij/sdk/docs/basics/platform_contributions.html) 章节。

## Rider

有一点需要注意，[Rider](https://www.jetbrains.com/rider/) 使用 IntelliJ Platform 的方式有别于其他基于 IntelliJ Platform 打造的 IDE。

Rider 基于 IntelliJ Platform 提供了面向 C# 和 .NET IDE 的用户界面接口，包括标准的 IntelliJ 编辑器、工具窗口、调试工具等等。此外，Rider 还集成了标准的 Find Usages 和 Search Everywhere 功能，以及包括代码自动完成、语法高亮在内的其他功能。

不同的是，Rider 并不会为 C# 文件创建完整的 PSI(语法句法)模型。相反，Rider 重用了 [ReSharper](https://www.jetbrains.com/resharper/) 以提供语言层面的功能。所有的 C# PSI 模型，所有的 inspections 和代码重写（比如快速修复和重构）等，都是在命令行版本的 ReSharper 中完成的。综上所述，如果你要给 Rider 创建一个插件，这个插件应该包含两部分：第一个部分负责基于 IntelliJ Platform 来呈现用户界面，第二个部分则是基于 ReSharper 作为后端来解析 C# PSI。

幸运的是，许多插件可以很简单地与 ReSharper 一起协同工作，Rider 负责展示 inspections 和代码自动完成的结果展示，许多插件都不需要重新编写 前端的 IntelliJ UI 组件。关于这个的更多细节参见特定产品章节。
