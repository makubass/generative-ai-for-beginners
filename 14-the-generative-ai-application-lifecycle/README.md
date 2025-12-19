[![Integrating with function calling](./images/14-lesson-banner.png?WT.mc_id=academic-105485-koreyst)](https://youtu.be/ewtQY_RJrzs?si=dyJ2bjiljH7UUHCh)

# 生成系AIアプリケーションのライフサイクル

すべてのAIアプリケーションにとって重要な問いは、AI機能がどれだけ関連性を保てるかです。AIは急速に進化する分野であるため、アプリケーションを常に関連性があり、信頼でき、堅牢な状態に保つには、継続的な監視、評価、改善が必要です。ここで生成系AIのライフサイクルが役に立ちます。

生成系AIのライフサイクルは、生成系AIアプリケーションを開発、デプロイ、維持する各段階を導くフレームワークです。目標の定義、性能の測定、課題の特定、解決策の実装を助けるだけでなく、ドメインや利害関係者の倫理的・法的基準にアプリを整合させるのにも役立ちます。ライフサイクルに従うことで、常に価値を提供しユーザーを満足させるアプリを維持できます。

## はじめに

この章では以下を学びます：

- MLOps から LLMOps へのパラダイムシフト
- LLM のライフサイクル
- ライフサイクルのためのツール
- メトリクス化と評価

## MLOps から LLMOps へのパラダイムシフトを理解する

LLM（大規模言語モデル）はAIの新たなツールであり、分析や生成タスクで極めて強力です。ただし、この力は従来の機械学習タスクとAIの運用方法にも影響を与えます。

そのため、このツールを動的かつ適切なインセンティブで適用するための新しいパラダイムが必要です。古い世代のAIアプリを「MLアプリ」とし、より新しいものを「生成系（GenAI）アプリ」または単に「AIアプリ」と分類することで、その時点で主流の技術と手法の差を反映できます。以下の比較に注目してください。

![LLMOps vs. MLOps comparison](./images/01-llmops-shift.png?WT.mc_id=academic-105485-koreys)

LLMOps ではアプリ開発者に焦点を当て、統合を重要視し、"Models-as-a-Service" の利用や次のようなメトリクスを考慮します。

- 品質（Quality）：応答の品質
- 有害性（Harm）：責任あるAI（Responsible AI）
- 正確性（Honesty）：応答の根拠（意味は通っているか？正しいか？）
- コスト（Cost）：ソリューションの予算
- レイテンシ（Latency）：トークン応答の平均時間

## LLM のライフサイクル

ライフサイクルとその変化を理解するために、次のインフォグラフィックを確認してください。

![LLMOps infographic](./images/02-llmops.png?WT.mc_id=academic-105485-koreys)

ご覧のとおり、これは従来の MLOps のライフサイクルとは異なります。LLM はプロンプト、品質向上のための様々な手法（ファインチューニング、RAG、メタプロンプト）や責任あるAIに関する評価など、多くの新しい要件を持ちます。評価指標も新しく（品質、有害性、正確性、コスト、レイテンシなど）なっています。

例えば、アイデア出しの際はプロンプトエンジニアリングを使って複数の LLM を試し、仮説が正しいか検証します。

このプロセスは線形ではなく、統合されたループであり反復的に行われます。

では、具体的にどのようにライフサイクルを構築するか見ていきましょう。

![LLMOps Workflow](./images/03-llm-stage-flows.png?WT.mc_id=academic-105485-koreys)

やや複雑に見えるかもしれませんが、まずは大きく3つのステップに注目しましょう。

1. アイデア出し／探索（Ideating/Exploring）: ビジネスニーズに応じて探索し、プロトタイピングや [PromptFlow](https://microsoft.github.io/promptflow/index.html?WT.mc_id=academic-105485-koreyst) を使って仮説検証を行います。
2. 構築／拡張（Building/Augmenting）: 実装段階では大規模データセットで評価し、ファインチューニングや RAG のような手法を導入して解決策の堅牢性を確認します。必要であれば再設計やデータ構造の変更を行います。フローとスケールをテストしてメトリクスを満たせば次の段階へ進みます。
3. 運用化（Operationalizing）: モニタリングやアラートシステムの追加、デプロイとアプリケーション統合を行います。

その上で、セキュリティ、コンプライアンス、ガバナンスに焦点を当てた管理という大きなサイクルがあります。

おめでとうございます。これで AI アプリは稼働準備が整いました。ハンズオンの例として、[Contoso Chat Demo](https://nitya.github.io/contoso-chat/?WT.mc_id=academic-105485-koreys) をご覧ください。

次に、どんなツールが使えるか見てみましょう。

## ライフサイクルのためのツール

ツールとしては、Microsoft が提供する [Azure AI Platform](https://azure.microsoft.com/solutions/ai/?WT.mc_id=academic-105485-koreys) と [PromptFlow](https://microsoft.github.io/promptflow/index.html?WT.mc_id=academic-105485-koreyst) が、ライフサイクルの実装を容易にします。

[Azure AI Platform](https://azure.microsoft.com/solutions/ai/?WT.mc_id=academic-105485-koreys) では [AI Studio](https://ai.azure.com/?WT.mc_id=academic-105485-koreys) を利用できます。AI Studio はモデル、サンプル、ツールを探索できるウェブポータルで、リソース管理、UI 開発フロー、そしてコードファースト開発のための SDK/CLI オプションを提供します。

![Azure AI possibilities](./images/04-azure-ai-platform.png?WT.mc_id=academic-105485-koreys)

Azure AI を使うことで、運用、サービス、プロジェクト、ベクトル検索やデータベースのニーズを複数のリソースで管理できます。

![LLMOps with Azure AI](./images/05-llm-azure-ai-prompt.png?WT.mc_id=academic-105485-koreys)

PromptFlow を使えば、PoC（概念実証）から大規模アプリケーションまで次のことが行えます：

- VS Code から視覚的かつ機能的なツールでアプリを設計・構築する
- 品質の高い AI をテスト・ファインチューニングしやすくする
- Azure AI Studio を使ってクラウドと統合し、迅速にデプロイできるようにする

![LLMOps with PromptFlow](./images/06-llm-promptflow.png?WT.mc_id=academic-105485-koreys)

## 続けて学びましょう！

作成したアプリに概念を適用する方法を学ぶには、[Contoso Chat App](https://nitya.github.io/contoso-chat/?WT.mc_id=academic-105485-koreyst) を参照してみてください。Cloud Advocacy がこれらの概念をどのようにデモに組み込んでいるか確認できます。さらにコンテンツを見たい場合は、私たちの [Ignite ブレイクアウトセッション](https://www.youtube.com/watch?v=DdOylyrTOWg) をチェックしてください。

次にレッスン 15 を見て、[Retrieval Augmented Generation（RAG）とベクトルデータベース](../15-rag-and-vector-databases/README.md?WT.mc_id=academic-105485-koreyst) が生成系AIに与える影響を学び、より魅力的なアプリケーションを作ってみましょう！
