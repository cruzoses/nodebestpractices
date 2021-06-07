# スマートログを使ってアプリを透明にする

<br/><br/>

### 一段落説明

どちらにしろログステートメントを出力しているのだから、エラーやコアメトリクスをトレースできるような本番環境の情報をラップアップするインタフェースが必要なのは明らかです (例えば、毎時何個のエラーが発生しているか、最も遅いAPIエンドポイントはどれかなど)。すべてのボックスをチェックする堅牢なロギングフレームワークに適度な努力を投資してはどうでしょうか？ これを実現するためには、3つのステップについて熟考した上での決断が必要です。

**1. スマートロギング** – 最低限、[Winston](https://github.com/winstonjs/winston)、[Bunyan](https://github.com/trentm/node-bunyan) のような評判の良いロギングライブラリを使用し、各トランザクションの開始と終了時に意味のある情報を書く必要があります。また、ログステートメントを JSON としてフォーマットし、すべてのコンテキストプロパティ（ユーザーID、操作タイプなど）を提供して、運用チームがそれらのフィールドを操作できるようにすることを検討してください。また、各ログ行に一意のトランザクションIDを含めてください。詳細については、以下の「トランザクションIDをログに書き込む」を参照してください。最後に考慮すべき点として、Elastic Beat のようにメモリや CPU のようなシステムリソースをログに記録するエージェントも含まれています。

**2. スマートアグリゲーション** – サーバーのファイルシステムに関する包括的な情報を入手したら、定期的にこれらのデータを集約し、容易にし、可視化するシステムにプッシュしてください。 例えば、Elastic stack は、データを集約して可視化するためのすべてのコンポーネントを提供する、人気のある無料の選択肢です。多くの商用製品が同様の機能を提供していますが、それらはセットアップ時間を大幅に削減し、ホスティングを必要としません。

**3. スマートビジュアライゼーション** – 情報が集約され、検索可能になった今、ログを簡単に検索できる力だけで満足することができますが、これはコーディングや労力をかけずにはるかに先に進むことができます。 エラー率、1日の平均 CPU、最後の1時間にオプトインした新規ユーザーの数など、重要な運用上の指標を表示することができ、アプリの管理と改善に役立つその他の指標を表示することができます。

<br/><br/>

### 可視化の例: Kibana (Elastic stack の一部) はログコンテンツの高度な検索を容易にします

![Kibana はログコンテンツの高度な検索を容易にします](./assets/images/smartlogging1.png)

<br/><br/>

### 可視化の例: Kibana (Elastic stack の一部) はログに基づいてデータを可視化します

![Kibana はログを元にデータを可視化する](./assets/images/smartlogging2.jpg)

<br/><br/>

### ブログ引用: ロガーの要件

ブログ [Strong Loop](https://strongloop.com/strongblog/compare-node-js-logging-winston-bunyan/) より:

> いくつかの（ロガーのための）要件を確認してみましょう:
> 1. 各ログ行にタイムスタンプを付けます。これは非常にわかりやすいものです - 各ログエントリがいつ発生したかがわかるはずです。
> 2. ロギングのフォーマットは、機械だけでなく人間にも分かりやすいものでなければなりません。
> 3. 複数の設定可能な送信先ストリームを許可します。例えば、トレースログを一つのファイルに書き込んでいるが、エラーが発生したときに同じファイルに書き込んで、次にエラーファイルに書き込んで、同時に電子メールを送信する...といった具合です。

<br/><br/>

<br/><br/>