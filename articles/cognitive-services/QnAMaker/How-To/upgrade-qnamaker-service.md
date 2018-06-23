---
title: Upgradujte QnA Maker službu - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Postup upgradu služby QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e8d3713d6729c4e30da9a64a382e9d5a647dfefd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343252"
---
# <a name="upgrade-your-qna-maker-service"></a>Upgrade služby QnA Maker
Můžete upgradovat jednotlivých součástí zásobníku QnA Maker po vytvoření počáteční. Zobrazí podrobnosti o závislé součásti a výběr SKU [zde](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>Upgrade QnA Maker správu SKU
K upgradu správu QnA Maker SKU:
1. Přejděte do programu QnA Maker prostředku na portálu Azure a vyberte **cenová úroveň**.

    ![Prostředek QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Vyberte příslušné SKU a stiskněte klávesu **vyberte**.

    ![Ceny QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Upgrade služby App service
Můžete [škálovat](https://docs.microsoft.com/azure/app-service/web-sites-scale) nebo snižovat App service.

1. Přejděte na prostředek aplikace služby na portálu Azure a vyberte **škálovat** nebo **snižovat** možnosti podle potřeby.

    ![Škálování služby app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Upgrade služby Azure Search
Aktuálně není možné provést v místní upgrade Azure vyhledávání SKU. Můžete však vytvořit nový prostředek Azure search s požadovanou SKU, obnovení dat do nového prostředku a potom ji propojte s QnA Maker zásobníku.

1. Vytvoření nového prostředku Azure search na portálu Azure a vyberte požadované SKU.

    ![Vyhledávání prostředků QnA Maker Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Obnovte indexy z původní prostředku Azure search novým. Najdete v části obnovení zálohy ukázkový kód [zde](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Jakmile budou data obnovena, přejděte do nové zdroje vyhledávání systému Azure, vyberte **klíče**a poznamenejte si **název** a **klíč správce**.

    ![Klíče QnA Maker Azure search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Chcete-li propojit nový prostředek vyhledávání systému Azure se zásobníkem QnA Maker, přejděte na QnA Maker App service.

    ![Služby App Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Vyberte **nastavení aplikace** a nahraďte **AzureSearchName** a **AzureSearchAdminKey** polí z kroku 3.

    ![Nastavení služby App Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Restartujte službu aplikace.

    ![Restartování služby App Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použití Maker QnA rozhraní API](../Quickstarts/csharp.md)
