[就職試験対策 第11回]

# 成績をファイルに出力する

## 1. 問題作成プログラムを整理する

### 1.1 数学の問題作成プログラムを関数化する

数学の問題作成プログラムは最初に作成したこともあり、基本的な仕組みに慣れてもらうために、`main`関数に直接書いていました。しかし、様々なプログラムを追加した今となっては、数学プログラムだけが`main`関数にあるのは構造的に不自然です。

そこで、他の教科と同じように、数学の問題作成プログラムを別のファイルに移動して、問題作成関数を呼び出す形式に変えようと思います。

さっそくヘッダファイルを作成しましょう。数学は英語で`mathematics`(マスマティックス)なので、ファイル名は`exam_mathematics.h`とします。

ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。そして、`exam_mathematics.h`という名前のヘッダファイルを追加してください。

「数学の問題を作成する関数」の名前は`CreateMathematicsExam`(クリエイト・マスマティックス・エグザム、「数学の試験を作成する」という意味)とします。追加した`exam_mathematics.h`を開き、次のプログラムを追加してください。

```diff
+#pragma once
+#include "question.h"
+
+// 数学の問題を作成する
+QuestionList CreateMathematicsExam();
```

続いて、C++ファイルを追加します。ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。そして、`exam_mathematics.cpp`という名前のC++ファイルを追加してください。

最初に、数学の問題を作成する関数を定義します。追加した`exam_mathematics.cpp`を開き、次のプログラムを追加してください。

```diff
+#include "exam_mathematics.h"
+#include "utility.h"
+#include <random>
+using namespace std;
+
+/*
+* 数学の問題を作成する
+*/
+QuestionList CreateMathematicsExam()
+{
+  QuestionList questions(3);
+
+  return questions;
+}
```

この関数の定義に、`main`関数からプログラムを切り取って貼り付けます。`main.cpp`を開き、数学の問題を作成するプログラムを切り取ってください。

```diff
 int main()
 {
   vector<Question> questions(3);
-
-  random_device rd;
-  mt19937 rand(rd());
-
-  // 掛け算
-  int x = uniform_int_distribution<>(1, 30)(rand);
-  int y = uniform_int_distribution<>(1, 20)(rand);
-  questions[0].q = to_string(x) + "x" + to_string(y) + "の答えは？";
-  questions[0].a = to_string(x * y);
-
-  // 割り算
-  x = uniform_int_distribution<>(1, 30)(rand);
-           ・
-           ・
-         (省略)
-           ・
-           ・
-  questions.push_back({
-    to_string(x) + "人のうち" + to_string(y) + "人を選んで並べる方法は何通りあるか？",
-    to_string(z) });
-
-  // 組み合わせ
-  x = uniform_int_distribution<>(3, 6)(rand);
-  y = uniform_int_distribution<>(1, x)(rand);
-  z = 1;
-  for (int i = 0; i < y; i++) {
-    z *= x - i;
-  }
-  for (int i = 0; i < y; i++) {
-    z /= y - i;
-  }
-  questions.push_back({
-    to_string(x) + "人のうち" + to_string(y) + "人を選ぶ組み合わせは何通りあるか？",
-    to_string(z) });

   cout << "[リクルート試験対策クイズ]\n";

   cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n6=政治\n7=経済\n";
   int subject;
   cin >> subject;
```

次に、`exam_mathematics.cpp`を開き、切り取ったプログラムを`CreateMathematicsExam`関数の定義に貼り付けてください。

```diff
 /*
 * 数学の問題を作成する
 */
 QuestionList CreateMathematicsExam()
 {
   QuestionList questions(3);
+
+  random_device rd;
+  mt19937 rand(rd());
+
+  // 掛け算
+  int x = uniform_int_distribution<>(1, 30)(rand);
+  int y = uniform_int_distribution<>(1, 20)(rand);
+  questions[0].q = to_string(x) + "x" + to_string(y) + "の答えは？";
+  questions[0].a = to_string(x * y);
+
+  // 割り算
+  x = uniform_int_distribution<>(1, 30)(rand);
+           ・
+           ・
+         (省略)
+           ・
+           ・
+  questions.push_back({
+    to_string(x) + "人のうち" + to_string(y) + "人を選んで並べる方法は何通りあるか？",
+    to_string(z) });
+
+  // 組み合わせ
+  x = uniform_int_distribution<>(3, 6)(rand);
+  y = uniform_int_distribution<>(1, x)(rand);
+  z = 1;
+  for (int i = 0; i < y; i++) {
+    z *= x - i;
+  }
+  for (int i = 0; i < y; i++) {
+    z /= y - i;
+  }
+  questions.push_back({
+    to_string(x) + "人のうち" + to_string(y) + "人を選ぶ組み合わせは何通りあるか？",
+    to_string(z) });

   return questions;
 }
```

それから、数学の問題作成プログラムでは最大公約数を求める`gcd`関数を使っています。この関数も`main.cpp`から`exam_mathematics.cpp`に移動させる必要があります。`main.cpp`を開き、`gcd`関数の定義を切り取ってください。

```diff
 #include <vector>
 #include <random>
 using namespace std;
-
-// 最大公約数を求める
-int gcd(int a, int b)
-{
-  while (b) {
-    int r = a % b;
-    a = b;
-    b = r;
-  }
-  return a;
-}

 int main()
 {
   vector<Question> questions(3);
```

次に`exam_mathematics.cpp`を開き、切り取ったプログラムを`using`宣言の下に貼り付けてください。

```diff
 #include "exam_mathematics.h"
 #include "utility.h"
 #include <random>
 using namespace std;
+
+// 最大公約数を求める
+int gcd(int a, int b)
+{
+  while (b) {
+    int r = a % b;
+    a = b;
+    b = r;
+  }
+  return a;
+}

 /*
 * 数学の問題を作成する
 */
 QuestionList CreateMathematicsExam()
 {
   QuestionList questions(3);
```

これで、「数学の問題を作成するプログラム」を関数化することができました。

仕上げに、`main`関数から作成した関数を呼び出しましょう。`main.cpp`を開き、`exam_mathematics.h`をインクルードしてください。

```diff
 #include "question.h"
+#include "exam_mathematics.h"
 #include "exam_japanese.h"
 #include "exam_english.h"
 #include "exam_science.h"
```

続いて、`main`関数の定義を次のように変更してください。

```diff
   cout << "[リクルート試験対策クイズ]\n";

   cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n6=政治\n7=経済\n";
   int subject;
   cin >> subject;
+  if (subject == 1) {
+    questions = CreateMathematicsExam();
-  if (subject == 2) {
+  } else if (subject == 2) {
     questions = CreateKanjiExam();
     QuestionList synonymExam = CreateSynonymExam();
```

プログラムが書けたらビルドして実行してください。教科選択で`1`の数学を選んだとき、関数化する前と同じように問題が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題01】</strong>
<code>main.cpp</code>, <code>exam_mathematics.h</code>, <code>exam_mathematics.cpp</code>, <code>recruit_quiz.vcproj</code>,
<code>recruit_quiz.vcproj.filters</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.2 国語の問題をまとめて作成する関数を追加する

現在、選択された教科を`if`で判定して、対応する問題文を作成しています。このとき、国語と英語では、複数の問題リストを`insert`メンバ関数でつなげて、教科全体の問題リストを作成しています。

ただ、この部分は少し読みづらいプログラムになっています。そこで、`main`関数を読みやすくするために、このプログラムの関数化を試してみようと思います。`exam_japanese.h`を開き、次のプログラムを追加してください。

```diff
 // 対義語の問題を作成する
 QuestionList CreateAntonymExam();

 // 類義語の問題を作成する
 QuestionList CreateSynonymExam();
+
+// 国語の問題を作成する
+QuestionList CreateJapaneseExam();
```

次に`exam_japanese.cpp`を開き、ファイルの末尾に`CreateJapaneseExam`関数の定義を追加してください。

```diff
     questions.push_back({ s, to_string(correctNo) });
   }
   return questions;
 }
+
+/*
+* 国語の問題を作成する
+*/
+QuestionList CreateJapaneseExam()
+{
+  QuestionList questions;
+  return questions;
+}
```

`main.cpp`を開き、`main`関数の定義から、国語の問題リストを作成するプログラムを切り取ってください。

```diff
   cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n6=政治\n7=経済\n";
   int subject;
   cin >> subject;
   if (subject == 1) {
     questions = CreateMathematicsExam();
   } else if (subject == 2) {
-    questions = CreateKanjiExam();
-    QuestionList synonymExam = CreateSynonymExam();
-    questions.insert(questions.end(), synonymExam.begin(), synonymExam.end());
-    QuestionList antonymExam = CreateAntonymExam();
-    questions.insert(questions.end(), antonymExam.begin(), antonymExam.end());
-    QuestionList idiomExam = CreateIdiomExam();
-    questions.insert(questions.end(), idiomExam.begin(), idiomExam.end());
-    QuestionList homophoneExam = CreateHomophoneExam();
-    questions.insert(questions.end(), homophoneExam.begin(), homophoneExam.end());
   } else if (subject == 3) {
     questions = CreateEnglishWordExam();
     QuestionList phraseExam = CreateEnglishPhraseExam();
     questions.insert(questions.end(), phraseExam.begin(), phraseExam.end());
```

`exam_japanese.cpp`に戻り、切り取ったプログラムを`CreateJapaneseExam`関数の定義に貼り付けてください。

```diff
 /*
 * 国語の問題を作成する
 */
 QuestionList CreateJapaneseExam()
 {
   QuestionList questions;
+  questions = CreateKanjiExam();
+  QuestionList synonymExam = CreateSynonymExam();
+  questions.insert(questions.end(), synonymExam.begin(), synonymExam.end());
+  QuestionList antonymExam = CreateAntonymExam();
+  questions.insert(questions.end(), antonymExam.begin(), antonymExam.end());
+  QuestionList idiomExam = CreateIdiomExam();
+  questions.insert(questions.end(), idiomExam.begin(), idiomExam.end());
+  QuestionList homophoneExam = CreateHomophoneExam();
+  questions.insert(questions.end(), homophoneExam.begin(), homophoneExam.end());
   return questions;
 }
```

再び`main.cpp`を開き、`main`関数の定義に`CreateJapaneseExam`関数を呼び出すプログラムを追加してください。

```diff
   cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n6=政治\n7=経済\n";
   int subject;
   cin >> subject;
   if (subject == 1) {
     questions = CreateMathematicsExam();
   } else if (subject == 2) {
+    questions = CreateMathematicsExam();
   } else if (subject == 3) {
     questions = CreateEnglishWordExam();
     QuestionList phraseExam = CreateEnglishPhraseExam();
     questions.insert(questions.end(), phraseExam.begin(), phraseExam.end());
```

これで`main`関数が少し読みやすくなりました。

<pre class="tnmai_assignment">
<strong>【課題02】</strong>
<code>main.cpp</code>, <code>exam_japanese.h</code>, <code>exam_japanese.cpp</code>を「ステージ」し、
適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題03】</strong>
<code>CreateJapaneseExam</code>関数の作成手順を参考に、英語の問題リストを作成するプログラムを関数化しなさい。関数名は<code>CreateEnglishExam</code>としてください。
</pre>

### 1.3 教科のデータを配列にする

現在のプログラムでは、「教科名」と教科の「問題作成関数」のあいだに関連がありません。そのかわり、プログラムで教科番号と`if`の条件文を一致させることで、それらしく動く仕組みになっています。

そこで、教科名と問題作成関数を構造体にまとめることを考えます。`main.cpp`を開き、`main`関数の定義の先頭に、次のプログラムを追加してください。

```diff
 #include <vector>
 #include <random>
 using namespace std;

 int main()
 {
+  // 教科データ配列
+  static const struct {
+    const char* name;        // 教科名
+    QuestionList(*create)(); // 問題作成関数のアドレス
+  } subjectData[] = {
+    { "数学", CreateMathematicsExam },
+    { "国語", CreateJapaneseExam },
+    { "英語", CreateEnglishExam },
+    { "物理", CreatePhysicsExam },
+    { "地理", CreatePrefecturesExam },
+    { "政治", CreatePoliticsExam },
+    { "経済", CreateEconomicsExam },
+  };
+
   vector<Question> questions(3);

   cout << "[リクルート試験対策クイズ]\n";

   cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n6=政治\n7=経済\n";
```

C/C++言語では「関数のアドレス(メモリ上の位置)」を扱う機能があります。<br>
「関数のアドレス」は「関数ポインタ」に代入して使います。<br>
関数ポインタは「関数と同じ構文で、代入された関数を実行する」ことができます。

関数ポインタの構文は少し特殊で、`戻り値(*変数名)(引数)`の形式になります。<br>
上のプログラムでは`QuestionList(*create)();`の部分です。

分かりにくいですが、`create`が「変数名」に当たります。<br>
そして、残りの`戻り値(*)(引数)`の部分が「関数ポインタの型」です。

普通に関数を宣言するときは`戻り値 関数名(引数)`のように書きますよね。<br>
この「`関数名`の部分を`(*変数名)`で置き換えたもの」が関数ポインタです。

>変数名をカッコで囲っているのは、`戻り値 *変数名(引数)`と書いた場合に`*`が`戻り値`側に接続されてしまうという、C/C++言語の問題を避けるためです。

それでは、`subjectData`配列を使うように、教科選択プログラムを書き換えましょう。<br>
`main`関数の定義にある教科選択プログラムを、次のように変更してください。

```diff
     { "政治", CreatePoliticsExam },
     { "経済", CreateEconomicsExam },
   };

   vector<Question> questions(3);

   cout << "[リクルート試験対策クイズ]\n";

-  cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n6=政治\n7=経済\n";
+  cout << "教科を選んでください\n";
+  for (int i = 0; i < size(subjectData); i++) {
+    cout << i + 1 << '=' << subjectData[i].name << '\n';
+  }

   int subject;
   cin >> subject;
-  if (subject == 1) {
-    questions = CreateMathematicsExam();
-  } else if (subject == 2) {
-    questions = CreateJapaneseExam();
-  } else if (subject == 3) {
-    questions = CreateEnglishExam();
-  } else if (subject == 4) {
-    questions = CreatePhysicsExam();
-  } else if (subject == 5) {
-    questions = CreatePrefecturesExam();
-  } else if (subject == 6) {
-    questions = CreatePoliticsExam();
-  } else if (subject == 7) {
-    questions = CreateEconomicsExam();
-  }
+  if (subject > 0 && subject <= size(subjectData)) {
+    questions = subjectData[subject - 1].create();
+  }

  for (const auto& e : questions) {
    cout << e.q << "\n";
    string answer;
    cin >> answer;
```

関数ポインタに代入された関数を呼び出すには、通常の関数呼び出しと同じように、変数名の後ろに`()`を付けます。

>この書き方は実際には`(*変数名)()`の省略形です。C/C++言語のルールで特別に許可されています。

関数ポインタを使うことで、もとの「`if`によって複数の関数を呼び分ける」プログラムが、「ひとつの関数ポインタを呼び出す」プログラムに置き換わっている点に注目してください。

`if`や`switch`では、すべての分岐を調べない限り「同じ処理をしている」ことを確認できません。しかし、関数ポインタを使って処理を単純化できれば、一目で「同じ処理をしている」ことが分かります。

プログラムが書けたらビルドして実行してください。大きな変更を加えたにも関わらず、変更前と全く同じように教科選択が表示され、選択した教科の問題が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題04】</strong>
<code>main.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<div style="page-break-after: always"></div>

### 1.4 総合テストを追加する

次に、すべての教科を通して実行する「総合テスト」を追加します。

```diff
   vector<Question> questions(3);

   cout << "[リクルート試験対策クイズ]\n";

   cout << "教科を選んでください\n";
+  cout << "0=総合テスト\n";
   for (int i = 0; i < size(subjectData); i++) {
     cout << i + 1 << '=' << subjectData[i].name << '\n';
   }

   int subject;
   cin >> subject;
   if (subject > 0 && subject <= size(subjectData)) {
     questions = subjectData[subject - 1].create();
+  } else if (subject == 0) {
+    // 総合テスト
+    questions.clear();
+    for (int i = 0; i < size(subjectData); i++) {
+      QuestionList tmp = subjectData[i].create();
+      questions.insert(questions.end(), tmp.begin(), tmp.end());
+    }
   }

  for (const auto& e : questions) {
    cout << e.q << "\n";
    string answer;
```

教科の番号は`1`から始まるので、総合テストの教科番号は`0`としました。

そして、総合テストが選ばれた場合は、`subjectData`配列に含まれるすべての問題作成関数を実行し、結果を連結することで問題リストを作成しています。

プログラムが書けたらビルドして実行してください。教科選択で`0`を選んだとき、数学、国語、英語、...の順ですべての教科の問題が出題されたら成功です。

>総合テストは全部で80問くらい出題されるので、全部解くのは時間がかかります。英語が出題されたあたりで確認できたとみなし、`Ctrl+C`を押してプログラムを終了しても構いません。

<pre class="tnmai_assignment">
<strong>【課題05】</strong>
<code>main.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.5 成績を表示する

続いて、試験の成績を出力する機能を作成します。成績は`教科名: 正答数/問題数`の形式で出力することにします。

しかし、ここにひとつ問題があります。現在のプログラムでは、すべての問題がひとつの
`questions`配列に格納されるため、総合テストの場合に教科ごとの問題数を知る手段がありません。

そのため、総合テストが選ばれた場合は、問題リスト作成時に、教科ごとの問題数を記録しておく必要があります。さっそく、教科ごとの問題数を記録する配列を用意し、問題数を記録しましょう。

配列名は`questionCounts`(クエスチョン・カウンツ)とします。<br>
`main.cpp`を開き、教科選択を行うプログラムに、次のプログラムを追加してください。

```diff
   cout << "教科を選んでください\n";
   cout << "0=総合テスト\n";
   for (int i = 0; i < size(subjectData); i++) {
     cout << i + 1 << '=' << subjectData[i].name << '\n';
   }

+  vector<int> questionCounts(size(subjectData)); // 各教科の問題数
   int subject;
   cin >> subject;
   if (subject > 0 && subject <= size(subjectData)) {
     // 教科テスト
     questions = subjectData[subject - 1].create();
   } else if (subject == 0) {
     // 総合テスト
     questions.clear();
     for (int i = 0; i < size(subjectData); i++) {
       const QuestionList tmp = subjectData[i].create();
       questions.insert(questions.end(), tmp.begin(), tmp.end());
+      questionCounts[i] = (int)tmp.size();
     }
   }
```

次に、正しく答えられた数、つまり<ruby>正答数<rt>せいとうすう</rt></ruby>を数えます。まず「正答数を記録する配列」を作成しましょう。

配列変数の名前は`correctCounts`(コレクト・カウンツ、`correct`は「正しい、正解」という意味)とします。問題を出題するプログラムの前に、次のプログラムを追加してください。

```diff
     for (int i = 0; i < size(subjectData); i++) {
       const QuestionList tmp = subjectData[i].create();
       questions.insert(questions.end(), tmp.begin(), tmp.end());
       questionCounts[i] = (int)tmp.size();
     }
   }

+  vector<int> correctCounts(size(subjectData));  // 各教科の正答数
   for (const auto& e : questions) {
     cout << e.q << "\n";
     string answer;
```

総合テストの場合、教科ごとの正答数を数えるには、「出題中の教科」が分かっている必要があります。次の教科に進むタイミングをはかるには、回答数を数えます。回答数が教科の問題数以上になったら、次の教科に進んだと分かります。

出題中の教科を示す変数名は`currentSubjectNo`(カレント・サブジェクト・ナンバー、「現在の教科番号」という意味)、回答数を示す変数名は`currentAnsweredCount`(カレント・アンサード・カウント、「現在の回答済み数」という意味)とします。

`correctCounts`配列変数の定義の下に、次のプログラムを追加してください。

```diff
     for (int i = 0; i < size(subjectData); i++) {
       const QuestionList tmp = subjectData[i].create();
       questions.insert(questions.end(), tmp.begin(), tmp.end());
       questionCounts[i] = (int)tmp.size();
     }
   }

   vector<int> correctCounts(size(subjectData));  // 各教科の正答数
+  int currentSubjectNo = 0;     // 出題中の教科番号
+  int currentAnsweredCount = 0; // 回答済みの問題数
   for (const auto& e : questions) {
     cout << e.q << "\n";
     string answer;
```

それでは、正しい答えを選んだときに正答数を増やすプログラムを追加しましょう。正しい答えを判定するプログラムに、次のプログラムを追加してください。

```diff
     if (answer == e.a) {
       cout << "正解！\n";
+      correctCounts[currentSubjectNo]++; // 正答数を増やす
     } else if (e.b.empty()) {
       // 答えがひとつだけの場合
       cout << "間違い！ 正解は" << e.a << "\n";
     } else {
       // 答えが複数ある場合、いずれかと一致すれば正解とする
       bool isMatch = false;
       for (const auto& b : e.b) {
         if (answer == b) {
           isMatch = true;
           break;
         }
       }

       if (isMatch) {
         cout << "正解！\n";
+        correctCounts[currentSubjectNo]++; // 正答数を増やす
       } else {
         cout << "間違い！ 正解は" << e.a << "(または";
         for (auto& b : e.b) {
           cout << "、" << b;
         }
```

これで、各教科の正答数が記録されるようになりました。続いて、回答済み問題数と出題中の教科番号を更新します。正しい答えを判定するプログラムの下に、次のプログラムを追加してください。

```diff
         cout << "間違い！ 正解は" << e.a << "(または";
         for (auto& b : e.b) {
           cout << "、" << b;
         }
         cout << ")\n";
       }
     } // if answer == e.a
+
+    // 「回答済み問題数」が「教科の問題数」以上になったら、次の教科に進む
+    if (subject == 0) {
+      currentAnsweredCount++; // 回答済み問題数を増やす
+      if (currentAnsweredCount >= questionCounts[currentSubjectNo]) {
+        currentSubjectNo++; // 次の教科に進む
+        currentAnsweredCount = 0; // 回答済み問題数をリセット
+      }
+    } // if subject == 0
   } // for questions
 }
```

これで、正しい教科番号が選択されるようになりました。

なお、この教科番号を更新するプログラムは総合テストでしか働きません。そのため、教科テストの正答数は常に`correctCounts[0]`に記録されます。

それでは、ここまでに計算した問題数と正答数を画面に出力しましょう。<br>
出題プログラムの下に、次のプログラムを追加してください。

```diff
         currentSubjectNo++; // 次の教科に進む
         currentAnsweredCount = 0; // 回答済み問題数をリセット
       }
     } // if subject == 0
   } // for questions
+
+  // 成績を表示
+  cout << "\n--- 成績 ---\n";
+  if (subject > 0 && subject <= size(subjectData)) {
+    cout << subjectData[subject - 1].name << ": "
+      << correctCounts[0] << '/' << questions.size() << '\n';
+  } else if (subject == 0) {
+    // 教科ごとの成績を表示しつつ、正答数の合計を計算
+    size_t totalCorrectCount = 0; // 正答数の合計
+    for (int i = 0; i < size(subjectData); i++) {
+      cout << subjectData[i].name << ": "
+        << correctCounts[i] << '/' << questionCounts[i] << '\n';
+      totalCorrectCount += correctCounts[i];
+    }
+    cout << "合計: " << totalCorrectCount << '/' << questions.size() << '\n';
+  }
 }
```

プログラムが書けたらビルドして実行してください。テストが終了したとき、正しい成績が表示されたら成功です。総合テストの動作は、いくつかの問題にだけ正しく答えて、残りは`0`を答え続ける方法で確認するとよいでしょう。

<pre class="tnmai_assignment">
<strong>【課題06】</strong>
<code>main.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.6 成績をファイルに出力する

成績をファイルに記録しておくと、継続的な成績の変化を調べられます。成績が向上していると感じられれば、もっとやる気が出るかもしれません。

とはいえ、出力された成績を手動でファイルに記録するのは面倒です。そこで、成績をファイルに記録する機能を追加します。また、ファイルには「時刻」も出力することにします。いつの成績か分からないと、成績の比較がしにくいからです。

まずは必要なヘッダファイルを追加しましょう。`main.cpp`を開き、次のプログラムを追加してください。

```diff
 #include "utility.h"
 #include <iostream>
 #include <string>
 #include <vector>
 #include <random>
+#include <fstream>
+#include <time.h>
 using namespace std;

 int main()
 {
```

`time.h`(タイム・エイチ)には、時間を扱う関数や構造体が宣言されています。まずはファイルを開きましょう。ファイルに書き込むには`ofstream`(オー・エフ・ストリーム、`o`はアウトプット、`f`はファイルの短縮形)クラスを使います。

成績を標準出力に出力するプログラムの下に、次のプログラムを追加してください。

```diff
       cout << subjectData[i].name << ':'
         << correctCounts[i] << '/' << questionCounts[i] << '\n';
       totalCorrectCount += correctCounts[i];
     }
     cout << "合計:" << totalCorrectCount << '/' << questions.size() << '\n';
   }
+
+  // 成績をファイルに出力する
+  static const char filename[] = "リクルート対策試験成績表.txt";
+  ofstream ofs(filename, ios_base::app);
+  if (!ofs) {
+    cerr << "エラー: " << filename << "を開けません\n";
+  } else {
+  } // if !ofs
 }
```

`ofstream`コンストラクタの第2引数には、ファイルを開いたときの状態(「オープンモード」といいます)を指定できます。

オープンモードに`ios_base::app`(アイオーエス・ベース・アップ)を指定すると、書き込み位置がファイルの末尾に設定されます。なお`app`は`append`(アペンド、「追加」という意味)の短縮形です。

次に、試験実施時間を取得します。これには以下の3つの関数を組み合わせます。

>* `time`関数: 現在の時刻(協定世界時)を取得する。
>* `localtime_s`関数: 協定世界時を時間構造体に変換する。
>* `strftime`関数: 時間構造体を文字列に変換する。

それでは、成績をファイルに出力するプログラムに、次のプログラムを追加してください。

```diff
   if (!ofs) {
     cerr << "エラー: " << filename << "を開けません\n";
   } else {
+    // 現在の時刻(協定世界時)を取得
+    const time_t t = time(nullptr);
+
+    // 協定世界時を時間構造体型に変換
+    tm examDate;
+    localtime_s(&examDate, &t);
+
+    // 時間構造体を文字列に変換
+    char strDate[100];
+    strftime(strDate, size(strDate), "%Y/%m/%d(%a) %T", &examDate);
   } // if !ofs
 }
```

`strftime`関数の第3引数には「時間構造体を文字列化するための書式」を指定します。`printf`や`scanf`と似ていますが、書式パラメータはこの関数専用のものを使います。

すべての書式パラメータをここに記載するには種類が多すぎるため、プログラムで使用したパラメータだけ以下に示します。詳しい書式については`strftime`で検索してください。

>* `%Y`: 西暦を4桁の数値で出力する。
>* `%m`: 月を2桁の数値で出力する。
>* `%d`: 日を2桁の数値で出力する。
>* `%a`: 曜日の省略名を出力する。
>* `%T`: 時刻を「時:分:秒」の形式で出力する。

次に、成績を出力するプログラムを追加します。時刻を文字列に変換するプログラムの下に、次のプログラムを追加してください。

```diff
     // 時刻構造体を文字列に変換
     char strDate[100];
     strftime(strDate, size(strDate), "%Y/%m/%d(%a) %T", &examDate);
+
+    if (subject > 0 && subject <= size(subjectData)) {
+      // 教科テストの場合、試験した教科の成績だけを出力し、それ以外は空欄とする
+      ofs << strDate;
+      for (int i = 0; i < size(subjectData); i++) {
+        ofs << ',';
+        if (i == subject - 1) {
+          ofs << correctCounts[0] << '/' << questions.size();
+        }
+      }
+      cout << "成績を" << filename << "に出力しました\n";
+    } else if (subject == 0) {
+      // 総合テストの場合、すべての教科の成績を出力する
+      ofs << strDate;
+      for (int i = 0; i < size(subjectData); i++) {
+        ofs << ',' << correctCounts[i] << '/' << questionCounts[i];
+      }
+      cout << "成績を" << filename << "に出力しました\n";
+    }
   } // if !ofs
 }
```

プログラムが書けたらビルドして実行してください。テスト終了後に成績ファイルが出力されていたら成功です。

<pre class="tnmai_assignment">
<strong>【課題07】</strong>
<code>main.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題08】</strong>
<code>Git</code>メニューから「同期」を選択し、コミットをリモートリポジトリに反映しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題09】</strong>
出力された成績ファイルを次のように改名し、以下の学校の共有フォルダにコピーしなさい。
ファイル名: <code>リクルート対策試験試験成績表_自分の名前.txt</code>
        (例: <code>リクルート対策試験試験成績表_田中真依.txt</code>)
提出先: <code>Share2/※講師フォルダ※/田中真依講師/2024年度後期_リクルート対策_試験結果提出先</code>
</pre>
