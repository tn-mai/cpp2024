[就職試験対策 第08回]

# 一般常識(地理)

## 1 地理

### 1.1 文字列をカンマ区切りで分割する関数を作成する

このテキストでは、日本の都道府県に関する問題を出題する関数を作っていきます。科目としては「地理」になります。

とはいえ、47都道府県のデータを入力するのは大変です。そこで、入力済みのデータファイルを用意しました。Webブラウザで以下のURLを開き、`japanese_prefectures.txt`というファイルダウンロードして、プロジェクトのフォルダにコピーしてください。

>github.com/tn-mai/cpp2024/tree/main/data

`prefecture`(プリフェクチャ)は「県」という意味です。英語には「都道府県」の分類がないので、「都」や「道」や「府」を表す場合にもこの単語が使われます。

さて、`japanese_prefectures.txt`には、次のような「カンマで区切られた都道府県のデータ」が入っています。1行がひとつの都道府県のデータを表します。

>北海道,札幌市,じゃがいも,小麦,玉ねぎ,羊蹄山(ようていざん),雪まつり<br>
>青森県,青森市,りんご,ねぶた祭り,恐山<br>
>秋田県,秋田市,きりたんぽ,竿燈(かんとう)祭り,なまはげ

このような「カンマで区切られたデータ」のことを`CSV`(シーエスブイ)といいます。`CSV`は`Comma Separated Values`(カンマ・セパレーテッド・バリューズ、「カンマ区切りの値」という意味)の短縮形です。

`CSV`ファイルのデータの解釈は利用者が決定します。`japanese_prefectures.txt`の場合、データの並び順は

>`都道府県名, 県庁所在地, 特徴1, 特徴2, ...`

のようになっています。つまり、「都道府県名」と「県庁所在地」のあとに、ひとつ以上の「特徴」が続く、という形式です。このような`CSV`形式のデータを解析するには、「文字列をカンマ区切りで分割する関数」があると便利です。

文字列を分割する関数は、一般的に`split`(スプリット, 「裂く、割る」という意味)と名付けられます。そこで、本テキストでも`Split`という名前にします。`utility.h`を開き、次のように`Split`関数の宣言を追加してください。

```diff
 #pragma once
 #include <vector>
+#include <string>

 // 配列をシャッフルする
 void Shuffle(std::vector<int>& indices);

 // シャッフルした番号配列を作成する
 std::vector<int> CreateRandomIndices(int n);

 // 間違った番号の配列を作成する
 // n            作成する番号の範囲
 // correctIndex 正解の番号
 std::vector<int> CreateWrongIndices(int n, int correctIndex);
+
+// 文字列を分割する
+// s 分解する文字列
+// c 区切り文字
+std::vector<std::string> Split(const std::string& s, char c);
```

次に`utility.cpp`を開き、`CreateWrongIndices`関数の定義の下に、次のプログラムを追加してください。

```diff
   // 番号の配列をシャッフル
   Shuffle(indices);

   return indices;
 }
+
+/*
+* 文字列を分割する
+*/
+vector<string> Split(const string& s, char c)
+{
+  vector<string> v; // 分割した文字列を格納配列(戻り値)
+
+  // すべての文字をループ処理
+  auto begin = s.begin();   // 分割範囲の先頭位置
+  const auto end = s.end(); // 文字列の終端
+  for (auto p = begin; p != end; ++p) {
+    // 区切り文字の前までを文字列として分割
+    if (*p == c) {
+      v.push_back(string(begin, p));
+      begin = p + 1; // 先頭位置を「区切り文字の次」の位置に更新
+    }
+  }
+
+  // 残りの部分を配列に追加
+  v.push_back(string(begin, end));
+
+  return v; // 分割した文字列を返す
+}
```

### 1.2 地理の問題を作成する(その１)

それでは、地理のプログラム用のファイルを追加しましょう。ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。

そして、`exam_geography.h`という名前のヘッダファイルを追加してください(`geography`(ジオグラフィ)は「地理」という意味です)。

「都道府県に関する問題を作成する関数」の名前は`CreatePrefecturesExam`(クリエイト・プリフェクチャーズ・エグザム、「都道府県の試験を作成する」という意味)とします。追加した`exam_geography.h`を開き、次のプログラムを追加してください。

```diff
 #pragma once
+#include "question.h"
+
+// 都道府県の問題を作成する
+QuestionList CreatePrefecturesExam();
```

続いて、C++ファイルを追加します。ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。そして、`exam_geography.cpp`という名前のC++ファイルを追加してください。

最初に、都道府県データを読み込む配列を定義します。追加した`exam_english.cpp`を開き、次のプログラムを追加してください。

```diff
+#include "exam_geography.h"
+#include "utility.h"
+#include <iostream>
+#include <fstream>
+#include <random>
+using namespace std;
+
+/**
+* 都道府県の問題を作成する
+*/
+QuestionList CreateGeographyExam()
+{
+  // 都道府県データ
+  struct PrefectureData
+  {
+    string name;             // 都道府県名
+    string capital;          // 県庁所在地
+    vector<string> features; // 特徴
+  };
+  vector<PrefectureData> data;
+}
```

次に、`japanese_prefectures.txt`を読み込みます。ファイルの読み込みには`ifstream`(アイ・エフ・ストリーム)クラスを使います。都道府県データの配列の定義の下に、次のプログラムを追加してください。

>`ifstream`の`i`は`input`(インプット)、`f`は`file`(ファイル)の頭文字です。

```diff
     string capital;          // 県庁所在地
     vector<string> features; // 特徴
   };
   vector<PrefectureData> data;
+
+  { // 都道府県データを読み込む
+    constexpr char filename[] = "japanese_prefectures.txt";
+    ifstream ifs(filename);
+    if (!ifs) {
+      cerr << "エラー: " << filename << "を読み込めません\n";
+      return {};
+    }
+
+    string s;
+    while (true) {
+      getline(ifs, s); // 1行読み取る
+      if (!ifs) {
+        break; // ファイル終端に達したので終了
+      }
+      const vector<string> v = Split(s, ',');
+      data.push_back({ v[0], v[1], vector<string>(v.begin() + 2, v.end()) });
+    }
+  } // 都道府県データを読み込む
 }
```

都道府県データを読み込んだら、次は問題を作成します。都道府県データを読み込むプログラムの下に、次のプログラムを追加してください。

```diff
       const vector<string> v = Split(s, ',');
       data.push_back({ v[0], v[1], vector<string>(v.begin() + 2, v.end()) });
     }
   } // 都道府県データを読み込む
+
+  constexpr int quizCount = 5;
+  QuestionList questions;
+  questions.reserve(quizCount);
+  vector<int> indices = CreateRandomIndices((int)data.size());
+  random_device rd;
+
+  const int type = uniform_int_distribution<>(0, 2)(rd);
+  switch (type) {
+  case 0: // 特徴から都道府県を答える
+    break;
+
+  case 1: // 都道府県をあらわす特徴を答える
+    break;
+
+  case 2: { // 都道府県から県庁所在地を答える
+    break;
+  } // switch (type)
+
+  return questions;
 }
```

都道府県データは「都道府県名」、「県庁所在地」、「都道府県の特徴」という三種類のデータから構成されます。これに合わせて、問題も三種類の出題パターンを作ることにしました。

まずは「特徴から都道府県を答える」問題を作成しましょう。`case 0`に次のプログラムを追加してください。

```diff
   const int type = uniform_int_distribution<>(0, 2)(rd);
   switch (type) {
   case 0: // 特徴から都道府県を答える
+    for (int i = 0; i < quizCount; i++) {
+      // 正解以外の答えをランダムに選ぶ
+      const int correctIndex = indices[i];
+      vector<int> answers = CreateWrongIndices((int)data.size(), correctIndex);
+
+      // ランダムな位置を正しい番号で上書き
+      const int correctNo = std::uniform_int_distribution<>(1, 3)(rd);
+      answers[correctNo - 1] = correctIndex;
+
+      // 問題文を作成
+      string s = "以下の特徴を持つ都道府県の番号を選べ\n";
+
+      // 都道府県の特徴をランダムな順番で連結し、問題文に追加
+      const auto& features = data[correctIndex].features;
+      vector<int> featureIndices = CreateRandomIndices((int)features.size());
+      s += features[featureIndices[0]];
+      for (int j = 1; j < features.size(); j++) {
+        s += ", ";
+        s += features[featureIndices[j]];
+      }
+
+      // 答えとなる都道府県を問題文に追加
+      for (int j = 0; j < 3; j++) {
+        s += "\n  " + to_string(j + 1) + ":" + data[answers[j]].name;
+      }
+
+      // 問題文と答えのペアを問題配列に追加
+      questions.push_back({ s, to_string(correctNo) });
+    }
     break;

   case 1: // 都道府県をあらわす特徴を答える
     break;
```

いつも同じ順番で特徴が表示されると、最初の特徴だけを覚えるだけで答えられてしまいます。その対策として、特徴の順序をランダムにしてみました。

意図したように問題が出題されることを、実験して確かめてみましょう。まずは`type`変数が常に`0`となるように、プログラムを次のように変更してください。

```diff
   questions.reserve(quizCount);
   vector<int> indices = CreateRandomIndices((int)data.size());
   random_device rd;

-  const int type = uniform_int_distribution<>(0, 2)(rd);
+  const int type = 0; // uniform_int_distribution<>(0, 2)(rd);
   switch (type) {
   case 0: // 特徴から都道府県を答える
     for (int i = 0; i < quizCount; i++) {
       // 正解以外の答えをランダムに選ぶ
```

次に`main.cpp`を開き、`exam_geography.h`をインクルードしてください。

```diff
 #include "question.h"
 #include "exam_japanese.h"
 #include "exam_english.h"
 #include "exam_science.h"
+#include "exam_geography.h"
 #include <iostream>
 #include <string>
 #include <vector>
```

続いて、選択できる教科を表示するプログラムに、「物理」の項目を追加してください。

```diff
   questions.push_back({
     to_string(x) + "人のうち" + to_string(y) + "人を選ぶ組み合わせは何通りあるか？",
     to_string(z) });

   cout << "[リクルート試験対策クイズ]\n";

-  cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n";
+  cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n";
   int subject;
   cin >> subject;
   if (subject == 2) {
```

最後に、教科ごとの問題文を選択するプログラムに、地理の問題文を選択する`if`文を追加してください。

```diff
   } else if (subject == 3) {
     questions = CreateEnglishWordExam();
     QuestionList phraseExam = CreateEnglishPhraseExam();
     questions.insert(questions.end(), phraseExam.begin(), phraseExam.end());
   } else if (subject == 4) {
     questions = CreatePhysicsExam();
+  } else if (subject == 5) {
+    questions = CreateGeographyExam();
   }

   for (const auto& e : questions) {
     cout << e.q << "\n";
```

プログラムが書けたらビルドして実行してください。教科選択で「地理」を選んだとき、「特徴から都道府県を答える問題」が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題01】</strong>
<code>main.cpp</code>, <code>exam_geography.h</code>, <code>exam_geography.cpp</code>, <code>recruit_quiz.vcproj</code>,
<code>recruit_quiz.vcproj.filters</code>, <code>japanese_prefectures.txt</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.3 地理の問題を作成する(その２)

続いて、「都道府県をあらわす特徴を答える」問題を作成します。「特徴から都道府県を答える」問題を作成するプログラムの下に、次のプログラムを追加してください。

```diff
       // 問題文と答えのペアを問題配列に追加
       questions.push_back({ s, to_string(correctNo) });
     }
     break;

   case 1: // 都道府県をあらわす特徴を答える
+    for (int i = 0; i < quizCount; i++) {
+      // 正解以外の答えをランダムに選ぶ
+      const int correctIndex = indices[i];
+      vector<int> answers = CreateWrongIndices((int)data.size(), correctIndex);
+
+      // ランダムな位置を正しい番号で上書き
+      const int correctNo = std::uniform_int_distribution<>(1, 3)(rd);
+      answers[correctNo - 1] = correctIndex;
+
+      // 問題文を作成
+      string s = "「" + data[correctIndex].name + "」の特徴をあらわす番号を選べ";
+      for (int j = 0; j < 3; j++) {
+        const auto& features = data[answers[j]].features;
+        const int n = uniform_int_distribution<>(0, (int)features.size() - 1)(rd);
+        s += "\n  " + to_string(j + 1) + ":" + features[n];
+      }
+
+      // 問題文と答えのペアを問題配列に追加
+      questions.push_back({ s, to_string(correctNo) });
+    }
     break;

   case 2: { // 都道府県から県庁所在地を答える
     break;
```

この問題では、ひとつの特徴を覚えるだけでは正答が難しくなるように、都道府県の特徴をランダムに選択しています。

それでは、意図したように問題が出題されることを、実験して確かめてみましょう。`type`変数が常に`1`となるように、プログラムを次のように変更してください。

```diff
   questions.reserve(quizCount);
   vector<int> indices = CreateRandomIndices((int)data.size());
   random_device rd;

-  const int type = 0; // uniform_int_distribution<>(0, 2)(rd);
+  const int type = 1; // uniform_int_distribution<>(0, 2)(rd);
   switch (type) {
   case 0: // 特徴から都道府県を答える
     for (int i = 0; i < quizCount; i++) {
       // 正解以外の答えをランダムに選ぶ
```

プログラムが書けたらビルドして実行してください。教科選択で「地理」を選んだとき、「都道府県をあらわす特徴を答える問題」が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題02】</strong>
<code>exam_geography.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.4 地理の問題を作成する(その３)

最後に、「都道府県から県庁所在地を答える」問題を作成します。「都道府県をあらわす特徴を答える」問題を作成するプログラムの下に、次のプログラムを追加してください。

```diff
       // 問題文と答えのペアを問題配列に追加
       questions.push_back({ s, to_string(correctNo) });
     }
     break;

   case 2: // 都道府県から県庁所在地を答える
+    {
+    // 都道府県名と県庁所在地が異なる都道府県のリストを作る
+    vector<pair<string, string>> differentNames;
+    for (const auto& e : data) {
+      // 都道府県名と県庁所在地名の長さが異なるか、末尾の１文字を除く部分文字列が異なる場合、
+      // 「都道府県名と県庁所在地が異なるリスト」に追加する
+      if (e.name.size() != e.capital.size() ||
+        memcmp(e.name.data(), e.capital.data(), e.name.size() - 2) != 0) {
+        differentNames.push_back({ e.name, e.capital });
+      }
+    }
+
+    // 作成したリストからランダムに出題する
+    vector<int> indices = CreateRandomIndices((int)differentNames.size());
+    for (int i = 0; i < quizCount; i++) {
+      const int correctIndex = indices[i];
+      questions.push_back({
+        "「" + differentNames[correctIndex].first + "」の県庁所在地を答えよ",
+        differentNames[correctIndex].second });
+    }
+    break;
+    }
   } // switch (type)

   return questions;
```

県庁所在地の問題では、都道府県名と県庁所在地が異なる都道府県が出題される傾向にあります。ということは、都道府県名と県庁所在地が異なる組み合わせだけを覚えておけば十分です。

そこで、データから「都道府県名と県庁所在地が異なる都道府県」だけを抜き出して、その中から出題するようにしています。

それでは、意図したように問題が出題されることを、実験して確かめてみましょう。`type`変数が常に`2`となるように、プログラムを次のように変更してください。

```diff
   questions.reserve(quizCount);
   vector<int> indices = CreateRandomIndices((int)data.size());
   random_device rd;

-  const int type = 1; // uniform_int_distribution<>(0, 2)(rd);
+  const int type = 2; // uniform_int_distribution<>(0, 2)(rd);
   switch (type) {
   case 0: // 特徴から都道府県を答える
     for (int i = 0; i < quizCount; i++) {
       // 正解以外の答えをランダムに選ぶ
```

プログラムが書けたらビルドして実行してください。教科選択で「地理」を選んだとき、「都道府県から県庁所在地を答える問題」が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題03】</strong>
<code>exam_geography.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.5 出題される問題の種類をランダムに戻す

三種類の問題の実験が完了したので、出題される問題の種類をランダムに戻します。`type`変数の定義を次のように変更してください。

```diff
   questions.reserve(quizCount);
   vector<int> indices = CreateRandomIndices((int)data.size());
   random_device rd;

-  const int type = 2; // uniform_int_distribution<>(0, 2)(rd);
+  const int type = uniform_int_distribution<>(0, 2)(rd);
   switch (type) {
   case 0: // 特徴から都道府県を答える
     for (int i = 0; i < quizCount; i++) {
       // 正解以外の答えをランダムに選ぶ
```

プログラムが書けたらビルドして実行してください。教科選択で「地理」を選ぶことを数回繰り返して、出題される問題の種類がランダムになっていたら成功です。

<pre class="tnmai_assignment">
<strong>【課題04】</strong>
<code>exam_geography.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題05】</strong>
<code>Git</code>メニューから「同期」を選択し、コミットをリモートリポジトリに反映しなさい。
</pre>
