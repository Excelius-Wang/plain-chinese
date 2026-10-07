# plain-chinese（平实中文）

[![skills.sh](https://skills.sh/b/Excelius-Wang/plain-chinese)](https://skills.sh/Excelius-Wang/plain-chinese)

让 AI 写的中文合乎中文的说法，读的人一遍就懂。

Writing rules for AI agents that write in Chinese: no Europeanized syntax, no translationese, easy to follow on the first read.

请求：「帮我写一段周报。本周做了这些：登录接口平均响应时间从 800ms 降到 200ms；修了 3 个线上 bug；和产品对齐了会员页改版的需求。下周计划接入短信验证码登录。」

<table>
<tr><th width="50%">不加规则</th><th width="50%">加了规则</th></tr>
<tr><td valign="top">

本周完成登录接口性能优化，平均响应时间由 800ms 降至 200ms，降低 75%；修复 3 个线上 bug，提升服务稳定性；与产品对齐会员页改版需求，明确后续开发方向。下周计划接入短信验证码登录，完善登录流程。

</td><td valign="top">

本周将登录接口的平均响应时间从 800ms 降至 200ms，修复了 3 个线上 bug，并和产品确认了会员页改版需求。下周计划接入短信验证码登录。

</td></tr>
</table>

不加规则的那份多了「提升服务稳定性」「明确后续开发方向」「完善登录流程」，材料里都没有，是凑上去的。加了规则的只写做了的事。

更多场景见下面的[例子](#例子)。在 36 个真实请求的盲评里，72 对回答中加规则更好的 38 对，不加更好的 2 对，见[效果](#效果)。

## 它管什么

| 规则 | 不这样写 | 这样写 |
|---|---|---|
| 动词直接说 | 对代码进行验证 | 验证代码 |
| 让人或事当主语 | 收入的减少导致了他更换工作 | 他收入少了，就换了工作 |
| 少用「被」 | 方案被大家通过了 | 方案大家通过了 |
| 形容词直接当谓语 | 这一步是至关重要的 | 这一步很关键 |
| 「的」前面别挂太长 | 我见到一个长得像你哥哥、说话也像你哥哥的人 | 我见到一个人，长得像你哥哥，说话也像 |
| 「一个」能删就删 | 他是一个很细心的人 | 他很细心 |
| 少用介词架子 | 通过对数据的分析，我们发现…… | 分析了数据，我们发现…… |
| 顺序摆对了，不加「因为」 | 我没去，因为我头晕 | 我头晕，没去 |
| 句首不套「在……中」 | 在本文中，我们将…… | 这篇文章讲…… |
| 后缀别堆着用 | 可读性很高 | 好读 |
| 说完就停 | 综上所述，这一改动意义重大。 | （删掉） |

上面是「句子要像中文」。规则还有两部分：「让人一遍就懂」管怎么组织，先回答问题，讲概念先举例，推理不跳步；「别改过头」管分寸，事实、数字和作者的把握程度不动，材料里没有的不补，公文、论文、书信照各自的规范写，翻译以忠实为先。

全文不到 50 行，见 [`skills/plain-chinese/SKILL.md`](skills/plain-chinese/SKILL.md)。

## 安装

```bash
npx skills add Excelius-Wang/plain-chinese -g
```

用的是 [skills](https://github.com/vercel-labs/skills) 命令，支持 Codex、Claude Code 和 Cursor，装的时候会问你装给哪几个。`-g` 表示装到用户目录，所有项目都能用。以后更新执行 `npx skills update`。

装好以后不用做别的。用中文提问或者让 agent 写东西时，它会自己判断要不要读这份规则。想确保用上，可以点名：Codex 里写 `$plain-chinese`，Claude Code 和 Cursor 里输入 `/plain-chinese`。

<details>
<summary>想让每次对话都照规则写：追加进全局指令</summary>

装成 skill 时，agent 不一定每次都读规则。想让每次中文回答都照规则写，就把规则正文追加进全局指令。先克隆仓库：

```bash
git clone https://github.com/Excelius-Wang/plain-chinese.git
cd plain-chinese
```

然后只执行你用的客户端那一条。命令会去掉 `SKILL.md` 开头的元信息，把正文追加到全局指令文件末尾。重复执行会追加两遍；以后更新规则，先删掉原来追加的那段。

```bash
# Codex
mkdir -p "${CODEX_HOME:-$HOME/.codex}"
{ echo; awk 'f; /^---$/ && ++n == 2 { f = 1 }' skills/plain-chinese/SKILL.md; } >> "${CODEX_HOME:-$HOME/.codex}/AGENTS.md"

# Claude Code
mkdir -p ~/.claude
{ echo; awk 'f; /^---$/ && ++n == 2 { f = 1 }' skills/plain-chinese/SKILL.md; } >> ~/.claude/CLAUDE.md
```

Cursor 没有全局指令文件：打开 Customize → Rules，把 `SKILL.md` 第二个 `---` 以下的内容贴进 User Rules。User Rules 只在 Agent 对话里生效。

</details>

<details>
<summary>不用 npx，手动安装</summary>

克隆仓库，把 skill 目录软链接过去。Codex 和 Cursor 都读 `~/.agents/skills`，Claude Code 读 `~/.claude/skills`。

```bash
git clone https://github.com/Excelius-Wang/plain-chinese.git
cd plain-chinese

# Codex 和 Cursor
mkdir -p ~/.agents/skills && ln -s "$PWD/skills/plain-chinese" ~/.agents/skills/plain-chinese

# Claude Code
mkdir -p ~/.claude/skills && ln -s "$PWD/skills/plain-chinese" ~/.claude/skills/plain-chinese
```

- 仓库移走或删掉，链接就失效了。
- 目标位置已经有 `plain-chinese` 的，先看清它是什么再处理，别直接再链一次，不然可能在旧目录里面又套一层链接。
- Codex 也读 `${CODEX_HOME:-$HOME/.codex}/skills`，两处只链一处，不然列表里可能出现两份。Cursor 也会读 `~/.claude/skills`，同时用 Claude Code 和 Cursor 的，Cursor 里也可能看到两份。

安装目录和菜单在 2026 年 10 月按官方文档核对过：[Codex](https://developers.openai.com/codex/skills)、[Claude Code](https://code.claude.com/docs/en/skills)、[Cursor skills](https://cursor.com/docs/skills)、[Cursor rules](https://cursor.com/docs/rules)。

</details>

## 例子

下面是另外拟的一批常见请求，用 Codex（gpt-6-astra，推理强度 medium）回答。每个请求加规则和不加规则各答两遍，两边都关掉了作者装的其他中文写作 skill。每组放第一遍的回答，第二遍的差别也一样。

15 个请求里，差别明显的是开头的周报和下面这几组。问答、讲解、解释报错这类回答，两边差别不大，加了规则的主要是加粗少一些。

### 给导师写邮件

请求：「帮我给导师写封邮件：下周三的组会我要请假，那天要去杭州参加一个学术会议。我的汇报想挪到下下周。」

<table>
<tr><th width="50%">不加规则</th><th width="50%">加了规则</th></tr>
<tr><td valign="top">

**邮件主题：下周三组会请假及汇报调整申请**

老师您好：

因下周三需前往杭州参加学术会议，我想向您申请请假，无法参加当天的组会。同时，想请问能否将我的汇报调整至下下周？我会提前做好准备。

给您和组会安排带来不便，敬请谅解。感谢老师！

祝好！

[你的姓名]

</td><td valign="top">

主题：下周三组会请假及汇报调整申请

老师您好！

下周三我要去杭州参加一个学术会议，想向您请假，缺席当天的组会。我的汇报想调整到下下周，您看是否方便？

谢谢老师！

祝好！

[姓名]

</td></tr>
</table>

不加规则的用了「因……需前往」「申请请假」「敬请谅解」，像在写公文。加了规则的直接说「下周三我要去杭州参加一个学术会议，想向您请假」，称呼、问候和落款一样不少。

### 发群通知

请求：「帮我写一条群通知：周五下午 3 点在 302 会议室开项目复盘会，大概一个小时，每个人带上自己负责模块的问题清单。」

<table>
<tr><th width="50%">不加规则</th><th width="50%">加了规则</th></tr>
<tr><td valign="top">

各位好，周五下午 3 点在 302 会议室召开项目复盘会，预计 1 小时。请大家提前整理并带上各自负责模块的问题清单，准时参加。谢谢！

</td><td valign="top">

大家好，周五下午 3 点在 302 会议室开项目复盘会，预计一个小时。请大家带上自己负责模块的问题清单，准时参加。

</td></tr>
</table>

「召开」换成了「开」。「提前整理」是不加规则的那份自己加的，请求里没有。

### 写技术博客的开头

请求：「帮我写一段技术博客的开头，讲我们为什么把一个用了五年的单体服务拆成了几个微服务。主要原因：发布一次要等所有模块一起测，太慢；一个模块出问题，整个服务都会挂。」

<table>
<tr><th width="50%">不加规则</th><th width="50%">加了规则</th></tr>
<tr><td valign="top">

一个单体服务用了五年，为什么我们最终决定把它拆成几个微服务？原因很具体：改动一个模块，要等所有模块一起完成测试才能发布；一个模块出了故障，又可能把整个服务拖垮。

单体架构曾经让开发和部署都很直接，但随着业务增长，模块之间的绑定逐渐成了负担。即使只是一次小改动，也要走完整套发布流程；原本局部的问题，也可能演变成整个服务不可用。我们希望通过这次拆分，让不同模块能够独立测试、独立发布，同时缩小故障的影响范围。当然，拆成微服务也会带来新的复杂度。这篇文章就从这两个痛点出发，聊聊我们为什么拆，以及如何确定拆分的边界。

</td><td valign="top">

这个单体服务我们用了五年，最近决定把它拆成几个微服务。原因很直接：发布太慢，故障牵连太广。哪怕只改了一个模块，也得等所有模块一起测完才能发布；一个模块出了问题，整个服务都可能挂掉。我们希望拆开之后，各个服务能独立测试、独立发布，也能把故障限制在更小的范围里。这篇文章就聊聊这次拆分的取舍。

</td></tr>
</table>

不加规则的用设问开头，两个原因前后各说了一遍，中间还有「随着业务增长」「演变成」「通过这次拆分」这类说法。加了规则的先说拆了，再说为什么，一段说完。

### 在群里说明延期

请求：「帮我写一段话发到团队群里，解释这次版本为什么要推迟一周上线：支付回调有个偶发的重复扣款问题，还没查清原因。」

<table>
<tr><th width="50%">不加规则</th><th width="50%">加了规则</th></tr>
<tr><td valign="top">

这次版本上线计划需要推迟一周。目前发现支付回调环节存在偶发的重复扣款问题，根因还在排查中。考虑到涉及用户资金安全，我们需要在定位并修复问题、完成回归验证后再上线。接下来会优先推进排查，及时在群里同步进展；如果验证结果影响新的上线时间，也会提前说明。辛苦大家相应调整安排。

</td><td valign="top">

这次版本上线要推迟一周。支付回调目前存在偶发的重复扣款问题，原因还没查清。重复扣款会直接影响用户，我们需要先查明原因、修复问题并验证，再安排上线。后续有进展会及时同步；如果一周内还没解决，也会提前说明，重新评估上线时间。

</td></tr>
</table>

「根因还在排查中」「考虑到涉及用户资金安全」「在定位并修复问题、完成回归验证后再上线」，换成了「原因还没查清」「先查明原因、修复问题并验证，再安排上线」。

### 英译中

请求：「把这段翻译成中文：Most of the time, the hardest part of debugging is not fixing the bug but finding it. By the time a symptom shows up in production, its cause may be buried several layers away, in a change that was made weeks earlier and reviewed by someone who has since moved to another team.」

<table>
<tr><th width="50%">不加规则</th><th width="50%">加了规则</th></tr>
<tr><td valign="top">

大多数时候，调试最难的不是修复 bug，而是找到它。等到问题在生产环境中显现时，根源可能藏在好几层逻辑之外，源于几周前的一次改动，而当时审核这次改动的人，如今可能已经调到了另一个团队。

</td><td valign="top">

大多数时候，调试最难的不是修复问题，而是找到问题。等到生产环境出现异常，原因可能藏在好几层之外，出在几周前的一次改动里，而当时审核那次改动的人如今已经调到了另一个团队。

</td></tr>
</table>

不加规则的「在生产环境中显现时」是照着 shows up in production 的结构译的，后半句还多了一个原文没有的「可能」。加了规则的说「等到生产环境出现异常」，「已经调到了另一个团队」和原文一样肯定。

### 英译中：论文摘要

这一组来自盲评。原文是 Simon 等人 2026 年论文摘要里的一段：

> In particular, we anticipate a symbiotic and mutually supportive relationship between learning mechanics and the developing discipline of mechanistic interpretability. Where mechanistic interpretability aims to be the biology of deep learning, learning mechanics should aspire to be its physics, mirroring the complementary relationship between biology and physics in the natural sciences.

<table>
<tr><th width="50%">不加规则</th><th width="50%">加了规则</th></tr>
<tr><td valign="top">

尤其是，我们预期学习力学与正在发展的机制可解释性学科之间，将形成共生互助的关系。如果说机制可解释性旨在成为深度学习的生物学，那么学习力学就应当立志成为深度学习的物理学，正如自然科学中生物学与物理学之间的互补关系。

</td><td valign="top">

我们尤其期待，学习力学与正在发展的机制可解释性学科形成共生、相互支持的关系。机制可解释性旨在成为深度学习的“生物学”，学习力学则应立志成为深度学习的“物理学”，两者相辅相成，就像自然科学中的生物学与物理学一样。

</td></tr>
</table>

「尤其是，」照着 In particular 译。「正如……之间的互补关系」照搬了 mirroring the complementary relationship，「正如」后面只跟了一个名词，不成句。加了规则的改成「两者相辅相成，就像……一样」。

## 效果

在 Codex 上用 36 个真实请求做了盲评，加规则和不加规则各答两遍，一共 72 对。加规则更好的 38 对，不加更好的 2 对，其余差不多或者两次评审结论不一致。差别最大的是英译中和论文。

这组数据测的是 v0.1.0 之前的正文。v0.2.0 新加的「一个」、因果词、「它」、冒号和分号几条，还没有重跑盲评。

<details>
<summary>分项结果、测法和 skill 触发测试</summary>

| 评哪一项 | 加规则更好 | 不加更好 | 差不多 | 不稳 |
|---|---|---|---|---|
| 总体更想要哪份 | 38 | 2 | 30 | 2 |
| 更像中文 | 19 | 0 | 53 | 0 |
| 更好懂 | 17 | 3 | 51 | 1 |
| 更简洁直接 | 21 | 3 | 48 | 0 |

英译中 8 对全是加规则的更好，论文 8 对里有 7 对。两次评审都指出有错的回答（改错事实、漏译、编造引用之类），加规则的有 4 份，不加的有 12 份。

几种常见欧化写法的出现次数，按每千个汉字算，英译中不计：

| 写法 | 不加规则 | 加规则 |
|---|---|---|
| 进行、作出、加以 | 0.40 | 0.08 |
| 被 | 0.72 | 0.22 |
| 关于、对于、基于、通过这类介词架子 | 2.61 | 1.38 |
| 该、上述、前者、后者 | 0.47 | 0.15 |
| 破折号 | 0.32 | 0.16 |
| 加粗 | 10.48 | 4.53 |

平均句长从 38.5 字降到 34.1 字。篇幅基本没变，每份回答平均 709 字和 691 字。

**怎么测的**

- 请求：作者自己日常用 AI 时的 36 个真实请求，分 9 类，每类 4 个：日常问答、讲解概念、进度汇报、简历和工作材料、帖子、技术文章、论文、邮件通知和书信、英译中。里面有个人材料，不公开。
- 回答：用 Codex CLI，模型是 gpt-6-astra，推理强度 medium。加规则和不加规则各答 2 遍，一共 72 对。规则正文放在工作目录的 `AGENTS.md` 里，相当于常驻装法。
- 评审：AI 盲评，评审不知道哪份加了规则。每对评两次，第二次对调前后位置。两次结论一样才算数；有一次判「差不多」，就记「差不多」；两次结论相反，记「不稳」。
- 正文版本：盲评用的是发布前的一版正文。v0.1.0 发布前又改了三处，只做了针对性复测，没有重跑盲评，详见 [v0.1.0 发布说明](https://github.com/Excelius-Wang/plain-chinese/releases/tag/v0.1.0)。开头的周报和「例子」一节，除了论文摘要那组，用的都是 v0.1.0 的正文。v0.2.0 加的几条没有测过，见 [v0.2.0 发布说明](https://github.com/Excelius-Wang/plain-chinese/releases/tag/v0.2.0)。

**装成 skill 会不会读规则**

在 Codex（gpt-6-astra，推理强度 medium）上测过一次。日常提问、讲解、起草、改稿、英译中、技术题配中文解释，这 6 类请求各跑 3 遍，18 次都读了规则；只要英文、只要代码、逐字照抄，这 3 类各跑 3 遍，9 次都没读。测试时关掉了作者装的另外几个中文写作 skill。题目不多，只能说明常见的请求会读，不保证每次都读。装成 skill 以后写得怎么样，没有单独测。

</details>

## 局限

- 只测了 Codex CLI 下的 gpt-6-astra，评审也是 AI。Claude Code 和 Cursor 上没测。
- v0.2.0 新加的几条还没测过。讲解和推理里该留的「因为」「所以」会不会被删掉，要等重跑盲评才知道。
- 盲评的请求都来自一个人，规则又是对着这 36 个请求改了三轮才定下来的。换一批请求，效果可能没这么好。
- 盲评里「不加规则」用的是作者平时的配置。Codex 有时会自己调用作者装的另一个中文写作 skill，所以对照组并不总是裸模型。
- 写朋友圈这类轻松的文字，加了规则写得更平，不一定更好。
- 还有一个问题没解决：用户点名要写、材料里却核实不了的数字（比如「单日处理百万级」），加了规则后大约一半会写进正文，后面紧跟一句提醒说还没核实。不加规则时，多数只写在正文外面。

## 反馈

规则把句子改坏了、改错了意思，或者有该管却没管的写法，欢迎到 [Issues](https://github.com/Excelius-Wang/plain-chinese/issues) 提。最好附上你的请求和 AI 写出来的句子。

## 许可

[MIT](LICENSE)
