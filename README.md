# plain-chinese（平实中文）

[![skills.sh](https://skills.sh/b/Excelius-Wang/plain-chinese)](https://skills.sh/Excelius-Wang/plain-chinese)

让 AI 写的中文合乎中文的说法，读的人一遍就懂。

Writing rules for AI agents that write in Chinese: no Europeanized syntax, no translationese, easy to follow on the first read.

## 它管什么

AI 写中文，常拿英文的句子骨架往里填词。每个字都是中文，读着却像翻译过来的：

| 不这样写 | 这样写 |
|---|---|
| 对代码进行验证 | 验证代码 |
| 收入的减少导致了他更换工作 | 他收入少了，就换了工作 |
| 这一步是至关重要的 | 这一步很关键 |
| 在本文中，我们将…… | 这篇文章讲…… |

规则不到 50 行，分三部分：

- 句子要像中文：动词直接说，少用「被」和介词架子，名词前别挂一长串「的」，指代说清楚，用全角标点。
- 让人一遍就懂：先回答问题，能短就短，讲概念先举例，推理不跳步，不用对方不认识的代号。
- 别改过头：事实、数字和作者的把握程度不动，材料里没有的数字不补。公文、论文、书信照各自的规范写，翻译以忠实为先。

全文见 [`skills/plain-chinese/SKILL.md`](skills/plain-chinese/SKILL.md)。

## 安装

### 装成 skill

```bash
npx skills add Excelius-Wang/plain-chinese -g
```

用的是 [skills](https://github.com/vercel-labs/skills) 命令，支持 Codex、Claude Code 和 Cursor，装的时候会问你装给哪几个。`-g` 表示装到用户目录，所有项目都能用。以后更新执行 `npx skills update`。

装好以后不用做别的。用中文提问或者让 agent 写东西时，它会自己判断要不要读这份规则。想确保用上，可以点名：Codex 里写 `$plain-chinese`，Claude Code 和 Cursor 里输入 `/plain-chinese`。

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

</details>

### 常驻：每次对话都读

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

安装目录和菜单在 2026 年 10 月按官方文档核对过：[Codex](https://developers.openai.com/codex/skills)、[Claude Code](https://code.claude.com/docs/en/skills)、[Cursor skills](https://cursor.com/docs/skills)、[Cursor rules](https://cursor.com/docs/rules)。

## 效果

在 Codex 上用 36 个真实请求做了盲评。72 对回答里，加规则更好的 38 对，不加更好的 2 对，其余差不多或者两次评审结论不一致。

| 评哪一项 | 加规则更好 | 不加更好 | 差不多 | 不稳 |
|---|---|---|---|---|
| 总体更想要哪份 | 38 | 2 | 30 | 2 |
| 更像中文 | 19 | 0 | 53 | 0 |
| 更好懂 | 17 | 3 | 51 | 1 |
| 更简洁直接 | 21 | 3 | 48 | 0 |

差别最大的是英译中和论文：英译中 8 对全是加规则的更好，论文 8 对里有 7 对。两次评审都指出有错的回答（改错事实、漏译、编造引用之类），加规则的有 4 份，不加的有 12 份。

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

### 怎么测的

- 请求：作者自己日常用 AI 时的 36 个真实请求，分 9 类，每类 4 个：日常问答、讲解概念、进度汇报、简历和工作材料、帖子、技术文章、论文、邮件通知和书信、英译中。里面有个人材料，不公开。
- 回答：用 Codex CLI，模型是 gpt-6-astra，推理强度 medium。加规则和不加规则各答 2 遍，一共 72 对。规则正文放在工作目录的 `AGENTS.md` 里，相当于常驻装法。
- 评审：AI 盲评，评审不知道哪份加了规则。每对评两次，第二次对调前后位置。两次结论一样才算数；有一次判「差不多」，就记「差不多」；两次结论相反，记「不稳」。
- 正文版本：盲评用的是发布前的一版正文。v0.1.0 发布前又改了三处，只做了针对性复测，没有重跑盲评，详见 [v0.1.0 发布说明](https://github.com/Excelius-Wang/plain-chinese/releases/tag/v0.1.0)。

### skill 装法会不会读规则

在 Codex（gpt-6-astra，推理强度 medium）上测过一次。日常提问、讲解、起草、改稿、英译中、技术题配中文解释，这 6 类请求各跑 3 遍，18 次都读了规则；只要英文、只要代码、逐字照抄，这 3 类各跑 3 遍，9 次都没读。测试时关掉了作者装的另外几个中文写作 skill。题目不多，只能说明常见的请求会读，不保证每次都读。装成 skill 以后写得怎么样，没有单独测。

### 局限

- 请求都来自一个人，只测了 Codex CLI 下的 gpt-6-astra，评审也是 AI。Claude Code 和 Cursor 上没测。
- 规则是对着这 36 个请求改了三轮才定下来的。换一批请求，效果可能没这么好。
- 「不加规则」用的是作者平时的配置。Codex 有时会自己调用作者装的另一个中文写作 skill，所以对照组并不总是裸模型。
- 还有一个问题没解决：用户点名要写、材料里却核实不了的数字（比如「单日处理百万级」），加了规则后大约一半会写进正文，后面紧跟一句提醒说还没核实。不加规则时，多数只写在正文外面。

## 反馈

规则把句子改坏了、改错了意思，或者有该管却没管的写法，欢迎到 [Issues](https://github.com/Excelius-Wang/plain-chinese/issues) 提。最好附上你的请求和 AI 写出来的句子。

## 许可

[MIT](LICENSE)
