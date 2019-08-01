---
title: Nástroj QnA bot – Azure Bot Service – QnA Maker
titleSuffix: Azure Cognitive Services
description: Vytvořte QnA chat robota ze stránky publikování pro existující znalostní bázi. Tato robot používá sadu robot Framework SDK v4. Nemusíte psát žádný kód pro sestavování robota, veškerý kód je k dispozici za vás.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: 4bb987a5a091871bec2c0cc8cec6d9ab804bb244
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697995"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Kurz: Vytvoření QnA robota s Azure Bot Service v4

Vytvořte QnA chat robota ze stránky **publikování** pro existující znalostní bázi. Tato robot používá sadu robot Framework SDK v4. Nemusíte psát žádný kód pro sestavování robota, veškerý kód je k dispozici za vás.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření Azure Bot Service z existující znalostní báze
> * Chat s roboty k ověření, že kód funguje 

## <a name="prerequisites"></a>Požadavky

Musíte mít publikované znalostní báze pro účely tohoto kurzu. Pokud ho nemáte, postupujte podle kroků v kurzu [Vytvoření a odpověď z znalostní báze KB](create-publish-query-in-portal.md) a vytvořte QnA maker znalostní bázi s dotazy a odpověďmi.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Vytváření robotů QnA

Vytvořte robot jako klientskou aplikaci pro znalostní bázi. 

1. Na portálu QnA Maker otevřete stránku **publikovat** a publikujte znalostní bázi. Vyberte **vytvořit robot**. 

    ![Na portálu QnA Maker otevřete stránku publikovat a publikujte znalostní bázi. Vyberte vytvořit robot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Azure Portal se otevře s konfigurací robota pro vytvoření.

1.  Zadejte nastavení pro vytvoření robota:

    |Nastavení|Value|Účel|
    |--|--|--|
    |Bot name (Název robota)|`my-tutorial-kb-bot`|Toto je název prostředku Azure pro robota.|
    |Subscription|Viz účel.|Vyberte stejné předplatné, jako jste použili k vytvoření prostředků QnA Maker.|
    |Resource group|`my-tutorial-rg`|Skupina prostředků použitá pro všechny prostředky Azure související s robotem.|
    |Location|`west us`|Umístění prostředku Azure bot.|
    |Cenová úroveň|`F0`|Úroveň Free pro Azure bot Service.|
    |App name (Název aplikace)|`my-tutorial-kb-bot-app`|Toto je webová aplikace, která podporuje pouze robot. Nemělo by se jednat o stejný název aplikace, protože vaše služba QnA Maker už používá. Sdílení webové aplikace QnA Maker s jakýmkoli jiným prostředkem není podporováno.|
    |Jazyk sady SDK|C#|Toto je základní programovací jazyk používaný sadou robot Framework SDK. Vaše volby jsou [C#](https://github.com/Microsoft/botbuilder-dotnet) nebo [Node. js](https://github.com/Microsoft/botbuilder-js).|
    |QnA ověřovací klíč|**Neměnit**|Tato hodnota je vyplněna za vás.|
    |Plán služby App Service / umístění|**Neměnit**|Pro tento kurz není umístění důležité.|
    |Azure Storage|**Neměnit**|Data konverzace se ukládají v Azure Storagech tabulkách.|
    |Application Insights|**Neměnit**|Do Application Insights se pošle protokolování.|
    |ID aplikace Microsoftu|**Neměnit**|Je nutné zadat uživatele a heslo služby Active Directory.|

    ![Pomocí těchto nastavení vytvořte robota znalostní báze Knowledge Base.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Počkejte několik minut, dokud oznámení o procesu vytváření robotů neproběhne úspěšně.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Chat s roboty

1. V Azure Portal otevřete nový prostředek robota z oznámení. 

    ![V Azure Portal otevřete nový prostředek robota z oznámení.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. V **nástroji pro správu robotů**vyberte **test na webu chat** a `How large can my KB be?`zadejte:. Robot bude reagovat na: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Otestujte nový robot znalostní báze.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Další informace o Azure roboty najdete v tématu [použití QnA maker k zodpovězení otázek](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s robotem v tomto kurzu, odeberte robota na webu Azure Portal. 

Pokud jste pro prostředky robota vytvořili novou skupinu prostředků, odstraňte skupinu prostředků. 

Pokud jste nevytvořili novou skupinu prostředků, potřebujete najít prostředky přidružené k robotovi. Nejjednodušší způsob je hledat podle názvu aplikace robot a robot. Mezi prostředky robota patří:

* Plán služby App Service
* Vyhledávací služba
* Služby Cognitive Services
* App service
* Volitelně může také zahrnovat služby application insights a úložiště pro application insights data


## <a name="related-to-qna-maker-bots"></a>Související s QnA Maker roboty

* QnA Maker Help robot, který se používá na portálu QnA Maker, je k dispozici jako [Ukázka robota](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support).
    ![Ikona robota s QnA Makerem pomocníka je červený robot](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Zdravotní péče roboty](https://docs.microsoft.com/HealthBot/qna_model_howto) QnA Maker použít jako jeden z jejich [jazykových modelů](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Koncept: znalostní báze](../concepts/knowledge-base.md)

## <a name="see-also"></a>Další informace najdete v tématech

- [Správa znalostní báze](https://qnamaker.ai)
- [Umožňují robotům v různých kanálech](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
