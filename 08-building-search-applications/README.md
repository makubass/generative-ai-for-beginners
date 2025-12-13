# 検索アプリケーションの構築

[![生成AIと大規模言語モデルの紹介](./images/08-lesson-banner.png?WT.mc_id=academic-105485-koreyst)](https://youtu.be/W0-nzXjOjr0?si=GcsqiTTvd7RKbo7V)

> > _上の画像をクリックすると、このレッスンのビデオが表示されます_

LLM（大規模言語モデル）はチャットボットやテキスト生成だけではありません。埋め込み（Embedding）を使って検索アプリケーションを構築することもできます。埋め込みはベクトルとしても知られるデータの数値表現であり、意味（セマンティック）に基づく検索に使用できます。

このレッスンでは、教育系スタートアップ向けの検索アプリケーションを構築します。私たちのスタートアップは途上国の学生に無料教育を提供する非営利団体で、多数のYouTube動画を教材として保有しています。学生が質問を入力してYouTube動画を検索できる検索アプリケーションを作成します。

たとえば、生徒が「What are Jupyter Notebooks?」や「What is Azure ML」と入力すると、検索アプリはその質問に関連するYouTube動画の一覧を返し、さらに回答が含まれる動画内の位置（タイムスタンプ）へのリンクを返します。

## 導入

このレッスンで学ぶ内容:

- セマンティック検索とキーワード検索の違い
- テキスト埋め込み（Text Embeddings）とは何か
- テキスト埋め込みインデックスの作成
- テキスト埋め込みインデックスの検索

## 学習目標

このレッスンを修了すると、以下ができるようになります:

- セマンティック検索とキーワード検索の違いを説明できる
- テキスト埋め込みとは何かを説明できる
- 埋め込み（Embeddings）を用いた検索アプリケーションを作成できる

## なぜ検索アプリを作るのか？

検索アプリケーションを構築することにより、埋め込みを使ったデータ検索の仕組みを理解できます。また、学生が必要な情報を素早く見つけられる実用的な検索アプリの作り方を学べます。

本レッスンには、Microsoft の YouTube チャンネル「AI Show」(https://www.youtube.com/playlist?list=PLlrxD0HtieHi0mwteKBOfEeOYf0LJU4O1) の動画トランスクリプトから作成した埋め込みインデックスが含まれています。AI Show は AI と機械学習を教えるチャンネルです。埋め込みインデックスは2023年10月までの各動画トランスクリプトに対する埋め込みを含んでいます。これを利用して検索アプリを構築し、質問に対する回答が含まれている動画の該当箇所へのリンクを返す機能を実装します。

以下は「can you use rstudio with azure ml?」という質問に対するセマンティック検索の例です。YouTubeのURLを見ると、URLにタイムスタンプが含まれており、回答がある動画内の場所へ直接移動できることが分かります。

![「can you use rstudio with Azure ML?」という質問に対するセマンティック検索の例](./images/query-results.png?WT.mc_id=academic-105485-koreyst)

## セマンティック検索とは何か？

セマンティック検索とは、クエリ内の単語の意味（セマンティクス）に基づいて関連性の高い結果を返す検索手法です。

例として、「車を買いたい」とします。キーワード検索で「my dream car」と検索すると、字義どおり「dream」と「car」にマッチする結果が返る可能性がありますが、セマンティック検索はこのクエリを「理想の車を探している」という意図として解釈し、より関連性の高い結果を返します。

## テキスト埋め込みとは何か？

[テキスト埋め込み](https://en.wikipedia.org/wiki/Word_embedding?WT.mc_id=academic-105485-koreyst)は、自然言語処理で使われるテキスト表現技術で、テキストを意味的に数値化したものです。埋め込みは機械が理解しやすい形でデータを表現します。本レッスンでは、OpenAIの埋め込みモデルを使って埋め込みを生成する方法に注目します。

次のような文がトランスクリプトにあるとします:

```text
Today we are going to learn about Azure Machine Learning.
```

これを OpenAI Embedding API に渡すと、1536 個の数値（ベクトル）で表される埋め込みが返されます。ベクトル内の各数値はテキストが持つ異なる意味的側面を表します。短縮してベクトルの最初の10個の数値を示すと以下のようになります。

```python
[-0.006655829958617687, 0.0026128944009542465, 0.008792596869170666, -0.02446001023054123, -0.008540431968867779, 0.022071078419685364, -0.010703742504119873, 0.003311325330287218, -0.011632772162556648, -0.02187200076878071, ...]
```

## 埋め込みインデックスはどのように作られるのか？

このレッスンの埋め込みインデックスは、複数の Python スクリプトで生成されました。スクリプトと手順は `scripts` フォルダーの [README](./scripts/README.md?WT.mc_id=academic-105485-koreyst) に記載されています。埋め込みインデックスは既に提供されているため、レッスンを進めるだけならスクリプトを実行する必要はありません。

スクリプトは次の処理を行います:

1. [AI Show](https://www.youtube.com/playlist?list=PLlrxD0HtieHi0mwteKBOfEeOYf0LJU4O1) プレイリスト内の各動画のトランスクリプトをダウンロードします。
2. [OpenAI Functions](https://learn.microsoft.com/azure/ai-services/openai/how-to/function-calling?WT.mc_id=academic-105485-koreyst) を利用してトランスクリプトの最初の3分から話者の名前を抽出しようとします。抽出した話者名は `embedding_index_3m.json` に格納されます。
3. トランスクリプトを**3分ごとのテキストセグメント**に分割します。次のセグメントと約20語を重複させることで、セグメントの境界で意味が切れないようにし、検索の文脈を保ちます。
4. 各テキストセグメントを OpenAI Chat API に渡して60語程度に要約します。要約も `embedding_index_3m.json` に保存されます。
5. 最後に、各セグメントのテキストを OpenAI Embedding API に渡し、1536個の数値ベクトルを取得します。セグメントテキストと対応する埋め込みベクトルは `embedding_index_3m.json` に保存されます。

### ベクターデータベース

このレッスンでは簡便さのため、埋め込みインデックスを `embedding_index_3m.json` という JSON ファイルに保存し、Pandas DataFrame に読み込んで利用しています。実運用では、埋め込みインデックスは [Azure Cognitive Search](https://learn.microsoft.com/training/modules/improve-search-results-vector-search?WT.mc_id=academic-105485-koreyst)、[Redis](https://cookbook.openai.com/examples/vector_databases/redis/readme?WT.mc_id=academic-105485-koreyst)、[Pinecone](https://cookbook.openai.com/examples/vector_databases/pinecone/readme?WT.mc_id=academic-105485-koreyst)、[Weaviate](https://cookbook.openai.com/examples/vector_databases/weaviate/readme?WT.mc_id=academic-105485-koreyst) などのベクターデータベースに保存することが一般的です。

## コサイン類似度（cosine similarity）を理解する

テキスト埋め込みについて学んだら、その埋め込みを使ってデータ検索を行う方法、特にクエリに対して最も類似した埋め込みを見つける方法（コサイン類似度）を学びます。

### コサイン類似度とは？

コサイン類似度は2つのベクトル間の類似度を測る尺度で、`最近傍検索（nearest neighbor search）` として知られることもあります。コサイン類似度検索を行うには、クエリテキストを OpenAI Embedding API でベクトル化し、そのクエリベクトルと埋め込みインデックス内の各ベクトルとのコサイン類似度を計算します。埋め込みインデックスは各YouTubeトランスクリプトのテキストセグメントごとにベクトルを保持しており、類似度の高い順にソートすることでクエリに最も近いテキストセグメントを見つけられます。

数学的には、コサイン類似度は多次元空間における2つのベクトルのなす角の余弦を測ります。ユークリッド距離が大きくても、ベクトル間の角度が小さければコサイン類似度は高くなるため、文書の類似性評価に有益です。コサイン類似度の数式については、[Cosine similarity](https://en.wikipedia.org/wiki/Cosine_similarity?WT.mc_id=academic-105485-koreyst) を参照してください。

## 最初の検索アプリケーションを構築する

ここからは、埋め込みを使った検索アプリケーションの構築方法をステップで学びます。検索アプリは質問を受け取り、それに関連する動画一覧と該当箇所へのリンクを返します。

このソリューションは Windows 11、macOS、Ubuntu 22.04 上で Python 3.10 以降を使って構築・テストされています。Python は [python.org](https://www.python.org/downloads/?WT.mc_id=academic-105485-koreyst) からダウンロードできます。

## 課題：学生のための検索アプリケーションを構築する

このレッスンの冒頭で紹介したスタートアップのために、学生が評価課題として検索アプリケーションを構築できるようにします。

この課題では、検索アプリで使用する Azure OpenAI サービスを作成します。作業を進めるには Azure サブスクリプションが必要です。

### Azure Cloud Shell を起動する

1. [Azure ポータル](https://portal.azure.com/?WT.mc_id=academic-105485-koreyst) にサインインします。
2. Azure ポータルの右上にある Cloud Shell アイコンを選択します。
3. 環境タイプとして **Bash** を選択します。

#### リソースグループを作成する

> この手順では、East US にあるリソースグループ名を `semantic-video-search` としています。
> リソースグループ名は変更できますが、リソースのロケーションを変更する場合は [モデルの利用可能性テーブル](https://aka.ms/oai/models?WT.mc_id=academic-105485-koreyst) を確認してください。

```shell
az group create --name semantic-video-search --location eastus
```

#### Azure OpenAI サービスリソースを作成する

Azure Cloud Shell から次のコマンドを実行して Azure OpenAI サービスのリソースを作成します。

```shell
az cognitiveservices account create --name semantic-video-openai --resource-group semantic-video-search \
    --location eastus --kind OpenAI --sku s0
```

#### このアプリケーションで使用するエンドポイントとキーを取得する

Azure Cloud Shell から次のコマンドを実行して、Azure OpenAI サービスのエンドポイントとキーを取得します。

```shell
az cognitiveservices account show --name semantic-video-openai \
   --resource-group  semantic-video-search | jq -r .properties.endpoint
az cognitiveservices account keys list --name semantic-video-openai \
   --resource-group semantic-video-search | jq -r .key1
```

#### OpenAI Embedding モデルをデプロイする

Azure Cloud Shell から次のコマンドを実行して OpenAI の埋め込みモデルをデプロイします。

```shell
az cognitiveservices account deployment create \
    --name semantic-video-openai \
    --resource-group  semantic-video-search \
    --deployment-name text-embedding-ada-002 \
    --model-name text-embedding-ada-002 \
    --model-version "2"  \
    --model-format OpenAI \
    --sku-capacity 100 --sku-name "Standard"
```

## ソリューション

GitHub Codespaces 上で [ソリューションノートブック](./python/aoai-solution.ipynb?WT.mc_id=academic-105485-koreyst) を開き、Jupyter Notebook の指示に従ってください。

ノートブックを実行するとクエリの入力が求められます。入力ボックスは次のようになります:

![ユーザーがクエリを入力するための入力ボックス](./images/notebook-search.png?WT.mc_id=academic-105485-koreyst)

## お疲れ様でした！学習を続けましょう

レッスンを修了したら、[Generative AI Learning コレクション](https://aka.ms/genai-collection?WT.mc_id=academic-105485-koreyst) を参照して生成AIの学習を続けてください。

次は Lesson 9 に進み、[画像生成アプリケーションの構築](../09-building-image-applications/README.md?WT.mc_id=academic-105485-koreyst) を学びましょう！
