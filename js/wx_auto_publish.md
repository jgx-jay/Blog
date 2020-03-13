## 前言

自从微信小程序问世以来，为了在小程序红利期占据有利地位，各大公司将小程序作为非常有竞争力的流量投放渠道，大厂商也在逐渐接入自己的小程序等开放平台，如支付宝小程序,百度智能小程序, 头条小程序, H5, 快应用...

坊间也诞生了不少小程序跨平台编译器，而我们今天谈论的是小程序开发周期的最后一公里：自动化部署小程序。

## 痛点
先来看看在没有自动化部署小程序的流程：

1. 人工IDE上传代码。
2. 人工登录微信后台。
3. 人工提交审核。
4. 等待审核。
5. 人工上线。

从上面的流程可以看出：步骤1、2、3、5是完全手工的。如果只部署一个小程序平台还好，而现实是，越来越多的公司在多个小程序平台进行渠道投放。如何解放在多个平台手工部署的生产力，是我们面临的一个大问题。

## 我们如何做的
去哪儿网在自动化部署小程序的实践中，经历了2个不同技术方案。

1. hack 小程序 IDE。
2. 使用官方提供的接口。

早期小程序官方未提供对应接口，我们是通过像IDE中写入 hack 代码，来拦截 request 进行对应操作。虽然能达到目的，但存在两个很大的问题：

1. 不稳定：小程序的IDE也不断的升级，优化，安全措施也越发完善。
2. 成本高：我们需要随时跟进小程序的IDE的升级，如果升级后与我们的 hack 方式产生冲突，我们必须立马响应寻求新的方案。

幸运的是，后来小程序官方提供了对应的接口，我们技术组立马积极响应，制定新的技术方案 ，力求稳定可靠。

### 整体流程
![ddd](https://user-images.githubusercontent.com/8794029/63823518-b3272b00-c986-11e9-893d-b4562172c32e.jpg)


#### 1. IDE 自动上传代码
官方提供可靠的接口，可实现 IDE 的代开工具、自动登录、预览、上传等操作。
写好脚本, 便可实现预览或上传版本, 但是官方并没有提供提交审核和上线的接口.
提交审核和上线都是要在官方的后台进行操作的, 所以就需要把这个部分做成可自动化的.

#### 2. 公众平台后台自动提交审核/上传
利用 web 自动化测试框架 nightmare, 在权限合法的情况下模拟审核和上线。
实现的原理不复杂, 根据nightmare的官方文档(https://github.com/segmentio/nightmare)就可以实现
当然, 类似的框架也可以实现. 期间要缓存一下 cookie 和 token, 以便下次使用, 有效期是 1 天.

![image](https://user-images.githubusercontent.com/8794029/63823768-8e7f8300-c987-11e9-8e22-a15d8e697c65.png)


#### 3. 自动扫码认证
对于微信小程序而言, 在 ide 登录m, 后台登录, 开发权限 和发布上线时都是需要进行扫二维码认证。
所以如何解决扫码的问题成为了整个发布过程至关重要的一步.
如何实现这一步的自动化？我们提供了可靠的二维码自动扫码服务 [qscan](http://ued.qunar.com/qscan/index.html)。

通过官方提供的接口获取到二维码，然后同步给 qscan, qscan 来完成自动扫码认证。
qscan 使用了appium 框架来实现对手机的控制, 由于微信只可以用手机摄像头来识别二维码,
所以我们的扫码机就变成了这样.

![633d4fe980e118170a33e56cdda600e6](https://user-images.githubusercontent.com/8794029/63823324-fe8d0980-c985-11e9-8b7e-17b4f823f2bc.jpg)

#### 4. 微信开发权限管理
通过以上的思路, 我们也开发了一个可以自动完成微信开发权限的授权.
原理类似, 通过自动化框架和爬虫来实现对权限的操作, 在利用自动化扫码工具完成授权.
现在只需要填写个人信息然后申请即可, 管理人员只需要审批然后点击同步即可,
完全省掉了重复繁琐的登录扫码过程, 而且我们还可以对应 qtalk id, 可直观的管理和统计.
也方便删除和替换过期人员权限和离职人员权限.

![WX20190830-103431](https://user-images.githubusercontent.com/16551050/63989129-cca8ae00-cb11-11e9-9ead-c0ef14e5ef44.png)


## 效果
去哪儿网在微信、支付宝、百度都上线了小程序，并且各小程序发版频次不尽相同，这三个小程序每周发版频次在8次左右。可得出，我们公司单人一年节约115.2个工作日，极大的提升了效率。
结合自动化测试等工具，我们能有效的打通整个小程序的 CI/CD 流程，提升快速响应的能力和商业竞争力。

























