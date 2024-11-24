[就職試験対策 第09回]

# 一般常識(政治)

## 1 政治

### 1.1 政治問題のデータファイルについて

このテキストでは、日本の政治に関する問題を出題する関数を作っていきます。科目としては「社会」になります。

前回の地理と同様に、入力済みのデータファイルを用意しました。Webブラウザで以下のURLを開き、`japanese_politics.txt`というファイルダウンロードして、プロジェクトのフォルダにコピーしてください(`politics`(ポリティクス)は「政治」という意味)。

>github.com/tn-mai/cpp2024/tree/main/data

地理のデータファイルと同じく、`japanese_politics.txt`もCSV形式でデータを記録しています。実際のデータは次のようになります。

>憲法,日本国憲法の三つの基本原理は{}、{}、{}である,国民主権,基本的人権の尊重,平和主義<br>
>憲法,国民主権は{}の{}に定められている,日本国憲法,前文<br>
>国会,日本の国会は{}と{}からなる{}を採用している,衆議院,参議院,二院制<br>
>国会,衆議院議員の任期は{}年で、定数は{}人である,4,465

そして、このデータの並び順は次のようになっています。

>`ジャンル,問題文,空欄1の答え,空欄2の答え,空欄3の答え,...`

問題文の`{}`が回答するべき「空欄」になります。各問題文に`{}`はひとつ以上存在し、先頭から「空欄1」、「空欄2」、「空欄3」のようになっています。これらの空欄に割り当てる答えが、問題文の直後に並べられています。

### 1.2 政治問題のデータファイルを読み込む

それでは、政治のプログラム用のファイルを追加しましょう。ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。

そして、`exam_politics.h`という名前のヘッダファイルを追加してください。

「都道府県に関する問題を作成する関数」の名前は`CreatePoliticsExam`(クリエイト・ポリティクス・エグザム、「政治の試験を作成する」という意味)とします。追加した`exam_politics.h`を開き、次のプログラムを追加してください。

```diff
 #pragma once
+#include "question.h"
+
+// 政治の問題を作成する
+QuestionList CreatePoliticsExam();
```

続いて、C++ファイルを追加します。ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。そして、`exam_politics.cpp`という名前のC++ファイルを追加してください。

最初に、政治データを読み込む配列を定義します。追加した`exam_politics.cpp`を開き、次のプログラムを追加してください。

```diff
+#include "exam_politics.h"
+#include "utility.h"
+#include <fstream>
+#include <iostream>
+#include <random>
+#include <unordered_map>
+using namespace std;
+
+/*
+* 日本の政治の問題を作成する
+*/
+QuestionList CreatePoliticsExam()
+{
+  // 政治問題データ
+  struct PoliticsData
+  {
+    string genre;           // ジャンル
+    string text;            // 問題文
+    vector<string> answers; // 空欄に入る答えの配列
+  };
+  unordered_map<string, vector<PoliticsData>> data;
+}
```

データの管理には、`unordered_map`(アンオーダード・マップ)クラスを使うことにしました。アンオーダードマップは「連想配列(れんそうはいれつ)」と呼ばれるデータ構造で、配列の添字として、整数型に限らずどんな型でも指定できるのが特徴です。

`unordered_map`の定義は、

>```cpp
>unordered_map<添字の型, 格納する値の型> data;
>```

のように書きます。比較用に、`vector`型の定義を書くと、次のようになります。

>```cpp
>vector<格納する値の型> data;
>```

`vector`型では「添字の型」を指定していないことに注目してください。`vector`型の添字は自動的に`size_t`型になるからです。

連想配列の添字は`key`(キー)と呼ばれます。今回は「ジャンル名」をキーとして使うことにします。「値の型」は`vector<PoliticsData>`なので、データ構造としては「配列の配列」となります(正確には「配列の連想配列」)。

「`vector`の`vector`」ではなく「`vector`の`unordered_map`」を選んだ理由は、「ジャンルごとにデータをまとめたかったから」です。

実のところ、ジャンルごとに番号を割りふれば`vector`でも管理できます。しかし、番号をふるためには、事前にジャンルの情報が分かっていなくてはなりません。

ところが今回の場合、ファイルを読み込むまでジャンルの名前も数も分かりません。この場合、「ファイルからジャンル情報を収集して」、「ジャンルに番号をふり」、それから「ジャンルごとにデータを分類する」という手間が必要になります。

これに対して`unorderd_map`のキーに「ジャンル名」を指定した場合は、「ジャンル情報の収集」と「ジャンルに番号をふる」という手間がなくなります。これらの作業は`unordered_map`がやってくれるからです。

それでは、`japanese_politics.txt`を読み込みましょう。ファイルの読み込みには`ifstream`(アイ・エフ・ストリーム)クラスを使います。都道府県データの配列の定義の下に、次のプログラムを追加してください。

>`ifstream`の`i`は`input`(インプット)、`f`は`file`(ファイル)の頭文字です。

```diff
     string text;            // 問題文
     vector<string> answers; // 空欄に入る答えの配列
   };
   unordered_map<string, vector<PoliticsData>> data;
+
+  { // 政治問題データを読み込む
+    constexpr char filename[] = "japanese_politics.txt";
+    ifstream ifs(filename);
+    if ( ! ifs) {
+      cerr << "エラー: " << filename << "を読み込めません\n";
+      return {};
+    }
+
+    while (true) {
+      string s;
+      getline(ifs, s);
+      if ( ! ifs) {
+        break; // ファイル終端に達したので終了
+      }
+      const vector<string> v = Split(s, ',');
+      const string& genre = v[0]; // ジャンルを取得
+      data[genre].push_back({ genre, v[1], vector<string>(v.begin() + 2, v.end()) });
+    }
+  } // 政治問題データを読み込む
 }
```

`data[genre]`の部分で、添字にジャンル名を使っています。これは、`unordered_map`は文字列も添え字に使える、という例です。

### 1.3 政治の問題文を作成する

政治データを読み込んだら、次は問題を作成します。政治データはジャンルごとに別れているので、ランダムに選んだ２つのジャンルから出題することにしましょう。そして、それぞれのジャンルからランダムに選んだ５問を出題します。

それでは、ジャンルをランダムに選択するプログラムを作成しましょう。政治データを読み込むプログラムの下に、次のプログラムを追加してください。

```diff
       const string& genre = v[0]; // ジャンルを取得
       data[genre].push_back({ genre, v[1], vector<string>(v.begin() + 2, v.end()) });
     }
   } // 政治問題データを読み込む
+
+  constexpr int genreCount = 2; // 出題するジャンル数
+  constexpr int quizCount = 5;  // 各ジャンルの出題数
+  QuestionList questions;
+  questions.reserve(genreCount * quizCount);
+  random_device rd;
+
+  // ランダムに選んだ２つのジャンルから、ジャンル内でランダムに選んだ５問を出題する
+  const vector<int> genreIndices = CreateRandomIndices((int)data.size());
+  for (int i = 0; i < genreCount; i++) {
+    // 出題するジャンルを選択
+    auto itr = data.begin();
+    advance(itr, genreIndices[i]);
+    const auto& genre = itr->second;
+  } // for i < genreCount
+
+  return questions;
}
```

`vector`クラスのイテレータとは異なり、`unordered_map`クラスのイテレータはランダムアクセスができません(`list`クラスのイテレータと同じタイプ)。つまり、適当な数値を足し引きして、指す位置を移動させることはできません。

ただし、`++`演算子と`--`演算子は使えるので、両隣への移動は可能です。そのため、特定の位置に移動するには`++`または`--`を`for`文で繰り返すか、内部的に`for`ループを実行する`advance`(アドバンス)関数を使う必要があります。

`unordered_map`のイテレータが指すのは、「キーと値(あたい)のペア」を保持する`std::pair`(ペア)クラスのオブジェクトです。キーは`first`メンバ、値は`second`メンバで取得します。

ジャンルのデータ配列は「値」になります。そのため、`second`メンバ変数によって参照できます。

ジャンルを選択したら、次は選んだジャンルの問題をランダムに５問選びます。ジャンルを選択するプログラムの下に、次のプログラムを追加してください。

```diff
     // 出題するジャンルを選択
     auto itr = data.begin();
     advance(itr, genreIndices[i]);
     const auto& genre = itr->second;
+
+    // ジャンル内でランダムに５問選ぶ
+    const vector<int> questionIndices = CreateRandomIndices((int)genre.size());
+    for (int j = 0; j < quizCount; j++) {
+      const auto& question = genre[questionIndices[j]]; // 選んだ問題データ
+    } // for j < quizCount
   } // for i < genreCount

   return questions;
}
```

問題文には空欄がひとつ以上存在します。しかし、本テキストで作成しているプログラムには、「答えはひとつだけ」という制限があります。そこで、「複数の空欄を持つ問題文」を「ひとつだけ空欄を持つ問題文」に変換します。この変換は、

>1. 問題文に含まれる空欄の位置と数を調べる。
>2. 空欄のひとつをランダムに選ぶ。
>3. 選ばれなかった空欄を「答え」で置き換える。

という手順で行います。それでは、まず「空欄の位置と数」を調べましょう。問題データを選ぶプログラムの下に、次のプログラムを追加してください。

```diff
     // ジャンル内でランダムに５問選ぶ
     const vector<int> questionIndices = CreateRandomIndices((int)genre.size());
     for (int j = 0; j < quizCount; j++) {
       const auto& question = genre[questionIndices[j]]; // 選んだ問題データ
+
+      // 空欄の位置と数を調べる
+      // 空欄は「開き波括弧」と「閉じ波括弧」のペア、つまり{}で表される
+      vector<int> blanks; // 空欄の位置の配列
+      for (int k = 0; k < question.text.size() - 1; k++) {
+        if (question.text[k] == '{' && question.text[k + 1] == '}') {
+          blanks.push_back(k);
+        }
+      }
     } // for j < quizCount
   } // for i < genreCount
```

次に、「空欄の一つをランダムに選ぶ」プログラムを作成しましょう。空欄の数は分かっているので、これは`uniform_int_distribution`クラスで範囲内の数をランダムに生成するだけです。空欄の位置と数を調べるプログラムの下に、次のプログラムを追加してください。

```diff
       vector<int> blanks; // 空欄の位置の配列
       for (int k = 0; k < question.text.size() - 1; k++) {
         if (question.text[k] == '{' && question.text[k + 1] == '}') {
           blanks.push_back(k);
         }
       }
+
+      // 空欄のうち１つをランダムに選ぶ
+      const int index = uniform_int_distribution<>(0, (int)blanks.size() - 1)(rd);
     } // for j < quizCount
   } // for i < genreCount
```

続いて、「選ばれなかった空欄を答えで置き換える」プログラムを作成します。実際に作成するプログラムでは、

>問題文のコピーを作成する。このとき、空欄部分には答えをコピーする。

という設計を採用しています。この設計を採用した理由は、

>既存の文字列の置き換えは、「置換部分より後ろにある文字」を移動させなくてはならないので処理が遅くなる

からです。新規にコピーを作る場合は「後ろの文字」がまだ存在しないため、移動処理が不要になるので速くなります。それに、最終的には、問題文は`questions`配列にコピーしなくてはなりません。それなら、コピーのついでに置き換えも行うほうが効率的です。

それでは、「問題文のコピーを作成しながら、空欄を答えに置き換える」プログラムを作成しましょう。空欄のひとつをランダムに選ぶプログラムの下に、次のプログラムを追加してください。

```diff
       // 空欄のうち１つをランダムに選ぶ
       const int index = uniform_int_distribution<>(0, (int)blanks.size() - 1)(rd);
+
+      // 問題文のコピーを作成しつつ、選ばれなかった空欄を「答え」に置き換える
+      string s = "[" + question.genre + "] 次の文章の[ ? ]に入る単語を答えよ\n";
+      int from = 0; // コピー元の先頭位置
+      for (int k = 0; k < blanks.size(); k++) {
+        const int end = blanks[k]; // 空欄の位置
+        s.append(question.text, from, end - from); // 空欄の手前までの範囲コピー
+
+        if (k != index) {
+          s += question.answers[k]; // 選ばれなかった空欄の場合は答えをコピー
+        }
+        from = end + 2; // コピー元の位置を更新
+      }
+      s.append(question.text, from); // 問題文の末尾部分をコピー
+
+      questions.push_back({ s, question.answers[index] });
     } // for j < quizCount
   } // for i < genreCount
```

`string`の末尾に他の文字列の一部を付け足すには、`append`(アペンド)メンバ関数を使います。

>`substr`メンバ関数で部分文字列を作成して`+=`演算子で連結する方法もあります。しかし、部分文字列の作成にコストがかかるので、`append`メンバ関数を使うほうが速くなります。

ところで、空欄が`{}`のままでは設問の内容と一致しません。設問の文章では、これまでの問題似合わせて空欄を`[ ? ]`としているからです。そこで、`{}`を`[ ? ]`に置き換えましょう。空欄を答えに置き換えるプログラムに、次のプログラムを追加してください。

```diff
       int from = 0; // コピー元の先頭位置
       for (int k = 0; k < blanks.size(); k++) {
         const int end = blanks[k];
         s.append(question.text, from, end - from); // 問題文をコピー
         if (k != index) {
           s.append(question.answers[k]); // 選ばれなかった空欄の場合は答えをコピー
+        } else {
+          s.append("[ ? ]"); // 選ばれた空欄の場合は「表示用の空欄文字列」をコピー
         }
         from = end + 2; // コピー元の位置を更新
       }
```

これで、政治の問題文を作成する`CreatePoliticsExam`関数は完成です。

### 1.4 政治の問題を選べるようにする

それでは、政治問題を選べるようにしましょう。`main.cpp`を開き、`exam_politics.h`をインクルードしてください。

```diff
 #include "question.h"
 #include "exam_japanese.h"
 #include "exam_english.h"
 #include "exam_science.h"
 #include "exam_geography.h"
+#include "exam_politics.h"
 #include <iostream>
 #include <string>
 #include <vector>
```

続いて、選択できる教科を表示するプログラムに、「政治」の項目を追加してください。

```diff
   questions.push_back({
     to_string(x) + "人のうち" + to_string(y) + "人を選ぶ組み合わせは何通りあるか？",
     to_string(z) });

   cout << "[リクルート試験対策クイズ]\n";

-  cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n";
+  cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n6=政治\n";
   int subject;
   cin >> subject;
   if (subject == 2) {
```

最後に、教科ごとの問題文を選択するプログラムに、政治の問題文を選択する`if`文を追加してください。

```diff
   } else if (subject == 3) {
     questions = CreateEnglishWordExam();
     QuestionList phraseExam = CreateEnglishPhraseExam();
     questions.insert(questions.end(), phraseExam.begin(), phraseExam.end());
   } else if (subject == 4) {
     questions = CreatePhysicsExam();
   } else if (subject == 5) {
     questions = CreateGeographyExam();
+  } else if (subject == 6) {
+    questions = CreatePoliticsExam();
   }

   for (const auto& e : questions) {
     cout << e.q << "\n";
```

プログラムが書けたらビルドして実行してください。教科選択で「政治」を選んだとき、「政治の問題」が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題01】</strong>
<code>main.cpp</code>, <code>exam_politics.h</code>, <code>exam_politics.cpp</code>, <code>recruit_quiz.vcproj</code>,
<code>recruit_quiz.vcproj.filters</code>, <code>japanese_politics.txt</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題02】</strong>
<code>Git</code>メニューから「同期」を選択し、コミットをリモートリポジトリに反映しなさい。
</pre>










