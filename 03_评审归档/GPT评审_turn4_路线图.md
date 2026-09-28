# GPT 路线图评审回复（turn2 · 17 号草案）

- 会话：vip-11 热会话复用（同 turn3 窗口）
- 提取时间：2026-09-29
- 状态：len=8374

---

## GPT 回复（全文）

先给“读过证明”，控制在 6 行：

三阶段 AI 角色：Phase A 反馈者；Phase B 设计者；Phase C 课程架构师。

W2 = Parsing Challenge α；W3 = Parsing 打磨 + Micro Grammar α（听写错句 top3 模式）；W4 = 3 primitives + AI 组合练习的 Phase B 打样。

v0.9.1 三件套：Meaning Recall 入口移位、ProductionTarget/ListenRetryTarget 双出口、Markdown 轻量清洗。

V1.0 gate：三交互均走 governor 且可降级；听写→复习→产出全链路留痕；真机 E2E 通过。

15 号把 Chunk Composer 后置，核心原因是它依赖 chunk 数据/语料标注，前置数据条件比 Meaning Recall、Parsing 更重；15 号同时把 Phase A 的原则定为先补“真正的训练缺口”，而不是先扩 UI 花样。

17 号的最大路线问题：W4 现在验证的是“AI 能组合题型”，还没有真正验证 Phase B 所需的 interaction graph + evaluator + 受控 runtime。

先说明核验边界：本轮公开搜索没有成功索引到 17 号新文件，因此对 17 的事实以上面你内嵌的送审正文为准；08/09/15/16 的上游契约沿用上轮已经核过的 main 内容。仓库公开入口仍为 listenloop-curated。
总结判断
四周方向可行，但当前路线图有一个战略顺序倒置：W4 不应该以“AI 生成练习”为验收目标，而应该以“AI 在一个受限协议内生成可执行、可验证、可降级的 interaction spec”为验收目标。
换句话说：

不要在 W4 证明“AI 很会出题”。
要证明“APP 已经拥有一个 AI 可以安全编排的学习交互运行时”。

这是“AI 生成各种交互设计”能不能成为产品架构，而不是一句产品 slogan 的分界线。
此外，v0.9.1 还漏了一个上轮已经判定为学习闭环必要项的低成本修补：Meaning Recall 的一次 modified output / 再答一次。 我会把它排在 Markdown 清洗之前。

1. 四周节奏：可以，但我会改路线
结论
Phase A 三种交互四周内做到 α/β 水平可行；四周内同时宣布 Phase B primitives“验证完成”，过于激进。
最大的风险不是工程量，而是：

为了赶 W4，连续造 Parsing、Micro Grammar、填空/排序/选择四套 UI，却没有抽出它们真正共享的训练协议。

结果四周以后可能有很多“能玩的页面”，但仍然没有 Phase B。
这恰好违反 15 号启动 Phase B 的原意：Phase A 是为了通过多个真实训练交互发现 primitives 边界，而不是为了凑够三个题型。
依据
15 号的关键逻辑其实非常好：
先有人类设计的多种真实交互 → 从真实重复中抽 primitive → 再让 AI 组合。
17 号目前：
W2 Parsing → W3 Grammar → W4 立刻定义填空/排序/选择 primitives
时间上只有大约一周观察两个新交互。
这里容易犯一个典型架构错误：
先根据视觉控件抽象 primitive，而不是根据学习行为抽象 primitive。
“选择题”“排序题”“填空题”首先是 UI 形态，不一定是 Learning Runtime 最稳定的 primitive。

我建议的四周替换路线
W1：Completion Loop β
保留现在的双出口，但补齐上轮留下的闭环：
Dictation evidence      ↓ProductionTarget ─→ Meaning Recall      │                    ↓      │              feedback      │                    ↓      │              modified output      │ListenRetryTarget ─→ original audio
这一周同时开始记录最小 telemetry：
source → target reason → interaction → attempt → result → fallback
不要求上完整 Policy Engine。
W2：Parsing Challenge α
照做。
但 W2 不只是交付一个页面，还必须同时记录：

Parsing 和 Meaning Recall 到底共享了哪些交互行为？

例如：
present stimulus / collect response / evaluate / feedback / retry / advance
这才是在为 Phase B 找 primitive。
W3：Micro Grammar α + Interaction Contract v0
Micro Grammar 可以做，但 top3 不要凭开发者直觉定义。
如果目前还没有足够真实用户错误数据，就把它叫：

3 个首批支持模式

而不要暗示它们真的是用户总体的“top 3”。
W3 最重要的隐藏交付应该是：
把前三种训练的共同协议写出来。
W4：Phase B Runtime Spike
不要把验收写成：

AI 成功生成一个练习。

改成：

同一个 renderer/runtime，在不新增专用 Screen 的情况下，成功执行至少 3 个由结构化 spec 描述的 interaction graph；其中至少 1 个 spec 由 AI 根据真实 DictationEvidence 生成。

这个 gate 强得多。

2. Phase B primitives：不建议首批定义成「填空 / 排序 / 选择」
结论
它们可以作为首批 renderer components，但不应该成为 Phase B 的三个核心 primitives。
这是我对 17 号最大的修改建议。
原因很简单：

填空 / 排序 / 选择描述的是“用户怎么输入答案”。

而 Phase B 真正需要组合的是：

学习任务如何运行。

如果 primitive 抽错层级，以后 AI 只能：

“今天给你出一道选择，明天给你出一道排序。”

这叫 AI-generated exercises。
而你真正想做的是：

AI 根据 evidence 选择认知操作、提示方式、反馈方式、重试方式，并由 App 安全执行。

这才叫 AI-generated interaction design。

更好的 Phase B 最小验证
我建议 W4 不做“三题型生成器”，做一个极小的 InteractionSpec v0。
至少包含五类东西：
Evidence   ↓Prompt / Stimulus   ↓Response   ↓Evaluator   ↓Feedback   ↓Transition
不是让 AI 生成 Flutter。
AI 只能输出受 schema 限制的 spec。
例如：
InteractionSpecobjectivesourceEvidencesteps:  - stimulus  - response  - evaluate  - feedback  - transition
其中每层只开放极少量白名单能力。

第一批真正值得验证的 primitive
Stimulus
meaningCueoriginalAudiosentenceTexttokenSequence
Response
freeTextchoiceorderingcloze
这里才出现你原来的“填空 / 排序 / 选择”。
也就是说：
它们是 Response primitive，不是整个 interaction primitive。
Evaluator
exacttokenDifforderingaiSemantic
这是 W4 必须有的。
没有 evaluator，AI 只是在生成 UI。
Feedback
correctnessdiffhintexplanationreference
Transition
retryadvancerelistenfinish
Transition 特别重要。
因为上轮 16 号刚证明：

“错了以后去哪儿”

本身就是学习设计。
所以如果 Phase B schema 不能表达：
still_missing → retry
或：
perception_failure → relisten
那这个所谓 interaction graph 实际上还不是 graph。

W4 最小实验应该长这样
拿同一个真实听写错误作为输入，例如：
DictationEvidencesentencemissing tokenserror typeconfidence/reliability
让 AI 生成一个受限 InteractionSpec。
比如 AI 决定：
先给 meaning cue↓freeText↓semantic evaluator↓如果意思正确但 target 未修复↓target hint↓retry↓结束
另一个 evidence，它可以决定：
sentence with blank↓choice↓tokenDiff evaluator↓explanation↓advance
第三个：
chunks↓ordering↓exact/order evaluator↓retry
三个都由同一个 runtime执行。
W4 成功标准
我建议明确写成四条：

AI 不生成 UI/code，只生成 schema-valid InteractionSpec。

一个通用 renderer 能执行至少 3 种不同 interaction graph，不增加专用 Screen。

非法 primitive / 非法 transition / schema failure 必须拒绝并降级到固定模板。

同一个 DictationEvidence 能通过不同合法 spec 形成不同训练交互，并都能产生统一 AttemptResult。

如果这四条过了：
Phase B 值得继续。
如果只能生成：

{type:"multipleChoice", question:"...", options:[...]}

我不会把它算 Phase B 验证成功。
那只是 AI 出题。

还有一个非常重要的 evaluator 原则
AI 不应该既设计任务，又天然拥有最终裁判权。
能 deterministic evaluate 的东西一定 deterministic：
排序 → 本地 evaluator。
选择 → 本地 evaluator。
cloze → token/normalized evaluator。
只有：

意义是否等价、开放表达质量

这类 deterministic evaluator 无法可靠完成的任务，才调用 governor。
这样同时符合 08 的降级红线：

AI 挂了以后，整个 Learning Runtime 仍然有大量可执行交互。

这也是 Phase B 架构能不能商业化的重要区别。

3. v0.9.1：三件套之外漏了什么
结论
有，而且第一优先级不是 Error 153，而是 Meaning Recall 的“再答一次”。
当前三件套我会调整成“四件套”，甚至把 Markdown 清洗降一级。
优先顺序：

双出口；

入口位置；

Meaning Recall modified output；

AI 文本清洗。

为什么
15 号定义的 pushed-output loop 是：
output → gap → feedback → modified output
16 号目前却是：
output → feedback → 下一句
所以 v0.9.1 如果目标是让用户“明天就明显感觉版本进了一步”，一个：

根据反馈再试一次

比 Markdown 的 ** 消失更能改变学习体验。

modified output 的最小规格
不要做复杂状态机。
AI 返回：
correct + target repaired
直接：
下一句
否则：
反馈卡意思基本表达到了 ✓这次再注意：have been[再说一次]       ← primary[看参考表达]     ← secondary[下一句]         ← tertiary
只允许 1 次 guided retry。
第二次提交后无论结果如何都允许下一句。
这样不会变成无限纠错。
无 AI
静态 fallback 不要求 repair，因为系统没有能力可靠判断开放表达是否正确。
直接：

AI 反馈暂不可用，下面是原句供对照。

看原句 → 下一句
这继续符合 08 的降级原则。

Error 153 要不要明天补提示？
如果 Error 153 是当前真实用户可遇到、且现有界面只显示技术错误或空白，那么值得修；如果已经自动恢复且用户几乎看不到，不应该挤掉上面四项。
你这轮没有给我 Error 153 的具体触发条件、发生率和当前 UI，我也没有足够仓库证据核实，所以我不建议凭错误码本身提高优先级。
正确 gate 是：

用户遇到以后是否知道下一步该做什么？

如果答案是否，就做一个非常小的恢复 UI：
视频暂时加载失败
[重新加载]
以及不阻塞学习的已有替代路径。
不要给用户显示 Error 153 作为主要文案。

路线图还有 5 个需要直接修的逻辑问题
① “Phase A 三交互”命名现在不稳定
17 写：

Meaning Recall / Parsing / Chunk Composer 缓 / Micro Grammar 四种中完成三种。

同时 V1.0 又叫：

Phase A 三交互闭环。

建议明确把 V1.0 scope 冻结为：
Meaning Recall + Parsing Challenge + Micro Grammar。
Chunk Composer 明确移入 post-V1.0 backlog。
否则以后“Phase A 完成”会出现口径争议。

② V1.0 gate 缺了“学习效果正确性”
现在三条是：

governor + fallback；

数据留痕；

E2E。

这些基本都是工程 gate。
但这是从“精听工具→习得系统”的大版本，至少还需要：

三种训练都能从明确 evidence/reason 进入，并产生结构化 AttemptResult。

否则系统只是三个功能页。
我建议 V1.0 gate 改成四条：
① evidence-driven routing；② structured attempt/result；③ governor/fallback；④ E2E。
“全链路留痕”包含在 ②。

③ W3 的 “top3” 暂时没有证据
如果有真实 corpus 统计，我收回这条。
但就目前送审材料，我没有看到：

用户听写错误模式的频率分布。

所以路线图不能先宣布 top3。
应该写：

首批 3 个可可靠识别的错误模式

等数据积累以后再变成真正的 top-N。
这个差别很重要：
“最常见”是数据结论；“先支持”是产品决策。

④ Phase C 的表述跳得太远
“用英语学进化论，而不是用进化论学英语”是一个很清晰的愿景，但它其实意味着从 SLA training runtime 跨向 CLIL / content learning。
这会带来：

学科知识正确性；

learner language level；

prerequisite graph；

内容授权/生成质量；

知识 assessment；

一整套新问题。
所以我支持保留 12 月愿景，但建议把 Phase C gate 写成：

只有 LearnerModel 能稳定区分“语言不会”与“知识不会”之后启动。

否则系统看到用户答不出“自然选择”，不知道到底应该教英语还是教生物。
这是 Phase C 最大的模型难题。

⑤ “AI 生成各种交互设计”必须加一条产品宪法
我建议直接写进 17：

AI 生成的是受约束的 InteractionSpec，不生成任意 App UI，不绕过 governor/evaluator，不改变核心播放器控制权。

这是非常重要的一句话。
否则“AI 生成各种交互设计”很容易在未来被解释成：

LLM 返回 JSON，客户端想渲什么就渲什么。

最后五层架构、08 的核心红线、15 的 bounded primitives 会一起被侵蚀。

我建议直接替换成的版本节奏
时间交付真正验证什么v0.9.1 / 明天双出口 + 入口优化 + MR 一次 retry + AI 清洗听写完成流第一次真正闭环v0.9.2 / W2Parsing α + AttemptResulttimed processing + 第二种 interactionv0.9.3 / W3Micro Grammar α + Interaction Contract v0第三种 interaction + 抽公共协议v0.9.4 / W4InteractionSpec Runtime SpikeAI 是否能安全编排已有 primitivesV1.0三训练正式化 + evidence routing + 留痕 + fallback + E2ELearning Runtime Phase AV1.1 PreviewAI 根据 learner evidence 动态选 spec/graphPhase B 真正开始
这里我特意把 W4 叫 v0.9.4 / Runtime Spike，而不是 V1.1。
因为 W4 的任务应该是：

验证架构假设。

不是向用户承诺：

“AI 设计练习已经成为正式能力。”

最终对三个送审问题的判定
问题 1：四周可不可行？
可行，但要把 W4 从“Phase B 功能交付”降成“Phase B 架构 spike”。 最大风险不是来不及写代码，而是为了四周目标过早抽象错误的 primitives。W2/W3 必须同时产出 interaction contract，而不能只产出两个新 Screen。
问题 2：填空 / 排序 / 选择是不是正确 primitives？
作为 Response components 正确；作为 Phase B 顶层 primitives 不正确。 顶层最小模型应至少是 Stimulus → Response → Evaluator → Feedback → Transition，AI 生成受限 InteractionSpec；填空/排序/选择只是其中 Response 的三个实现。
问题 3：明天还缺什么？
优先补 Meaning Recall 的一次 modified output，再考虑 Error 153。 Markdown 清洗可以保留，但学习价值低于“反馈后再试一次”。Error 153 是否进入 v0.9.1，要看真实发生率和当前失败 UX；这部分信息不足，我不做假判断。
如果按这个修订，17 号路线会形成一个非常漂亮的连续演进：

v0.9.1 证明“诊断能驱动下一动作” → W2/W3 证明“不同学习动作存在共同协议” → W4 证明“AI 能在这个协议里设计交互” → V1.0 把协议和数据正式化 → V1.1 才让 AI 真正开始动态编排。

这条路线比“连续开发三个题型，然后第四周让 AI 出题”更接近你这次拍板的真正目标：APP 提供可靠的交互运行时，AI 根据学习证据设计下一段学习体验。
