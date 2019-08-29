---
title: Upgrade služby QnA Maker – QnA Maker
titleSuffix: Azure Cognitive Services
description: Pokud chcete prostředky spravovat lépe, sdílejte nebo Upgradujte své služby QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/26/2019
ms.author: diberry
ms.openlocfilehash: ba9c2cd5a85e02a7dd4b1091a050d76e94861964
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147092"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Sdílení nebo upgrade služby QnA Maker
Pokud chcete prostředky spravovat lépe, sdílejte nebo Upgradujte své služby QnA Maker. 

Po počátečním vytvoření můžete upgradovat jednotlivé součásti QnA Maker stacku. Podívejte se na podrobnosti o závislých součástech a [](https://aka.ms/qnamaker-docs-capacity)výběru SKU.

## <a name="share-existing-services-with-qna-maker"></a>Sdílejte existující služby s QnA Maker

QnA Maker vytvoří několik prostředků Azure. Aby se snížila úroveň správy a výhoda sdílení nákladů, použijte následující tabulku, která vám pomůže pochopit, co můžete a nemůžete sdílet:

|Služba|Sdílení|Reason|
|--|--|--|
|Cognitive Services|X|Není možné podle návrhu|
|Plán služby App Service|✔|Pevné místo na disku přidělené pro plán služby App Service. Pokud jiné aplikace, sdílející stejný plán App Service, využijete významné místo na disku, v Qnamakerem App Service dojde k problémům.|
|App Service|X|Není možné podle návrhu|
|Application Insights|✔|Může být sdíleno|
|Služba vyhledávání|✔|1. `testkb` je rezervovaný název služby qnamakerem, kterou nemůžou použít jiní uživatelé.<br>2. Mapování synonym podle názvu `synonym-map` je vyhrazeno pro službu qnamakerem.<br>3. Počet publikovaných aktualizací KB je omezený na úrovni služby vyhledávání. Pokud jsou dostupné bezplatné indexy, můžou je používat i jiné služby.|


## <a name="upgrade-qna-maker-management-sku"></a>Aktualizace SKU správy QnA Maker

Pokud potřebujete mít ve znalostní bázi více otázek a odpovědí za rámec vaší aktuální úrovně, upgradujte si cenovou úroveň služby QnA Maker. 

Postup při upgradu SKU QnA Maker Management:

1. V Azure Portal otevřete prostředek QnA Maker a vyberte **cenovou úroveň**.

    ![Prostředek QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Zvolte odpovídající SKU a stiskněte **Vybrat**.

    ![Ceny QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Upgradovat službu App Service

 Pokud vaše znalostní báze potřebuje k obsluze více požadavků z klientské aplikace, upgradujte si cenovou úroveň služby App Service.

Službu App Service můžete [škálovat směrem nahoru](https://docs.microsoft.com/azure/app-service/manage-scale-up) nebo dolů.

1. V Azure Portal přejděte do prostředku App Service a podle potřeby vyberte možnosti **horizontálního** navýšení nebo **snížení** kapacity.

    ![QnA Maker škálování služby App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Aktualizace služby Azure Search

Pokud plánujete, že máte spoustu znalostní báze, upgradujte si cenovou úroveň služby Azure Search. 

V současné době není možné provést místní upgrade skladové položky služby Azure Search. Můžete ale vytvořit nový prostředek služby Azure Search s požadovanou skladovou jednotkou, obnovit data do nového prostředku a pak ho propojit s QnA Makerm zásobníkem.

1. Vytvořte nový prostředek Azure Search v Azure Portal a vyberte požadovanou SKU.

    ![Prostředek služby Azure Search QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Obnovte indexy z původního prostředku Azure Search do nového. [Tady](https://github.com/pchoudhari/QnAMakerBackupRestore)najdete ukázkový kód pro obnovení zálohování.

3. Po obnovení dat přejděte na nový prostředek služby Azure Search, vyberte **klíče**a poznamenejte si **název** a **klíč správce**.

    ![QnA Maker klíčů pro hledání Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Pokud chcete propojit nový prostředek služby Azure Search se zásobníkem QnA Maker, přečtěte si do QnA Maker App Service.

    ![QnA Maker AppService](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Vyberte **nastavení aplikace** a v kroku 3 nahraďte pole **AzureSearchName** a **AzureSearchAdminKey** .

    ![Nastavení AppService QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Restartujte služby App service.

    ![QnA Maker AppService restartování](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použít rozhraní API služby QnA Maker](../Quickstarts/csharp.md)
