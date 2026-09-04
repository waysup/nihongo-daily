# 素材使用记录

<!-- 每次运行追加一节，格式：
## YYYY-MM-DD
- NHK: https://nhkeasier.com/YYYY/MM/DD/ （实际文章日期，可能不等于运行日期）
- Wikipedia: 条目标题
-->

## 【例句出处补充规则 · 2026-09-02起生效】

例句铁律允许引用的"权威日语语法站"，除 jlptsensei.com、bunpro.jp 外，新增 **jozu.tw**（出口仁本人的语法讲解站，例如 https://jozu.tw/docs/deguchi-N5/ni-change-result-particle ，和用户主用的出口仁语法视频是同一体系，讲解风格、例句可直接采用，优先级可以高于前两者）。当天材料里找不到某语法点的真实例句、又判断值得补充讲解时，可以去 jozu.tw 对应语法条目页找例句，注明来源为"jozu.tw"。

## 【页面音乐栏目 · 2026-09-02新增，2026-09-04已移除】

index.html 顶部曾有一个 `.music-card` 栏目，嵌入网易云音乐播放器（歌单「【中島美雪】全曲集」id=57217843）。2026-09-04排查发现该歌单外链接口对匿名/未登录访客只返回10首（不是宣称的355首），播放器加载出UI骨架但曲目列表空白、时长显示0:00，判断是网易云对外链嵌入的匿名访问限制，非页面代码问题。用户决定直接移除，已删除 `.music-card` 相关HTML和CSS。**以后不要再加回这个栏目**，除非用户重新明确要求。

## 【标准模板规则 · 每次运行前必读，2026-09-01起生效】

index.html 的 `.quote-box` / `.htag` / `.reply` 必须满足以下全部规则，写之前建议先看一个最新 day 里的现成例子照抄格式：

1. **中文翻译**：`.quote-box` 里必须有 `.qtrans`，是对已合法引用的单句配翻译（不是整篇翻译，不违反版权边界）。
2. **语法高亮**：原句中体现语法结构的部分用 `<mark>...</mark>` 包住，范围尽量只包语法标志本身；两段式语法（如〜から〜にかけて）用两个 `<mark>`。
3. **生词假名**：`.h-word` 必须含假名读音，格式 `<span class="h-word">#漢字<span class="kana">（ひらがな）</span></span>`。
4. **原句整句注音**：`.qtext` 里每个含漢字的词都要用 `<ruby>漢字<rt>假名</rt></ruby>` 包住（纯假名/片假名部分不用包）；如果 `<mark>` 里本身含漢字，ruby 写在 mark 内部，例如 `<mark><ruby>場合<rt>ばあい</rt></ruby></mark>`。
5. **朗读按钮覆盖整句**：`.reply` 里"朗读"按钮的 `data-say` 必须是该 `.qtext` **完整原句**的纯假名注音（不含标签），绝不能只从语法标志出现的位置截取——写完之后对照一遍 `.qtext` 确认没有截断前半句或后半句。
6. **语法尽量全覆盖**：当天每篇 NHK Easy 文章、选中的 NHK 普通版文章、维基条目里，只要出现有教学价值的语法结构（不限N几），都写进 `.reply`，不要每篇只挑1-2个敷衍。
7. **已掌握的语法点跳过不再讲解**：页面"记住了"按钮点击后同步进 Firebase Firestore（项目 `nihongo-daily-40390`，公开可读）。**写 grammar-log.md / index.html 之前，先读取**：
   `https://firestore.googleapis.com/v1/projects/nihongo-daily-40390/databases/(default)/documents/progress/main?key=AIzaSyBBMvIa4U2TXX6ziH3INYyuMHKg930DlIo`
   从 `fields.m.arrayValue.values[].stringValue` 取出已掌握语法点列表。当天素材如果又出现这些语法点，跳过、不要再单独写 `.reply` 讲解它，除非它已从 `m` 数组里被用户移除（取消了"记住了"）。一句话里若同时有已掌握点和新语法点，只讲新的那个，不要因为有已掌握点就跳过整句。Firestore 读取失败时按"已掌握库为空"处理，不要中断任务。
8. **NHK Easy 标题链接：跳到当天日历页的对应位置，不要跳去单篇 story permalink 页**。写法：`href="https://nhkeasier.com/YYYY/MM/DD/#:~:text=<对该篇标题在日历页上原样渲染出的文字（含逐字假名注音、按 NHK Easier 自己的格式，如"多おおくの場所ばしょで猛暑日もうしょび"）做 URL 百分号编码>"`，`target="nhk-easy"`（同一个 target，所有 NHK Easy 链接共用一个浏览器标签页，点哪篇就把这个标签页导航过去，不再新开分页）。`#:~:text=` 是浏览器原生的 Text Fragment 功能（Chrome/Edge 支持，会自动滚动到该文字出现的位置并高亮；Safari/Firefox 可能不支持滚动，但链接本身仍然有效，只是落在日历页顶部）。獲取标题文字的方法：先 curl 那天的日历页 `https://nhkeasier.com/YYYY/MM/DD/`（注意可能不是运行当天，是文章实际发布日，回溯规则见下方历史记录），找到对应 `<article>` 里 `<h3>...</h3>` 的内容，把里面所有标签去掉后剩下的纯文字（漢字和假名会交替出现，这是网站自己的排版，照抄不要精简）就是要拿去做 text fragment 的字符串。
9. **维基百科选文方向：聚焦日本文化/历史/当代政治社会**。仍然只能选秀逸/良质条目（质量门槛不变），但选题范围从"任意学科"收窄为**和日本这个国家本身相关**的主题——日本历史（时代、事件、人物、制度）、传统文化（艺能、宗教、习俗、建筑、饮食等）、当代政治与社会（选举制度、社会议题、地方自治、经济政策沿革等）——目的是帮用户通过阅读真正理解日本这个国家，不要再选纯自然科学/地理/生物类跟"日本"关系不大的条目（黄砂、彗星、深海魚、オーロラ、雨氷这几个过去选过的话题，以后不要再选这类）。
10. **"复现"引用要带跳转链接（2026-09-04起生效）**：`.reply-explain` 里提到"08-27学过""09-01学过"这类日期时，如果该日期当前仍在页面上（今天 + 归档7天，即最近8天）能找到对应 `id="day-2026-MM-DD"` 的区块（当天 `.day` 或 `.archived-day`），就要写成 `<a href="#day-2026-MM-DD" class="date-jump">MM-DD</a>学过`，点击会自动展开对应的 `<details>` 归档并滚动过去（逻辑见 index.html 里的 `jumpToDay`/`dateIdFromLabel`/点击委托，不用改JS，写HTML时套用这个格式即可）。如果引用的日期已经滚出7天归档窗口（比如今天是09-04，归档最早只到08-28，再往前的08-27及更早就没有锚点了），就不要加链接，保持纯文字，不要链接到不存在的锚点。
11. **N2以下等级进度条（2026-09-04新增）**：`.progress-tweet` 里除了原有的 N2 大进度条，下面新增了一行 `.lvl-progress-row`，用小chip（`.lvl-chip`）分别显示 N5/N4/N3/N2/N1 五个等级的"累计个数/估算总数"和一条4px迷你进度条，样式要保持紧凑（不要改回5条完整大小的进度条，容易显乱）。每天写完 grammar-log.md 后，同步更新这一行的分子（当天新增且不重复的语法点数，按等级分类累加）——具体计数口径：以 grammar-log.md 全文（去掉顶部注释模板）中所有 `- 模式 [N级别]` 格式的行，按"模式"文本去除括注和开头的〜后去重计数，这样可以在每次改动后用脚本重新核对，不用纯靠记忆滚动累加。估算总数（分母）沿用：N5≈80、N4≈120、N3≈180、N2≈200（原有）、N1≈180，这些都是粗略估算，跟原有"约200个（估算）"的表述口径一致。

## 2026-09-03
- NHK Easy（当日页面 https://nhkeasier.com/2026/09/03/ 直接有更新，当日4篇全部读完）：
  - https://nhkeasier.com/story/9928/ 「青森県などでたくさん雨が降っている　災害に気をつけて」
  - https://nhkeasier.com/story/9929/ 「映画「スター・ウォーズ」の監督が美術館をつくった」
  - https://nhkeasier.com/story/9930/ 「山梨県　外国人のために消防隊員が英語を勉強」
  - https://nhkeasier.com/story/9931/ 「山口県下関市　ふぐをとる漁が始まった」
- NHK普通版（news.web.nhk/newsweb 可正常访问首页新闻列表；www3.nhk.or.jp/news/ 仍是403无法访问；选取2篇作为今日N2锚点）：
  - https://news.web.nhk/newsweb/na/nd-20260903de48019 「熊本 八代 日本製紙八代工場の火事　ほぼ消し止められる」（消防局消息1句完整可引用，写明起火点是7月熊本地震中受损的烟囱、事发时正在拆除施工，之后被"確認您的使用意向"会员墙截断）
  - https://news.web.nhk/newsweb/na/nd-20260903de48044 「「まんじゅうや」は有効票？　NW9広内キャスターが専門家に聞く」（茨城县神栖市市长因选票争议提起诉讼一案，开头3句完整可引用，后续被会员墙截断；结合相关标题「「まんじゅうや」は無効票　東京高裁　神栖市長の訴え退ける」可知法院最终认定为无效票，此为公开标题事实，非正文翻译）
- Wikipedia: 三島由紀夫（秀逸な記事）https://ja.wikipedia.org/wiki/三島由紀夫

## 2026-09-02
- NHK Easy（当日页面 https://nhkeasier.com/2026/09/02/ 直接有更新，当日4篇全部读完）：
  - https://nhkeasier.com/story/9924/ 「日本のどこでも　たくさんの雨などに気をつけて」
  - https://nhkeasier.com/story/9925/ 「ネパールの「土石流」から1週間　1000人以上が亡くなった」
  - https://nhkeasier.com/story/9926/ 「三重県伊勢市で大きい火事　44時間でやっと消えた」
  - https://nhkeasier.com/story/9927/ 「北海道乙部町　子どもたちが「かかし」を作った」
- NHK普通版（news.web.nhk/newsweb 可正常访问首页新闻列表；www3.nhk.or.jp/news/ 仍是301跳转到newsweb；选取2篇作为今日N2锚点）：
  - https://news.web.nhk/newsweb/na/nd-20260902de47743 「台風24号　4日にかけて沖縄・奄美に接近　土砂災害など十分注意」（正文前2句完整可引用，第3句起被"確認您的使用意向"会员墙截断；与09-01「2日～4日ごろに近づく見込み」为同一台风路径预报的更新）
  - https://news.web.nhk/newsweb/na/nd-20260901de47705 「ネパール土石流　発生から1週間　避難所の衛生状態維持も課題に」（文章URL日期为09-01但dateModified为09-02 18:52，属当天更新内容；正文仅开头1句完整可引用，后续被会员墙截断；与08-29〜09-01连续4天的死亡人数追踪报道为同一事件，今天角度从数字更新转为"发生满一周+避难所卫生状况"）
- Wikipedia: 神戸外国人居留地（秀逸な記事）https://ja.wikipedia.org/wiki/神戸外国人居留地

## 2026-09-01
- NHK Easy（当日页面 https://nhkeasier.com/2026/09/01/ 直接有更新，当日4篇全部读完）：
  - https://nhkeasier.com/story/9920/ 「多くの場所で猛暑日　北海道や東北ではこれから雨に気をつけて」
  - https://nhkeasier.com/story/9921/ 「福井県　大雨のあと家具などたくさんのごみ」
  - https://nhkeasier.com/story/9922/ 「9月1日　子どもたちが津波から逃げる訓練をした」
  - https://nhkeasier.com/story/9923/ 「車のスピード　「生活道路」を走るときは時速30kmまで」
- NHK普通版（news.web.nhk/newsweb 可正常访问首页新闻列表；选取2篇作为今日N2锚点）：
  - https://news.web.nhk/newsweb/na/nd-20260901de47558 「台風24号　2日～4日ごろに沖縄・奄美に近づく見込み」（正文较短，两段均可完整引用，无会员墙限制）
  - https://news.web.nhk/newsweb/na/nd-20260901de47575 「ネパール土石流死者1000人超　要支援6万人超　被災者心のケアも」（与08-29「633人死亡」、08-30「768人死亡」、08-31「919人死亡」为同一事件的数字更新：919人→1019人死亡，4700人以上→4400人以上不明〈搜救进展使不明人数略降〉，是连续第4天报道的进展；正文除开头1句外被"確認您的使用意向"会员墙截断）
- Wikipedia: 利根川（秀逸な記事）https://ja.wikipedia.org/wiki/利根川

## 2026-08-31
- NHK Easy（当日页面 https://nhkeasier.com/2026/08/31/ 直接有更新，当日4篇全部读完）：
  - https://nhkeasier.com/story/9916/ 「福井県でとてもたくさんの雨が降った」
  - https://nhkeasier.com/story/9917/ 「国　常陸大宮市で「核のごみ」を埋めるための調査をしたい」
  - https://nhkeasier.com/story/9918/ 「ネパールと中国の土石流　亡くなった人は900人以上になった」
  - https://nhkeasier.com/story/9919/ 「北海道　牧草の大きいかたまりを転がす大会があった」
- NHK普通版（news.web.nhk/newsweb 可正常访问首页新闻列表；选取2篇作为今日N2锚点，均只有开头1句完整可引用，后续内容被"確認您的使用意向"会员墙截断）：
  - https://news.web.nhk/newsweb/na/nd-20260831de47330 「北陸は土砂災害など十分注意　熱帯低気圧あす台風に発達か」
  - https://news.web.nhk/newsweb/na/nd-20260831de47364 「ネパール土石流　919人死亡確認　4793人行方不明」（与08-29「633人死亡」、08-30「768人死亡」为同一事件的数字更新：768人→919人死亡，3000人以上→4700人以上不明，是连续第3天报道的进展）
- Wikipedia: 南硫黄島原生自然環境保全地域（秀逸な記事）https://ja.wikipedia.org/wiki/南硫黄島原生自然環境保全地域

## 2026-08-30
- NHK Easy（当日页面 https://nhkeasier.com/2026/08/30/ 返回404；RSS feed https://nhkeasier.com/feed/ 最新条目仍是08-28发布的4篇（story/9912〜9915），故事编号9916直接访问也是404，说明还没有新文章；额外核实了08-29页面同样404。今天是周日，延续08-29已确认的"NHK Easy周末不发布新内容"规律，本次NHK Easy部分继续空缺）
- NHK普通版（news.web.nhk/newsweb 可正常访问首页新闻列表；选取2篇作为今日N2锚点，均只有开头1-2句完整可引用，后续内容被"確認您的使用意向"会员墙截断）：
  - https://news.web.nhk/newsweb/na/nd-20260830de47200 「福井県で記録的な大雨　土砂災害に引き続き厳重に警戒を」（与08-29「福井 勝山市避難指示」为同一波灾情的最新进展）
  - https://news.web.nhk/newsweb/na/nd-20260830de47292 「ネパール土石流　768人死亡　3000人以上不明　6歳女児を救出」（与08-29「ネパール土石流633人死亡」为同一事件的数字更新：633人→768人死亡，2400人以上→3000人以上不明）
- Wikipedia: 雨氷（秀逸な記事）https://ja.wikipedia.org/wiki/雨氷

## 2026-08-29
- NHK Easy（当日页面 https://nhkeasier.com/2026/08/29/ 返回404；RSS feed https://nhkeasier.com/feed/ 的最新条目仍是08-28发布的4篇，没有新文章；额外核实了过去几个周六/周日的日历页 2026/08/22、2026/08/23、2026/08/15、2026/08/16，全部404，可判断NHK Easy在周末不发布新内容——今天是周六，本身就没有新文章，08-28的4篇已在昨天完整读完并收录，故本次NHK Easy部分空缺）
- NHK普通版（www3.nhk.or.jp/news/ 会301跳转到 news.web.nhk/newsweb，可正常访问；因NHK Easy今天空缺，改用NHK普通版补充2篇作为今日N2锚点。两篇正文均只有开头1句完整可引用，后续内容被"確認您的使用意向"会员墙截断）：
  - https://news.web.nhk/newsweb/na/nd-20260829de47180 「福井 勝山市　約8300世帯に避難指示」
  - https://news.web.nhk/newsweb/na/nd-20260829de47121 「ネパール土石流　633人が死亡　二次災害のリスクを警戒」
- Wikipedia: 黄砂（秀逸な記事）https://ja.wikipedia.org/wiki/黄砂

## 2026-08-28
- NHK Easy（当日页面 https://nhkeasier.com/2026/08/28/ 直接有更新，当日4篇全部读完）：
  - https://nhkeasier.com/story/9912/ 「雨が降りやすい天気が続きそう」
  - https://nhkeasier.com/story/9913/ 「熊本の地震から1か月　安心して生活できる家の用意が必要」
  - https://nhkeasier.com/story/9914/ 「ネパールで「土石流」　橋が壊れて助ける品物を届けるのが難しい」
  - https://nhkeasier.com/story/9915/ 「野菜の値段　9月はトマトやきゅうりが高い」
- NHK普通版（www3.nhk.or.jp/news/ 仍无法访问，改用 news.web.nhk/newsweb：首页新闻列表可正常读取，「熊本地震1か月」「ネパール土石流552人死亡」两篇候选文章正文同样被前端JS截断、无法取得完整可引用原文，尝试后放弃；仅下面这篇能取到完整可引用段落，N2锚点本次新增1篇）：
  - https://news.web.nhk/newsweb/na/nd-20260828de46874 「パワハラ認定の山中 横浜市長が辞職届「けじめつけるべき」」
- Wikipedia: 深海魚（秀逸な記事）https://ja.wikipedia.org/wiki/深海魚

## 2026-08-27
- NHK Easy（当日页面 https://nhkeasier.com/2026/08/27/ 直接有更新，当日4篇全部读完）：
  - https://nhkeasier.com/story/9908/ 「石川県と富山県に「大雨特別警報」が出た　災害に気をつけて」
  - https://nhkeasier.com/story/9909/ 「大雨で被害　家に水が入ったり山が崩れたりした」
  - https://nhkeasier.com/story/9910/ 「ネパールと中国の国境で「土石流」　273人が亡くなった」
  - https://nhkeasier.com/story/9911/ 「山梨県の新しいぶどう　市場に出す仕事が忙しい」
- NHK普通版（www3.nhk.or.jp/news/ 仍无法访问，改用 news.web.nhk/newsweb：首页新闻列表可正常读取，但当天多篇候选文章正文被前端JS渲染或返回403，无法取得可引用原文——「【被害まとめ】石川 富山 一時レベル5大雨特別警報」「石川 富山の大雨特別警報を切り替え」「ネパール・中国の国境地帯で土石流」「プロ野球結果」均尝试后放弃；仅下面这篇能取到少量可引用正文，N2锚点本次只新增1篇）：
  - https://news.web.nhk/newsweb/na/nd-20260827de46651 「芸術家 草間彌生さん死去　97歳　水玉モチーフの作品で知られる」
- Wikipedia: 彗星（秀逸な記事）https://ja.wikipedia.org/wiki/彗星

## 2026-08-26
- NHK Easy（当日页面 https://nhkeasier.com/2026/08/26/ 无更新，回溯1天至 08-25 页面，当日4篇全部读完）：
  - https://nhkeasier.com/story/9900/ 「危険な暑さ「疲れている人は特に熱中症に気をつけて」」
  - https://nhkeasier.com/story/9901/ 「台風18号　奄美地方と沖縄県とても強い風と雨などに気をつけて」
  - https://nhkeasier.com/story/9902/ 「大雨被害の千葉県　車を無料で貸すサービスが始まった」
  - https://nhkeasier.com/story/9903/ 「高知市の子どもたちが外国人に折り紙を教えた」
- NHK普通版（www3.nhk.or.jp/news/ 无法访问，改用 news.web.nhk/newsweb 成功，N2锚点新增2篇）：
  - https://news.web.nhk/newsweb/na/nd-20260826de46337 「福岡空港で40.4度観測「酷暑日」に　熱中症対策を」
  - https://news.web.nhk/newsweb/na/nd-20260826de46333 「台風18号　奄美や沖縄本島から遠ざかるも土砂災害に厳重警戒を」
- Wikipedia: オーロラ（秀逸な記事）https://ja.wikipedia.org/wiki/オーロラ

## 2026-08-25
- NHK: https://nhkeasier.com/story/9897/ 「23日に関東地方で震度5弱の地震　1週間ぐらい気をつけて」（実際文章日期 2026-08-24，当日08-25页面尚无更新，回溯1天）
- Wikipedia: 道教（良質な記事）https://ja.wikipedia.org/wiki/道教
- NHK普通版（本次补录，N2锚点新增）：
  - https://news.web.nhk/newsweb/na/nd-20260825de46130 「【比較動画】クマ撃退スプレー　市販製品の『性能』に大きな差」
  - https://news.web.nhk/newsweb/na/nd-20260825de46156 「高額療養費制度　なぜ8月から見直し？家計の注意点は」
