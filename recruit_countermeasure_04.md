[就職試験対策 第04回]

# 一般常識(国語その2)

## 1 同音異義語、同訓異義語、対義語、類義語

### 1.1 読みが同じで意味が異なる語の問題を作成する

本章では「読みが同じで意味が異なる語」の問題を作成します。「読みが同じで意味が異なる単語」には以下の二種類があります。

>* 同音異義語(どうおんいぎご): 音読みが同じで意味が異なる漢字や熟語。
>* 同訓異義語(どうくんいぎご): 訓読みが同じで意味が異なる漢字や熟語。

このふたつは「読みが同じで意味が異なる語」という点では全く同じです。そこで、問題の作成ではこれらを区別せずに扱うことにします。

「読みが同じで意味が異なる語」は英語で`Homophone`(ホモフォン)といいます。そこで、問題を作成する関数名は`CreateHomophoneExam`(クリエイト・ホモフォン・エグザム)とします。

`exam_japanese.h`を開き、次のプログラムを追加してください。

```diff
 // 漢字の読み取り問題を作成する
 QuestionList CreateKanjiExam();

 // 慣用句の意味を答える問題を作成する
 QuestionList CreateIdiomExam();
+
+// 同じ読みで意味の異なる語の問題を作成する
+QuestionList CreateHomophoneExam();
```

続いて、`CreateHomophoneExam`関数の定義を作成しましょう。まずは問題データを定義します。`exam_japanese.cpp`を開き、`CreateIdiomExam`関数の定義の下に、次のプログラムを追加してください。

```diff
       s += "\n  " + to_string(j + 1) + ":" + data[answers[j]].meaning;
     }

     questions.push_back({ s, to_string(correctNo) });
   }
   return questions;
 }
+
+/**
+* 同じ読みで意味の異なる漢字の問題を作成する
+*/
+QuestionList CreateHomophoneExam()
+{
+  const struct {
+    const char* reading;   // 読み
+    struct {
+      const char* kanji;   // 漢字
+      const char* meaning; // 意味
+    } words[3];
+  } data[] = {
+    { "じき", {
+      { "時期", "何かを行うとき、期間。" },
+      { "時機" , "物事を行うのによい機会" }}},
+    { "そうぞう", {
+      { "想像", "実際には経験していない事柄を思い描くこと" },
+      { "創造", "新しいものを作り上げること" }}},
+    { "ほしょう", {
+      { "保証", "間違いがなく確かであると約束すること" },
+      { "保障", "権利や地位などが維持されるように保護し守ること" },
+      { "補償", "損失をおぎなってつぐなうこと" }}},
+    { "たいしょう", {
+      { "対象", "行為の目標となるもの" },
+      { "対称", "２つの図形や物事が互いにつり合っていること" },
+      { "対照", "見比べること、違いが際立つこと" }}},
+    { "あやまる", {
+      { "謝る", "失敗について許しを求める" },
+      { "誤る", "間違った判断をする" }}},
+    { "おさめる", {
+      { "納める", "金や物を渡すべきところに渡す" },
+      { "治める", "乱れている物事を落ち着いて穏やかな状態にする" },
+      { "修める", "行いや人格を正しくする、学問や技芸などを学んで身につける" }}},
+  };
+}
```

次に、問題データから問題を作成します。問題データの定義の下に、次のプログラムを追加してください。

```diff
       { "納める", "金や物を渡すべきところに渡す" },
       { "治める", "乱れている物事を落ち着いて穏やかな状態にする" },
       { "修める", "行いや人格を正しくする、学問や技芸などを学んで身につける" }}},
   };
+
+  constexpr int quizCount = 5;
+  QuestionList questions;
+  questions.reserve(quizCount);
+  const vector<int> indices = CreateRandomIndices(size(data));
+  random_device rd;
+
+  for (int i = 0; i < quizCount; i++) {
+    const auto& e = data[indices[i]];
+
+    // 要素数を計算
+    int count = 0;
+    for (; count < size(e.words); count++) {
+      if (!e.words[count].kanji) {
+        break;
+      }
+    }
+
+    // 正しい番号を選択
+    const int correctNo = uniform_int_distribution<>(1, count)(rd);
+
+    // 問題文を作成
+    const vector<int> answers = CreateRandomIndices(count);
+    string s = "「" + string(e.words[answers[correctNo - 1]].kanji) +
+      "」の意味として正しい番号を選べ";
+    for (int j = 0; j < count; j++) {
+      s += "\n  " + to_string(j + 1) + ":" + e.words[answers[j]].meaning;
+    }
+    questions.push_back({ s, to_string(correctNo) });
+  }
+
+  return questions;
 }
```

このプログラムでは、ひとつの「読み」に対応する漢字データを、2個または3個設定できるようにしています。何個の要素を持つのかは、実行時に計算します。

初期値を指定しなかった変数にはデフォルト値が設定されます。数値型のデフォルト値は`0`、ポインタ型のデフォルト値はヌルです。そのため、ポインタがヌルになるまで要素を数えれば、要素数を計算できます。

また、答えの順序は`CreateRandomIndices`関数を使ってランダムにします。毎回同じ順番だと、問題の内容から答えを導くのではなく、番号だけを覚えてしまう恐れがあるからです。

それでは、作成した関数を`main`関数から呼び出しましょう。`main.cpp`を開き、国語の問題を設定するプログラムに次のプログラムを追加してください。

```diff
   if (subject == 2) {
     questions = CreateKanjiExam();
     QuestionList idiomExam = CreateIdiomExam();
     questions.insert(questions.end(), idiomExam.begin(), idiomExam.end());
+    QuestionList homophoneExam = CreateHomophoneExam();
+    questions.insert(questions.end(), homophoneExam.begin(), homophoneExam.end());
   }

   for (const auto& e : questions) {
     cout << e.q << "\n";
```

プログラムが書けたらビルドして実行してください。出題の選択で「国語」を選んだとき、慣用句の問題のあとで「読みが同じで意味が異なる語」の問題が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題01】</strong>
<code>main.cpp</code>、<code>exam_japanese.h</code>、<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題02】</strong>
<code>CreateHomophoneExam</code>関数の<code>data</code>配列に以下の問題を追加しなさい。
<table>
<tr><th>漢字</th><th>意味</th></tr>
<tr><td>紹介</td><td>未知の人や集団を引き合わせること</td></tr>
<tr><td>照会</td><td>問い合わせて確かめること</td></tr>
<tr><td></td><td></td></tr>
<tr><td>異常</td><td>普段と異なるようす</td></tr>
<tr><td>異状</td><td>姿や形が異なるようす</td></tr>
<tr><td></td><td></td></tr>
<tr><td>強行</td><td>困難があると分かっていて無理に物事を行うこと</td></tr>
<tr><td>強硬</td><td>自分の立場や主張を強い態度で押し通そうとすること</td></tr>
<tr><td></td><td></td></tr>
<tr><td>実体</td><td>物事の本当の姿や形</td></tr>
<tr><td>実態</td><td>物事の本当の状態</td></tr>
<tr><td></td><td></td></tr>
<tr><td>脅威</td><td>強い力や勢いによって恐れさせること</td></tr>
<tr><td>驚異</td><td>驚くほど素晴らしいものごと</td></tr></table>

<table>
<tr><th>漢字</th><th>意味</th></tr>
<tr><td>会心</td><td>期待どおりに物事が運んで満足すること</td></tr>
<tr><td>改心</td><td>悪い考えや行いを反省し、良い心に改めること</td></tr>
<tr><td></td><td></td></tr>
<tr><td>占める</td><td>場所、位置、地位などを自分のものにする</td></tr>
<tr><td>締める</td><td>強く引っ張ったりひねったりして、緩みのないようにする</td></tr>
<tr><td>閉める</td><td>物を動かしてすき間をふさぐ</td></tr>
<tr><td></td><td></td></tr>
<tr><td>謹む</td><td>相手を敬い尊重する</td></tr>
<tr><td>慎む</td><td>あやまちを起こさないよう控えめに行動する</td></tr></table></pre>

<pre class="tnmai_assignment">
<strong>【課題03】</strong>
<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.2 反対の意味を持つ漢字の問題を作成する

次は、「反対の意味を持つ漢字」、つまり「対義語」の問題を作成します。対義語は英語で
`Antonym`(アントニム)なので、関数名は`CreateAntonymExam`(クリエイト・アントニム・エグザム)とします。

`exam_japanese.h`を開き、次のプログラムを追加してください。

```diff
 // 慣用句の意味を答える問題を作成する
 QuestionList CreateIdiomExam();

 // 同じ読みで意味の異なる語の問題を作成する
 QuestionList CreateHomophoneExam();
+
+// 対義語の問題を作成する
+QuestionList CreateAntonymExam();
```

それでは、対義語の関数を定義しましょう。<br>
`exam_japanese.cpp`を開き、`CreateHomophoneExam`関数の定義の下に、次のプログラムを追加してください。

```diff
       s += "\n  " + to_string(j + 1) + ":" + e.words[answers[j]].meaning;
     }
     questions.push_back({ s, to_string(correctNo) });
   }
   return questions;
 }
+
+/**
+* 対義語の問題を作成する
+*/
+QuestionList CreateAntonymExam()
+{
+  const struct {
+    const char* kanji[2];
+  } data[] = {
+    { "意図(いと)", "恣意(しい)" }, { "需要(じゅよう)", "供給(きょうきゅう)" },
+    { "故意(こい)", "過失(かしつ)" }, { "曖昧(あいまい)", "明瞭(めいりょう)" },
+    { "緊張(きんちょう)", "弛緩(しかん)" }, { "過疎(かそ)", "過密(かみつ)" },
+    { "栄転(えいてん)", "左遷(させん)" }, { "消費(しょうひ)", "生産(せいさん)" },
+    { "異端(いたん)", "正統(せいとう)" }, { "尊敬(そんけい)", "軽蔑(けいべつ)" },
+  };
+
+  constexpr int quizCount = 5;
+  QuestionList questions;
+  questions.reserve(quizCount);
+  const vector<int> indices = CreateRandomIndices(size(data));
+  random_device rd;
+
+  for (int i = 0; i < quizCount; i++) {
+    // 間違った番号をランダムに選ぶ
+    const int correctIndex = indices[i];
+    vector<int> answers = CreateWrongIndices(size(data), correctIndex);
+
+    // ランダムな位置を正しい番号で上書き
+    const int correctNo = uniform_int_distribution<>(1, 4)(rd);
+    answers[correctNo - 1] = correctIndex;
+
+    // 問題文を作成
+    const int object = uniform_int_distribution<>(0, 1)(rd);
+    const int other = (object + 1) % 2;
+    string s = "「" + string(data[correctIndex].kanji[object]) +
+      "」の対義語として正しい番号を選べ";
+    for (int j = 0; j < 4; j++) {
+      s += "\n  " + to_string(j + 1) + ":" + data[answers[j]].kanji[other];
+    }
+    questions.push_back({ s, to_string(correctNo) });
+  }
+  return questions;
+}
```

今回は、難易度を高めるために四択問題としました。

それでは、作成した関数を`main`関数から呼び出しましょう。`main.cpp`を開き、国語の問題を設定するプログラムに次のプログラムを追加してください。

```diff
     QuestionList idiomExam = CreateIdiomExam();
     questions.insert(questions.end(), idiomExam.begin(), idiomExam.end());
     QuestionList homophoneExam = CreateHomophoneExam();
     questions.insert(questions.end(), homophoneExam.begin(), homophoneExam.end());
+    QuestionList antonymExam = CreateAntonymExam();
+    questions.insert(questions.end(), antonymExam.begin(), antonymExam.end());
   }

   for (const auto& e : questions) {
     cout << e.q << "\n";
```

プログラムが書けたらビルドして実行してください。出題の選択で「国語」を選んだとき、慣用句の問題のあとで「読みが同じで意味が異なる語」の問題が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題04】</strong>
<code>main.cpp</code>、<code>exam_japanese.h</code>、<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題05】</strong>
<code>CreateAntonymExam</code>関数の<code>data</code>配列に以下の問題を追加しなさい。
<table>
<tr><th>漢字1</th><th>漢字2</th></tr>
<tr><td>警戒(けいかい)</td><td>油断(ゆだん)</td></tr>
<tr><td>帰納(きのう)</td><td>演繹(えんえき)</td></tr>
<tr><td>枯渇(こかつ)</td><td>潤沢(じゅんたく)</td></tr>
<tr><td>乾燥(かんそう)</td><td>湿潤(しつじゅん)</td></tr>
<tr><td>賞賛(しょうさん)</td><td>罵倒(ばとう)</td></tr>
<tr><td>中枢(ちゅうすう)</td><td>末端(まったん)</td></tr>
<tr><td>絶賛(ぜっさん)</td><td>酷評(こくひょう)</td></tr>
<tr><td>創造(そうぞう)</td><td>模倣(もほう)</td></tr>
<tr><td>過激(かげき)</td><td>穏健(おんけん)</td></tr>
<tr><td>質素(しっそ)</td><td>華美(かび)</td></tr></table></pre>

<pre class="tnmai_assignment">
<strong>【課題06】</strong>
<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

### 1.3 似た意味を持つ漢字の問題

続いて、「似た意味を持つ漢字」、つまり「類義語」の問題を作成する関数を作成しましょう。類義語は英語で`Synonym`(シノニム)なので、関数名は`CreateSynonymExam`(クリエイト・シノニム・エグザム)とします。

`exam_japanese.h`を開き、次のプログラムを追加してください。

```diff
 // 同じ読みで意味の異なる語の問題を作成する
 QuestionList CreateHomophoneExam();

 // 対義語の問題を作成する
 QuestionList CreateAntonymExam();
+
+// 類義語の問題を作成する
+QuestionList CreateSynonymExam();
```

次に、`exam_japanese.cpp`を開き、`CreateAntonymExam`関数の定義の下に、次のプログラムを追加してください。

```diff
       s += "\n  " + to_string(j + 1) + ":" + data[answers[j]].kanji[other];
     }
     questions.push_back({ s, to_string(correctNo) });
   }
   return questions;
 }
+
+/**
+* 類義語の問題を作成する
+*/
+QuestionList CreateSynonymExam()
+{
+  const struct {
+    int count;            // 要素数
+    const char* kanji[4]; // 類義語の配列
+  } data[] = {
+    { 2, "仲介(ちゅうかい)", "斡旋(あっせん)" },
+    { 3, "夭逝(ようせい)", "夭折(ようせつ)", "早世(そうせい)" },
+    { 3, "交渉(こうしょう)", "折衝(せっしょう)", "協議(きょうぎ)" },
+    { 3, "抜群(ばつぐん)", "傑出(けっしゅつ)", "出色(しゅっしょく)" },
+    { 4, "熟知(じゅくち)", "通暁(つうぎょう)", "知悉(ちしつ)", "精通(せいつう)" },
+  };
+
+  constexpr int quizCount = 5;
+  QuestionList questions;
+  questions.reserve(quizCount);
+  const vector<int> indices = CreateRandomIndices(size(data));
+  random_device rd;
+
+  return questions;
+}
```

類義語は複数存在することが多いため、最大4つまで設定できるようにしてみました。問題文を作成する

```diff
   QuestionList questions;
   questions.reserve(quizCount);
   const vector<int> indices = CreateRandomIndices(size(data));
   random_device rd;
+
+  for (int i = 0; i < quizCount; i++) {
+    // 間違った番号をランダムに選ぶ
+    const int correctIndex = indices[i];
+    vector<int> answers = CreateWrongIndices(size(data), correctIndex);
+
+    // ランダムな位置を正しい番号で上書き
+    const int correctNo = uniform_int_distribution<>(1, 4)(rd);
+    answers[correctNo - 1] = correctIndex;
+
+    // 出題する類義語を選択
+    const auto& e = data[indices[i]];
+    const int object = uniform_int_distribution<>(0, e.count - 1)(rd);
+
+    // 問題文を作成
+    string s = "「" + string(data[correctIndex].kanji[object]) +
+      "」の類義語として正しい番号を選べ";
+    for (int j = 0; j < 4; j++) {
+      if (j == correctIndex) {
+        // 出題する語「以外」の類義語を正解として選択
+        int other = uniform_int_distribution<>(0, e.count - 2)(rd);
+        if (other >= object) {
+          other++;
+        }
+        s += "\n  " + to_string(j + 1) + ":" + e.kanji[other];
+      } else {
+        // 誤答を選択
+        const auto& f = data[answers[j]];
+        const  int k = uniform_int_distribution<>(0, f.count - 1)(rd);
+        s += "\n  " + to_string(j + 1) + ":" + f.kanji[k];
+      }
+    }
+
+    questions.push_back({ s, to_string(correctNo) });
+  }

   return questions;
 }
```

類義語が複数あるので、正解も複数の熟語から選ぶ必要があります。<br>
このプログラムでは`CreateWrongIndices`関数を使うことで、出題に使った類義語以外の数列の「最初の要素」を正解としています。

それでは、作成した関数を`main`関数から呼び出しましょう。`main.cpp`を開き、国語の問題を設定するプログラムに次のプログラムを追加してください。

```diff
     questions.insert(questions.end(), idiomExam.begin(), idiomExam.end());
     QuestionList homophoneExam = CreateHomophoneExam();
     questions.insert(questions.end(), homophoneExam.begin(), homophoneExam.end());
     QuestionList antonymExam = CreateAntonymExam();
     questions.insert(questions.end(), antonymExam.begin(), antonymExam.end());
+    QuestionList synonymExam = CreateSynonymExam();
+    questions.insert(questions.end(), synonymExam.begin(), synonymExam.end());
   }

   for (const auto& e : questions) {
     cout << e.q << "\n";
```

プログラムが書けたらビルドして実行してください。出題の選択で「国語」を選んだとき、対義語の問題のあとに「類義語」の問題が出題されたら成功です。

<pre class="tnmai_assignment">
<strong>【課題07】</strong>
<code>main.cpp</code>、<code>exam_japanese.h</code>、<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<div style="page-break-after: always"></div>

<pre class="tnmai_assignment">
<strong>【課題08】</strong>
<code>CreateSynonymExam</code>関数の<code>data</code>配列に以下の問題を追加しなさい。
<table>
<tr><th>漢字1</th><th>漢字2</th><th>漢字3</th></tr>
<tr><td>沿革(えんかく)</td><td>変遷(へんせん)</td><td></td></tr>
<tr><td>解雇(かいこ)</td><td>罷免(ひめん)</td><td></td></tr>
<tr><td>架空(かくう)</td><td>虚構(きょこう)</td><td></td></tr>
<tr><td>機敏(きびん)</td><td>迅速(じんそく)</td><td></td></tr>
<tr><td>委細(いさい)</td><td>詳細(しょうさい)</td><td>子細(しさい)</td></tr>
<tr><td>丁寧(ていねい)</td><td>慇懃(いんぎん)</td><td>丁重(ていちょう)</td></tr>
<tr><td>寄与(きよ)</td><td>貢献(こうけん)</td><td>尽力(じんりょく)</td></tr>
<tr><td>危惧(きぐ)</td><td>懸念(けねん)</td><td>憂慮(ゆうりょ)</td></tr>
<tr><td>敬服(けいふく)</td><td>感心(かんしん)</td><td>感銘(かんめい)</td></tr>
<tr><td>堅持(けんじ)</td><td>固執(こしつ)</td><td>墨守(ぼくしゅ)</td></tr>
</table></pre>

<pre class="tnmai_assignment">
<strong>【課題09】</strong>
<code>exam_japanese.cpp</code>を「ステージ」し、適切なメッセージを書いて「コミット」しなさい。
</pre>

<pre class="tnmai_assignment">
<strong>【課題10】</strong>
<code>Git</code>メニューから「同期」を選択し、コミットをリモートリポジトリに反映しなさい。
</pre>
