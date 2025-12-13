# 関数呼び出しの統合

[![関数呼び出しの統合](./images/11-lesson-banner.png?WT.mc_id=academic-105485-koreyst)](https://youtu.be/DgUdCLX8qYQ?si=f1ouQU5HQx6F8Gl2)

これまでのレッスンで多くのことを学んできましたが、さらに改善できる点があります。たとえば、下流処理で扱いやすくするために応答フォーマットをより一貫して得る方法や、外部データを組み合わせてアプリケーションを強化する方法などです。

本章では、こうした問題を解決するためのアプローチを扱います。

## 導入

このレッスンで扱う内容:

- 関数呼び出し（Function Calling）とは何か、そのユースケース
- Azure OpenAI を使った関数呼び出しの作成
- 関数呼び出しをアプリケーションに統合する方法

## 学習目標

このレッスンの終了時には、以下ができるようになります:

- 関数呼び出しを使う目的を説明できる
- Azure OpenAI サービスで関数呼び出しを設定できる
- アプリケーションのユースケースに合わせた効果的な関数呼び出しを設計できる

## シナリオ：関数でチャットボットを改善する

このレッスンでは、教育系スタートアップ向けに、チャットボットを使って技術コースを探す機能を実装します。ユーザーのスキルレベルや役割、関心のある技術に合ったコースを推奨します。

このシナリオを実現するために以下を組み合わせます:

- `Azure OpenAI`：ユーザーにチャット体験を提供するため
- `Microsoft Learn Catalog API`：ユーザーのリクエストに基づいてコースを検索するため
- `Function Calling`：ユーザーのクエリを関数に渡し、API リクエストを行うため

まずは、なぜ関数呼び出しを使用するのかを見てみましょう。

## 関数呼び出しを使う理由

従来、LLM の応答は構造化されておらず一貫性がありませんでした。そのため、開発者は応答の様々なパターンを扱うための複雑なバリデーションコードを書く必要がありました。たとえば「ストックホルムの現在の天気は？」のような問いに対して、モデルは学習時点での情報に制限されるため、常に最新の応答を返せるわけではありません。

関数呼び出しは Azure OpenAI サービスの機能で、以下のような制限を克服します:

- **一貫した応答フォーマット**：応答フォーマットを制御することで、下流システムへの連携が容易になります。
- **外部データの活用**：アプリケーションの他のデータソースをチャット文脈で利用できるようにします。

## シナリオで問題を説明する

このシナリオを実行したい場合は、同梱の [ノートブック](./python/aoai-assignment.ipynb?WT.mc_id=academic-105485-koreyst) を使うことをお勧めします。以下は、関数が問題解決に役立つ例を示すための説明です。

ここでは、生徒データのデータベースを作成し、適切なコースを提案する例を考えます。以下には、ほぼ同じ情報を含む2つの生徒の説明が示されています。

1. Azure OpenAI リソースへの接続を作成する:

   ```python
   import os
   import json
   from openai import AzureOpenAI
   from dotenv import load_dotenv
   load_dotenv()

   client = AzureOpenAI(
   api_key=os.environ['AZURE_OPENAI_API_KEY'],  # 既定値でもあり、省略可能
   api_version = "2023-07-01-preview"
   )

   deployment=os.environ['AZURE_OPENAI_DEPLOYMENT']
   ```

   以下は、`api_type`、`api_base`、`api_version`、`api_key` を設定して Azure OpenAI への接続を構成するための Python コードです。

2. 変数 `student_1_description` と `student_2_description` を使って、2 件の生徒説明文を作成します。

   ```python
   student_1_description="Emily Johnson is a sophomore majoring in computer science at Duke University. She has a 3.7 GPA. Emily is an active member of the university's Chess Club and Debate Team. She hopes to pursue a career in software engineering after graduating."

   student_2_description = "Michael Lee is a sophomore majoring in computer science at Stanford University. He has a 3.8 GPA. Michael is known for his programming skills and is an active member of the university's Robotics Club. He hopes to pursue a career in artificial intelligence after finishing his studies."
   ```

   上記の生徒説明文を LLM に送ってデータを解析したいと考えています。このデータは後で API に送信したり、データベースに保存してアプリケーションで利用できます。

3. LLM に抽出してほしい情報を指示する同一のプロンプトを 2 つ作成します:

   ```python
   prompt1 = f'''
   Please extract the following information from the given text and return it as a JSON object:

   name
   major
   school
   grades
   club

   This is the body of text to extract the information from:
   {student_1_description}
   '''

   prompt2 = f'''
   Please extract the following information from the given text and return it as a JSON object:

   name
   major
   school
   grades
   club

   This is the body of text to extract the information from:
   {student_2_description}
   '''
   ```

   The above prompts instruct the LLM to extract information and return the response in JSON format.

4. プロンプトと Azure OpenAI への接続を設定したら、`openai.ChatCompletion` を使って LLM にプロンプトを送信します。プロンプトは `messages` 変数に保存し、`user` ロールを割り当てます。これはチャットボットにユーザーから投稿されたメッセージを模したものです。

   ```python
   # response from prompt one
   openai_response1 = client.chat.completions.create(
   model=deployment,
   messages = [{'role': 'user', 'content': prompt1}]
   )
   openai_response1.choices[0].message.content

   # response from prompt two
   openai_response2 = client.chat.completions.create(
   model=deployment,
   messages = [{'role': 'user', 'content': prompt2}]
   )
   openai_response2.choices[0].message.content
   ```

ここで両方のリクエストを LLM に送信し、`openai_response1['choices'][0]['message']['content']` のように応答を確認できます。

5. 最後に、`json.loads` を呼び出して応答を JSON に変換します:

   ```python
   # Loading the response as a JSON object
   json_response1 = json.loads(openai_response1.choices[0].message.content)
   json_response1
   ```

   レスポンス 1:

    ```json
    {
       "name": "Emily Johnson",
       "major": "computer science",
       "school": "Duke University",
       "grades": "3.7",
       "club": "Chess Club"
    }
    ```

   レスポンス 2:

    ```json
    {
       "name": "Michael Lee",
       "major": "computer science",
       "school": "Stanford University",
       "grades": "3.8 GPA",
       "club": "Robotics Club"
    }
    ```

   同じプロンプトで説明が類似しているにもかかわらず、`grades` プロパティの値が `3.7` のように数値のみで返ってくる場合と `3.7 GPA` のように単位付きで返ってくる場合など、フォーマットに差が出ることがあります。

   この結果は、LLM が非構造化テキストを受け取り、非構造化の応答を返すために発生します。データを格納したり利用したりする際に何を期待すべきかを明確にするために、構造化されたフォーマットが必要です。

ではフォーマットの問題をどのように解決するか？関数呼び出しを使えば、構造化されたデータを受け取れるようになります。関数呼び出しを使う際、LLM は実際に関数を実行するわけではありません。代わりに、LLM が応答で従うべき構造を定義し、その構造化された応答を元にアプリケーション側でどの関数を実行するかを判断します。

![関数のフロー](./images/Function-Flow.png?WT.mc_id=academic-105485-koreyst)

関数から返された結果を LLM に送り返し、LLM が自然言語でユーザーへの回答を生成するようにできます。

## 関数呼び出しのユースケース

関数呼び出しによってアプリを改善できるユースケースは多数あります。いくつかの例を挙げます:

- **外部ツールの呼び出し**: チャットボットはユーザーの質問に答えるのが得意です。関数呼び出しを使えば、チャットボットがユーザーのメッセージを基に特定のタスクを実行できます。たとえば「この件で担当教員にメールを送ってほしい」といった要求は `send_email(to: string, body: string)` のような関数呼び出しで実現できます。

- **API やデータベースクエリの生成**: ユーザーの自然言語をフォーマット済みのクエリや API リクエストに変換できます。例として、教師が「前回の課題を提出した学生は誰か？」と尋ねると、`get_completed(student_name: string, assignment: int, current_status: string)` のような関数呼び出しに変換できます。

- **構造化データ作成**: テキストや CSV を取り込み、重要情報を抽出して構造化データに変換できます。例えば、平和協定に関する Wikipedia 記事から重要点を抽出して AI フラッシュカードを生成する場合は、`get_important_facts(agreement_name: string, date_signed: string, parties_involved: list)` のような関数呼び出しが有用です。

## 最初の関数呼び出しを作成する

関数呼び出しを作成する過程は主に 3 つのステップに分かれます:

1. 関数のリストとユーザーメッセージを与えて Chat Completions API を呼び出す（Calling）。
2. モデルの応答を読み取り、アクション（関数または API 呼び出し）を実行する（Reading）。
3. 関数の結果を用いて再度 Chat Completions API を呼び出し、ユーザー向けの自然言語応答を生成する（Making）。

![LLM フロー](./images/LLM-Flow.png?WT.mc_id=academic-105485-koreyst)

### ステップ 1 - メッセージの作成

最初のステップはユーザーメッセージを作成することです。これはテキスト入力の値を動的に設定しても良いですし、ここで直接値を設定しても構いません。Chat Completions API を使う場合、メッセージには `role` と `content` を定義する必要があります。

`role` は `system`（ルール作成者）、`assistant`（モデル）、`user`（エンドユーザー）のいずれかです。関数呼び出しの例では `user` を使用し、次のような質問にします。

```python
messages= [ {"role": "user", "content": "Find me a good course for a beginner student to learn Azure."} ]
```

異なるロールを割り当てることで、システムからのメッセージかユーザーからかが明確になり、LLM が会話履歴に基づいて応答を生成できます。

### ステップ 2 - 関数の定義

次に、関数とそのパラメータを定義します。ここでは `search_courses` という 1 つの関数を使いますが、複数の関数を定義することも可能です。

重要: 関数の定義は LLM のシステムメッセージに含まれるため、トークン数にカウントされます。

以下では、関数を配列として定義します。各要素は `name`, `description`, `parameters` を持つ関数定義です:

```python
functions = [
   {
      "name":"search_courses",
      "description":"Retrieves courses from the search index based on the parameters provided",
      "parameters":{
         "type":"object",
         "properties":{
            "role":{
               "type":"string",
               "description":"The role of the learner (i.e. developer, data scientist, student, etc.)"
            },
            "product":{
               "type":"string",
               "description":"The product that the lesson is covering (i.e. Azure, Power BI, etc.)"
            },
            "level":{
               "type":"string",
               "description":"The level of experience the learner has prior to taking the course (i.e. beginner, intermediate, advanced)"
            }
         },
         "required":[
            "role"
         ]
      }
   }
]
```

以下に各プロパティの詳細を説明します:

 - `name` - 呼び出したい関数の名前です。
 - `description` - 関数の動作についての説明です。ここは明確で具体的であることが重要です。
 - `parameters` - モデルの応答に含めたい値とその形式の一覧です。`parameters` は以下のプロパティを持ちます:
    1. `type` - プロパティのデータ型。
    2. `properties` - モデルが応答で使用する特定のプロパティ一覧。
         1. `name` - プロパティのキー名（例: `product`）。
         2. `type` - そのプロパティのデータ型（例: `string`）。
         3. `description` - 個別プロパティの説明。

オプションとして `required` を指定でき、関数呼び出しを完了するために必須のプロパティを定義できます。

### ステップ 3 - 関数呼び出しを行う

関数を定義したら、その関数情報を Chat Completion API のリクエストに含めます。これは `functions=functions` を追加することで行います。

また、`function_call` を `auto` に設定すると、ユーザーのメッセージに基づいて LLM がどの関数を呼び出すべきかを自動的に選択します。以下のコードは `ChatCompletion.create` を呼び出し、`functions=functions` と `function_call="auto"` を指定して LLM に関数呼び出しの判断を任せています。

```python
response = client.chat.completions.create(model=deployment,
                                        messages=messages,
                                        functions=functions,
                                        function_call="auto")

print(response.choices[0].message)
```

返ってくる応答は次のようになります:

```json
{
   "role": "assistant",
   "function_call": {
      "name": "search_courses",
      "arguments": "{\n  \"role\": \"student\",\n  \"product\": \"Azure\",\n  \"level\": \"beginner\"\n}"
   }
}
```

ここでは `search_courses` が呼び出され、どの引数が渡されたか（`arguments` プロパティ）を確認できます。

LLM は `messages` パラメータに与えられた値から必要な情報（role、product、level）を抽出して、関数の引数に適合させています。以下が `messages` の値です。

```python
messages= [ {"role": "user", "content": "Find me a good course for a beginner student to learn Azure."} ]
```

この例では、`student`、`Azure`、`beginner` が `messages` から抽出され、関数入力として設定されています。このように関数呼び出しを使うことで、プロンプトから情報を抽出して構造化し、再利用可能な機能を実現できます。

次に、この仕組みをアプリケーションにどのように組み込むかを見ていきます。

## アプリケーションへの関数呼び出しの統合

LLM からの構造化された応答を確認したら、それを実際のアプリケーションに組み込みます。

### フローの管理

アプリケーションへの統合手順は以下の通りです:

1. まず、OpenAI サービスへ呼び出しを行い、応答メッセージを `response_message` 変数へ格納します。

   ```python
   response_message = response.choices[0].message
   ```

2. 次に、Microsoft Learn API を呼び出してコース一覧を取得する関数を定義します:

   ```python
   import requests

   def search_courses(role, product, level):
     url = "https://learn.microsoft.com/api/catalog/"
     params = {
        "role": role,
        "product": product,
        "level": level
     }
     response = requests.get(url, params=params)
     modules = response.json()["modules"]
     results = []
     for module in modules[:5]:
        title = module["title"]
        url = module["url"]
        results.append({"title": title, "url": url})
     return str(results)
   ```

   ここでは、`functions` で定義した名前に対応する実際の Python 関数を作成しています。また、必要なデータを取得するために実際の外部 API を呼び出しています。この例では Microsoft Learn API を利用してトレーニングモジュールを検索しています。

`functions` と対応する Python 関数を作成したら、LLM の応答をどのように検査して関数を呼び出すかを実装する必要があります。

3. LLM 応答に `function_call` が含まれるかをチェックし、含まれている場合は指摘された関数を呼び出します。次のような手順です:

      ```python
      # Check if the model wants to call a function
      if response_message.function_call.name:
      print("推奨される関数呼び出し:")
      print(response_message.function_call.name)
      print()

      # Call the function.
      function_name = response_message.function_call.name

      available_functions = {
            "search_courses": search_courses,
      }
      function_to_call = available_functions[function_name]

      function_args = json.loads(response_message.function_call.arguments)
      function_response = function_to_call(**function_args)

      print("関数呼び出しの出力:")
      print(function_response)
      print(type(function_response))


      # Add the assistant response and function response to the messages
      messages.append( # adding assistant response to messages
         {
            "role": response_message.role,
            "function_call": {
               "name": function_name,
               "arguments": response_message.function_call.arguments,
            },
            "content": None
         }
      )
      messages.append( # adding function response to messages
         {
            "role": "function",
            "name": function_name,
            "content":function_response,
         }
      )
      ```

   上記では、関数名の抽出、引数の解析、関数呼び出しを行っています:

   ```python
   function_to_call = available_functions[function_name]

   function_args = json.loads(response_message.function_call.arguments)
   function_response = function_to_call(**function_args)
   ```

   以下は実行結果の例です:

    **出力**

    推奨される関数呼び出し:

    ```json
    {
       "name": "search_courses",
       "arguments": "{\n  \"role\": \"student\",\n  \"product\": \"Azure\",\n  \"level\": \"beginner\"\n}"
    }

    関数呼び出しの出力:
    [{'title': 'Describe concepts of cryptography', 'url': 'https://learn.microsoft.com/training/modules/describe-concepts-of-cryptography/?
    WT.mc_id=api_CatalogApi'}, {'title': 'Introduction to audio classification with TensorFlow', 'url': 'https://learn.microsoft.com/en-
    us/training/modules/intro-audio-classification-tensorflow/?WT.mc_id=api_CatalogApi'}, {'title': 'Design a Performant Data Model in Azure SQL
    Database with Azure Data Studio', 'url': 'https://learn.microsoft.com/training/modules/design-a-data-model-with-ads/?
    WT.mc_id=api_CatalogApi'}, {'title': 'Getting started with the Microsoft Cloud Adoption Framework for Azure', 'url':
    'https://learn.microsoft.com/training/modules/cloud-adoption-framework-getting-started/?WT.mc_id=api_CatalogApi'}, {'title': 'Set up the
    Rust development environment', 'url': 'https://learn.microsoft.com/training/modules/rust-set-up-environment/?WT.mc_id=api_CatalogApi'}]
    <class 'str'>
    ```

4. 関数の実行結果を受け取ったら、更新した `messages` を LLM に送信し、自然言語の応答を得ます。

   ```python
   print("次のリクエストのメッセージ:")
   print(messages)
   print()

   second_response = client.chat.completions.create(
      messages=messages,
      model=deployment,
      function_call="auto",
      functions=functions,
      temperature=0
         )  # get a new response from GPT where it can see the function response


   print(second_response.choices[0].message)
   ```

    **出力**

    ```python
    {
       "role": "assistant",
       "content": "I found some good courses for beginner students to learn Azure:\n\n1. [Describe concepts of cryptography] (https://learn.microsoft.com/training/modules/describe-concepts-of-cryptography/?WT.mc_id=api_CatalogApi)\n2. [Introduction to audio classification with TensorFlow](https://learn.microsoft.com/training/modules/intro-audio-classification-tensorflow/?WT.mc_id=api_CatalogApi)\n3. [Design a Performant Data Model in Azure SQL Database with Azure Data Studio](https://learn.microsoft.com/training/modules/design-a-data-model-with-ads/?WT.mc_id=api_CatalogApi)\n4. [Getting started with the Microsoft Cloud Adoption Framework for Azure](https://learn.microsoft.com/training/modules/cloud-adoption-framework-getting-started/?WT.mc_id=api_CatalogApi)\n5. [Set up the Rust development environment](https://learn.microsoft.com/training/modules/rust-set-up-environment/?WT.mc_id=api_CatalogApi)\n\nYou can click on the links to access the courses."
    }

    ```

## 課題

Azure OpenAI の関数呼び出しをさらに学ぶために、以下の課題に挑戦してみてください:

- 学習者がより適切なコースを見つけられるように、関数のパラメータを増やす。
- 学習者の母国語など、追加情報を受け取る別の関数呼び出しを作成する。
- 関数呼び出しや API 呼び出しが適切なコースを返さない場合のエラーハンドリングを実装する。

補足: データの取得方法や利用可能なフィールドについては、[Learn API リファレンス](https://learn.microsoft.com/training/support/catalog-api-developer-reference?WT.mc_id=academic-105485-koreyst) を参照してください。

## お疲れ様でした！学習を続けましょう

このレッスンを修了したら、[Generative AI Learning コレクション](https://aka.ms/genai-collection?WT.mc_id=academic-105485-koreyst) を参照して生成AIの知識をさらに高めましょう。

次は Lesson 12 に進み、[AI アプリケーションの UX 設計](../12-designing-ux-for-ai-applications/README.md?WT.mc_id=academic-105485-koreyst) を学びましょう！
