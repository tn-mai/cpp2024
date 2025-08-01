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

「政治問題を作成する関数」の名前は`CreatePoliticsExam`(クリエイト・ポリティクス・エグザム、「政治の試験を作成する」という意味)とします。追加した`exam_politics.h`を開き、次のプログラムを追加してください。

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

データの管理には、`unordered_map`(アンオーダード・マップ)クラスを使います。

アンオーダードマップは「連想配列(れんそうはいれつ)」と呼ばれるデータ構造で、配列の添字として、整数型に限らずどんな型でも指定できるのが特徴です。

`unordered_map`型の定義は、

>```cpp
>unordered_map<添字の型, 格納する値の型> data;
>```

のように書きます。比較用に、`vector`型の定義を書くと、次のようになります。

>```cpp
>vector<格納する値の型> data;
>```

`vector`型では「添字の型」を指定していないことに注目してください。<br>
`vector`型の添字は整数型に決められていて、変えられないからです。

連想配列の添字は`key`(キー)と呼ばれます。今回は「ジャンル名」をキーとして使うことにします。「値の型」は`vector<PoliticsData>`なので、データ構造としては「配列の配列」となります(正確には「配列の連想配列」)。

「`vector`の`vector`」ではなく「`vector`の`unordered_map`」を選んだ理由は、<br>
「ジャンルごとにデータをまとめたかったから」です。

実のところ、ジャンルごとに番号を割りふれば`vector`でも管理できます。<br>
しかし、番号をふるためには、事前にジャンルの情報が分かっていなくてはなりません。

ところが今回の場合、ファイルを読み込むまでジャンルの名前も数も分かりません。<br>
この場合、例えば次の手順でジャンル情報を収集しなくてはなりません。

&emsp; 1. ファイルからジャンル情報を収集<br>
&emsp; 2. ジャンルに番号をふる<br>
&emsp; 3. ジャンルごとにデータを分類する

これに対して`unorderd_map`のキーに「ジャンル名」を指定した場合は、上の手順の1と2は不要です。これらの作業は`unordered_map`がやってくれるからです。

それでは、`japanese_politics.txt`を読み込みましょう。<br>
ファイルの読み込みには`ifstream`(アイ・エフ・ストリーム)クラスを使います。<br>
政治データの配列の定義の下に、次のプログラムを追加してください。

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

`data[genre]`の部分で、添字にジャンル名を使っています。<br>
これは、`unordered_map`は文字列も添え字に使える、という例です。

### 1.3 政治の問題文を作成する

政治データを読み込んだら、次は問題を作成します。<br>
政治データはジャンルごとに別れているので、ランダムに選んだ２つのジャンルから出題することにしましょう。そして、それぞれのジャンルからランダムに選んだ５問を出題します。

それでは、ジャンルをランダムに選択するプログラムを作成しましょう。<br>
政治データを読み込むプログラムの下に、次のプログラムを追加してください。

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
+    advance(itr, genreIndices[i]);   // 参照位置を指定したところまで進める
+    const auto& genre = itr->second; // 値(ジャンルのデータ配列)を取り出す
+  } // for i < genreCount
+
+  return questions;
 }
```

`vector`クラスのイテレータとは異なり、`unordered_map`クラスのイテレータはランダムアクセスができません(`list`クラスのイテレータと同じタイプ)。つまり、適当な数値を足し引きして、指す位置を移動させることはできません。

ただし、`++`演算子と`--`演算子は使えるので、両隣への移動は可能です。そのため、特定の位置に移動するには`++`または`--`を`for`文で繰り返すか、内部的に`for`ループを実行する`advance`(アドバンス)関数を使う必要があります。

`unordered_map`のイテレータが指すのは、「キーと値(あたい)のペア」を保持する`pair`
(ペア)クラスのオブジェクトです。`pair`クラスは、以下のようなクラステンプレートになっています(詳細は実装によって異なります)。

><br>
>
>```c++
>template<typename T1, typename T2>
>struct pair
>{
>  T1 first;
>  T2 second;
>}
>```
>
><br>

`first`や`second`メンバに格納するデータの種類は、`pair`を利用する側が決定します。
`unordered_map`の場合、`first`には「キー」、`second`には「値」が格納されます。

そして、`unordered_map`の「キー」と「値」にどんなデータを指定するかは、プログラム作成者が決めることになります。このプログラムの場合、ジャンルのデータ配列は「値」に割り当てています。そのため、`second`メンバ変数によって参照できます。

ジャンルを選択したら、次は選んだジャンルの問題をランダムに５問選びます。<br>
ジャンルを選択するプログラムの下に、次のプログラムを追加してください。

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

問題文には空欄がひとつ以上存在します。しかし、本テキストで作成しているプログラムには、「答えはひとつだけ」という制限があります。そこで、「複数の空欄を持つ問題文」を「ひとつだけ空欄を持つ問題文」に変換します。この変換は、以下の手順で行います。

&emsp; 1. 問題文に含まれる空欄の位置と数を調べる。<br>
&emsp; 2. 空欄のひとつをランダムに選ぶ。<br>
&emsp; 3. 選ばれなかった空欄を「答え」で置き換える。

それでは、まず「空欄の位置と数」を調べましょう。<br>
問題データを選ぶプログラムの下に、次のプログラムを追加してください。

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

次に、「空欄の一つをランダムに選ぶ」プログラムを作成しましょう。空欄の数は分かっているので、`uniform_int_distribution`クラスで範囲内の数をランダムに生成します。

空欄の位置と数を調べるプログラムの下に、次のプログラムを追加してください。

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

からです。新規にコピーを作る場合は「後ろの文字」がまだ存在しないため、移動処理が不要なぶん速くなります。それに、最終的には、問題文は`questions`配列にコピーしなくてはなりません。それなら、コピーのついでに置き換えも行うほうが効率的です。

それでは、「問題文のコピーを作りながら空欄を答えに置き換える」プログラムを作成しましょう。空欄のひとつをランダムに選ぶプログラムの下に、次のプログラムを追加してください。

```diff
       // 空欄のうち１つをランダムに選ぶ
       const int index = uniform_int_distribution<>(0, (int)blanks.size() - 1)(rd);
+
+      // 問題文のコピーを作成しつつ、選ばれなかった空欄を「答え」に置き換える
+      string s = "[" + question.genre + "] 次の文章の[ ? ]に入る単語を答えよ\n";
+      int from = 0; // コピー元の先頭位置
+      for (int k = 0; k < blanks.size(); k++) {
+        const int end = blanks[k]; // 空欄の位置
+        s.append(question.text, from, end - from); // 空欄の手前までの範囲をコピー
+
+        if (k != index) {
+          s.append(question.answers[k]); // 選ばれなかった空欄の場合は答えをコピー
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

>**【その他の追加方法】**<br>
>`substr`メンバ関数で「部分文字列オブジェクト」を作成し、`+=`演算子で連結する方法もあります。しかし、部分文字列オブジェクトの作成にコストがかるため、`append`メンバ関数を使うほうが速くなります。

ところで、空欄が`{}`のままでは設問の内容と一致しません。設問の文章では、これまでの問題似合わせて空欄を`[ ? ]`としているからです。そこで、`{}`を`[ ? ]`に置き換えましょう。空欄を答えに置き換えるプログラムに、次のプログラムを追加してください。

```diff
       int from = 0; // コピー元の先頭位置
       for (int k = 0; k < blanks.size(); k++) {
         const int end = blanks[k];
         s.append(question.text, from, end - from); // 空欄の手前までの範囲をコピー
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

プログラムが書けたらビルドして実行してください。教科選択で`6`番の「政治」を選んだとき、「政治の問題」が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題01】</strong>
<code>main.cpp</code>, <code>exam_politics.h</code>, <code>exam_politics.cpp</code>, <code>recruit_quiz.vcproj</code>,
<code>recruit_quiz.vcproj.filters</code>, <code>japanese_politics.txt</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.5 全角数値を半角数値に変換する

現在のプログラムでは、単語などの入力は全角モード、数値の入力は半角モードのように、求められる答えによって全角モードと半角モードを切り替える必要があります。

これまでは、数学では半角モード、それ以外は全角モードのように、教科単位で切り替えればよかったので、あまり問題にはなりませんでした。しかし、今回の政治問題は状況が異なります。

政治問題の答えは、単語と数値の両方が混在しているからです。そのため、設問によって、全角モードと半角モードを適切に切り替えなくてはなりません。

いちいちモードを切り替えるのは面倒ですし、うっかり全角モードで数値を入力して誤答になってしまう、という問題も起こり得ります。

この問題を解決するために、全角の数値を半角に変換する関数を追加します。答えを全角で入力してしまった場合でも、この関数で半角に変換すれば、正答と判定されるはずです。

なお、「全角」、「半角」という２つの名前が実際に意味するところは、環境によって異なります。Visual Studio 2022でコンソールアプリを開発している場合、全角は`SJIS`(エス・ジス)という文字コード、半角は`ASCII`(アスキー)という文字コードを意味します。

>より正確には、Windowsで使われているのは`CP932`という、Microsoftが`SJIS`にいくつかの文字を追加したバージョンです。なお、`CP`は`Code Page`の略称です。

そのため、実際に作成するのは「`SJIS`の数字を`ASCII`の数字に変換する関数」になります。関数名は`ConvertSjisNumberToAscii`(コンバート・エスジス・ナンバー・トゥ・アスキー、「SJISの数字をASCIIに変換する」という意味)とします。

`utility.h`を開き、`Split`関数の宣言の下に、次のプログラムを追加してください。

```diff
 // 文字列を分割する
 // s 分解する文字列
 // c 区切り文字
 std::vector<std::string> Split(const std::string& s, char c);
+
+// SJIS数値文字列をASCII数値文字列に変換する
+// sjis 変換元のsjis文字列
+std::string ConvertSjisNumberToAscii(const std::string& sjis);
```

次に`utility.cpp`を開き、`Split`関数の定義の下に、次のプログラムを追加してください。

```diff
   // 残った部分を文字列として分割
   v.push_back(string(begin, end));

   return v; // 分割した文字列を返す
 }
+
+/*
+* SJIS数値文字列をASCII数値文字列に変換する
+*/
+std::string ConvertSjisNumberToAscii(const std::string& sjis)
+{
+  // 数値文字のSJISからASCIIへの変換表
+  static const struct {
+    unsigned int sjis;
+    char ascii;
+  } conversionTable[] = {
+    { 0x824f, '0' }, { 0x8250, '1' }, { 0x8251, '2' }, { 0x8252, '3' }, { 0x8253, '4' },
+    { 0x8254, '5' }, { 0x8255, '6' }, { 0x8256, '7' }, { 0x8257, '8' }, { 0x8258, '9' },
+    { 0x8144, '.'}, {0x817c, '-'},
+  };
+
+  // 文字コードを変換
+  string ascii;
+  for (auto i = sjis.begin(); i != sjis.end(); i++) {
+    const unsigned char a = i[0];
+    if (a < 0x80) {
+      // ASCII文字の場合はそのままコピーする
+      ascii.push_back(*i);
+    } else {
+      // SJIS文字の場合はASCII文字に変換する
+      const unsigned int code = a * 0x100 + (unsigned char)i[1];
+      const auto itr = find_if(begin(conversionTable), end(conversionTable),
+        [code](const auto& e) { return e.sjis == code; });
+      if (itr == end(conversionTable)) {
+        break; // 変換できない文字が見つかったら変換を打ち切る
+      }
+      ascii.push_back(itr->ascii);
+      i++; // 2バイト文字なので1バイト余分に進める
+    }
+  } // for i
+
+  return ascii;
+}
```

`SJIS`コードは「符号なし16ビット」で表現されるため、`char`型配列に格納すると一部は負数として見えます。しかし、見かけが負数だろうと、実体は「符号なし16ビット」です。<br>
そのため、値を読み取るときに`unsigned char`型にキャストする必要があります。

`SJIS`コードを`char`型配列に格納するとき、1バイト目が上位8ビット、2バイト目が下位8ビットになります。そこで、1バイト目を`0x100`倍して、上位8ビットに変換しています。

<table>
<tr style="border:none;"><td>0x8251</td><td style="border:none;">→</td><td>0x82</td><td>0x51</td><td style="border:none;">→</td><td>0x82 * 0x100 + 0x51</td></tr>
</table>

それでは、数字文字を変換する関数を使って、答えを変換しましょう。<br>
`main.cpp`を開き、`utility.h`をインクルードしてください。

```diff
 #include "exam_science.h"
 #include "exam_geography.h"
 #include "exam_politics.h"
+#include "utility.h"
 #include <iostream>
 #include <string>
```

そして、答えを入力するプログラムの下に、答えをSJISからASCIIに変換するプログラムを追加してください。

```diff
   for (const auto& e : questions) {
     cout << e.q << "\n";
     string answer;
     cin >> answer;
+
+    // 入力された答えをSJISからASCIIに変換する
+    const string ascii = ConvertSjisNumberToAscii(answer);
+    // 変換が成功した場合はASCII文字列に置き換える
+    if ( ! ascii.empty()) {
+      answer = ascii;
+    }

     if (answer == e.a) {
       cout << "正解！\n";
     } else {
       cout << "間違い！ 正解は" << e.a << "\n";
     }
   } // for questions
```

プログラムが書けたらビルドして実行してください。教科選択で`1`の「数学」を選び、全角モードで正しい答えを入力したとき「正解！」と表示されたら成功です。

>**【全角・半角とは何者なのか】**<br>
>「全角」、「半角」という呼び名は、昔のコンピューターにおいて、日本語の漢字かな文字を16x16ドット、アルファベットなどの英数字を8x16ドットで表示していた頃の名残です。漢字かな文字を「全角」と呼ぶのは、印刷業界でほぼ正方形の文字を「全角」と呼んでいることに由来します。そして、英数字は漢字かな文字の半分の幅で表示されるので、「半角」と呼ばれるようになりました。

<pre class="tnmai_assignment">
<strong>【課題02】</strong>
<code>utility.h</code>, <code>utility.cpp</code>, <code>main.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題03】</strong>
<code>Git</code>メニューから「同期」を選択し、コミットをリモートリポジトリに反映しなさい。
</pre>
