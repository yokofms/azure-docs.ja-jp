---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 0c48116c285ee5b9f0de0d9333a49d5f8ac183af
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425112"
---
:::row:::
    :::column span="3":::
        JavaScript 用 Speech SDK は npm パッケージとして提供されています。<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk<span class="docon docon-navigate-external x-hidden-focus"></span></a> とそれに付随する GitHub リポジトリ <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js<span class="docon docon-navigate-external x-hidden-focus"></span></a> を参照してください。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> JavaScript 用 Speech SDK は npm パッケージとして提供されており、Node.js とクライアントの Web ブラウザーの両方で使用できますが、各環境のさまざまなアーキテクチャの影響を考慮してください。 例えば、<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">ドキュメントオブジェクトモデル (DOM)<span class="docon docon-navigate-external x-hidden-focus"></span></a>は、<a href="https://nodejs.org/api/fs.html" target="_blank">ファイルシステム<span class="docon docon-navigate-external x-hidden-focus"></span></a>がクライアント側アプリケーションで使用できない場合と同様に、サーバー側アプリケーションでは使用できません。

### <a name="nodejs-package-manager-npm"></a>Node.js パッケージマネージャー (NPM)

JavaScript 用 Speech SDK をインストールするには、次の `npm install` コマンドを実行します。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

詳細については、<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">node.js 音声SDK のクイックスタート<span class="docon docon-navigate-external x-hidden-focus"></span></a>を参照してください。
