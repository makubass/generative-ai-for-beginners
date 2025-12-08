# 画像生成アプリケーションの構築

[![Building Image Generation Applications](./images/09-lesson-banner.png?WT.mc_id=academic-105485-koreyst)](https://youtu.be/B5VP0_J7cs8?si=5P3L5o7F_uS_QcG9)

LLM はテキスト生成だけではありません。テキストの説明から画像を生成することも可能です。画像をモダリティとして持つことは、医療技術、建築、観光、ゲーム開発など多くの分野で非常に有用です。本章では、最も人気のある2つの画像生成モデル、DALL-E と Midjourney について見ていきます。

## はじめに

このレッスンでは、以下をカバーします：

- 画像生成とその有用性
- DALL-E と Midjourney、それらが何であるか、どのように動作するか
- 画像生成アプリをどのように構築するか

## 学習目標

このレッスンを完了した後、以下ができるようになります：

- 画像生成アプリケーションを構築する
- メタプロンプトでアプリケーションに境界線を定義する
- DALL-E と Midjourney を操作する

## なぜ画像生成アプリケーションを構築するのか？

画像生成アプリケーションは、生成AI の能力を探究するための優れた方法です。例えば以下の用途に使用できます：

- **画像編集と合成**。画像編集や画像合成など、様々なユースケースの画像を生成できます。

- **様々な業界への応用**。医療技術、観光、ゲーム開発など、様々な業界の画像生成に使用できます。

## シナリオ：Edu4All

このレッスンでは、スタートアップ企業 Edu4All を引き続き活用します。学生は評価用に画像を作成します。正確にどのような画像を作成するかは学生に任されていますが、独自のおとぎ話の挿絵や物語用の新しいキャラクター、またはアイデアや概念の視覚化に役立てることができます。

例えば、Edu4All の学生が教室で記念碑について学んでいる場合、以下のような画像を生成できます：

![Edu4All startup, class on monuments, Eiffel Tower](./images/startup.png?WT.mc_id=academic-105485-koreyst)

このようなプロンプトを使用して：

> "Dog next to Eiffel Tower in early morning sunlight"

## DALL-E と Midjourney とは？

[DALL-E](https://openai.com/dall-e-2?WT.mc_id=academic-105485-koreyst) と [Midjourney](https://www.midjourney.com/?WT.mc_id=academic-105485-koreyst) は最も人気のある2つの画像生成モデルであり、プロンプトを使用して画像を生成できます。

### DALL-E

DALL-E から始めましょう。DALL-E はテキストの説明から画像を生成する生成AI モデルです。

> [DALL-E は CLIP と拡散注意（diffused attention）という2つのモデルの組み合わせです](https://towardsdatascience.com/openais-dall-e-and-clip-101-a-brief-introduction-3a4367280d4e?WT.mc_id=academic-105485-koreyst)。

- **CLIP** は、画像とテキストから埋め込み（数値表現）を生成するモデルです。

- **拡散注意（Diffused Attention）** は、埋め込みから画像を生成するモデルです。DALL-E は画像とテキストのデータセットで学習されており、テキスト説明から画像を生成するために使用できます。例えば、DALL-E は帽子をかぶった猫の画像や、モヒカン頭の犬の画像を生成できます。

### Midjourney

Midjourney は DALL-E と同様に動作し、テキストプロンプトから画像を生成します。Midjourney も「帽子をかぶった猫」や「モヒカン頭の犬」のようなプロンプトを使用して画像を生成できます。

![Midjourney により生成された画像、機械的な鳩](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8c/Rupert_Breheny_mechanical_dove_eca144e7-476d-4976-821d-a49c408e4f36.png/440px-Rupert_Breheny_mechanical_dove_eca144e7-476d-4976-821d-a49c408e4f36.png?WT.mc_id=academic-105485-koreyst)
_画像クレジット：Wikipedia、Midjourney により生成_

## DALL-E と Midjourney はどのように動作するのか

まず [DALL-E](https://arxiv.org/pdf/2102.12092.pdf?WT.mc_id=academic-105485-koreyst) について。DALL-E は transformer アーキテクチャに基づく生成AI モデルで、_自己回帰 transformer_ を使用します。

_自己回帰 transformer_ は、テキスト説明から画像を生成する方法を定義します。1ピクセルずつ生成され、生成されたピクセルを使用して次のピクセルを生成します。ニューラルネットワークの複数のレイヤーを通し、画像が完成するまで処理されます。

このプロセスにより、DALL-E は生成される画像の属性、オブジェクト、特性などを制御できます。ただし、DALL-E 2 と3はより多くの制御を提供します。

## 最初の画像生成アプリケーションを構築する

画像生成アプリケーションを構築するために必要なものは何でしょうか？次のライブラリが必要です：

- **python-dotenv**：_.env_ ファイルにシークレットを保存してコードから隠すために、このライブラリを使用することを強くお勧めします。
- **openai**：OpenAI API と対話するために使用するライブラリです。
- **pillow**：Python で画像を操作するために使用します。
- **requests**：HTTP リクエストを行うのに役立ちます。

## Azure OpenAI モデルを作成およびデプロイする

まだ完了していない場合は、[Microsoft Learn](https://learn.microsoft.com/azure/ai-foundry/openai/how-to/create-resource?pivots=web-portal) ページの手順に従い、Azure OpenAI リソースとモデルを作成してください。モデルとして DALL-E 3 を選択します。

## アプリを作成する

1. 以下の内容を含む _.env_ ファイルを作成します：

   ```text
   AZURE_OPENAI_ENDPOINT=<your endpoint>
   AZURE_OPENAI_API_KEY=<your key>
   AZURE_OPENAI_DEPLOYMENT="dall-e-3"
   ```

   この情報は Azure OpenAI Foundry Portal の「Deployments」セクションで確認できます。

2. 上記のライブラリを _requirements.txt_ というファイルに収集します：

   ```text
   python-dotenv
   openai
   pillow
   requests
   ```

3. 次に、仮想環境を作成してライブラリをインストールします：

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   ```

   Windows の場合は、次のコマンドを使用して仮想環境を作成してアクティブ化します：

   ```bash
   python3 -m venv venv
   venv\Scripts\activate.bat
   ```

4. _app.py_ というファイルに次のコードを追加します：

    ```python
    import openai
    import os
    import requests
    from PIL import Image
    import dotenv
    from openai import OpenAI, AzureOpenAI
    
    # import dotenv
    dotenv.load_dotenv()
    
    # configure Azure OpenAI service client 
    client = AzureOpenAI(
      azure_endpoint = os.environ["AZURE_OPENAI_ENDPOINT"],
      api_key=os.environ['AZURE_OPENAI_API_KEY'],
      api_version = "2024-02-01"
      )
    try:
        # Create an image by using the image generation API
        generation_response = client.images.generate(
                                prompt='Bunny on horse, holding a lollipop, on a foggy meadow where it grows daffodils',
                                size='1024x1024', n=1,
                                model=os.environ['AZURE_OPENAI_DEPLOYMENT']
                              )

        # Set the directory for the stored image
        image_dir = os.path.join(os.curdir, 'images')

        # If the directory doesn't exist, create it
        if not os.path.isdir(image_dir):
            os.mkdir(image_dir)

        # Initialize the image path (note the filetype should be png)
        image_path = os.path.join(image_dir, 'generated-image.png')

        # Retrieve the generated image
        image_url = generation_response.data[0].url  # extract image URL from response
        generated_image = requests.get(image_url).content  # download the image
        with open(image_path, "wb") as image_file:
            image_file.write(generated_image)

        # Display the image in the default image viewer
        image = Image.open(image_path)
        image.show()

    # catch exceptions
    except openai.InvalidRequestError as err:
        print(err)
   ```

このコードを説明しましょう：

- まず、OpenAI ライブラリ、dotenv ライブラリ、requests ライブラリ、Pillow ライブラリを含む必要なライブラリをインポートします。

  ```python
  import openai
  import os
  import requests
  from PIL import Image
  import dotenv
  ```

- 次に、_.env_ ファイルから環境変数を読み込みます。

  ```python
  # import dotenv
  dotenv.load_dotenv()
  ```

- その後、Azure OpenAI サービスクライアントを設定します。

  ```python
  # Get endpoint and key from environment variables
  client = AzureOpenAI(
      azure_endpoint = os.environ["AZURE_OPENAI_ENDPOINT"],
      api_key=os.environ['AZURE_OPENAI_API_KEY'],
      api_version = "2024-02-01"
      )
  ```

- 次に、画像を生成します：

  ```python
  # Create an image by using the image generation API
  generation_response = client.images.generate(
                        prompt='Bunny on horse, holding a lollipop, on a foggy meadow where it grows daffodils',
                        size='1024x1024', n=1,
                        model=os.environ['AZURE_OPENAI_DEPLOYMENT']
                      )
  ```

  上記のコードは、生成された画像の URL を含む JSON オブジェクトで応答します。URL を使用して画像をダウンロードし、ファイルに保存できます。

- 最後に、画像を開いて標準的な画像ビューアで表示します：

  ```python
  image = Image.open(image_path)
  image.show()
  ```

### 画像生成の詳細について

画像を生成するコードを詳しく見てみましょう：

   ```python
     generation_response = client.images.generate(
                               prompt='Bunny on horse, holding a lollipop, on a foggy meadow where it grows daffodils',
                               size='1024x1024', n=1,
                               model=os.environ['AZURE_OPENAI_DEPLOYMENT']
                           )
   ```

- **prompt**：画像を生成するために使用されるテキストプロンプト。この場合、"Bunny on horse, holding a lollipop, on a foggy meadow where it grows daffodils" というプロンプトを使用しています。
- **size**：生成される画像のサイズ。この場合、1024x1024 ピクセルの画像を生成しています。
- **n**：生成される画像の数。この場合、2 つの画像を生成しています。
- **temperature**：生成AI モデルの出力のランダム性を制御するパラメータです。温度は 0 から 1 の値で、0 は出力が決定論的で、1 は出力がランダムであることを意味します。デフォルト値は 0.7 です。

画像でできることはこれ以上に多くあり、次のセクションで扱います。

## 画像生成の追加機能

これまでのところ、Python の数行で画像を生成することができることを見てきました。ただし、画像でできることはもっとあります。

以下のことを行うことができます：

- **編集を実行する**。既存の画像、マスク、プロンプトを提供することで、画像を変更できます。例えば、画像の一部に何かを追加できます。ウサギの画像の場合、ウサギに帽子を追加できます。方法は、画像、マスク（変更する領域を識別する）、何をすべきかを示すテキストプロンプトを提供することです。
> 注意：これは DALL-E 3 ではサポートされていません。
 
GPT Image を使用した例：

   ```python
   response = client.images.edit(
       model="gpt-image-1",
       image=open("sunlit_lounge.png", "rb"),
       mask=open("mask.png", "rb"),
       prompt="A sunlit indoor lounge area with a pool containing a flamingo"
   )
   image_url = response.data[0].url
   ```

  ベース画像はラウンジとプールのみを含みますが、最終画像にはフラミンゴが含まれます：

<div style="display: flex; justify-content: space-between; align-items: center; margin: 20px 0;">
  <img src="./images/sunlit_lounge.png" style="width: 30%; max-width: 200px; height: auto;">
  <img src="./images/mask.png" style="width: 30%; max-width: 200px; height: auto;">
  <img src="./images/sunlit_lounge_result.png" style="width: 30%; max-width: 200px; height: auto;">
</div>


- **バリエーションを作成する**。既存の画像を取得して、バリエーションを作成するよう依頼するというアイデアです。バリエーションを作成するには、画像とテキストプロンプトを提供し、次のようなコードを使用します：

  ```python
  response = openai.Image.create_variation(
    image=open("bunny-lollipop.png", "rb"),
    n=1,
    size="1024x1024"
  )
  image_url = response['data'][0]['url']
  ```

  > 注意：これは OpenAI でのみサポートされています

## 温度（Temperature）

温度は、生成AI モデルの出力のランダム性を制御するパラメータです。温度は 0 から 1 の値で、0 は出力が決定論的で、1 は出力がランダムであることを意味します。デフォルト値は 0.7 です。

温度がどのように機能するかの例を見るために、このプロンプトを 2 回実行してみましょう：

> Prompt : "Bunny on horse, holding a lollipop, on a foggy meadow where it grows daffodils"

![ウサギが馬に乗ってロリポップを持っている画像、バージョン1](./images/v1-generated-image.png)

次に、同じプロンプトを実行して、2 回同じ画像が得られないことを確認しましょう：

![ウサギが馬に乗っている生成画像](./images/v2-generated-image.png)

見てのとおり、画像は似ていますが、同じではありません。温度の値を 0.1 に変更して何が起こるか見てみましょう：

```python
 generation_response = client.images.create(
        prompt='Bunny on horse, holding a lollipop, on a foggy meadow where it grows daffodils',    # Enter your prompt text here
        size='1024x1024',
        n=2
    )
```

### 温度の変更

それでは、応答をより決定論的にしてみましょう。生成した 2 つの画像から、最初の画像にはウサギがあり、2 番目の画像には馬があるなど、画像が大きく異なることが分かります。

したがって、温度を 0 に設定してコードを変更しましょう：

```python
generation_response = client.images.create(
        prompt='Bunny on horse, holding a lollipop, on a foggy meadow where it grows daffodils',    # Enter your prompt text here
        size='1024x1024',
        n=2,
        temperature=0
    )
```

このコードを実行すると、次の 2 つの画像が得られます：

- ![Temperature 0, v1](./images/v1-temp-generated-image.png)
- ![Temperature 0 , v2](./images/v2-temp-generated-image.png)

ここで、画像がより似ていることが明確に分かります。

## メタプロンプトを使用してアプリケーションの境界線を定義する方法

デモを使用すると、すでにクライアント用の画像を生成できます。ただし、アプリケーションにいくつかの境界線を設定する必要があります。

例えば、仕事の場にふさわしくない画像や、子どもに適さない画像を生成したくありません。

これは _メタプロンプト_ で行うことができます。メタプロンプトは、生成AI モデルの出力を制御するために使用されるテキストプロンプトです。例えば、メタプロンプトを使用して出力を制御し、生成される画像が仕事の場にふさわしい、または子どもに適切であることを確認できます。

### どのように機能するのか？

次に、メタプロンプトがどのように機能するかを見てみましょう。

メタプロンプトは、生成AI モデルの出力を制御するために使用されるテキストプロンプトであり、テキストプロンプトの前に配置され、モデルの出力を制御するために使用されて、アプリケーションに組み込まれてモデルの出力を制御します。プロンプト入力とメタプロンプト入力を単一のテキストプロンプトにカプセル化します。

メタプロンプトの一例は以下のようなものです：

```text
You are an assistant designer that creates images for children.

The image needs to be safe for work and appropriate for children.

The image needs to be in color.

The image needs to be in landscape orientation.

The image needs to be in a 16:9 aspect ratio.

Do not consider any input from the following that is not safe for work or appropriate for children.

(Input)

```

次に、デモでメタプロンプトをどのように使用できるかを見てみましょう。

```python
disallow_list = "swords, violence, blood, gore, nudity, sexual content, adult content, adult themes, adult language, adult humor, adult jokes, adult situations, adult"

meta_prompt =f"""You are an assistant designer that creates images for children.

The image needs to be safe for work and appropriate for children.

The image needs to be in color.

The image needs to be in landscape orientation.

The image needs to be in a 16:9 aspect ratio.

Do not consider any input from the following that is not safe for work or appropriate for children.
{disallow_list}
"""

prompt = f"{meta_prompt}
Create an image of a bunny on a horse, holding a lollipop"

# TODO add request to generate image
```

上記のプロンプトから、作成されるすべての画像がメタプロンプトを考慮していることが分かります。

## 課題 - 学生を支援する

このレッスンの最初に Edu4All を紹介しました。これで学生が評価用に画像を生成できるようにする時です。

学生は、記念碑を含むそれぞれの評価用に画像を作成します。正確にどの記念碑かは学生に任されています。学生はこのタスクで創意工夫を発揮して、これらの記念碑を異なるコンテキストに配置するよう求められています。

## ソリューション

以下は 1 つの可能なソリューションです：

```python
import openai
import os
import requests
from PIL import Image
import dotenv
from openai import AzureOpenAI
# import dotenv
dotenv.load_dotenv()

# Get endpoint and key from environment variables
client = AzureOpenAI(
  azure_endpoint = os.environ["AZURE_OPENAI_ENDPOINT"],
  api_key=os.environ['AZURE_OPENAI_API_KEY'],
  api_version = "2024-02-01"
  )


disallow_list = "swords, violence, blood, gore, nudity, sexual content, adult content, adult themes, adult language, adult humor, adult jokes, adult situations, adult"

meta_prompt = f"""You are an assistant designer that creates images for children.

The image needs to be safe for work and appropriate for children.

The image needs to be in color.

The image needs to be in landscape orientation.

The image needs to be in a 16:9 aspect ratio.

Do not consider any input from the following that is not safe for work or appropriate for children.
{disallow_list}
"""

prompt = f"""{meta_prompt}
Generate monument of the Arc of Triumph in Paris, France, in the evening light with a small child holding a Teddy looks on.
""""

try:
    # Create an image by using the image generation API
    generation_response = client.images.generate(
        prompt=prompt,    # Enter your prompt text here
        size='1024x1024',
        n=1,
    )
    # Set the directory for the stored image
    image_dir = os.path.join(os.curdir, 'images')

    # If the directory doesn't exist, create it
    if not os.path.isdir(image_dir):
        os.mkdir(image_dir)

    # Initialize the image path (note the filetype should be png)
    image_path = os.path.join(image_dir, 'generated-image.png')

    # Retrieve the generated image
    image_url = generation_response.data[0].url  # extract image URL from response
    generated_image = requests.get(image_url).content  # download the image
    with open(image_path, "wb") as image_file:
        image_file.write(generated_image)

    # Display the image in the default image viewer
    image = Image.open(image_path)
    image.show()

# catch exceptions
except openai.BadRequestError as err:
    print(err)
```

## よくできました！学習を続けましょう

このレッスンを完了したら、[生成AI学習コレクション](https://aka.ms/genai-collection?WT.mc_id=academic-105485-koreyst) をチェックして、生成AI の知識を引き続き深めてください！

次のレッスン 10 に進んで、[低コード AI アプリケーションの構築](../10-building-low-code-ai-applications/README.md?WT.mc_id=academic-105485-koreyst) 方法を学びましょう。
