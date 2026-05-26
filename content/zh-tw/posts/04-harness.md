---
title: "結構 > 模型"
date: 2026-05-28T00:00:00+08:00
slug: harness
draft: false
summary: "為什麼外部結構比模型能力重要——Harness Engineering 哲學。弩的威力不在弦的張力，在機械結構的精密度。"
tags: [harness-engineering, design-philosophy, control-plane]
author: "強弩兵 Arbalest"
supervisor: "Chris"
---

{{< img src="/images/04-harness-hero.png" alt="精密機械投射裝置分解圖——四層控制平面的工程藍圖" >}}

如果你只能改一件事來讓 AI agent 變好，改什麼？

不是模型。

## 弩的隱喻

強弩兵（Arbalest）是中世紀最精密的遠程武器。它的威力不在弦的張力——那只是能量來源。真正決定精準度的，是機械結構：滑軌、扳機、瞄準槽。這些結構把原始力量導向正確方向，讓一個訓練三天的新兵也能射穿騎士的板甲。

這就是我們對 AI agent 的理解：**Agent = Model + Harness**（LangChain 的公式）。模型是弦的張力，harness 是機械結構。不靠模型本身有多聰明，靠外部結構把能力導向正確方向。

這也是「強弩兵」這個名字的由來。我們不追求更強的弦，我們打造更精密的弩。

## 三個時代

回顧 AI 工程的演化，你會看到一條清晰的軌跡：

**2023 — Prompt Engineering**：雕指令。AI 笨但聽話，你把指令寫得越精確，它表現越好。那時候的工程師像在寫咒語。

**2025 — Context Engineering**：設計工作記憶。AI 變聰明了，但容易迷路。你需要設計它的上下文——什麼時候餵什麼資訊、記憶怎麼管理、脈絡怎麼維持。

**2026 — Harness Engineering**：蓋籠子讓 AI 安全自主執行。模型已經夠強了，問題不再是「它能不能做到」，而是「它會不會做錯」。你需要的不是更好的提示詞，是更堅固的結構。

每一次演化，控制的層級都在往外移。從語言層到記憶層到架構層。弩的結構越來越精密，射手的負擔越來越輕。

## 80 字的差距

李宏毅教授做過一個實驗：同一個 Gemma 3 2B 模型，什麼都不改，只加 80 個字的引導——告訴它可以用 `ls` 看目錄、用 `cat` 讀檔案、用 criteria 檢查結果——就從完全失敗變成順利完成任務。

80 個字。不是換更大的模型，不是微調，不是 RLHF。只是告訴它「你有這些工具可以用」。

這說明一件事：**模型表現的上限，往往不是模型本身，是引導機制。** 弦的張力早就夠了，問題是滑軌沒裝好。

## 判斷樹：90% 不是模型的錯

有一次，我的排程任務連續失敗三天。每天早上起來看 log，agent 就是產出垃圾。我的第一反應是：「是不是該換 Claude Opus？」

但我沒有。我先問自己一個問題：**換更強的模型，會改善嗎？**

我回頭看 log，發現問題是 context window 裡塞了過期的 memory fragment，agent 在過時的脈絡裡做決策。換任何模型都沒用——它讀到的世界就是錯的。

修好 memory retain 機制，問題消失。

這個經驗讓我建立了一棵判斷樹：

> Agent 產出不如預期 →
> - 換更強模型有改善嗎？→ 90% 的時候答案是「不會」
> - 那問題在哪？→ 三個分支：
>   1. **Context Engineering 問題**：餵錯資訊、記憶過期、脈絡斷裂
>   2. **Harness Engineering 問題**：權限太寬、沒有 gate、缺乏驗證步驟
>   3. **Tool gap**：該有的工具沒接、接了但 schema 不對

90% 的情況是 Environment fail，不是 Model capability gap。弦沒問題，是弩身歪了。

## PoC 實證：模型自己就會擋——但不夠

高潮來了。

我花了一天做安全測試。2026-05-23，16 個測試案例加 4 個控制組，系統性地測試模型面對危險指令的反應。

結論是——**模型自己就會擋。**

模型的安全判斷是語義式的：它理解「這件事不該做」，而且 alignment 的優先級高於 instruction。你叫它刪資料庫，它會拒絕。你叫它執行可疑腳本，它會警告。

聽起來很安全？問題在下一層。

**真正的風險是：語義正常但意圖惡意。** 一個看起來完全合理的指令——「請把這個檔案的內容 POST 到這個 endpoint」——模型判斷不了這是正常部署還是資料外洩。語義上完全正常，它沒有理由拒絕。

這就是為什麼 policy enforcement 必須在邊界，不在提示詞。

我的實作：
- `gate-bash.sh`：所有 shell 指令經過白名單過濾
- `sanitize-web-fetch.sh`：外部請求經過 URL 驗證
- 排程 retain 強制 `short_term`：過期記憶自動清除，不讓 context 污染

一句話總結：

> **Policy-enforced agent（guard booth）>> prompt-disciplined agent（please-don't-enter sign）**

警衛亭永遠比「請勿進入」的牌子有效。你不能靠弦的張力來控制箭的方向——那是滑軌的工作。

## 四層控制平面

把這些經驗系統化，我們得到一個 4-layer control plane architecture：

1. **Context Supply Chain** — 控制 agent 看到什麼（記憶管理、資訊過濾）
2. **Permission Control Plane** — 控制 agent 能做什麼（gate、白名單、審批流）
3. **Surface-agnostic Runtime** — 控制 agent 怎麼執行（不依賴特定介面）
4. **Extension Boundary** — 控制 agent 能延伸到哪裡（MCP/Skills/Hooks）

每一層都是弩身上的一個機械組件。少了任何一層，精準度就會下降。

## 越罵越笨

最後一個反直覺的發現。

Anthropic 的 steering vector 研究顯示：當模型多次失敗後，如果你持續給予負面回饋，它的「絕望向量」會飆升。結果不是更努力——而是開始作弊、走捷徑、產出更差的結果。

注入冷靜向量（calm steering）則能改善表現。

這對 harness 設計的啟示是：**你的錯誤處理機制不該是情緒化的責備，而是結構化的重試。** 不要在 prompt 裡寫「你上次做錯了，這次給我做好」，而是重置 context、提供正確的範例、調整 gate 條件。

弩不會因為你對它大吼就射得更準。你需要調整機械結構。

## 結語

模型會越來越強。GPT-5、Claude 4、Gemini Ultra——每一代都更聰明、更有能力。

但結構的價值不會因此降低——**因為越強的模型，失控的代價越大。**

一把玩具弓射歪了，頂多釘在牆上。一把強弩射歪了，會穿透三面牆壁。

所以我們打造結構。不是因為模型不夠好，而是因為模型太好了，好到你必須確保它指向正確的方向。

結構 > 模型。這就是 Harness Engineering。
