[就職試験対策 第05回]

# 一般常識(英語)

## 1 英単語

### 1.1 英単語の問題を出題する

今回は、「英単語の読み書き」問題を出題する関数を作っていきます。

最初に、関数を宣言するヘッダファイルを追加します。ソリューションエクスプローラーの<br>
「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。<br>
そして、`exam_english.h`という名前のヘッダファイルを追加してください。

「英単語問題を作成する関数」の名前は`CreateEnglishWordExam`(クリエイト・イングリッシュ・ワード・エグザム、「英単語試験を作成する」という意味)とします。<br>
追加した`exam_english.h`を開き、次のプログラムを追加してください。

```diff
 #pragma once
+#include "question.h"
+
+// 英単語の問題を作成する
+QuestionList CreateEnglishWordExam();
```

続いて、C++ファイルを追加します。ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。そして、`exam_english.cpp`という名前のC++ファイルを追加してください。

追加した`exam_english.cpp`を開き、次のプログラムを追加してください。

```diff
+#include "exam_english.h"
+#include "utility.h"
+using namespace std;
+
+/*
+* 英単語の問題を作成する
+*/
+QuestionList CreateEnglishWordExam()
+{
+  const struct {
+    const char* word; // 英単語
+    const char* reading; // 読み
+    const char* meaning; // 意味
+  } data[] = {
+    { "state", "ステート", "状態" },
+    { "business", "ビジネス", "事業、仕事" },
+    { "abstract", "アブストラクト", "抽象的な" },
+    { "concrete", "コンクリート", "具体的な" },
+    { "digital", "デジタル", "数字で情報を伝える" },
+    { "analogue", "アナログ", "類似している、類似物" },
+    { "exchange", "エクスチェンジ", "交換、両替、為替" },
+    { "infrastructure", "インフラストラクチャ", "下部構造、基盤となる施設や設備" },
+    { "knowledge", "ナレッジ", "知識" },
+    { "credit", "クレジット", "信用" },
+  };
+
+  constexpr int quizCount = 5;
+  QuestionList questions;
+  questions.reserve(quizCount);
+  const vector<int> indices = CreateRandomIndices(size(data));
+
+  for (int i = 0; i < quizCount; i++) {
+    const auto& e = data[indices[i]];
+    questions.push_back({
+      "「" + string(e.meaning) + "」を意味する英単語を答えよ",
+      e.word });
+  }
+
+  return questions;
+}
```

英単語の問題を作成するプログラムの内容は、国語のプログラムとよく似ています。言語が違うだけで、「語と意味のつながりを答える」という点は同じだからです。

それでは、英語の問題が出題されるようにしましょう。<br>
`main.cpp`を開き、`exam_english.h`をインクルードしてください。

```diff
 #include "question.h"
 #include "exam_japanese.h"
+#include "exam_english.h"
 #include <iostream>
 #include <string>
 #include <vector>
```

次に、教科を選択するプログラムを、次のように変更してください。

```diff
   questions.push_back({
     to_string(x) + "人のうち" + to_string(y) + "人を選ぶ組み合わせは何通りあるか？",
     to_string(z) });

   cout << "[リクルート試験対策クイズ]\n";

-  cout << "教科を選んでください\n1=数学\n2=国語\n";
+  cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n";
   int subject;
   cin >> subject;
   if (subject == 2) {
     questions = CreateKanjiExam();
     QuestionList synonymExam = CreateSynonymExam();
     questions.insert(questions.end(), synonymExam.begin(), synonymExam.end());
     QuestionList antonymExam = CreateAntonymExam();
     questions.insert(questions.end(), antonymExam.begin(), antonymExam.end());
     QuestionList idiomExam = CreateIdiomExam();
     questions.insert(questions.end(), idiomExam.begin(), idiomExam.end());
     QuestionList homophoneExam = CreateHomophoneExam();
     questions.insert(questions.end(), homophoneExam.begin(), homophoneExam.end());
+  } else if (subject == 3) {
+    questions = CreateEnglishWordExam();
   }

   for (const auto& e : questions) {
     cout << e.q << "\n";
```

プログラムが書けたらビルドして実行してください。教科選択で`3`を選び、英単語の問題が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題01】</strong>
<code>exam_english.h</code>、<code>exam_english.cpp</code>, <code>recruit_quiz.vcproj</code>, <code>recruit_quiz.vcproj.filters</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.2 問題のバリエーションを追加する

`1.1`節では、「意味から単語を答える問題」を作成しました。単語の理解度を高められるように、以下の種類の問題を出題する機能を追加しましょう。

>1. 「読み」から英単語を答える
>2. 英単語から「読み」を答える
>3. 英単語の意味を答える

`exam_english.cpp`を開き、`random`ヘッダをインクルードしてください。

```diff
 #include "exam_english.h"
 #include "utility.h"
+#include <random>
 using namespace std;

 /*
 * 英単語の問題を作成する
 */
```

次に、問題を作成するプログラムに、「問題の種類をランダムに選ぶ」機能を追加してください。

```diff
   constexpr int quizCount = 5;
   QuestionList questions;
   questions.reserve(quizCount);
   const vector<int> indices = CreateRandomIndices(size(data));
+  random_device rd;

+  // 問題の種類をランダムに選ぶ
+  const int type = uniform_int_distribution<>(0, 3)(rd);
+  switch (type) {
+  case 0: // 意味から英単語を答える
     for (int i = 0; i < quizCount; i++) {
       const auto& e = data[indices[i]];
       questions.push_back({
         "「" + string(e.meaning) + "」を意味する英単語を答えよ",
         e.word });
     }
+    break;
+  } // switch (type)

   return questions;
 }
```

それでは、まず`1`の「読みから英単語を答える」問題を作成できるようにしましょう。
`switch`文にある「意味から英単語を答える」プログラムの下に、次の`case`文を追加してください。

```diff
   case 0: // 意味から英単語を答える
     for (int i = 0; i < quizCount; i++) {
       const auto& e = data[indices[i]];
       questions.push_back({
         "「" + string(e.meaning) + "」を意味する英単語を答えよ",
         e.word });
     }
     break;
+
+  case 1: // 「読み」から英単語を答える
+    for (int i = 0; i < quizCount; i++) {
+      const auto& e = data[indices[i]];
+      questions.push_back({
+        "カタカナの読み「" + string(e.reading) + "」に対応する英単語を答えよ",
+        e.word });
+    }
+    break;
   } // switch (type)

   return questions;
 }
```

次に、「英単語から読みを答える」問題を作成できるようにします。<br>
「読みから英単語を答える」プログラムの下に、次の`case`文を追加してください。

```diff
   case 1: // 「読み」から英単語を答える
     for (int i = 0; i < quizCount; i++) {
       const auto& e = data[indices[i]];
       questions.push_back({
         "カタカナの読み「" + string(e.reading) + "」に対応する英単語を答えよ",
         e.word });
     }
     break;
+
+  case 2: // 英単語から「読み」を答える
+    for (int i = 0; i < quizCount; i++) {
+      const auto& e = data[indices[i]];
+      questions.push_back({
+        string(e.word) + "の読みをカタカナで答えよ",
+        e.reading });
+    }
+    break;
   } // switch (type)

   return questions;
 }
```

同様に、「英単語の意味を答える」問題を作成可能にします。<br>
「英単語から読みを答える」プログラムの下に、次の`case`文を追加してください。

```diff
   case 2: // 英単語から「読み」を答える
     for (int i = 0; i < quizCount; i++) {
       const auto& e = data[indices[i]];
       questions.push_back({
         string(e.word) + "の読みをカタカナで答えよ",
         e.reading });
     }
     break;
+
+  case 3: // 英単語の意味を答える
+    for (int i = 0; i < quizCount; i++) {
+      // 間違った番号をランダムに選ぶ
+      const int correctIndex = indices[i];
+      vector<int> answers = CreateWrongIndices(size(data), correctIndex);
+
+      // ランダムな位置を正しい番号で上書き
+      const int correctNo = std::uniform_int_distribution<>(1, 3)(rd);
+      answers[correctNo - 1] = correctIndex;
+
+      // 問題文を作成
+      string s = "「" + string(data[correctIndex].word) +
+        "」の意味として正しい番号を選べ\n";
+      s += std::string("  1:") + data[answers[0]].meaning + "\n";
+      s += std::string("  2:") + data[answers[1]].meaning + "\n";
+      s += std::string("  3:") + data[answers[2]].meaning;
+
+      questions.push_back({ s, to_string(correctNo) });
+    }
+    break;
   } // switch (type)

   return questions;
 }
```

国語の問題のときと同じ方法を使って、「正解」と「間違い」の順番をランダムに決めています。

プログラムが書けたらビルドして実行してください。何度か英語の問題を実行し、違う種類の問題が出題されることを確認してください。そのうえで、問題の正答、誤答が正しく判定されることが確認できたら成功です。

<pre class="tnmai_assignment">
<strong>【課題02】</strong>
<code>exam_english.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<div style="page-break-after: always"></div>

<pre class="tnmai_assignment">
<strong>【課題03】</strong>
英単語の<code>data</code>配列に以下の問題を追加しなさい。
<table>
<tr><th>英単語(読み)</th><th>意味</th></tr>
<tr><td>routine(ルーティン)</td><td>決まった手順、日課</td></tr>
<tr><td>management(マネジメント)</td><td>管理、経営</td></tr>
<tr><td>account(アカウント)</td><td>会計、口座</td></tr>
<tr><td>unique(ユニーク)</td><td>他に類を見ない、個性的な</td></tr>
<tr><td>variety(バラエティ)</td><td>変化に富む、多様な</td></tr>
<tr><td>schedule(スケジュール)</td><td>予定、計画</td></tr>
<tr><td>agenda(アジェンダ)</td><td>課題、議題</td></tr>
<tr><td>technology(テクノロジー)</td><td>科学技術</td></tr>
<tr><td>collaboration(コラボレーション)</td><td>協力、共同事業、共同作業</td></tr>
<tr><td>tax(タックス)</td><td>税金</td></tr>
<tr><td>stock(ストック)</td><td>在庫、株券</td></tr>
<tr><td>product(プロダクト)</td><td>製品</td></tr>
<tr><td>booking(ブッキング)</td><td>予約、帳簿への記入</td></tr>
<tr><td>weight(ウェイト)</td><td>重さ</td></tr>
<tr><td>compliance(コンプライアンス)</td><td>法令・社会的規範を守る</td></tr>
<tr><td>receipt(レシート)</td><td>領収書</td></tr>
</table>
</pre>

<pre class="tnmai_assignment">
<strong>【課題04】</strong>
<code>exam_english.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.3 熟語と構文

就職試験では、英語の「熟語」や「基本的な構文」もよく出題されます。これらの「定型文」は英語の長い歴史をへて現在の意味にたどり着いています。そのため、個々の単語だけを知っていても、文章としては理解できません。

そこで、これらの「定型文」を出題する関数を作成し、学習に使えるようにしましょう。「定型文」は`phrase`(フレーズ)というので、関数名は`CreateEnglishPhraseExam`(クリエイト・イングリッシュ・フレーズ・エグザム)とします。

`exam_english.h`を開き、次の関数宣言を追加してください。

```diff
 #pragma once
 #include "question.h"

 // 英単語の問題を作成する
 QuestionList CreateEnglishWordExam();
+
+// 英語の定型文の問題を作成する
+QuestionList CreateEnglishPhraseExam();
```

定型文の問題の定番は「定型文の意味を答える問題」と「穴埋め問題」です。そこで、定型文の問題では、各問題ごとに以下の5つの要素を持たせることにします。

>1. 定型文の形
>2. 定型文の意味
>3. 英語の例文
>4. 例文の日本語訳
>5. 穴にする単語の位置

最初に、上記の4つの要素を持つ問題データを作成しましょう。`exam_english.cpp`を開き、次のプログラムを追加してください。

```diff
     break;
   } // switch (type)
 
   return questions;
 }
+
+/*
+* 英語の定型文の問題を作成する
+*/
+QuestionList CreateEnglishPhraseExam()
+{
+  const struct {
+    const char* phrase;      // 定型文の形
+    const char* meaning;     // 定型文の意味
+    const char* example;     // 英語の例文
+    const char* translation; // 例文の日本語訳
+    int blankCount;          // 穴の候補の数
+    int blankOptions[3];     // 穴の候補になる単語の位置
+  } data[] = {
+    { "too [A] to [B]", "とても[A]なので[B]できない",
+      "The tea was too hot to drink.", "お茶が熱すぎて飲めなかった。",
+      2, { 3, 5 }},
+    { "be looking forward to [A]", "[A]を楽しみにする",
+      "I'm looking forward to seening you", "あなたに会うのを楽しみにしています。",
+      3, { 1, 2, 3 }},
+    { "according to [A]", "[A]によると",
+      "Acoording to the weather forcast, today is rain.", "天気予報によると、今日は雨だ。",
+      2, { 0, 1 }},
+    { "as soon as [A]", "[A]するとすぐに",
+      "As soon as I arrive, I will call you.", "到着したらすぐに電話します。",
+      3, { 0, 1, 2 }},
+    { "be not supposed to [A]", "[A]してはいけないことになっている",
+      "You are not supposed to enter this room.", "この部屋に入ってはいけません。",
+      3, { 2, 3, 4 }},
+  };
+}
```

次に、このデータから問題を作成します。問題のパターンには以下の3つが考えられます。

>1. 空欄にはいる単語を答える。
>2. 定型文の意味を答える。
>3. 意味の合う定型文を答える。

それでは、問題データの定義の下に、これらのパターンをランダムに選ぶ`switch`文を追加してください。

```diff
     { "be not supposed to [A]", "[A]してはいけないことになっている",
       "You aren't supposed to enter this room.", "この部屋に入ってはいけません。"},
   };
+
+  constexpr int quizCount = 5;
+  QuestionList questions;
+  questions.reserve(quizCount);
+  const vector<int> indices = CreateRandomIndices(size(data));
+  random_device rd;
+
+  // 問題の種類をランダムに選ぶ
+  int type = uniform_int_distribution<>(0, 2)(rd);
+  switch (type) {
+  case 0: // 空欄にはいる単語を答える
+    break;
+
+  case 1: // 定型文の意味を答える
+    break;
+
+  case 2: // 意味の合う定型文を答える
+    break;
+  } // switch (type)
+
+  return questions;
 }
```

続いて、問題のパターンを一つずつ作成していきます。`0`番の`case`文に「空欄にはいる単語を答える問題」を作成するプログラムを追加してください。

```diff
   // 問題の種類をランダムに選ぶ
   int type = uniform_int_distribution<>(0, 2)(rd);
   switch (type) {
   case 0: // 空欄にはいる単語を答える
+    for (int i = 0; i < quizCount; i++) {
+      const auto& e = data[indices[i]];
+
+      // 例文を単語に分解
+      vector<string> words;
+      const char* start = e.example;
+      for (const char* p = e.example; *p; p++) {
+        if (*p == ' ') {
+          words.push_back(string(start, p));
+          start = p + 1;
+        }
+      }
+      words.push_back(string(start));
+
+      // 穴にする単語の位置を選ぶ
+      int n = uniform_int_distribution<>(0, e.blankCount - 1)(rd);
+      int blankIndex = e.blankOptions[n];
+
+      // 穴にする位置の単語を答えとする
+      string a = words[blankIndex];
+
+      // 穴にする位置の単語を「空欄」に置き換える
+      words[blankIndex] = "[ ? ]";
+
+      // 単語を文に復元
+      string s = words[0];
+      for (int j = 1; j < words.size(); j++) {
+        s += " " + words[j];
+      }
+
+      // 問題文と答えを追加
+      questions.push_back({
+        "[ ? ]に適切な語を入れて英文を完成させよ\n" + string(e.translation) + "\n" + s,
+        a });
+    }
     break;

   case 1: // 定型文の意味を答える
     break;
```

次に、「定型文の意味を答える問題」を作成しましょう。`1`番の`case`文に次のプログラムを追加してください。

```diff
       questions.push_back({
         "[ ? ]に適切な語を入れて英文を完成させよ\n" + string(e.translation) + "\n" + s,
         a});
     }
     break;

   case 1: // 定型文の意味を答える
+    for (int i = 0; i < quizCount; i++) {
+      // 間違った番号をランダムに選ぶ
+      const int correctIndex = indices[i];
+      vector<int> answers = CreateWrongIndices(size(data), correctIndex);
+
+      // ランダムな位置を正しい番号で上書き
+      const int correctNo = std::uniform_int_distribution<>(1, 3)(rd);
+      answers[correctNo - 1] = correctIndex;
+
+      // 問題文を作成
+      string s = "「" + string(data[correctIndex].phrase) +
+        "」の意味として正しい番号を選べ\n";
+      s += std::string("  1:") + data[answers[0]].meaning + "\n";
+      s += std::string("  2:") + data[answers[1]].meaning + "\n";
+      s += std::string("  3:") + data[answers[2]].meaning;
+
+      questions.push_back({ s, to_string(correctNo) });
+    }
     break;

   case 2: // 意味の合う定型文を答える
     break;
```

続いて、「意味の合う定型文を答える問題」を作成します。`2`番の`case`文に次のプログラムを追加してください。

```diff
       questions.push_back({ s, to_string(correctNo) });
     }
     break;

   case 2: // 意味の合う定型文を答える
+    for (int i = 0; i < quizCount; i++) {
+      // 間違った番号をランダムに選ぶ
+      const int correctIndex = indices[i];
+      vector<int> answers = CreateWrongIndices(size(data), correctIndex);
+
+      // ランダムな位置を正しい番号で上書き
+      const int correctNo = std::uniform_int_distribution<>(1, 3)(rd);
+      answers[correctNo - 1] = correctIndex;
+
+      // 問題文を作成
+      string s = "「" + string(data[correctIndex].meaning) + "」に対応する定型文を選べ\n";
+      s += std::string("  1:") + data[answers[0]].phrase + "\n";
+      s += std::string("  2:") + data[answers[1]].phrase + "\n";
+      s += std::string("  3:") + data[answers[2]].phrase;
+
+      questions.push_back({ s, to_string(correctNo) });
+    }
     break;
   } // switch (type)
```

最後に、英語の定型文の問題が出題されるようにします。`main.cpp`を開き、英語の問題を設定するプログラムに、次のプログラムを追加してください。

```diff
   if (subject == 2) {
     questions = CreateKanjiExam();
     QuestionList idiomExam = CreateIdiomExam();
     questions.insert(questions.end(), idiomExam.begin(), idiomExam.end());
   } else if (subject == 3) {
     questions = CreateEnglishWordExam();
+    QuestionList phraseExam = CreateEnglishPhraseExam();
+    questions.insert(questions.end(), phraseExam.begin(), phraseExam.end());
   }

   for (const auto& e : questions) {
     cout << e.q << "\n";
     string answer;
```

プログラムが書けたらビルドして実行してください。教科選択で`3`を選び、英単語の次に定型文の問題が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題05】</strong>
<code>exam_english.h</code>、<code>exam_english.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<div style="page-break-after: always"></div>

<pre class="tnmai_assignment">
<strong>【課題06】</strong>
定型文の<code>data</code>配列に以下の問題を追加しなさい。
空欄の位置は、定型文と例文を比較しながら設定すること。
<table>
<tr><th>定型文</th><th>意味</th><th>例文</th><th>日本語訳</th>
<tr><td>be going to [A]</td><td>[A]するつもりだ</td><td>I am going to visit Osaka.</td><td>大阪に行くつもりだ。</td></tr>
<tr><td>turn to [A] for [B]</td><td>[A]に[B]を求める</td><td>She turn to the friends for help.</td><td>彼女は友達に助けを求めた。</td></tr>
<tr><td>it is not until [A] that [B]</td><td>[A]して初めて[B]する</td><td>It was not until dawn that the trouble solved.</td><td>夜明けまで問題は解決しなかった。</td></tr>
<tr><td>[A] as well as [B]</td><td>[B]だけでなく[A]も</td><td>He can speak French as well as English.</td><td>彼は英語だけでなくフランス語も話せる。</td></tr>
<tr><td>[A] remain [B]</td><td>[A]が[B]のままでいる</td><td>She remained silent.</td><td>彼女は黙っていた。</td></tr>
<tr><td>both [A] and [B]</td><td>[A]と[B]の両方とも</td><td>This bag is both quality and cheap.</td><td>このかばんは品質が良いうえに安い。</td></tr>
<tr><td>either [A] or [B]</td><td>[A]か[B]のどちらか</td><td>Mom says that you can have either a dog or a cat.</td><td>母は、犬か猫のどちらかなら飼ってもいいと言っている。</td></tr>
<tr><td>neither [A] nor [B]</td><td>[A]も[B]も両方ともない</td><td>We have neither the time nor the resources.</td><td>我々には時間も資源もない。</td></tr>
<tr><td>not only [A] but also [B]</td><td>[A]だけでなく[B]も</td><td>He likes not only movies but also books.</td><td>彼は映画だけでなく本も好きだ。</td></tr>
<tr><td>take care of [A]</td><td>[A]の世話をする</td><td>She always takes care of her sister.</td><td>彼女はいつも妹の面倒を見ている。</td></tr></table></pre>

<pre class="tnmai_assignment">
<strong>【課題07】</strong>
<code>exam_english.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題08】</strong>
<code>Git</code>メニューから「同期」を選択し、コミットをリモートリポジトリに反映しなさい。
</pre>
