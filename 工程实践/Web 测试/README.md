[![返回目录](https://i.postimg.cc/JzFTMvjF/image.png)](https://github.com/wx-chevalier/Awesome-CheatSheets)

# Web Test CheatSheet | Web 自动化测试概述

自动化测试(Automated Test)即指利用一系列自动化测试工具自动执行测试脚本、生成输出结果以及进行结果的回归对比。用户验收测试(User Acceptance Test, UAT)又被成为 Beta 测试、应用测试或者终端用户测试，其类似于端到端测试，即在真实环境中以真实的用户逻辑对应用进行完整地测试。

单元测试、端到端测试与集成测试，在单元测试中我们应该尽可能地独立于副作用；在集成测试中，我们应该尽可能地独立于数据。端到端测试(End-to-end Test)旨在完全真实的用户运行环境中模拟用户交互逻辑对于单元功能或某条用户逻辑线。

![](https://user-images.githubusercontent.com/5803001/38910163-a8a79dea-42fa-11e8-9504-288043044f02.png)

在笔者看来，目前我们基于 Mocha、Jest、Karma 这些测试框架一次编码多次运行的测试应该都是自动化测试，自动化测试与手动测试的区别在于自动化测试不会依赖于测试数据。在早期的测试，特别是需要依赖于外部数据的测试中，我们往往需要根据外部数据来修改测试用例以保证测试的准确性。譬如我们在测试。端到端测试侧重于介绍所谓的自动化浏览器相关的技术与框架，而集成测试则偏重于介绍测试用例的构建理念。

对于纯 JavaScript 相关的代码我们可以运行在 Node 环境中，往往从测试耗时与可操作性上来看会好上很多。
笔者个人是比较推荐尽可能地编写同构(Isomorphic)代码，即可以同时运行在 Node.js 环境与浏览器环境中的代码。譬如我们推荐使用 fetch 函数来进行数据抓取，如果我们直接在代码中使用浏览器自带的 fetch 或者 whatwg-fetch 这样强耦合于浏览器环境的 Polyfill，就会限制我们的代码必须运行在，其代码并不复杂，其只是在 package.json 进行了不同环境的入口声明。

# 背景

单元/组件测试、集成测试、端到端测试(E2ETest)共同构成了测试的三级金字塔，其中端到端测试是收益比较低，且最为复杂的一层。

端到端测试更多的是一种范式，本节即按照端到端测试的目标将其细分为不同的模块，进行阐述。

## 渲染测试

所谓的渲染测试，即是保证界面能够准确加载资源、完成基础的界面渲染、在多执行机/多节点部署场景下比较不同执行机的渲染结果。

渲染测试往往用于保证产品的基本可用性，同样也常常用于回归测试中保证多节点部署的一致性。渲染测试往往不关注与界面的具体逻辑或者细节，而是整体页面的差异性。典型的工具包括 [Blink-Diff](https://github.com/yahoo/blink-diff), [PhantomCSS](https://github.com/HuddleEng/PhantomCSS), [Gemini](https://github.com/gemini-testing/gemini) 等：

![](https://camo.githubusercontent.com/bd2fd498218cfbb9f7d417b05ef35e92272bde2f/68747470733a2f2f7261772e6769746875622e636f6d2f487564646c652f5068616e746f6d4353532f6d61737465722f726561646d655f6173736574732f696e74726f2d6578616d706c652e706e67)

## 功能/逻辑测试

功能/逻辑测试是端到端测试中最重要、应用最广泛的部分，也是最为困难的部分；在下文的[挑战](#挑战)一节中我们将会进行详细分析。

## 性能测试

端到端测试是性能测试的重要手段之一，最为著名的性能测试工具当属 [LightHouse](https://developers.google.com/web/tools/lighthouse/):

![](https://developers.google.com/web/tools/lighthouse/images/cdt-report.png)

## 跨浏览器/兼容性测试

早期的 IE 等浏览器兼容性、移动互联网时代的碎片化终端设备的兼容性，都是比较棘手的问题，像 [BrowserStack](https://www.browserstack.com/)、[Airtap](https://github.com/airtap/airtap) 这样的工具，都是致力于解决跨浏览器兼容性测试的问题。

## 混沌测试/Monkey Test

顾名思义，混沌测试即是通过混乱无序的操作来检测 Web 应用的鲁棒性，典型的工具譬如 [gremlins.js](https://github.com/marmelab/gremlins.js):

![](https://camo.githubusercontent.com/130e101ee69d4d9b6f065df0a0404c861eb5ce18/687474703a2f2f7374617469632e6d61726d656c61622e636f6d2f746f646f2e676966)

## AI 测试

目前有部分自动化测试工具，宣称能够用人工智能/机器学习算法，来解决下文提及的[挑战](#挑战)。

# 对比

## 人工测试

自动化测试目前已经成为共识，是 DevOps 与持续集成 CI 的重要基石；但是多年的实践与案例分析表明，自动化测试并不能完全替代手工测试，其投入产出比在很多场景下仍然低于人工测试。

人工测试最大的优势在于灵活性与可判断性，能够从最终用户的角度出发，面向不同的业务逻辑迅速判断用例结果是否符合预期。

人工测试的缺陷在于不可重复性，无法用于快速回归测试。

## 自动化测试-控制脚本

譬如 [SeleniumHQ/WebDriverJs](https://github.com/SeleniumHQ/selenium/wiki/WebDriverJs) 这样的工具，允许开发者将测试用例以代码的方式固化，并进行重复运行：

```js
const { Builder, By, until } = require('selenium-webdriver');
new Builder()
  .forBrowser('firefox')
  .build()
  .then(driver => {
    return driver
      .get('http://www.google.com/ncr')
      .then(_ => driver.findElement(By.name('q')).sendKeys('webdriver'))
      .then(_ => driver.findElement(By.name('btnK')).click())
      .then(_ => driver.wait(until.titleIs('webdriver - Google Search'), 1000))
      .then(_ => driver.quit());
  });
```

## 自动化测试-Acceptance Test

在编写测试用例的时候，我们希望能够让更多地非技术人员/领域专家参与进来，由此催生了所谓的 Acceptance Test 的理念，即使用非技术性的语言来描述系统的组件或者操作逻辑。Acceptance Test 是所谓 Behaviour-Driven Development(BDD) 的重要保障，[Ruby/Cucumber](https://cucumber.io/) 是最为经典的 BDD 框架，其允许我们使用日常的语言来描述场景，然后将其转化为可执行的脚本：

```yaml
Feature: Is it Friday yet?
  Everybody wants to know when it's Friday

  Scenario: Sunday isn't Friday
    Given today is Sunday
    When I ask whether it's Friday yet
    Then I should be told "Nope"
```

[CodeceptJS](https://github.com/codeception/codeceptjs/) 是基于 Node.js 的 Acceptance Test 工具，其同样提供了 DSL 语法：

```js
Feature('CodeceptJS demo');

Scenario('check Welcome page on site', I => {
  I.amOnPage('/');
  I.see('Welcome');
});
```

## 自动化测试-生成式自动化脚本

[Selenium IDE](https://www.seleniumhq.org/projects/ide/) 或者 [其 Chrome 插件](https://chrome.google.com/webstore/detail/selenium-ide/mooikfkahbdckldjjndioackbalphokd?hl=en) 都允许以如下形式自动记录用户的行为操作，并且将其转化为可执行的步骤：

![](https://www.seleniumhq.org/projects/ide/selenium-ide.png)

其优势在于能够尽可能地让非技术人员参与进来，缺陷在于灵活性、可控性非常差。
