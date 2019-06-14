---
title: Nástroj QnA bot – Azure Bot Service – QnA Maker
titleSuffix: Azure Cognitive Services
description: Vytvoření chatovací robot QnA ze stránky publikování pro existující znalostní báze. Tato bot používá v4 Bot Framework SDK. Nemusíte psát žádný kód, sestavit robota, veškerý kód je k dispozici za vás.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/11/2019
ms.author: tulasim
ms.openlocfilehash: b3bae01d65685aa9ea7bfc95d1f1454741d37b5e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053235"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Kurz: Vytváření QnA robotů s využitím Azure Bot službu v4

Vytvoření je chatovací robot QnA z **publikovat** stránky pro existující znalostní báze. Tato bot používá v4 Bot Framework SDK. Nemusíte psát žádný kód, sestavit robota, veškerý kód je k dispozici za vás.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření Azure Bot Service z existující znalostní báze
> * Chat s roboty k ověření, že kód funguje 

## <a name="prerequisites"></a>Požadavky

Musíte mít publikované znalostní báze pro účely tohoto kurzu. Pokud nemáte, postupujte podle kroků v [vytvořit a odpověď z KB](create-publish-query-in-portal.md) kurzu a vytvořit nástroje QnA Maker znalostní bázi s dotazy a odpovědi.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Vytváření robotů QnA

Vytváření robotů jako klientskou aplikaci pro znalostní báze. 

1. Nástroj QnA Maker portal, přejděte na **publikovat** stránce a publikovat znalostní báze. Vyberte **vytváření robotů**. 

    ![Na portálu QnA Maker přejděte na stránku publikovat a publikovat znalostní báze. Vyberte vytváření robotů.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Na webu Azure portal otevře s konfigurací vytváření robotů.

1.  Zadejte nastavení pro vytvoření robota:

    |Nastavení|Hodnota|Účel|
    |--|--|--|
    |Bot name (Název robota)|`my-tutorial-kb-bot`|Toto je název prostředku Azure pro robota.|
    |Předplatné|Podívejte se na účel.|Vyberte stejné předplatné jako jste použili k vytvoření prostředků nástroje QnA Maker.|
    |Skupina prostředků|`my-tutorial-rg`|Skupina prostředků použitá pro všechny bot související prostředky Azure.|
    |Location|`west us`|Umístění bodu robotů také prostředků Azure.|
    |Cenová úroveň|`F0`|Pro službu Azure bot service na úrovni free.|
    |App name (Název aplikace)|`my-tutorial-kb-bot-app`|Toto je webová aplikace pro podporu svého robota pouze. Jak už používá vaše služba QnA Maker nemělo by to být stejný název aplikace. Nástroj QnA Maker webovou aplikaci pro sdílení obsahu s jakýmikoli prostředky se nepodporuje.|
    |Jazykové sady SDK|C#|Toto je základní programovací jazyk používá rozhraní bot framework SDK. Jsou vaše volby [ C# ](https://github.com/Microsoft/botbuilder-dotnet) nebo [Node.js](https://github.com/Microsoft/botbuilder-js).|
    |Nástroj QnA ověřovacím klíčem|**Neměnit**|Tato hodnota je vyplňují za vás.|
    |Plán App service/umístění|**Neměnit**|Umístění pro účely tohoto kurzu není důležité.|
    |Azure Storage|**Neměnit**|Konverzace data se ukládají do tabulek Azure Storage.|
    |Application Insights|**Neměnit**|Protokolování se odesílají do Application Insights.|
    |Microsoft App ID|**Neměnit**|Uživatel služby Active directory a heslo je povinné.|

    ![Vytváření robotů znalostní báze knowledge base s těmito nastaveními.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Počkejte pár minut, než oznámí úspěšné dokončení oznámení procesu vytváření robotů.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Chat s roboty

1. Na webu Azure Portal otevřete nový prostředek bot od oznámení. 

    ![Na webu Azure Portal otevřete nový prostředek bot od oznámení.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Z **Bot správu**vyberte **testování ve Web Chat** a zadejte: `How large can my KB be?`. Robot odpoví zprávou: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Otestujte novým robotem znalostní báze knowledge base.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Další informace o roboty Azure najdete v tématu [otázky, použijte nástroj QnA Maker odpovědět](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="related-to-qna-maker-bots"></a>Související s roboty QnA Maker

* Robot nápovědy nástroje QnA Maker, používaná na portálu QnA Maker je k dispozici jako [bot ukázka](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![Červené robot je ikona bot nápovědy nástroje QnA Maker](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Zdravotní péče robotů](https://docs.microsoft.com/HealthBot/qna_model_howto) použijte nástroj QnA Maker jako jeden z jejich [jazykových modelů](https://docs.microsoft.com/HealthBot/qna_model_howto).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s robotem v tomto kurzu, odeberte robota na webu Azure Portal. 

Pokud jste vytvořili pro prostředky bodu robotů také novou skupinu prostředků, odstraňte skupinu prostředků. 

Pokud jste nevytvořili novou skupinu prostředků, budete muset najít prostředky spojené s roboty. Nejjednodušší způsob je hledat podle názvu robotů a bot app. Bot prostředky zahrnují:

* Plán služby App Service
* Vyhledávací služba
* Služby Cognitive Services
* App service
* Volitelně může také zahrnovat služby application insights a úložiště pro application insights data

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Koncept: znalostní báze](../concepts/knowledge-base.md)

## <a name="see-also"></a>Další informace najdete v tématech

- [Správa znalostní báze](https://qnamaker.ai)
- [Umožňují robotům v různých kanálech](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
