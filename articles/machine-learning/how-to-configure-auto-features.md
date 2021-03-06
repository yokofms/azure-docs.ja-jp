---
title: AutoML 実験での特徴量化
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で提供されている特徴量化の設定と、自動 ML 実験における特徴エンジニアリングのサポートについて説明します。
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 94595bac2febdef1d3739703f0fa49c9ef15f218
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166622"
---
# <a name="featurization-in-automated-machine-learning"></a>自動機械学習での特徴量化

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

このガイドでは、以下のことについて説明します。

- Azure Machine Learning で提供されている特徴量化の設定。
- [自動機械学習実験](concept-automated-ml.md)用にそれらの特徴をカスタマイズする方法。

"*特徴エンジニアリング*" は、データに関するドメインの知識を使用して、機械学習 (ML) アルゴリズムの学習を支援する特徴を作成するプロセスです。 Azure Machine Learning では、特徴エンジニアリングを容易にするために、データのスケーリングと正規化の手法が適用されます。 自動機械学習 (*AutoML*) の実験では、これらの手法と特徴エンジニアリングが、まとめて "*特徴量化*" と呼ばれています。

この記事は、AutoML の実験を構成する方法についての知識が既にある読者を対象としています。 構成については、次の記事を参照してください。

- コード ファーストのエクスペリエンスについて: [Python 用 Azure Machine Learning SDK を使用して自動 ML の実験を構成する](how-to-configure-auto-train.md)。
- 少量のコードまたはコードなしのエクスペリエンスについて: [Azure Machine Learning Studio を使用して自動機械学習モデルを作成、確認、デプロイする](how-to-use-automated-ml-for-ml-models.md)。

## <a name="configure-featurization"></a>特徴量化を構成する

自動機械学習におけるあらゆる実験では、[自動スケーリングと正規化の手法](#featurization)が既定でデータに適用されます。 これらの手法は、さまざまなスケールの特徴に反応する "*特定*" のアルゴリズムを支援する特徴量化の一種です。 ただし、"*欠損値の補完*"、"*エンコード*"、"*変換*" などの特徴量化も追加で有効にできます。

> [!NOTE]
> 自動機械学習の特徴量化の手順 (機能の正規化、欠損データの処理、テキストから数値への変換など) は、基になるモデルの一部になります。 このモデルを予測に使用する場合、トレーニング中に適用されたのと同じ特徴量化の手順が入力データに自動的に適用されます。

Python SDK を使用して構成した実験では、特徴量化の設定を有効または無効にできるほか、実験に使用する特徴量化手順をさらに指定することもできます。 Azure Machine Learning Studio を使用している場合は、[特徴量化を有効にする手順](how-to-use-automated-ml-for-ml-models.md#customize-featurization)に関する記事を参照してください。

次の表は、[AutoMLConfig クラス](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)で `featurization` に使用できる設定を示したものです。

|特徴量化の構成 | 説明|
------------- | ------------- |
|`"featurization": 'auto'`| 前処理の一環として、[データ ガードレールと特徴量化の手順](#featurization)が自動的に実行されることを示します。 これは、既定の設定です。|
|`"featurization": 'off'`| 特徴量化手順が自動的に行われないことを指定します。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| カスタマイズした特徴量化手順を使用することを指定します。 [特徴付けをカスタマイズする方法の詳細](#customize-featurization)。|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>自動特徴付け

以下の表は、データに対して自動的に適用される手法の一覧です。 これらの手法は、SDK またはスタジオを使用して構成された実験に適用されます。 この動作を無効にするには、`AutoMLConfig` オブジェクトで `"featurization": 'off'` を設定します。

> [!NOTE]
> AutoML で作成されたモデルを [ONNX モデル](concept-onnx.md)にエクスポートする予定がある場合、アスタリスク ("*") で示された特徴量化オプションのみが ONNX 形式でサポートされます。 [モデルの ONNX への変換](concept-automated-ml.md#use-with-onnx)の詳細についてご確認ください。

|特徴量化&nbsp;ステップ| 説明 |
| ------------- | ------------- |
|**高カーディナリティまたは差異なしの特徴の削除*** |これらの特徴をトレーニング セットと検証セットから削除します。 これには、まったく値が存在しない特徴、すべての行の値が同じである特徴、高いカーディナリティ (ハッシュ、ID、GUID など) の特徴に適用します。|
|**欠損値の補完*** |数値特徴の場合、その列の平均値で補完します。<br/><br/>カテゴリ特徴の場合、出現回数が最も多い値で補完します。|
|**その他の特徴の生成*** |DateTime の特徴:年、月、日、曜日、年の通算日、四半期、年の通算週、時間、分、秒。<br><br> *予測タスクの場合*、次の追加の DateTime 機能が作成されます:ISO の年、半期、カレンダーの月の文字列、週、曜日の文字列、四半期の日、年の日、午前/午後 (時間が正午より前の場合は 0、それ以外の場合は 1)、午前/午後の文字列、時間 (12 時間ベース)<br/><br/>テキストの特徴:ユニグラム、バイグラム、トライグラムに基づく期間の頻度。 詳細については、[BERT を使用してこれを実行する方法](#bert-integration)に関する記事を参照してください。|
|**変換とエンコード***|一意の値がほとんどない数値特徴を、カテゴリ特徴に変換します。<br/><br/>カーディナリティの低いカテゴリ特徴の場合、ワンホット エンコードが使用されます。 カーディナリティが高いカテゴリ特徴の場合、ワンホット ハッシュ エンコードが使用されます。|
|**ワードの埋め込み**|事前トレーニングされたモデルを使用してテキスト トークンのベクトルをセンテンス ベクトルに変換するテキスト特性化機能です。 ドキュメント内の各ワードの埋め込みベクトルは、ドキュメント特徴ベクトルを生成するために他のものと集約されます。|
|**ターゲット エンコード**|カテゴリ特徴の場合、このステップは、回帰の問題について各カテゴリを平均ターゲット値にマップされます。分類の問題については、各クラスのクラス確率にマップされます。 マッピングの過剰適合および疎データ カテゴリによって発生するノイズを削減するために、頻度ベースの重み付けと k フォールド クロス検証が適用されます。|
|**テキスト ターゲット エンコード**|テキスト入力の場合、bag-of-words を使用するスタック線形モデルは、各クラスの確率を生成するために使用されます。|
|**証拠の重み (WoE)**|ターゲット列に対するカテゴリ列の相関関係のメジャーとして、WoE を計算します。 WoE は、クラス内およびクラス外の確率に対する比率の対数として計算されます。 このステップでは、クラスごとに 1 つの数値特徴列が生成され、明示的に欠損値と外れ値の処理を補完する必要がなくなります。|
|**クラスターの距離**|k-means クラスタリング モデルを、すべての数値列を対象にトレーニングします。 *k* 個の新しい特徴 (クラスターごとに 1 つの新しい数値特徴) が生成されます。各クラスターの重心までの各サンプルの距離が含まれます。|

## <a name="data-guardrails"></a>データ ガードレール

"*データ ガードレール*" は、データの潜在的な問題 (欠損値、[クラスの不均衡](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)など) を特定するのに役立ちます。 また、改善された結果を得るために是正措置を取るのにも役立ちます。

データ ガードレールは次の場合に適用されます。

- **SDK 実験の場合**: `AutoMLConfig` オブジェクトでパラメーター `"featurization": 'auto'` または `validation=auto` が指定されているとき。
- **Studio 実験の場合**: 自動特徴量化が有効になっているとき。

実験に対するデータ ガードレールは、次のようにして確認できます。

- SDK を使用して実験を送信するときに、`show_output=True` を設定します。

- Studio で、自動 ML 実行の **[Data guardrails]\(データ ガードレール\)** タブを確認します。

### <a name="data-guardrail-states"></a>データ ガードレールの状態

データ ガードレールには、3 つの状態のいずれかが表示されます。

|State| 説明 |
|----|---- |
|**成功**| データの問題は検出されませんでした。ユーザーの操作は不要です。 |
|**完了**| データに変更が適用されました。 AutoML で行われた修正アクションをレビューし、変更が予想される結果と一致することを確認することをお勧めします。 |
|**通知済み**| データの問題が検出されましたが、修復できませんでした。 修正して問題を解決することをお勧めします。|

### <a name="supported-data-guardrails"></a>サポートされているデータ ガードレール

次の表では、現在サポートされているデータ ガードレールと、実験を送信するときに表示される可能性がある関連する状態について説明します。

ガードレール|Status|トリガー&nbsp;の&nbsp;条件
---|---|---
**欠損特徴量値の補完** |Passed <br><br><br> 完了| トレーニング データで、不足している機能の値が検出されませんでした。 [不足している値の補完](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options)の詳細を確認してください。 <br><br> 不足している特徴の値が、トレーニング データで検出され、補完されました。
**高カーディナリティの特徴量の処理** |Passed <br><br><br> 完了| 入力が分析され、高カーディナリティの特徴は検出されませんでした。 <br><br> 高カーディナリティの特徴が入力で検出され、処理されました。
**検証分割処理** |完了| 検証構成は `'auto'` に設定されており、トレーニング データには "*20,000 行未満*" が含まれていました。 <br> トレーニング済みモデルの各イテレーションは、クロス検証を使用して検証されました。 [検証データ](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data)の詳細を確認してください。 <br><br> 検証構成は `'auto'` に設定されており、トレーニング データには "*20,000 行超*" が含まれていました。 <br> 入力データはトレーニング データセットと検証データセットに分割され、モデルが検証されます。
**クラス均衡の検出** |Passed <br><br>通知済み <br><br>完了 | 入力が分析され、すべてのクラスがトレーニング データ内で均衡が取られます。 サンプルの数と比率によって測定され、データセット内で各クラスに適正な表現がある場合、データセットは均衡が取れていると見なされます。 <br><br><br> 入力で、不均衡なクラスが検出されました。 モデルのバイアスを修正するには、バランスの問題を修正します。 [不均衡なデータ](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)の詳細を確認してください。<br><br><br> 入力で不均衡なクラスが検出され、スイープ ロジックによって分散の適用が決定されました。
**メモリの問題の検出** |Passed <br><br><br><br> 完了 |<br> 選択された値 (期間、ラグ、ローリング ウィンドウ) が分析され、潜在的なメモリ不足の問題は検出されませんでした。 時系列[予測構成](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)の詳細を確認してください。 <br><br><br>選択された値 (期間、ラグ、ローリング ウィンドウ) が分析され、実験でメモリが不足する可能性があります。 ラグまたはローリング ウィンドウの構成がオフになっています。
**頻度の検出** |Passed <br><br><br><br> 完了 |<br> 時系列が分析され、すべてのデータ ポイントが検出済みの頻度でアラインされます。 <br> <br> 時系列が分析され、検出された頻度にアラインしないデータ ポイントが検出されました。 このようなデータ ポイントはデータセットから削除されました。 [時系列予測のデータの準備](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)の詳細を確認してください。

## <a name="customize-featurization"></a>特徴量化のカスタマイズ

ML モデルのトレーニングに使用されたデータと特徴から適切な予測が得られるよう、特徴量化の設定をカスタマイズすることができます。

特徴量化をカスタマイズするには、`AutoMLConfig` オブジェクトで  `"featurization": FeaturizationConfig` を指定します。 Azure Machine Learning Studio を実験に使用している場合、[方法に関する記事](how-to-use-automated-ml-for-ml-models.md#customize-featurization)を参照してください。 予測のタスクの種類用に特徴量化をカスタマイズするには、[予測の方法](how-to-auto-train-forecast.md#customize-featurization)に関する記事を参照してください。

サポートされるカスタマイズは次のとおりです。

|カスタマイズ|定義|
|--|--|
|**列の目的の更新**|指定した列の自動検出された特徴の種類をオーバーライドします。|
|**トランスフォーマー パラメーターの更新** |指定したトランスフォーマーのパラメーターを更新します。 現在、*Imputer* (平均値、最頻値、中央値) と *HashOneHotEncoder* がサポートされています。|
|**列の削除** |特徴量化から削除する列を指定します。|
|**ブロック トランスフォーマー**| 特徴量化プロセスで使用されるブロック トランスフォーマーを指定します。|

次の API 呼び出しを使用して、`FeaturizationConfig` オブジェクトを作成します。

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="bert-integration"></a>BERT 統合 
[BERT](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) は、自動 ML の特徴量化層で使用されます。 この層では、列にフリー テキストやその他の種類のデータ (タイムスタンプや単純な数値など) が含まれているかどうかを検出し、それに応じて特徴量化を行います。 BERT の場合、ユーザーが指定したラベルを使用してモデルをファインチューニング/トレーニングした後、タイムスタンプベースの特徴 (曜日など) や多くの一般的なデータセットに含まれている数値などの他の特徴と一緒に、特徴としてドキュメントの埋め込み (BERT の場合、これらは特殊な [CLS] トークンに関連付けられた最終的な非表示状態です) を出力します。 

BERT を有効にするには、トレーニングに GPU コンピューティングを使用する必要があります。 CPU コンピューティングを使用した場合、AutoML は、BERT ではなく、BiLSTM DNN 特徴抽出器を有効にします。 BERT を呼び出すには、automl_settings に "enable_dnn:True" を設定し、GPU コンピューティング (例: vm_size = "STANDARD_NC6"、またはそれ以上の GPU) を使用する必要があります。 例については、[こちらのノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)をご覧ください。

BERT の場合、AutoML は次の手順を実行します (これらの項目が発生するためには、automl_settings に "enable_dnn:True" を設定する必要があることに注意してください)。

1. すべてのテキスト列のトークン化を含む前処理 (最終的なモデルの特徴量化の概要には "StringCast" トランスフォーマーが表示されます)。 `get_featurization_summary()` メソッドを使用してモデルの特徴量化の概要を生成する方法の例については、[こちらのノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)を参照してください。

```python
text_transformations_used = []
for column_group in fitted_model.named_steps['datatransformer'].get_featurization_summary():
    text_transformations_used.extend(column_group['Transformations'])
text_transformations_used
```

2. すべてのテキスト列を 1 つのテキスト列に連結します。そのため、最終的なモデルには "StringConcatTransformer" が表示されます。 

> [!NOTE]
> BERT の実装では、トレーニング サンプルのテキストの長さの合計は 128 トークンに制限されています。 つまり、すべてのテキスト列を連結した長さは、最大で 128 トークンになることが理想です。 理想的には、複数の列がある場合は、この条件が満たされるように各列を切り詰める必要があります。 たとえば、データに 2 つのテキスト列がある場合は、データを AutoML に供給する前に、両方のテキスト列を切り詰めてそれぞれ 64 トークンになるようにする必要があります (最終的に連結されたテキスト列で、両方の列を等しく表す必要があると仮定した場合)。 長さが 128 トークンを超える連結列の場合、BERT のトークナイザー層により、この入力は 128 トークンに切り詰められます。

3. 特徴量のスイープ手順で、AutoML は、BERT を、データ サンプルのベースライン (bag-of-words 特徴量) と比較し、BERT によって精度が向上するかどうかを判断します。 AutoML は、BERT のパフォーマンスがベースラインよりも優れていると判断すると、最適な特徴量化戦略としてテキストの特徴量化に BERT を使用して、データ全体の特徴量化を進めます。 その場合、最終的なモデルには "PretrainedTextDNNTransformer" と表示されます。

BERT は通常、他のほとんどの特徴抽出器よりも長く実行されます。 処理速度を上げるには、クラスターにより多くのコンピューティング リソースを提供します。 AutoML は、可能な場合、BERT トレーニングを複数のノードに分散します (最大 8 ノード)。 これを行うには、[max_concurrent_iterations](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) を 1 より大きい値に設定します。 パフォーマンスを向上させるには、RDMA 機能 ("STANDARD_NC24r"、"STANDARD_NC24rs_V3" など) を持つ SKU を使用することをお勧めします

AutoML では現在約 100 の言語がサポートされており、データセットの言語に応じて、適切な BERT モデルが選択されます。 ドイツ語のデータの場合は、ドイツ語の BERT モデルが使用されます。 英語の場合は、英語の BERT モデルが使用されます。 その他のすべての言語では、多言語 BERT モデルが使用されます。

次のコードでは、データセットの言語が "deu" ([ISO 分類](https://iso639-3.sil.org/code/deu)でドイツ語を示す 3 文字の言語コード) に指定されているため、ドイツ語の BERT モデルがトリガーされます。

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>次のステップ

* 自動 ML の実験を設定する方法を確認します。

    * コード ファーストのエクスペリエンスについて: [Azure Machine Learning SDK を使用して自動 ML の実験を構成する](how-to-configure-auto-train.md)。
    * 少量のコードまたはコードなしのエクスペリエンスについて: [Azure Machine Learning Studio で自動 ML 実験を作成する](how-to-use-automated-ml-for-ml-models.md)。

* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についてさらに詳しく学習する。

* [自動機械学習を使用して回帰モデルをトレーニングする方法](tutorial-auto-train-models.md)または[リモート リソースに対して自動機械学習を使用してトレーニングする方法](how-to-auto-train-remote.md)についてさらに詳しく確認します。
