[就職試験対策 第03回]

# 一般常識(国語)

## 1 漢字の読み

### 1.1 question.hを作成する

本テキストでは国語の問題を追加していきます。しかし、数々の数学の問題を作成したことで、`main.cpp`の全体を見るのが大変になりつつあります。これ以上`main.cpp`に問題を追加すると、もっと見づらくなるでしょう。

見づらくなることを避けるため、今後は教科ごとにファイルを分けることにします。そのためにまず、問題データを表現する`Question`構造体を、単独のファイルに移動させましょう。

ソリューションエクスプローラーの「ソースファイル」項目を右クリックし、「追加→新しい項目」を選びます。そして、`question.h`という名前のヘッダファイルを追加してください。

ヘッダファイルを作成したら`main.cpp`を開き、`question.h`をインクルードしてください。

```diff
+#include "question.h"
 #include <iostream>
 #include <string>
 #include <vector>
```

次に、`main.cpp`にある`Question`構造体の定義を切り取ってください。

```diff
 #include <string>
 #include <vector>
 #include <random>
 using namespace std;
-
-// 問題文と答えをまとめる構造体
-struct Question
-{
-  string q; // 問題文
-  string a; // 答え
-};

// 最大公約数を求める
int gcd(int a, int b)
```

そして、切り取ったテキストを`question.h`に貼り付けてください。

```diff
 #pragma once
+
+// 問題文と答えをまとめる構造体
+struct Question
+{
+  string q; // 問題文
+  string a; // 答え
+};
```

`question.h`には`string`ヘッダをインクルードしていないため、`string`型がエラーになってしまいます。そこで、`string`ヘッダをインクルードしてください。

```diff
 #pragma once
+#include <string>

 // 問題文と答えをまとめる構造体
 struct Question
 {
   string q; // 問題文
   string a; // 答え
 };
```

ところで、ヘッダファイルに`using namespace std`を書いてしまうと、標準ライブラリと重複する名前を定義できなくなってしまいます。

そのため、`using namespace std`が宣言されたヘッダファイルをインクルードしただけで、突然プログラムがビルドできなくなる恐れがあります。

このような問題が発生しないように、「ヘッダファイルには`using namespace std`を書かない」というのが暗黙のルールになっています。

`using namespace std`が使えないので、`Question`構造体の`string`は`std::string`に変える必要があります。`Question`構造体の定義を次のように変更してください。

```diff
 #pragma once
 #include <string>

 // 問題文と答えをまとめる構造体
 struct Question
 {
-  string q; // 問題文
-  string a; // 答え
+  std::string q; // 問題文
+  std::string a; // 答え
 };
```

次に、利便性を高めるために`vector<Question>`型の別名を定義します。`question.h`に以下のプログラムを追加してください。

```diff
 #pragma once
 #include <string>
+#include <vector>

 // 問題文と答えをまとめる構造体
 struct Question
 {
   std::string q; // 問題文
   std::string a; // 答え
 };
+
+// 問題の配列をあらわす型
+using QuestionList = std::vector<Question>;
```

<pre class="tnmai_assignment">
<strong>【課題01】</strong>
<code>main.cpp</code>と<code>question.h</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.2 漢字の読み取り問題を作成する

それでは、「漢字の読み取り」問題を出題する関数を作っていきましょう。

最初に、関数を宣言するヘッダファイルを追加します。ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。そして、`exam_japanese.h`という名前のヘッダファイルを追加してください。

>`exam`(エグザム)は「試験」という意味です。

「漢字問題を作成する関数」の名前は`CreateKanjiExam`(クリエイト・カンジ・エグザム、「漢字試験を作成する」という意味)とします。追加した`exam_japanese.h`を開き、次のプログラムを追加してください。

```diff
 #pragma once
+#include "question.h"
+
+// 漢字の読み取り問題を作成する
+QuestionList CreateKanjiExam();
```

続いて、C++ファイルを追加します。ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。そして、`exam_japanese.cpp`という名前のC++ファイルを追加してください。

追加した`exam_japanese.cpp`を開き、次のプログラムを追加してください。

```diff
+#include "exam_japanese.h"
+using namespace std;
+
+/*
+* 漢字の読み取り問題を作成する
+*/
+QuestionList CreateKanjiExam()
+{
+  static const struct {
+    const char* kanji;
+    const char* yomi;
+  } data[] = {
+    { "市井", "しせい" },
+    { "捺印", "なついん" },
+    { "相殺", "そうさい" },
+    { "凡例", "はんれい" },
+    { "約定", "やくじょう" },
+  };
+
+  constexpr int quizCount = 5;
+  QuestionList questions;
+  questions.reserve(quizCount);
+  for (int i = 0; i < quizCount; i++>) {
+    const auto& e = data[i];
+    questions.push_back({
+      "「" + string(e.kanji) + "」の読みをひらがなで答えよ",
+      e.yomi });
+  }
+  return questions;
+}
```

### 1.3 国語の問題を出題する

作成した関数を使って、国語の問題を出題するようにしましょう。`main.cpp`を開き、`exam_japanese.h`をインクルードしてください。

```diff
 #include "question.h"
+#include "exam_japanese.h"
 #include <iostream>
 #include <string>
 #include <vector>
 #include <random>
 using namespace std;
```

次に、`main`関数の定義に「数学と国語を切り替える機能」を追加してください。

```diff
   questions.push_back({
     to_string(x) + "人のうち" + to_string(y) + "人を選ぶ組み合わせは何通りあるか？",
     to_string(z) });

   cout << "[リクルート試験対策クイズ]\n";
+
+  cout << "教科を選んでください\n1=数学\n2=国語\n";
+  int subject;
+  cin >> subject;
+  if (subject == 2) {
+    questions = CreateKanjiExam();
+  }

   for (const auto& e : questions) {
     cout << e.q << "\n";
     string answer;
```

プログラムが書けたらビルドして実行してください。教科選択が表示されたら、`2`を入力して`Enter`を押してください。その後、国語の問題が出題されたら成功です。正答と誤答の両方を試すこと。

<pre class="tnmai_assignment">
<strong>【課題02】</strong>
<code>main.cpp</code>、<code>exam_japanese.h</code>、<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.4 出題順をランダムにする

毎回、同じ問題が同じ順番で出題されるのでは、あまり練習になりません。そこで、問題がランダムな順番で出題されるようにしましょう。これは、以下の手順(アルゴリズム)で実行できます。

>1. 問題の番号を配列に格納する。
>2. 配列をランダムに入れ替える(シャッフルする)。
>3. 配列の先頭から順番に番号を取り出し、番号に対応する問題を`questions`に追加する。

まず「ランダムな番号の配列を作る機能」を持つ関数を定義しましょう。「ランダムな番号の配列」を作るので、関数名は`CreateRandomIndices`(クリエイト・ランダム・インディシーズ)とします。

別の教科の問題を作成するときも使えるように、ファイルを分けましょう。ソリューションエクスプローラーの「ソースファイル」フィルタを右クリックし、「追加→新しい項目」を選んでください。

そして、`utility.h`(ユーティリティ・エイチ)という名前のヘッダファイルを追加してください。追加した`utility.h`を開き、次のプログラムを追加してください。

```diff
+#pragma once
+#include <vector>
+
+// ランダムな番号配列を作成する
+std::vector<int> CreateRandomIndices(int n);
```

次に関数を定義します。ソリューションエクスプローラーの「ソースファイル」フィルタを右クリックし、「追加→新しい項目」を選んでください。そして、`utility.cpp`という名前のCPPファイルを追加してください。

それでは、`1`の部分から作成しましょう。追加した`utility.cpp`を開き、次のプログラムを追加してください。

```diff
+#include "utility.h"
+#include <random>
+using namespace std;
+
+/*
+* ランダムな番号配列を作成する
+*/
+vector<int> CreateRandomIndices(int n)
+{
+  // 番号を配列に格納
+  vector<int> indices(n);
+  for (int i = 0; i < n; i++) {
+    indices[i] = i;
+  }
+
+  return indices;
+}
```

>`indices`(インディシーズ)は`index`の複数形です。なお、`index`の複数形には`indexes`(インデクセス)もあります。`indices`のほうが古くから使われており一般的ですが、`indexes`も間違いではありません。

続いて、`2`の「配列をランダムに入れ替える(シャッフルする)」プログラムを追加します。「配列のシャッフル」は以下の手順(アルゴリズム)で実現できます。

>1. 変数`i`を「配列の要素数 - 1」で初期化。
>2. `0`～`i`の範囲の乱数を生成し、変数`j`に代入。
>3. 配列の`i`番目と`j`番目を入れ替える。
>4. `i`を1減らす。
>5. `i`が0になったら終了。1以上なら「手順2」に戻る。

このアルゴリズムは、考案者の名前から「フィッシャー・イェーツのシャッフル」と呼ばれています(上記のアルゴリズムは、実際には「コンピューター用に改良されたバージョン」です)。

それでは、「フィッシャー・イェーツのシャッフル」を作成しましょう。問題の番号を配列に格納するプログラムの下に、次のプログラムを追加してください。

```diff
   // 番号を配列に格納
   vector<int> indices(n);
   for (int i = 0; i < n; i++) {
     indices[i] = i;
   }
+
+  // 番号の配列をシャッフル
+  random_device rd;
+  mt19937 rand(rd());
+  for (int i = n - 1; i > 0; i--) {
+    const int j = uniform_int_distribution<>(0, i)(rand);
+    const int tmp = indices[i];
+    indices[i] = indices[j];
+    indices[j] = tmp;
+  }

   return indices;
 }
```

これで「シャッフルされた番号の配列」を作れるようになりました。残る作業は、この配列を使って問題を作成するだけです。`exam_japanese.cpp`を開き、`utility.h`をインクルードしてください。

```diff
 #include "exam_japanese.h"
+#include "utility.h"
 #include <random>
 using namespace std;

 /*
 * 漢字の読み取り問題を作成する
 */
```

次に、`CreateKanjiExam`関数の定義にある、`questions`配列に問題を追加するプログラムを、次のように変更してください。

```diff
   constexpr int quizCount = 5;
   QuestionList questions;
   questions.reserve(quizCount);
+  const vector<int> indices = CreateRandomIndices(size(data));
+
   for (int i = 0; i < quizCount; i++>) {
-    const auto& e = data[i];
+    const auto& e = data[indices[i]];
     questions.push_back({
       "「" + string(e.kanji) + "」の読みをひらがなで答えよ",
       e.yomi });
   }
```

プログラムが書けたらビルドして実行してください。国語を選択したとき、出題される問題がランダムな順番になっていたら成功です。

<pre class="tnmai_assignment">
<strong>【課題03】</strong>
<code>utility.h</code>、<code>utility.cpp</code>、<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題04】</strong>
<code>data</code>配列に以下の問題を追加しなさい。
<table>
<tr><th>漢字(読み)</th><th>漢字(読み)</th>
<tr><td>必定(ひつじょう)</td><td>風情(ふぜい)</td>
<tr><td>知己(ちき)     </td><td>境内(けいだい)</td>
<tr><td>破綻(はたん)   </td><td>拘泥(こうでい)</td>
<tr><td>吟味(ぎんみ)   </td><td>承る(うけたまわる)</td>
<tr><td>寸暇(すんか)   </td><td>弄ぶ(もてあそぶ)</td>
<tr><td>灰汁(あく)     </td><td>潔い(いさぎよい)</td>
<tr><td>借款(しゃっかん)</td><td>培う(つちかう)</td>
<tr><td>老舗(しにせ)   </td><td>赴く(おもむく)</td>
<tr><td>疾病(しっぺい) </td><td>賄う(まかなう)</td>
<tr><td>界隈(かいわい) </td><td>背く(そむく)</td>
</table>
</pre>

<pre class="tnmai_assignment">
<strong>【課題05】</strong>
<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.5 慣用句の意味の問題を作成する

次に、「慣用句の意味を答える問題」を出題する関数を作成しましょう。慣用句は英語で`Idiom`(イディオム)なので、関数名は`CreateIdiomExam`(クリエイト・イディオム・エグザム)とします。

`exam_japanese.h`を開き、関数定義を追加してください。

```diff
 #pragma once
 #include "question.h"

 // 漢字の読み取り問題を作成する
 QuestionList CreateKanjiExam();
+
+// 慣用句の意味を答える問題を作成する
+QuestionList CreateIdiomExam();
```

次に`exam_japanese.cpp`を開き、`CreateKanjiExam`関数の定義の下に、次のプログラムを追加してください。

```diff
     questions.push_back({
       "「" + string(e.kanji) + "」の読みをひらがなで書くと？",
       e.yomi });
   }
   return questions;
 }
+
+/*
+* 慣用句の意味を答える問題を作成する
+*/
+QuestionList CreateIdiomExam()
+{
+  static const struct {
+    const char* idiom;   // 慣用句
+    const char* meaning; // 意味
+  } data[] = {
+    { "気のおけない", "気づかいがいらない" },
+    { "琴線に触れる", "心から感動する" },
+    { "汚名をそそぐ", "名誉を回復する" },
+    { "言質を取る", "証拠となる言葉を聞き出す" },
+    { "糠(ぬか)に釘", "効き目がない" },
+  };
+
+  constexpr int quizCount = 5;
+  QuestionList questions;
+  questions.reserve(quizCount);
+  const vector<int> indices = CreateRandomIndices(size(data));
+
+  for (int i = 0; i < quizCount; i++) {
+    const auto& e = data[indices[i]]; // 慣用句と正しい意味
+    const auto& f = data[indices[(i + 1) % size(data)]]; // 間違った意味
+
+    string s = "「" + string(e.idiom) + "」の意味として正しい番号を選べ\n";
+    s += string("  1:") + e.meaning + "\n";
+    s += string("  2:") + f.meaning;
+
+    questions.push_back({ s, "1" });
+  }
+  return questions;
+}
```

慣用句の意味を答える問題は、「2つの選択肢のうち正しいほうを答える」という形にしてみました。

それでは、国語に「慣用句の意味を答える問題」を追加しましょう。`main.cpp`を開き、`CreateIdiomExam`関数を呼び出すプログラムを追加してください。

```diff
   cout << "教科を選んでください\n1=数学\n2=国語\n";
   int subject;
   cin >> subject;
   if (subject == 2) {
     questions = CreateKanjiExam();
+    const QuestionList idiomExam = CreateIdiomExam();
+    questions.insert(questions.end(), idiomExam.begin(), idiomExam.end());
   }

   for (const auto& e : questions) {
     cout << e.q << "\n";
```

プログラムが書けたらビルドして実行してください。漢字の問題のあとで、慣用句の問題が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題06】</strong>
<code>main.cpp</code>、<code>exam_japanese.h</code>、<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.6 正解の番号をランダムにする

現在の慣用句の問題は、常に`1`が正解になっています。これではクイズの意味がありません。そこで、正解の番号をランダムにしましょう。

```diff
   constexpr int quizCount = 5;
   QuestionList questions;
   questions.reserve(quizCount);
   const vector<int> indices = CreateRandomIndices(size(data));
+  random_device rd;

   for (int i = 0; i < quizCount; i++) {
     const auto& e = data[indices[i]]; // 慣用句と正しい意味
     const auto& f = data[indices[(i + 1) % size(data)]]; // 間違った意味

     string s = "「" + string(e.idiom) + "」の意味として正しい番号を選べ\n";
+    const int correctNo = std::uniform_int_distribution<>(1, 2)(rd);
+    if (correctNo == 1) {
       s += std::string("  1:") + e.meaning + "\n";
       s += std::string("  2:") + f.meaning;
+    } else {
+      s += std::string("  1:") + f.meaning + "\n";
+      s += std::string("  2:") + e.meaning;
+    }

-    questions.push_back({ s, "1" });
+    questions.push_back({ s, to_string(correctNo) });
   }

   for (const auto& e : questions) {
     cout << e.q << "\n";
```

このプログラムでは`random_device`クラスで乱数を生成しています。「真の乱数」が遅いとはいっても、この程度の問題数ではほとんど影響はないからです。

プログラムが書けたらビルドして実行してください。慣用句の問題について、正解が`2`の問題が出題されていたら成功です。

<pre class="tnmai_assignment">
<strong>【課題08】</strong>
<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.7 間違った答えをランダムにする

現在、「間違った答え」は次の問題の答えになっています。これでは、次の問題の答えが分かったも同然です。そこで、間違った答えをランダムに選びましょう。

間違った意味を選ぶプログラムを、次のように変更してください。

```diff
   const vector<int> indices = CreateRandomIndices(size(data));
   random_device rd;

   for (int i = 0; i < quizCount; i++) {
     const auto& e = data[indices[i]]; // 慣用句と正しい意味
+
+    // 間違った意味をランダムに選ぶ
+    // i以上の値が選ばれた場合、1を足すことでi番目を除外する
+    // 1を足したときに値が範囲外にならないように、最大値を「要素数-2」としている
+    int wrongIndex = std::uniform_int_distribution<>(0, size(data) - 2)(rd);
+    if (wrongIndex >= i) {
+      wrongIndex++;
+    }
-    const auto& f = data[indices[(i + 1) % size(data)]]; // 間違った意味
+    const auto& f = data[indices[wrongIndex]]; // 間違った意味

     string s = "「" + string(e.idiom) + "」の意味として正しい番号を選べ\n";
     const int correctNo = std::uniform_int_distribution<>(1, 2)(rd);
     if (correctNo == 1) {
```

プログラムが書けたらビルドして実行してください。毎回、異なる「間違った答え」が表示されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題09】</strong>
慣用句の<code>data</code>配列に以下の問題を追加しなさい。
<table>
<tr><th>慣用句</th><th>意味</th>
<tr><td>二階から目薬</td><td>回りくどくて効果がない</td>
<tr><td>意表をつく</td><td>予想外なことをして驚かせる</td>
<tr><td>禁じざるを得ない</td><td>禁止しなくてはならない</td>
<tr><td>虎の尾を踏む</td><td>進んで危険なことをする</td>
<tr><td>目から鼻へ抜ける</td><td>頭の回転が早くて行動が素早い</td>
<tr><td>情けは人の為(ため)ならず</td><td>親切な行いは、いずれ自分のためになる</td>
<tr><td>青菜に塩</td><td>元気を失っている様(さま)</td>
<tr><td>他山の石</td><td>人の失敗を、自分の行いを正す参考にする</td>
<tr><td>口を糊(のり)する</td><td>余裕のない貧しい生活をする</td>
<tr><td>身命を賭す</td><td>命を投げ出す覚悟で努力する</td>
</table>
</pre>

<pre class="tnmai_assignment">
<strong>【課題10】</strong>
<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題11】</strong>
<code>Git</code>メニューから「同期」を選択し、コミットをリモートリポジトリに反映しなさい。
</pre>