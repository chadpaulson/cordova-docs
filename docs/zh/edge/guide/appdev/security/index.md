---

许可证： 下一个或多个参与者许可协议许可向阿帕奇软件基金会 (ASF)。 请参阅分布式与此工作为版权的所有权有关的其他信息的通知文件。 ASF 许可证，此文件到你根据 Apache 许可证，2.0 版 （"许可证"） ；您不可能使用此文件除了符合许可证。 您可能会获得在许可证的副本

           http://www.apache.org/licenses/LICENSE-2.0 除非适用的法律要求或书面同意，否则按照该许可证分发的软件分发上"按原样"的基础，而不担保或条件的任何种类的明示或暗示。  请参阅许可证规定的权限和限制的特定语言
    

## 根据许可证。

# 安全指南

以下指南包括开发科尔多瓦的应用程序时，应考虑一些安全最佳做法。 请务必注意安全是一个非常复杂的主题，因此本指南不是详尽无遗。 如果你认为你可以贡献本指南，请随时在科尔多瓦的 bug 追踪器下["文档"][1]中文件的问题。 本指南旨在适用于一般科尔多瓦发展 （所有平台），但将指出特定于平台的特殊注意事项。

 [1]: https://issues.apache.org/jira/browse/CB/component/12316407

## 本指南讨论了下列主题：

*   白名单
*   Iframe 和回调 Id 机制
*   证书寄
*   自签名的证书
*   加密的存储
*   一般提示
*   推荐的文章和其他资源

## 白名单

*   阅读和理解，白名单指南

*   默认情况下，白名单上新创建的应用程序将允许通过的每个域访问 `<access>` 标记： `<access origin="*">` 如果您想要针对白名单中，评估的网络请求，则它是重要的是要改变这和只允许，您需要访问的域。 这可以通过编辑应用程序级配置文件位于： `{project}/config.xml` （最近的项目） 或 `{project}/www/config.xml` （旧项目）

*   Android 的白名单中科尔多瓦 2.9.x 被认为安全，然而，它被发现是否 foo.com 列入白名单中，foo.com.evil.com 将能够通过白名单测试。 这固定在科尔多瓦 3.x。

*   域白不工作上 Android API 10 和下方和 WP7/8 iframe 和客户端代码。 这意味着攻击者可以加载任何域中 iframe 和科尔多瓦 JavaScript 对象和相应的本机 Java 对象，可以直接访问该 iframe 内的页面上的任何脚本。 构建这些平台的应用程序时，应考虑到这种情况。 在实践中这意味着确保目标高于 10，Android API 和如果可能不使用 iframe 加载外部内容-使用 inAppBrowser 插件或其他第三方插件。

## Iframe 和回调 Id 机制

如果内容提供在 iframe 从白名单中的域中，该域将可以访问到本机的科尔多瓦桥。 这意味着，如果白名单中的第三方广告网络和服务通过 iframe 的那些广告，它有可能是恶意的广告将能够打破 iframe 并执行恶意操作。 因此，您通常不应使用 iframe 除非你控制的服务器的承载的 iframe 内容。 此外请注意有第三方插件提供支持的广告网络。 注意此语句不是真正的 iOS，拦截一切包括 iframe 的连接。

## 证书寄

科尔多瓦不支持真正的证书固定。 对此的一个主要障碍是证书的缺乏在 android 系统中的本机 Api 拦截 SSL 连接执行检查服务器。 （虽然有可能要做证书寄于 Android 在 Java 中使用 JSSE，c + +，编写在 android 系统上的 web 视图和 web 视图为你处理服务器连接，所以它是不可能有使用 Java 和 JSSE)。因为 Apache 科尔多瓦要跨多个平台提供一致的 Api，不具有能力的一个主要平台打破了这种一致性。

有种方法近似证书锁定，如检查服务器的公钥 （指纹） 预期值，当您的应用程序启动时或在其他不同的时间，在您的应用程序的生存期内。 有第三方插件可供能不能做到的科尔多瓦。 然而，这不是真实证书将锁定，将自动验证每个连接到服务器上的预期值相同。

## 自签名的证书

不建议在您的服务器上使用自签名的证书。 如果你渴望 SSL，那么强烈建议您的服务器具有已正确地签署了著名的 CA （证书颁发机构） 的证书。 不能做真正的证书寄使这一重要。

原因是接受自签名的证书绕过证书链验证，它允许任何服务器证书才被视为有效的设备。 这将打开沟通的人在中间的攻击。 它变得非常容易为黑客不仅拦截并读取在设备和服务器之间的所有通信，但也要修改通信。 设备永远不会知道这种情况发生，因为它不会验证服务器的证书由受信任的 CA 签署。 该设备具有服务器是它期望的人没有证据证明。 因为做的人在中间攻击的易用性，接受自签名的证书才略微比只在不受信任的网络上运行而不是 https 的 http。 是的交通将会被加密，但它可能会用密钥加密从一个男人-中--中间，所以拦截中可以访问的一切，所以加密是无用除了对被动的观察员。 用户信任 SSL 以是安全的和这将故意做出它不安全的所以，SSL 利用成为具误导性。 如果这将受信任的网络上使用 （即，您是完全控制企业内部），然后仍不建议使用自签名的证书。 在受信任的网络中的两项建议是因为网络本身是受信任的只是使用 http 或获取由受信任的 CA （不自签名） 签署的证书。 网络是受信任的或者它不是。

在这里所描述的原则不是特定于 Apache 科尔多瓦，它们适用于所有客户端-服务器通信。

当运行时科尔多瓦在 android 系统上，使用 `android:debuggable="true"` 应用程序中清单将允许 SSL 错误，例如证书链验证错误的自签名证书。 所以您可以在此配置中，使用自签名的证书，但这不是一种配置，您的应用程序是在生产时，应使用。 意思是，只有在应用程序开发期间使用。

## 加密的存储

## 一般提示

### 不要使用 Android 姜饼 ！

*   设置您的 min-目标-sdk 级别高于 10。API 10 是姜饼，和姜饼由谷歌或设备制造商，不再受支持，因此不推荐由科尔多瓦队。 
*   姜饼已被证明是不安全和最大的一个有针对性的移动开放源码软件[http://www.mobilemag.com/2012/11/06/andriod-2-3-gingerbread-security/][2]. 
*   在 Android 上的白名单不工作与姜饼或更低。 这意味着攻击者可以加载，然后会对所有的科尔多瓦 Api 的访问，可以使用该访问权限来窃取个人数据、 发送 SMS 消息到溢价率的数字，以及执行其他恶意行为的 iframe 中的恶意代码。 

 [2]: http://bgr.com/2012/11/06/android-security-gingerbread-malware/

### 使用 InAppBrowser 的外部链接

*   打开任何外部网站的链接时，请使用 InAppBrowser。 这是比白一个域名和直接在您的应用程序中包括的内容，因为 InAppBrowser 将使用本机浏览器的安全功能，并不会给网站访问到您的科尔多瓦环境安全得多。 即使您信任的第三方网站，并将其包括在您的应用程序中直接，那第三方网站可能链接到恶意 web 内容。 

### 验证所有用户输入

*   始终验证您的应用程序接受的任何和所有输入。 这包括用户名、 密码、 日期、 上载的媒体等。 因为攻击者可以操纵您的 HTML 和 JS 资产 （无论是通过反编译您的应用程序或使用像 chrome://inspect 这样的调试工具），这应该也执行验证您在服务器上，尤其是在将数据提交给任何后端服务之前。 
*   其他来源数据应进行的验证： 用户文档、 联系人、 推式通知

### 不缓存敏感数据

*   如果用户名、 密码、 地理定位信息和其他敏感数据被缓存，然后它可以潜在检索以后由未经授权的用户或应用程序。

### 除非你知道你在做什么，否则不要使用 eval()

*   JavaScript 函数 eval() 有被虐待的历史悠久。不正确地使用它，可以打开您的代码注入攻击，调试困难和代码执行速度变慢了。 

### 不要假定您的源代码是安全的

*   因为从 HTML 和 JavaScript 的资产在本机容器中获取打包生成的科尔多瓦应用程序，不应考虑您的代码是安全的。 它是可能要进行反向工程科尔多瓦应用程序。 

## 推荐的文章和其他资源

*   [HTML5 安全作弊，详细说明了如何确保您的 HTML5 应用程序][3]
*   [Phonegap 条设备安全问题，例如，使用加密的数据][4]
*   [关于 web 视图中的众所周知的安全缺陷白皮书基于混合应用程序][5]

 [3]: https://www.owasp.org/index.php/HTML5_Security_Cheat_Sheet
 [4]: https://github.com/phonegap/phonegap/wiki/Platform-Security
 [5]: http://www.cis.syr.edu/~wedu/Research/paper/webview_acsac2011.pdf