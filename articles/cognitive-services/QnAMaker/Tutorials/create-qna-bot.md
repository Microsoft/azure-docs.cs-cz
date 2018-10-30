---
title: 'Kurz: QnA robotů pomocí služeb Azure Bot Service – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Tento kurz vás provede vytváření robota QnA s Azure Bot service v3 na portálu Azure portal.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker`
ms.topic: article
ms.date: 10/25/2018
ms.author: tulasim
ms.openlocfilehash: 19c56cf05e307deca52808b0eeba65b8949ffc0b
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212729"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>Kurz: Vytváření QnA robotů s využitím Azure Bot Service v3

Tento kurz vás provede postupem vytvoření QnA robotů s Azure Bot service v3 v [webu Azure portal](https://portal.azure.com) bez psaní kódu. Připojení k robotovi publikované znalostní bázi (KB) je stejně snadné jako změna nastavení aplikace robota. 

> [!Note] 
> Toto téma se týká verze 3 sady SDK robota. Můžete najít verzi 4 [tady](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs). 

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření Azure Bot Service pomocí šablony nástroje QnA Maker
> * Chat s roboty k ověření, že kód funguje 
> * Připojit robotu publikovaná KB
> * Testování bot dotazu

Pro účely tohoto článku můžete použít bezplatné nástroje QnA Maker [služby](../how-to/set-up-qnamaker-service-azure.md).

## <a name="prerequisites"></a>Požadavky

Musíte mít publikované znalostní báze pro účely tohoto kurzu. Pokud nemáte, postupujte podle kroků v [vytvoření znalostní báze](../How-To/create-knowledge-base.md) vytvořte službu QnA Maker s otázkami a odpověďmi.

## <a name="create-a-qna-bot"></a>Vytváření robotů QnA

1. Na webu Azure Portal vyberte **Vytvořit prostředek**.

    ![vytvoření služby bot](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. Do vyhledávacího pole vyhledejte **Web App Bot**.

    ![Výběr služby bot](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. V **Bot Service** (Služba robota) zadejte požadované informace:

    - Nastavte **název aplikace** na název svého robota. Název se používá jako subdoménu svého robota nasazené do cloudu (například mynotesbot.azurewebsites.net).
    - Vyberte předplatné, skupinu prostředků, plán služby App service a umístění.

4. Použití šablony verze 3, vyberte verzi sady SDK **SDK v3** a sady SDK jazyka **jazyka C#** nebo **Node.js**.

    ![nastavení sady sdk robota](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Vyberte **otázku a odpověď** šablon pro pole Bot šablony a potom uložte nastavení šablony tak, že vyberete **vyberte**.

    ![Výběr služby bot](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Zkontrolujte nastavení a pak vyberte **vytvořit**. Tím se vytvoří a nasadí bot service se do Azure.

    ![Výběr služby bot](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Potvrďte, že byla nasazena bot service.

    - Vyberte **oznámení** (ikona zvonku umístěné podél horního okraje na webu Azure portal). Oznámení se změní z **nasazení začalo** k **nasazení bylo úspěšné**.
    - Po oznámení změn **nasazení bylo úspěšné**vyberte **přejít k prostředku** na toto oznámení.

## <a name="chat-with-the-bot"></a>Chat s roboty

Výběr **přejít k prostředku** přejdete do bodu robotů také prostředků.

Vyberte **testování ve Web Chat** a otevřete tak podokno Web Chat. Zadejte "hi" Web Chat.

![Nástroj QnA bot webového chatu](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Robot odpoví "nastavte QnAKnowledgebaseId a QnASubscriptionKey v nastavení aplikace. Tato odpověď potvrdí, že váš robot QnA přijal zprávu, ale neexistuje žádný nástroj QnA Maker znalostní báze knowledge base s ním spojená ještě. 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Připojte se k robota znalostní báze QnA Maker

1. Otevřít **nastavení aplikace** a upravit **QnAKnowledgebaseId**, **QnAAuthKey**a **QnAEndpointHostName** pole tak, aby obsahovala hodnoty znalostní báze QnA Maker.

    ![Nastavení aplikace](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. Na kartě nastavení znalostní báze na portálu pro nástroj QnA Maker získáte vaše ID znalostní báze knowledge base, adresa url hostitele a klíče koncového bodu.

    - Přihlaste se k [QnA Maker](https://qnamaker.ai)
    - Přejděte do znalostní báze
    - Vyberte **nastavení** kartu
    - **Publikování** znalostní báze, pokud ještě neprovedli

    ![Nástroj QnA Maker hodnoty](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Pokud chcete připojit verze preview ve znalostní bázi s robotem QnA, nastavte hodnotu **Ocp-Apim-Subscription-Key** k **QnAAuthKey**. Nechte **QnAEndpointHostName** prázdný.

## <a name="test-the-bot"></a>Testování robota

Na webu Azure Portal, vyberte **testování ve Web Chat** otestovat robota. 

![Nástroj QnA Maker robota](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Váš robot QnA odpovědi ze znalostní báze.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s robotem v tomto kurzu, odeberte robota na webu Azure Portal. Služby bot patří:

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
