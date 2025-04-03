---
marp: true
paginate: true
theme: kdx
style: |
  section.lead h1 {
    text-align: center;
    font-size: 3em;
  }
  section.lead p {
    text-align: center;
    font-size: 1em;
  }
  section.small-title1 h1 {
    font-size: 2em !important;
  }
  section.small-title2 h1 {
    font-size: 1.8em !important;
  }
  section.small-text li {
    font-size: 0.8em !important;
  }
---

<!--
_class: lead
_paginate: false
-->

# AIポッドキャストを作ってみた話

Engineer Sync 2025年4月
クラウドサービス部
結城清太郎

---

<!--
_class: small-title2
-->

# なぜAIポッドキャストを作りたいのか？

- **単純によくポッドキャストを聴いているから！**

---

# よく聴くポッドキャスト

- NEWS CONNECT
- COTEN RADIO (コテンラジオ)
- mozaic.fm
    - 元ドワンゴのhiroppyさんも出演されていますね
- fukabori.fm
- EM.FM

---

# 影響を受けたポッドキャスト

- **zenncast にも影響を受けた**
    - [zenncastを支える技術 2023](https://zenn.dev/himara2/articles/db054d81b05d19)
    - OpenAI TTS (Text-to-Speech) を使っているらしい

---

# どんなポッドキャスト？

- **名前：「NEW CROSS RADIO」**
- **コンセプト：**
    - 「アイデアとは既存の要素の新しい組み合わせ以外の何ものでもない」
    - この番組では「**既存の要素の新しい組み合わせ**」を探るために、毎回**2つのキーワード**を組み合わせて考察していく。
- **プレイリスト:** [NEW CROSS RADIO（新しい順）](https://www.youtube.com/watch?v=9W4bivZ3rfQ&list=PLW9IHR33BNUxdxLKhV_7DAQm3bvR28df1&index=1)
- **動機:**
    - 生成AIをイノベーションに使いたかった。

---

# 使った技術 (1): AIモデル

- **Gemini 2.5 Pro**
    - テーマ考案、論考作成、台本作成
- **Gemini Deep Research**
    - 特定のテーマに関する深い論考の作成
- **Imagen 3**
    - ポッドキャストの表紙画像生成
- **Suno AI (v4)**
    - オープニング曲の作成
    - v3までは音が少し濁っている印象だったが、v4でかなり改善された感触

---

# 使った技術 (2): 音声合成 & SSML

- **Google Cloud Text-to-Speech API**
    - 採用理由: 複数話者のSSMLが簡単に使えた
        - これによって、自然な「人の掛け合い」を表現したかった。
        - 意外と複数の音声が必要になる場面があった。
    - 課題: 音声の自然さにはまだ改善の余地あり
- **SSML (Speech Synthesis Markup Language) とは？**
    - 音声合成の際に、発音、声色、間の取り方、話速などを細かく制御するためのXMLベースのマークアップ言語。
    - より自然で表現力豊かな音声生成を可能にする。

---

# 使った技術 (3): ツール・開発言語

- **FFmpeg**
    - 音声ファイルと音楽、表紙画像を結合
    - 生成AIのおかげでコマンドが分からなくても使えるようになった（以前は人類には早すぎると感じた）
- **TypeScript / Deno / Dax**
    - 生成AIでのコード生成はTypeScriptを使うことが多い
    - **Deno:**
        - セットアップは楽だが、AIの生成精度は少し低いかも？（あまり使われていない？）
        - Deno v1のコードが生成され、v2に手動で修正することもあった
    - **Dax:** Denoのスクリプトランナー。もしかしたら `zx` の方が良いかもしれない。

---

# ポッドキャストの作り方フロー (1/2)

1.  **テーマ考案:**
    - 過去のテーマリストを Gemini 2.5 Pro にインプット
    - 新しい「2つのキーワード」の組み合わせ案を生成させる
2.  **論考作成:**
    - 決定したキーワードについて、Gemini Deep Research で論考を作成
3.  **台本作成:**
    - 論考を元に、Gemini 2.5 Pro で SSML 形式の掛け合い台本を生成

---

# ポッドキャストの作り方フロー (2/2)

4.  **音声作成:**
    - SSML台本を Google Cloud Text-to-Speech API に投入し、音声ファイルを生成
5.  **ファイル結合:**
    - FFmpeg を使い、生成した音声ファイル、Suno AI で作ったオープニング曲、Imagen 3 で作った表紙画像を結合
6.  **公開:**
    - 完成したファイルを YouTube にアップロード

---

# プロンプトを見てみる

- https://github.com/Seitaro-Yuki/ai-podcast/blob/main/radio-prompt.md

---

# コードを見てみる

- https://github.com/Seitaro-Yuki/ai-podcast/blob/main/google-tts.ts
- なるべく1ファイルで完結するように記述
    - メンテナンス性より、生成AIでの扱いやすさを優先

---

# 実際にポッドキャストを聞いてみる

[NEW CROSS RADIO 第18回 フッサール『論理学研究』 × CMMI](https://www.youtube.com/watch?v=atwy4KB2wY0)

---

# 所感 (1): AIコーディング支援

- プログラム作成には、まだ Clin や Claude Code のような**エージェント系は使っていない**
    - **理由:** セキュリティ的な不安が大きい
- **過去の経験:** 以前 `Cursor` (AI搭載エディタ) を試した際、会社の情シスから連絡が来たことがある...

---

# 所感 (2): 利用AIモデルの変遷

- 以前は OpenAI **o3-mini** や Anthropic **Claude 3.5 Sonnet** も試していた
- 現在は **Gemini 2.5 Pro** で十分、かつ高品質だと感じている
- **実感:** ポッドキャスト制作途中から台本作成を Gemini 2.5 Pro に切り替えたところ、内容（掛け合いの質など）が明らかに良くなった

---

# 余談 (1): このスライドもAI製

- 実は、この発表スライドも **Gemini 2.5 Pro** に生成させている
- 最近 **Marp** をよく使っているのは、AIにMarkdown形式で出力させやすいから

---

# 余談 (2): Emacs回帰

- 普段のメモや下書きは **Emacs の org-mode** で雑多に書く
- それを Gemini に食わせて Marp 形式に清書させる、というワークフロー
- **個人的なトレンド:** 生成AIの活用で **Emacs 回帰**が起きている

---

# 余談 (3): 参考資料

- このスライドの元になった org-mode ファイル
- 実際に使ったプロンプト集

（必要であれば、これらのファイルへのリンクや内容をここに追記）

---

<!--
_class: lead
_paginate: false
-->

# ご清聴ありがとうございました。
