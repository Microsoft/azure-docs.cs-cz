---
title: Upgrade služby QnA Maker – QnA Maker
titleSuffix: Azure Cognitive Services
description: Můžete upgradovat po vytvoření počátečního jednotlivých komponent nástroje QnA Maker.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: e37a903112f30917ff5051305bb2144981de1c9d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883266"
---
# <a name="upgrade-your-qna-maker-service"></a>Upgrade služby QnA Maker
Můžete upgradovat po vytvoření počátečního jednotlivých komponent nástroje QnA Maker. Prohlédněte si podrobnosti o výběr SKU a závislé komponenty [tady](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>Upgrade nástroje QnA Maker správu SKU

Když budete chtít mít další otázky a odpovědi ve znalostní bázi, upgradujte mimo aktuální úroveň, vaše služba QnA Maker cenovou úroveň. 

Postup upgradu nástroje QnA Maker správu SKU:

1. Přejděte do prostředku nástroje QnA Maker na webu Azure Portal a vyberte **cenová úroveň**.

    ![Nástroj QnA Maker prostředků](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Zvolte odpovídající SKU a stiskněte klávesu **vyberte**.

    ![Nástroje QnA maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Upgradovat službu App service

 Když znalostní báze potřebuje více požadavků z aplikace pro klienta, upgrade vaše cenová úroveň služby app service.

Je možné [vertikálně navýšit kapacitu](https://docs.microsoft.com/azure/app-service/web-sites-scale) nebo vertikálně snížit kapacitu služby App service.

1. Přejít k prostředku App service na webu Azure Portal a vyberte **vertikálně navýšit kapacitu** nebo **vertikálně snížit kapacitu** možnosti podle potřeby.

    ![Škálování služby app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Upgradovat službu Azure Search

Když budete chtít mít mnoho znalostních bází, upgradujte cenovou úroveň služby Azure Search. 

Aktuálně není možné provádět v místě inovace Azure vyhledávání SKU. Můžete však vytvořit nový prostředek služby Azure search s požadovanou SKU, obnovení dat na nový prostředek a potom ji propojte s zásobníku QnA Maker.

1. Vytvoření nového prostředku Azure search na webu Azure Portal a vyberte požadovanou SKU.

    ![Nástroj QnA Maker Azure hledání prostředků](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Obnovení k novým indexy z váš původním prostředek Azure search. Ukázkový kód pro obnovení zálohy [tady](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Po obnovení dat, přejděte do nového prostředku Azure search, vyberte **klíče**a poznamenejte si **název** a **klíč správce**.

    ![Klíče nástroje QnA Maker Azure search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Odkaz nový prostředek Azure search na zásobníku QnA Maker, přejděte na nástroje QnA Maker App service.

    ![Nástroj QnA Maker služby App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Vyberte **nastavení aplikace** a nahraďte **AzureSearchName** a **AzureSearchAdminKey** polí z kroku 3.

    ![Nástroj QnA Maker nastavení služby App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Restartujte služby App service.

    ![Nástroj QnA Maker restartování služby App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použití rozhraní API nástroje QnA Maker](../Quickstarts/csharp.md)
