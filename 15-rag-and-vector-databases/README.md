# Retrieval Augmented Generation（RAG）とベクトルデータベース

[![Retrieval Augmented Generation (RAG) and Vector Databases](./images/15-lesson-banner.png?WT.mc_id=academic-105485-koreyst)](https://youtu.be/4l8zhHUBeyI?si=BmvDmL1fnHtgQYkL)

検索アプリケーションのレッスンでは、独自データを大規模言語モデル（LLM）に統合する方法を簡単に学びました。このレッスンでは、LLMアプリケーションにおけるデータのグラウンディング（根拠付け）や処理の仕組み、埋め込み（embeddings）とテキストの両方を含むデータ保存の方法について詳しく掘り下げます。

> **ビデオは近日公開予定**

## はじめに

このレッスンでは以下を扱います：

- RAG のイントロ（何か、なぜAIで使われるのか）
- ベクトルデータベースとは何か、それをアプリケーション用に作成する方法
- RAG をアプリケーションに統合する実践例

## 学習目標

このレッスンを終えると、次ができるようになります：

- データ取得と処理の観点で RAG の重要性を説明できる
- RAG アプリケーションをセットアップして LLM にデータをグラウンドできる
- LLM アプリケーションで RAG とベクトルデータベースを効果的に統合できる

## シナリオ：独自データで LLM を拡張する

このレッスンでは、教育スタートアップのノート（学習メモ）を追加して、チャットボットが各科目についてより多くの情報を参照できるようにします。ノートを活用することで、学習者はより効果的に学べ、試験対策の復習がしやすくなります。本シナリオで使用するものは次の通りです：

- `Azure OpenAI`: チャットボット作成に使用する LLM
- `AI for beginners' lesson on Neural Networks`: LLM をグラウンドするためのデータ
- `Azure AI Search` と `Azure Cosmos DB`: データを保存し検索インデックスを作成するためのベクトルデータベース

ユーザーはノートから練習問題を作成したり、復習用フラッシュカードを生成したり、要約を作成したりできるようになります。まずは RAG が何で、どのように機能するかを見ていきましょう。

## Retrieval Augmented Generation（RAG）とは

LLM を用いたチャットボットは、ユーザーのプロンプトを処理して応答を生成します。対話的に様々なトピックに対応できますが、応答は与えられたコンテキストと基礎学習データに依存します。例えば GPT-4 の知識カットオフは 2021 年 9 月であり、それ以降の出来事は知らない可能性があります。また、LLM の学習データは個人のノートや企業のマニュアルといった機密情報を含まない場合が一般的です。

### RAG の仕組み

![RAG の動作を示す図](images/how-rag-works.png?WT.mc_id=academic-105485-koreyst)

ノートからクイズを作るチャットボットを展開したいとします。このときナレッジベース（知識ベース）への接続が必要になります。RAG は次のように動作します：

- **ナレッジベース**：事前に文書を取り込み、前処理（大きな文書を小さなチャンクに分割し、テキスト埋め込みに変換してデータベースに保存するなど）を行います。
- **ユーザークエリ**：ユーザーが質問をする。
- **検索（Retrieval）**：クエリを埋め込みに変換し、ナレッジベースから関連情報を取得してプロンプトに追加する。
- **拡張生成（Augmented Generation）**：取得したデータを用いて LLM がより適切な応答を生成します。LLM は事前学習データだけでなく、追加されたコンテキストに基づいて答えを作成します。

![RAG のアーキテクチャ図](images/encoder-decode.png?WT.mc_id=academic-105485-koreyst)

RAG のアーキテクチャはエンコーダとデコーダの2つの部分で構成されるトランスフォーマーベースの実装が多いです。ユーザーの質問はエンコードされて意味を表したベクトルになり、そのベクトルがドキュメントインデックスと照合され、応答生成にデコードされます。LLM はエンコーダ・デコーダのモデルを使って出力を生成します。

研究論文 [Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks](https://arxiv.org/pdf/2005.11401.pdf?WT.mc_id=academic-105485-koreyst) では、RAG の実装に次の二つのアプローチが提案されています：

- **RAG-Sequence**：取得したドキュメント全体を用いて最適な回答を予測する方法
- **RAG-Token**：ドキュメントを使って次のトークンを生成し、その結果を逐次取得して応答を作る方法

### なぜ RAG を使うのか？

- **情報の豊富さ**：テキスト応答を最新の情報で補強できるため、ドメイン特化タスクの性能が向上します。
- **虚偽生成（ファブリケーション）の抑制**：ナレッジベース内の検証可能なデータを参照することで、事実に基づかない回答を減らせます。
- **コスト効果**：LLM のファインチューニングに比べて経済的に効率が良い場合があります。

## ナレッジベースの作成

本アプリケーションでは、個人のデータ（AI for Beginners のニューラルネットワークレッスン）を使います。

### ベクトルデータベース

ベクトルデータベースは、従来型データベースとは異なり、埋め込みベクトルを保存・管理・検索するために設計された専門的なデータベースです。文書の数値的表現（embeddings）を保存します。テキストを数値ベクトルに変換することで、AI システムがデータの意味を理解・処理しやすくなります。

LLM は入力トークン数に制限があるため、埋め込み全体を一度に渡せないことが多く、文書をチャンクに分割して類似する埋め込みだけを返す必要があります。チャンク化は渡すトークン数を削減し、コストの節約にもつながります。

代表的なベクトルデータベースには Azure Cosmos DB、Clarifyai、Pinecone、Chromadb、ScaNN、Qdrant、DeepLake などがあります。Azure Cosmos DB を Azure CLI で作成する例は次の通りです：

```bash
az login
az group create -n <resource-group-name> -l <location>
az cosmosdb create -n <cosmos-db-name> -r <resource-group-name>
az cosmosdb list-keys -n <cosmos-db-name> -g <resource-group-name>
```

### テキストから埋め込みへ

データを保存する前に、テキストをベクトル埋め込みに変換する必要があります。長文や大きなドキュメントを扱う場合、想定されるクエリに合わせてチャンク化することができます。チャンク化は文や段落単位で行えます。また、周辺の文脈を保持するために文書タイトルやチャンク前後のテキストを付与することも有効です。例としてチャンク化は次のように行えます：

```python
def split_text(text, max_length, min_length):
    words = text.split()
    chunks = []
    current_chunk = []

    for word in words:
        current_chunk.append(word)
        if len(' '.join(current_chunk)) < max_length and len(' '.join(current_chunk)) > min_length:
            chunks.append(' '.join(current_chunk))
            current_chunk = []

    # If the last chunk didn't reach the minimum length, add it anyway
    if current_chunk:
        chunks.append(' '.join(current_chunk))

    return chunks
```

チャンク化した後、異なる埋め込みモデルを使ってテキストを埋め込み化します。利用できるモデルには word2vec、OpenAI の `text-embedding-ada-002`、Azure の各種埋め込みなどがあります。モデル選択は使用言語、エンコード対象（テキスト/画像/音声）、入力長や出力ベクトル長に依存します。

OpenAI の `text-embedding-ada-002` を用いた埋め込みの例（図）：

!["cat" の埋め込み例](images/cat.png?WT.mc_id=academic-105485-koreyst)

## 検索（Retrieval）とベクトル検索

ユーザーが質問をすると、検索器（retriever）はクエリを埋め込みに変換し、ドキュメントインデックス内の関連ベクトルを探します。見つかった結果はテキストとして組み込まれ、LLM に渡されます。

### 検索（Retrieval）

検索は、インデックスから条件を満たす文書をすばやく見つけるプロセスです。retriever の目的は、LLM にコンテキストを与え、データに基づいた応答を生成できるような文書を取得することです。

データベース内検索の方法としては次のようなものがあります：

- **キーワード検索** - テキスト検索に使用する
- **セマンティック検索** - 単語の意味に基づいて検索する
- **ベクトル検索** - 文書を埋め込みベクトルに変換し、クエリベクトルに最も近いベクトルを検索する
- **ハイブリッド** - キーワード検索とベクトル検索の組み合わせ

データベース内にクエリと類似する応答が存在しない場合、システムは最善の情報を返しますが、関連度の閾値（最大距離）を設定したり、キーワードとベクトル検索を組み合わせるハイブリッド検索を用いることで精度を改善できます。レッスンではハイブリッド検索を用い、チャンクと埋め込みを格納したデータフレームを扱います。

### ベクトル類似度

retriever はナレッジベース内で近い埋め込みを検索します。ユーザーのクエリを埋め込みに変換し、最も類似する埋め込みを返します。類似度を測る一般的な手法はコサイン類似度で、二つのベクトル間の角度に基づいて類似性を評価します。

ほかにもユークリッド距離（ベクトルの端点間の直線距離）やドット積（対応要素の積和）を利用することができます。

### 検索インデックス

検索を行う前に、ナレッジベースの検索インデックスを構築する必要があります。インデックスは埋め込みを保存し、大規模データでも類似チャンクを高速に取得できます。ローカルでインデックスを作成する例：

```python
from sklearn.neighbors import NearestNeighbors

embeddings = flattened_df['embeddings'].to_list()

# Create the search index
nbrs = NearestNeighbors(n_neighbors=5, algorithm='ball_tree').fit(embeddings)

# To query the index, you can use the kneighbors method
distances, indices = nbrs.kneighbors(embeddings)
```

### 再ランキング（Re-ranking）

データベースに問い合わせた結果を、関連度の高い順に並べ替える必要がある場合があります。再ランキング用の LLM は機械学習を用いて検索結果の関連度を改善します。Azure AI Search ではセマンティックリランカーを使って自動的に再ランキングが行われます。Nearest Neighbors を使った再ランキングの例：

```python
# Find the most similar documents
distances, indices = nbrs.kneighbors([query_vector])

index = []
# Print the most similar documents
for i in range(3):
    index = indices[0][i]
    for index in indices[0]:
        print(flattened_df['chunks'].iloc[index])
        print(flattened_df['path'].iloc[index])
        print(flattened_df['distances'].iloc[index])
    else:
        print(f"Index {index} not found in DataFrame")
```

## すべてを統合する

最後のステップは、LLM を組み込んでデータに根拠のある応答を返すようにすることです。実装例は次の通りです：

```python
user_input = "what is a perceptron?"

def chatbot(user_input):
    # Convert the question to a query vector
    query_vector = create_embeddings(user_input)

    # Find the most similar documents
    distances, indices = nbrs.kneighbors([query_vector])

    # add documents to query  to provide context
    history = []
    for index in indices[0]:
        history.append(flattened_df['chunks'].iloc[index])

    # combine the history and the user input
    history.append(user_input)

    # create a message object
    messages=[
        {"role": "system", "content": "You are an AI assistant that helps with AI questions."},
        {"role": "user", "content": history[-1]}
    ]

    # use chat completion to generate a response
    response = openai.chat.completions.create(
        model="gpt-4",
        temperature=0.7,
        max_tokens=800,
        messages=messages
    )

    return response.choices[0].message

chatbot(user_input)
```

## アプリケーションの評価

### 評価指標

- 応答の品質：自然で流暢か、人間らしいか
- データに基づいているか（Groundedness）：応答が提供された文書から導かれているか
- 関連性：応答が質問に一致し関連しているか
- 流暢さ：文法的に意味が通っているか

## RAG とベクトルデータベースのユースケース

関数呼び出し（function calls）がアプリを改善するようなユースケースは多くあります：

- Q&A：社内データをグラウンドしたチャットを社員の問い合わせ対応に利用
- レコメンデーションシステム：映画やレストランなど、類似性に基づくマッチングを行う
- チャットボットサービス：チャット履歴を保存し、ユーザーデータに基づいて会話をパーソナライズ
- 画像埋め込みによる画像検索：画像認識や異常検出に有用

## まとめ

このレッスンでは、アプリにデータを追加する方法、ユーザーのクエリ、出力に至る一連の RAG の基本を扱いました。RAG の作成を簡素化するために、Semantic Kernel、LangChain、Autogen などのフレームワークを活用できます。

## 課題

Retrieval Augmented Generation（RAG）の学習を続けるために、次の課題を試してください：

- お好みのフレームワークを使ってアプリのフロントエンドを作成する
- LangChain または Semantic Kernel といったフレームワークを利用し、アプリケーションを再構築する

レッスン修了、おめでとうございます 👏。

## 学びはここで止まりません、旅を続けましょう

このレッスンを終えたら、[Generative AI Learning コレクション](https://aka.ms/genai-collection?WT.mc_id=academic-105485-koreyst) をチェックして、さらなる学習を続けてください！
