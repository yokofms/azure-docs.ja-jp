---
title: 移行の概要 - LUIS
description: 移行パスの内容を理解する
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: a27945b5ac3dc2625c9520a8dd413b774b5d7adf
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837444"
---
# <a name="migration-in-luis"></a>LUIS での移行

移行パスにいくつかの項目があります。 次の表を利用し、影響を受けるものと完全な移行が必要になる条件を理解します。

|領域|説明|移行の完了日|
|--|--|--|
|[予測 API](luis-migration-api-v3.md)|V3 API への移行。|TBD|
|[作成 API](luis-migration-authoring-entities.md)|V3 API への移行。|TBD|
|[作成リソース](luis-migration-authoring.md)|作成リソースなしから LUIS ポータルでの LUIS 作成リソース利用に移行。|2020 年 8 月 30 日 |
|[必須の特徴](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|この変更は 2020 年 5 月に //Build 会議で行われたもので、アプリが制約付きの特徴を使用している v3 オーサリング API にのみ適用されます。|2020 年 6 月 19 日|
|[複合エンティティ](migrate-from-composite-entity.md)|複合エンティティを機械学習エンティティにアップグレードすると、エンティティをデバッグするための分解可能性が向上し、より完全な予測を受け取るエンティティが構築されます。|TBD|
