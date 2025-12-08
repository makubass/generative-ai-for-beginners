# 高度なプロンプトの作成

[![Creating Advanced Prompts](./images/05-lesson-banner.png?WT.mc_id=academic-105485-koreyst)](https://youtu.be/BAjzkaCdRok?si=NmUIyRf7-cDgbjtt)

前の章での学習内容をおさらいしましょう：

> プロンプト**エンジニアリング**は、より有用な指示やコンテキストを提供することで**モデルをより関連性の高い応答へ導く**プロセスです。

プロンプトを書く際には2つのステップがあります。プロンプトを構成する際に関連するコンテキストを提供することと、プロンプトを段階的に改善する**最適化**です。

これまで基本的なプロンプトの書き方について理解してきましたが、さらに深く掘り下げる必要があります。本章では、様々なプロンプトを試すことから、なぜあるプロンプトが別のプロンプトより優れているのかを理解することへと進んでいきます。あらゆる LLM に適用できる基本的な技法に従ってプロンプトを構成する方法を学びます。

## はじめに

本章では、以下のトピックをカバーします：

- プロンプトに異なる技法を適用することでプロンプトエンジニアリング知識を広げる
- 出力を変動させるようにプロンプトを設定する

## 学習目標

このレッスンを完了した後、以下ができるようになります：

- プロンプトの成果を向上させるプロンプトエンジニアリング技法を適用できる
- 変動的または決定論的なプロンプティングを実行できる

## プロンプトエンジニアリング

プロンプトエンジニアリングは、望ましい結果を生み出すプロンプトを作成するプロセスです。プロンプトエンジニアリングはテキストプロンプトの作成以上の内容があります。プロンプトエンジニアリングはエンジニアリング規律ではなく、望ましい結果を得るために適用できる技法の集合です。

### プロンプトの例

次のような基本的なプロンプトを考えてみましょう：

> 地理に関する 10 個の問題を生成してください。

このプロンプトでは、異なるプロンプト技法のセットを実際に適用しています。

分解してみましょう。

- **コンテキスト**：「地理」についてであることを指定します。
- **出力を制限する**：10 個以下の問題を望んでいます。

### 単純なプロンプティングの制限

望ましい結果が得られるかどうかは保証されません。問題は生成されますが、地理は大きなトピックであり、次の理由で望ましい結果が得られない可能性があります：

- **大きなトピック**：国、首都、川など何についてのものになるか分かりません。
- **フォーマット**：問題を特定の方法でフォーマットしたい場合はどうしますか？

ご覧のとおり、プロンプト作成時に考慮すべきことはたくさんあります。

これまで単純なプロンプト例を見てきましたが、生成AI は様々な役割と業界の人々を支援するために、より多くのことが可能です。次は基本的な技法を探索してみましょう。

### プロンプティング技法

まず、プロンプティングは LLM の**新興**特性である、つまりモデルに組み込まれた機能ではなく、モデルを使用するにつれて発見するもの、であることを理解する必要があります。

LLM にプロンプトするために使用できるいくつかの基本的な技法があります。それらを探索してみましょう。

- **ゼロショット プロンプティング**：最も基本的なプロンプティング形式です。単一のプロンプトで、学習データのみに基づいて LLM からの応答を要求します。
- **フューショット プロンプティング**：このプロンプティングの種類は、依頼できる 1 つ以上の例を提供することで LLM を導きます。
- **思考の連鎖（Chain-of-thought）**：このプロンプティングの種類は、問題を段階に分割する方法を LLM に指示します。
- **生成知識（Generated knowledge）**：プロンプトの応答を改善するために、プロンプトに追加の生成事実または知識を提供できます。
- **最小から最大（Least to most）**：思考の連鎖と同様に、この技法は問題を一連の段階に分割し、その後それらの段階を順序通りに実行するよう依頼することについてです。
- **自己改善（Self-refine）**：この技法は LLM の出力を批評し、その後それを改善するよう依頼することについてです。
- **ソクラテス的プロンプティング（Maieutic prompting）**：ここで必要なのは LLM の答えが正しいことを確認し、答えの様々な部分を説明するよう依頼することです。これは自己改善の一形式です。

### ゼロショット プロンプティング

このスタイルのプロンプティングは非常に単純で、単一のプロンプトで構成されます。この技法は、LLM について学習を始めるにつれて、おそらくあなたが使用しているものです。以下は例です：

- プロンプト：「代数とは何ですか？」
- 応答：「代数は数学の分野で、数学的シンボルおよびこれらのシンボルを操作するための規則を研究しています。」

### フューショット プロンプティング

このスタイルのプロンプティングは、リクエストとともにいくつかの例を提供することでモデルを支援します。単一のプロンプトに追加のタスク固有データが含まれています。以下は例です：

- プロンプト：「シェークスピアのスタイルで詩を書いてください。シェークスピアのソネットの例をいくつかご紹介します：
  ソネット 18：「Shall I compare thee to a summer's day? Thou art more lovely and more temperate...」
  ソネット 116：「Let me not to the marriage of true minds Admit impediments. Love is not love Which alters when it alteration finds...」
  ソネット 132：「Thine eyes I love, and they, as pitying me, Knowing thy heart torment me with disdain,...」
  月の美しさについてのソネットを書いてください。」
- 応答：「Upon the sky, the moon doth softly gleam, In silv'ry light that casts its gentle grace,...」

例は LLM に、望ましい出力のコンテキスト、フォーマット、またはスタイルを提供します。これにより、モデルが特定のタスクを理解し、より正確で関連性の高い応答を生成するのに役立ちます。

### 思考の連鎖

思考の連鎖は非常に興味深い技法です。LLM を一連の段階を通じて進めることについてです。考えは、LLM が何をするかを理解する方法で LLM に指示することです。以下の例は、思考の連鎖ありとなしの両方を示しています：

- プロンプト：「アリスは 5 つのリンゴを持ち、3 つのリンゴを投げ、ボブに 2 つを与え、ボブが 1 つ返します。アリスはいくつのリンゴを持っていますか？」
- 応答：5

LLM は 5 で答えますが、これは正しくありません。計算を考えると、正しい答えは 1 個のリンゴです（5-3-2+1=1）。

では、LLM にこれを正しく行うようどのように教えることができるでしょうか？

思考の連鎖を試してみましょう。思考の連鎖を適用することは以下を意味します：

1. LLM に同様の例を与えます。
2. 計算を表示し、正しく計算する方法を示します。
3. 元のプロンプトを提供します。

方法は以下の通りです：

- プロンプト：「リサは 7 つのリンゴを持ち、1 つのリンゴを投げ、バートに 4 つのリンゴを与え、バートが 1 つ返します：
  7-1=6
  6-4=2
  2+1=3
  アリスは 5 つのリンゴを持ち、3 つのリンゴを投げ、ボブに 2 つを与え、ボブが 1 つ返します。アリスはいくつのリンゴを持っていますか？」
- 応答：1

別の例、計算、その後元のプロンプトとともに、実質的に長いプロンプトを書く方法に注目し、正しい答え 1 に到達します。

ご覧のとおり、思考の連鎖は非常に強力な技法です。

### 生成知識

プロンプトを構成する場合、自社のデータを使用してそうしたいことがよくあります。プロンプトの一部は会社のものであり、他の部分は実際に関心のあるプロンプトである必要があります。

例えば、保険事業に携わっている場合、プロンプトは次のようになります：

```text
{{company}}：{{company_name}}
{{products}}：
{{products_list}}
次の予算と要件を考慮して、保険を提案してください：
予算：{{budget}}
要件：{{requirements}}
```

上記は、テンプレートを使用してプロンプトがどのように構成されるかを示しています。テンプレート内には、`{{variable}}` で示される多くの変数があり、会社 API から実際の値で置き換えられます。

変数が会社からのコンテンツで置き換えられた後、プロンプトがどのように見えるかの例を示します：

```text
保険会社：ACME Insurance
保険商品（月額コスト）：
- 車、安い、500 米ドル
- 車、高い、1100 米ドル
- 家、安い、600 米ドル
- 家、高い、1200 米ドル
- 生命、安い、100 米ドル

次の予算と要件を考慮して、保険を提案してください：
予算：1000 米ドル
要件：自動車、住宅、生命保険
```

このプロンプトを LLM で実行すると、次のような応答が生成されます：

```output
予算と要件を踏まえて、ACME Insurance の次の保険パッケージをお勧めします：
- 車、安い、500 米ドル
- 家、安い、600 米ドル
- 生命、安い、100 米ドル
合計コスト：1,200 米ドル
```

ご覧のとおり、生命保険も提案していますが、これは提案すべきではありません。この結果は、プロンプトを変更して何が許可されるかについてより明確にするためにプロンプトを最適化する必要があることを示しています。何度か試行錯誤した後、次のプロンプトに到達します：

```text
保険会社：ACME Insurance
保険商品（月額コスト）：
- type：車、安い、コスト：500 米ドル
- type：車、高い、コスト：1100 米ドル
- type：家、安い、コスト：600 米ドル
- type：家、高い、コスト：1200 米ドル
- type：生命、安い、コスト：100 米ドル

次の予算と要件を考慮して、保険を提案してください：
予算：1000 米ドル、選択肢を次の種類に限定：車、家
```

_type_ と _cost_ を追加し、キーワード _restrict_ を使用することで、LLM が私たちが望むことを理解するのに役立つ方法に注意してください。

これで次の応答が得られます：

```output
予算と要件を踏まえて、月額 500 米ドルの「車、安い」保険商品をお勧めします。
```

この例の要点は、基本的な技法（生成知識）を使用している場合でも、ほとんどの場合、望ましい結果を得るためにプロンプトを最適化する必要があることを示すことでした。

### 最小から最大

最小から最大へのプロンプティングの考え方は、大きな問題をサブプロブレムに分割することです。こうすることで、LLM が大きな問題を「克服」する方法をガイドするのに役立てます。良い例は、データサイエンスの場合です。LLM に次のように問題を分割するよう求めることができます：

> プロンプト：データサイエンスを 5 ステップで実行する方法は？

AI アシスタントが以下で答えます：

1. データを収集する
2. データをクリーニングする
3. データを分析する
4. データをプロットする
5. データをプレゼンテーションする

### 自己改善、結果を批評する

生成AI と LLM では、出力を信頼することはできません。検証する必要があります。結局のところ、LLM は次の最も可能性が高いことを言っているだけであり、何が正しいかではなく、提示しているだけです。したがって、LLM に自分自身を批評するよう依頼することが良い考えであり、これにより自己改善技法へ導かれます。

動作方法は次のステップに従うことです：

1. LLM に問題を解決するよう依頼する初期プロンプト
2. LLM が応答する
3. 回答を批評し、AI に改善するよう依頼する
4. LLM が再び応答します。今回は批評を考慮し、それが考え出したソリューションを提案します

このプロセスは何度でも繰り返すことができます。

この技法を使用した例は以下の通りです：

> プロンプト：「products と customers のルートを持つ Python Web API を作成してください」

AI の回答：

```python
import flask
app = Flask(__name__)

@app.route('/products')
def products():
    return 'Products'

@app.route('/customers')
def customers():
    return 'Customers'
```

> プロンプト：上記のコードの 3 つの改善を提案してください

AI の回答：

```text
確かに、コードを改善するための 3 つの提案があります：

正しいモジュールをインポートする：最初の行は flask ではなく Flask をインポートする必要があります。正しいインポートステートメントは from flask import Flask である必要があります。

メイン関数を追加する：ファイルの最後に if __name__ == '__main__': ブロックを追加して、ファイルが実行されるときにアプリケーションを実行します。

f-strings を使用する：ルート関数の戻りステートメントをフォーマットするために f-strings を使用します。例えば、return 'Products' は return f'This is the {request.path} page' に変更できます。
```

```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/products')
def products():
    return f'This is the {request.path} page'

@app.route('/customers')
def customers():
    return f'This is the {request.path} page'

if __name__ == '__main__':
    app.run()
```

ご覧のとおり、上記の AI の応答は最初の提案されたコードを最初の答えを批評することによって改善しています。

### ソクラテス的プロンプティング

ソクラテス的プロンプティングは自己改善に似ていますが、LLM に自分自身を説明するよう求めることに関するものです。目標は LLM の出力の矛盾を減らし、正しい答えに到達することを確認することです。従うべきワークフローは以下の通りです：

1. LLM に質問に答えるよう依頼する
2. 答えの各部分について、LLM に詳細に説明するよう依頼する。
3. 矛盾がある場合は、矛盾している部分を破棄します。

すべての部分を通い、答えに満足するまで 2 と 3 を繰り返します。

プロンプトの例は以下の通りです：

> プロンプト：パンデミックを軽減するための危機計画を 5 つのステップで作成するにはどうすればよいですか？
> LLM の回答：

```text
1. リスクを特定する
2. ステークホルダーを特定する
3. リソースを特定する
4. アクションを特定する
5. コミュニケーションチャネルを特定する
```

5 つのステップが特定されていますが、これが正しいかどうかを判定できるでしょうか？各ステップを説明するよう LLM に依頼してみましょう：

> プロンプト：最初のステップについて詳しく説明してください。パンデミックに関するリスクについて詳しく教えてください。

```text
リスクは次の通りです：

- 命の喪失
- ビジネスの喪失
- 評判の喪失
- 顧客の喪失
- 従業員の喪失
```

この時点で、LLM が正しいことを確認したいので、最初のリスクについてより詳細に説明するよう依頼し、上記の答えと一致していることを願っています：

> プロンプト：パンデミックでは、最大のリスクは何で、その理由は？

```text
最大のリスクは命の喪失です。これが保護する最も重要なことだからです。
```

> パンデミックで最大の 2 つのリスクは何ですか？

```text
最大の 2 つのリスクは、命の喪失とビジネスの喪失です。
```

この時点で、LLM は一貫性があり、最大 2 つのリスクとして「命」と「ビジネス」を言及しています。これで次のステップに進むことができ、かなり自信を持つことができます。ただし、LLM を盲目的に信頼すべきではなく、常に出力を検証する必要があります。

## 出力を変動させる

LLM は本質的に非決定論的であり、同じプロンプトを実行するたびに異なる結果が得られることを意味しています。例えば、次のプロンプトを試してください：

> 「Python Web API のコードを生成してください」

```python
# Import necessary modules
import flask
from flask import request, jsonify

# Create a Flask app
app = flask.Flask(__name__)

# Create a route for the API
@app.route('/api', methods=['GET'])
def api():
    # Get the data from the request
    data = request.args

    # Process the data
    result = process_data(data)

    # Return the result as JSON
    return jsonify(result)

# Function to process the data
def process_data(data):
    # Do something with the data
    result = data + 1

    # Return the result
    return result

# Run the app
if __name__ == '__main__':
    app.run()

```

同じプロンプトを再度実行すると、わずかに異なる応答が生成されます：

```python
#import necessary packages
import flask
from flask import request, jsonify

#create the Flask application
app = flask.Flask(__name__)
app.config["DEBUG"] = True

#create a list of books
books = [
    {'id': 0,
     'title': 'A Fire Upon the Deep',
     'author': 'Vernor Vinge',
     'first_sentence': 'The coldsleep itself was dreamless.',
     'year_published': '1992'},
    {'id': 1,
     'title': 'The Ones Who Walk Away From Omelas',
     'author': 'Ursula K. Le Guin',
     'first_sentence': 'With a clamor of bells that set the swallows soaring, the Festival of Summer came to the city Omelas, bright-towered by the sea.',
     'published': '1973'},
    {'id': 2,
     'title': 'Dhalgren',
     'author': 'Samuel R. Delany',
     'first_sentence': 'to wound the autumnal city.',
     'published': '1975'}
]

#create an endpoint for the API
@app.route('/', methods=['GET'])
def home():
    return '''<h1>Books API</h1>
<p>A prototype API for retrieving books.</p>'''

#create an endpoint to return all books
@app.route('/api/v1/resources/books/all', methods=['GET'])
def api_all():
    return jsonify(books)

#create an endpoint to return a single book
@app.route('/api/v1/resources/books', methods=['GET'])
def api_id():
    #check if an ID was provided as part of the URL
    #if ID is provided, assign it to a variable
    #if no ID is provided, display an error
    if 'id' in request.args:
        id = int(request.args['id'])
    else:
        return "Error: No id field provided. Please specify an id."

    #create an empty list for our results
    results = []

    #loop through the data and match results that fit the requested ID
    #IDs are unique, but other fields might return many results
    for book in books:
        if book['id'] == id:
            results.append(book)

    #use the jsonify function from Flask to convert our list of
    #Python dictionaries to the JSON format
    return jsonify(results)

app.run()
```

> では、変動的な出力は問題でしょうか？

何をしようとしているかによって異なります。特定の応答が必要な場合は、問題です。「地理に関する任意の 3 つの問題を生成してください」のような変動的な出力でも構わない場合、問題ではありません。

### 温度を使用して出力を変動させる

それでは、出力をより予測可能にする、つまりより決定論的にしたいと決めました。どうすればよいでしょうか？

温度は 0 から 1 の値で、0 が最も決定論的で、1 が最も変動的です。デフォルト値は 0.7 です。温度が 0.1 に設定された同じプロンプトの 2 つの実行で何が起こるかを見てみましょう：

> 「Python Web API のコードを生成してください」

```python
#import necessary libraries
import flask
from flask import request, jsonify

#create an instance of the Flask class
app = flask.Flask(__name__)

#create an endpoint for the API
@app.route('/api/v1/endpoint', methods=['GET'])
def api_endpoint():
    #get the request data
    data = request.get_json()

    #process the data
    result = process_data(data)

    #return the result
    return jsonify(result)

#function to process the data
def process_data(data):
    #process the data
    result = {'result': 'success'}

    #return the result
    return result

#run the Flask app
if __name__ == '__main__':
    app.run()

```

プロンプトを再度実行すると、次の結果が得られます：

```python
#import necessary libraries
import flask
from flask import request, jsonify

#create a Flask app
app = flask.Flask(__name__)

#create a route for the API
@app.route('/api', methods=['GET'])
def api_call():
    #get the data from the request
    data = request.get_json()

    #process the data
    result = process_data(data)

    #return the result
    return jsonify(result)

#define the process_data function
def process_data(data):
    #do something with the data
    result = data + 1

    #return the result
    return result

#run the app
if __name__ == '__main__':
    app.run()

```

これら 2 つの出力の間に非常に小さな違いがあります。今度は逆をやってみましょう。温度を 0.9 に設定してみましょう：

```python
# Import necessary libraries
import flask
from flask import request, jsonify

# Create a Flask app
app = flask.Flask(__name__)

# Create a route for the API
@app.route('/api', methods=['GET'])
def api_call():
    # Get the data from the request
    data = request.args

    # Process the data
    result = process_data(data)

    # Return the result
    return jsonify(result)

# Function to process the data
def process_data(data):
    # Do something with the data
    result = data + 1

    # Return the result
    return result

# Run the app
if __name__ == '__main__':
    app.run()

```

温度値として 0.9 で 2 番目の試みを行います：

```python
import flask
from flask import request, jsonify

# create the Flask app
app = flask.Flask(__name__)
app.config['DEBUG'] = True

# create some test data
books = [
    {'id': 0, 'title': 'A Fire Upon The Deep', 'author': 'Vernor Vinge', 'first_sentence': 'The coldsleep itself was dreamless.', 'year_published': '1992'},
    {'id': 1, 'title': 'The Ones Who Walk Away From Omelas', 'author': 'Ursula K. Le Guin', 'first_sentence': 'With a clamor of bells that set the swallows soaring, the Festival of Summer came to the city Omelas, bright-towered by the sea.', 'published': '1973'},
    {'id': 2, 'title': 'Dhalgren', 'author': 'Samuel R. Delany', 'first_sentence': 'to wound the autumnal city.', 'published': '1975'}
]

# create an endpoint
@app.route('/', methods=['GET'])
def home():
    return '''<h1>Welcome to our book API!</h1>'''

@app.route('/api/v1/resources/books
```

ご覧のとおり、結果はそれ以上に多様です。

> 注：出力を変動させるために変更できるパラメータはもっと多くあります。例えば top-k、top-p、繰り返しペナルティ、長さペナルティ、多様性ペナルティなどがありますが、これらはこのカリキュラムの範囲外です。

## ベストプラクティス

プロンプティングをより多く使用するにつれて、望みのものを得るために適用できる多くの実践があります。独自のスタイルを見つけるでしょう。

カバーした技法に加えて、LLM にプロンプトするときに考慮するべきいくつかのベストプラクティスがあります。

考慮すべきベストプラクティスは以下の通りです：

- **コンテキストを指定する**。コンテキストは重要です。ドメイン、トピックなどを指定できるほど、良いです。
- 出力を制限します。特定の数のアイテムまたは特定の長さが必要な場合は、指定してください。
- **「何を」と「どのように」を両方指定する**。たとえば「Create a Python Web API with routes products and customers, divide it into 3 files」のように、何をしたいのか、どのようにしたいのかの両方を言及することを忘れずに。
- **テンプレートを使用する**。多くの場合、自社のデータを使用してプロンプトを充実させたいのです。この目的でテンプレートを使用してください。テンプレートには、実際のデータで置き換える変数を設定できます。
- **正しくスペルしてください**。LLM は正しい応答を提供する場合がありますが、正しくスペルする場合、より良い応答が得られます。

## 課題

次は、Flask を使用して単純な API を構築する方法を示す Python コードです：

```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/')
def hello():
    name = request.args.get('name', 'World')
    return f'Hello, {name}!'

if __name__ == '__main__':
    app.run()
```

GitHub Copilot または ChatGPT のような AI アシスタントを使用し、「自己改善」技法を適用してコードを改善してください。

## ソリューション

課題をコードに適切なプロンプトを追加して解いてください。

> [!TIP]
> それを改善するよう依頼するプロンプトを文言する。改善の数を制限することが良い考えです。また、特定の方法で改善するよう依頼することもできます。例えば、アーキテクチャ、パフォーマンス、セキュリティなど。

[ソリューション](./python/aoai-solution.py?WT.mc_id=academic-105485-koreyst)

## 知識確認

思考の連鎖プロンプティングをなぜ使用するのでしょうか？1 つの正しい応答と 2 つの不正解を示してください。

1. LLM に問題を解決する方法を教えるため。
2. B、LLM にコードのエラーを見つけるように教えるため。
3. C、LLM に異なるソリューションを思いつくように指示するため。

A：1 番目です。思考の連鎖は、LLM に一連のステップと同様の問題、およびそれらがどのように解決されたかを提供することで、問題を解決する方法を教えることについてです。

## 🚀 チャレンジ

課題では自己改善技法を使用しました。構築したプログラムを取り上げ、適用したい改善を検討してください。次に自己改善技法を使用して提案された変更を適用してください。結果はどう思いますか？より良い、または悪い？

## よくできました！学習を続けましょう

このレッスンを完了したら、[生成AI学習コレクション](https://aka.ms/genai-collection?WT.mc_id=academic-105485-koreyst) をチェックして、生成AI の知識を引き続き深めてください！

次のレッスン 6 に進んで、プロンプトエンジニアリング知識を活用して、[テキスト生成アプリを構築](../06-text-generation-apps/README.md?WT.mc_id=academic-105485-koreyst) しましょう。
