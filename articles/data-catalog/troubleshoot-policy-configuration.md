---
title: Azure Data Catalog をトラブルシューティングする方法
description: この記事では、Azure Data Catalog リソースのトラブルシューティングに関する一般的な考慮事項について説明します。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "68879554"
---
# <a name="troubleshooting-azure-data-catalog"></a>Azure Data Catalog のトラブルシューティング

この記事では、Azure Data Catalog リソースのトラブルシューティングに関する一般的な考慮事項について説明します。 

## <a name="functionality-limitations"></a>機能上の制限

Azure Data Catalog を使用しているときは、次の機能が制限されます。

- **ゲスト ロール** タイプのアカウントはサポートされません。 Azure Data Catalog のユーザーとしてゲスト アカウントを追加することはできません。また、ゲスト ユーザーがポータル ([https://www.azuredatacatalog.com](https://www.azuredatacatalog.com)) を使用することもできません。

- Azure Resource Manager テンプレートまたは Azure PowerShell コマンドを使用した Azure Data Catalog リソースの作成はサポートされません。

- Azure テナント間で Azure Data Catalog リソースを移動することはできません。

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory ポリシーの構成

Azure Data Catalog ポータルにはサインインできるものの、データ ソース登録ツールにサインインしようとするとエラー メッセージが表示され、サインインできないという状況に直面することがあります。 このようなエラーは、企業ネットワーク上にいる場合や、企業ネットワークの外部から接続している場合に発生する可能性があります。

登録ツールでは、" *フォーム認証* " を使用して、Azure Active Directory に対するユーザーのサインインを検証します。 サインインを成功させるためには、Azure Active Directory 管理者が、" *グローバル認証ポリシー*" でフォーム認証を有効にする必要があります。

グローバル認証ポリシーでは、次の画像に示すように、イントラネット接続とエクストラネット接続で個別に認証を有効にすることができます。 サインイン エラーは、接続元のネットワークで、フォーム認証が有効にされていない場合に発生する可能性があります。

 ![Azure Active Directory global authentication policy](./media/troubleshoot-policy-configuration/global-auth-policy.png)

詳細については、「 [認証ポリシーの構成](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))」を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure データ カタログを作成する](data-catalog-get-started.md)
