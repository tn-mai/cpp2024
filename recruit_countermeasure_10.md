[就職試験対策 第10回]

# 一般常識(経済)

## 1. 経済

### 1.1 経済問題のデータファイルについて

このテキストでは、日本の経済に関する問題を出題する関数を作っていきます。科目としては「社会」になります。

地理、政治と同様に、入力済みのデータファイルを用意しました。Webブラウザで以下のURLを開き、
`japanese_economics.txt`というファイルダウンロードして、プロジェクトのフォルダにコピーしてください(`economics`(エコノミクス)は「経済」という意味)。

>github.com/tn-mai/cpp2024/tree/main/data

地理や政治のデータファイルと同じく、`japanese_economics.txt`もCSV形式でデータを記録しています。実際のデータは次のようになります。

>経済,期間内に、{}で生産された財やサービスの合計額を{}という,国内,GDP|国内総生産<br>
>経済,期間内に、国民が{}及び{}で得た所得の合計を{}という,国内,国外,GNI|国民総所得<br>
>経済,{}は、平均的な家計について、費用の変化を測定したもの,CPI|消費者物価指数<br>
>財政,税金には、国に納める{}と、地方公共団体に納める{}がある,国税,地方税

そして、このデータの並び順は次のようになっています。

>`ジャンル,問題文,空欄1の答え,空欄2の答え,空欄3の答え,...`

問題文の`{}`が回答するべき「空欄」になります。各問題文に`{}`はひとつ以上存在し、先頭から「空欄1」、「空欄2」、「空欄3」のようになっています。これらの空欄に割り当てる答えが、問題文の直後に並べられています。

ここまでは、「政治のデータファイル」とおなじ構造です。ただし、経済の問題では「同じ空欄に対する正しい答えが２つ以上ある」場合があります。理由は、用語の「略称」や「別名」が存在するためです。

そこで、複数の答えがある場合は`|`で区切って定義しています。例えば`GDP|国内総生産`といった具合です。

### 1.2 経済問題のデータファイルを読み込む

それでは、経済のプログラム用のファイルを追加しましょう。ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。<br>
そして、`exam_economics.h`という名前のヘッダファイルを追加してください。

「経済問題を作成する関数」の名前は`CreateEconomicsExam`(クリエイト・エコノミクス・エグザム、「経済の試験を作成する」という意味)とします。追加した`exam_economics.h`を開き、次のプログラムを追加してください。

```diff
+#pragma once
+#include "question.h"
+
+// 経済の問題を作成する
+QuestionList CreateEconomicsExam();
```

続いて、C++ファイルを追加します。ソリューションエクスプローラーの「ソースファイル」を右クリックし、「追加→新しい項目」を選択してください。そして、`exam_economics.cpp`という名前のC++ファイルを追加してください。

最初に、経済問題のデータを読み込む配列を定義します。追加した`exam_economics.cpp`を開き、次のプログラムを追加してください。

```diff
+#include "exam_economics.h"
+#include "utility.h"
+#include <fstream>
+#include <iostream>
+#include <random>
+#include <unordered_map>
+using namespace std;
+
+/**
+* 経済の問題を作成する
+*/
+QuestionList CreateEconomicsExam()
+{
+  // 経済問題データ
+  struct EconomicsData
+  {
+    string genre;           // ジャンル
+    string text;            // 問題文
+    vector<vector<string>> answers; // 答えの配列
+  };
+  unordered_map<string, vector<EconomicsData>> data;
+}
```

<ruby>前述<rt>ぜんじゅつ</rt></ruby>したように、経済の問題では「ひとつの空欄に対応する答えが複数定義されている」場合があります。複数の答えを保持するために、`answers`変数を二次元配列としています。

それでは、`japanese_economics.txt`を読み込みましょう。<br>
ファイルの読み込みには`ifstream`(アイ・エフ・ストリーム)クラスを使います。都道府県データの配列の定義の下に、次のプログラムを追加してください。

```diff
     string text;            // 問題文
     vector<vector<string>> answers; // 答えの配列
   };
   unordered_map<string, vector<EconomicsData>> data;
+
+  { // 経済問題データを読み込む
+    constexpr char filename[] = "japanese_economics.txt";
+    ifstream ifs(filename);
+    if (!ifs) {
+      cerr << "エラー: " << filename << "を読み込めません\n";
+      return {};
+    }
+
+    while (true) {
+      string s;
+      getline(ifs, s);
+      if (!ifs) {
+        break;
+      }
+      const vector<string> v = Split(s, ',');
+      const string& genre = v[0]; // ジャンルを取得
+
+      // 答えを解析
+      vector<vector<string>> answers;
+      for (auto i = v.begin() + 2; i != v.end(); i++) {
+        answers.push_back(Split(*i, '|'));
+      }
+
+      // 問題データを追加
+      data[genre].push_back({ genre, v[1], answers });
+    }
+  } // 経済問題データを読み込む
 }
```

このプログラムは、前回作成した「政治問題データを読み込む」プログラムとほとんど同じです。コピーして違うところだけ変更すると早く完成するでしょう。

政治問題のプログラムとの最大の違いは、答えを複数の単語に分解する処理が追加されているところです。答えの解析には`Split`関数を使っています。「区切り文字引数」に`|`(垂直線)を指定することで、単語に分解しています。

### 1.3 経済の問題文を作成する

経済データを読み込んだら、次は問題を作成します。政治データと同様に、経済の問題でもランダムに選んだ２つのジャンルから出題することにしましょう。そして、それぞれのジャンルからランダムに選んだ５問を出題します。

それでは、ジャンルをランダムに選択するプログラムを作成しましょう。経済データを読み込むプログラムの下に、次のプログラムを追加してください。

>この節のプログラムは、最後の部分を除いて「政治問題のプログラム」と全く同じです。コピーすると簡単に完成するでしょう。

```diff
       // 問題データを追加
       data[genre].push_back({ genre, v[1], answers });
     }
   } // 経済問題データを読み込む
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

続いて、「空欄の位置と数」を調べましょう。問題データを選ぶプログラムの下に、次のプログラムを追加してください。

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

次に、「問題文のコピーを作成しながら、空欄を答えに置き換える」プログラムを作成しましょう。空欄のひとつをランダムに選ぶプログラムの下に、次のプログラムを追加してください。

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
+          s += question.answers[k][0]; // 選ばれなかった空欄の場合は最初の答えをコピー
+        } else {
+          s.append("[ ? ]"); // 選ばれた空欄の場合は「表示用の空欄文字列」をコピー
+        }
+        from = end + 2; // コピー元の位置を更新
+      }
+      s.append(question.text, from); // 問題文の末尾部分をコピー
+
+      questions.push_back({ s, question.answers[index][0] });
     } // for j < quizCount
   } // for i < genreCount
```

### 1.4 Question構造体に複数の答えを指定できるようにする

さて、ここまでは「政治の問題のプログラム」と全く同じです。ただし、経済問題には複数の答えがありえます。そのため、どの答えでも正解と判定できる必要があります。

そもそも、現在の`Question`構造体はひとつの答えしか扱えません。そこで、複数の答えを扱えるように`Qestion`構造体を変更します。`question.h`を開き、`Question`構造体の定義に次のプログラムを追加してください。

```diff
 // 問題文と答えをまとめる構造体
 struct Question
 {
   std::string q; // 問題文
   std::string a; // 答え
+  std::vector<std::string> b; // 答え(複数ある場合)
 };

 // 問題の配列をあらわす型
 using QuestionList = std::vector<Question>;
```

末尾に要素を追加するだけなら、`{}`によるオブジェクトの初期化は問題なくコンパイルされます(先頭や中間部分に追加すると動作がおかしくなるので注意)。

例えば、次のように`questions`配列変数に値を追加するプログラムがあるとします。

```c++
questions.push_back({ text, answer });
```

`{ text, answer }`の部分で`Question`オブジェクトを作成しています。`Qeustion`構造体の末尾に追加したメンバ変数`b`には何も指定していないので、デフォルト値が設定されます。なお、`b`の型である`vector`クラスのデフォルト値は「要素数`0`の配列」です。

### 1.5 main関数を複数の答えに対応させる

現在、`main`関数にある答えを判定するプログラムでは、メンバ変数`a`だけを判定しているため、複数の答えを判定できません。そこで、メンバ変数`b`についても答えの判定を行うように変更します。`main.cpp`を開き、答えを判定するプログラムを次のように変更してください。

```diff
     if (answer == e.a) {
       cout << "正解！\n";
-    } else {
+    } else if (e.b.empty()) {
+      // 答えがひとつだけの場合
       cout << "間違い！ 正解は" << e.a << "\n";
-    }
+    } else {
+      // 答えが複数ある場合、いずれかと一致すれば正解とする
+      bool isMatch = false;
+      for (const auto& b : e.b) {
+        if (answer == b) {
+          isMatch = true; // 一致する答えが見つかった
+          break;
+        }
+      }
+
+      // 比較結果を出力
+      if (isMatch) {
+        cout << "正解！\n";
+      } else {
+        cout << "間違い！　正解は" << e.a << "(または";
+        for (auto& b : e.b) {
+          cout << "、" << b;
+        }
+        cout << ")\n";
+      }
+    } // if answer == e.a
   } // for questions
 }
```

追加したプログラムは「メンバ変数`b`が空ではない場合」に実行されます。メンバ変数`b`に含まれるすべての答えと`answer`変数を比較し、一致する答えがあれば`isMatch`(イズ・マッチ、「一致しますか？」という意味)変数を`true`にしてループを抜けます。

これで、答えが複数ある問題を作れるようになりました。

>**【プログラミングで「冠詞を省略する」理由】**<br>
>`is match`は「動詞 + 動詞」に見えるため、英語としては不自然です。自然な表現にするには`is a match`のように冠詞を付けて、`match`が名詞であることを明確にする必要があります。しかし、プログラミングの世界では慣習的に「冠詞を省略する」ことが多いです。冠詞を省略する最も大きな理由は、
>
>*冠詞を付けても情報量が増えず、むしろ読みにくくなる*
>
>ためです。例えば、`A`には「単数」という意味があります。しかし、続く単語が複数形でない場合は単数だと判断できるため、なくても困りません。別の例として、`The`は単数か複数かの情報すら持たないため、結局は続く単語自体に単数形や複数形の情報を持たせる必要があり、やはりなくても困りません。
>
>もちろん、実際の英文では冠詞は「微妙な意味を伝える」という重要な役割があります。しかしながら、プログラミングでは「意味を明確に示す」ことが重要です。そのため、冠詞がなくても正しい意味が伝わるように書くのです。

### 1.6 複数の答えを設定する

それでは、複数の答えがある経済の問題を作成しましょう。`exam_economics.cpp`を開き、問題を作成するプログラムに次のプログラムを追加してください。

```diff
         from = end + 2; // コピー元の位置を更新
       }
       s.append(question.text, from); // 問題文の末尾部分をコピー

       questions.push_back({ s, question.answers[index][0] });
+
+      // 答えが複数ありうる場合、それらを「答えb」の配列に代入
+      const auto& answer = question.answers[index];
+      if (answer.size() > 1) {
+        questions.back().b.assign(answer.begin() + 1, answer.end());
+      }
     } // for j < quizCount
   } // for i < genreCount
```

追加したプログラムは、空欄にあう答えが複数ある場合に、２番目以降の答えをメンバ変数`b`に代入するものです。

### 1.7 経済の問題を選べるようにする

それでは、経済問題を選べるようにしましょう。`main.cpp`を開き、`exam_economics.h`をインクルードしてください。

```diff
 #include "question.h"
 #include "exam_japanese.h"
 #include "exam_english.h"
 #include "exam_science.h"
 #include "exam_geography.h"
 #include "exam_politics.h"
+#include "exam_economics.h"
 #include <iostream>
 #include <string>
 #include <vector>
```

続いて、選択できる教科を表示するプログラムに、「経済」の項目を追加してください。

```diff
   questions.push_back({
     to_string(x) + "人のうち" + to_string(y) + "人を選ぶ組み合わせは何通りあるか？",
     to_string(z) });

   cout << "[リクルート試験対策クイズ]\n";

-  cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n6=政治\n";
+  cout << "教科を選んでください\n1=数学\n2=国語\n3=英語\n4=物理\n5=地理\n6=政治\n7=経済\n";
   int subject;
   cin >> subject;
   if (subject == 2) {
```

最後に、教科ごとの問題文を選択するプログラムに、経済の問題文を選択する`if`文を追加してください。

```diff
   } else if (subject == 4) {
     questions = CreatePhysicsExam();
   } else if (subject == 5) {
     questions = CreateGeographyExam();
   } else if (subject == 6) {
     questions = CreatePoliticsExam();
+  } else if (subject == 7) {
+    questions = CreateEconomicsExam();
   }

   for (const auto& e : questions) {
     cout << e.q << "\n";
```

プログラムが書けたらビルドして実行してください。教科選択で`7`番の「経済」を選んだとき、「経済の問題」が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題01】</strong>
<code>main.cpp</code>, <code>exam_economics.h</code>, <code>exam_economics.cpp</code>, <code>recruit_quiz.vcproj</code>,
<code>recruit_quiz.vcproj.filters</code>, <code>japanese_economics.txt</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題02】</strong>
<code>Git</code>メニューから「同期」を選択し、コミットをリモートリポジトリに反映しなさい。
</pre>
