---
title: Nástroj QnA robotů pomocí služeb Azure Bot Service – QnA Maker
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: nstulasi
ms.openlocfilehash: 0c6feb67b273ec30afba89f2d4d9b59a8a9f5acf
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731407"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Vytváření robotů QnA pomocí služby Azure Bot Service
Tento kurz vás provede vytváření robota QnA pomocí služby Azure Bot service na portálu Azure portal.

## <a name="prerequisite"></a>Požadavek
Před sestavením, postupujte podle kroků v [vytvoření znalostní báze](../How-To/create-knowledge-base.md) vytvořte službu QnA Maker s otázkami a odpověďmi.

Robot odpoví na dotazy ze znalostní báze, který jste vytvořili prostřednictvím QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Vytváření robotů QnA
1. V [webu Azure portal](https://portal.azure.com)vyberte **vytvořit** nový prostředek v okně s nabídkou a pak vyberte **zobrazit všechny**.

    ![vytvoření služby bot](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. Do vyhledávacího pole vyhledejte **Web App Bot**.

    ![Výběr služby bot](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. V **Bot Service okno**, zadejte požadované informace a vyberte **vytvořit**. Tím se vytvoří a nasadí do Azure bot service s QnAMakerDialog.

    - Nastavte **název aplikace** na název svého robota. Název se používá jako subdoménu svého robota nasazené do cloudu (například mynotesbot.azurewebsites.net).
    - Vyberte předplatné, skupinu prostředků, plán služby App service a umístění.
    - Vyberte **otázek a odpovědí** (Node.js, nebo C#) šablona pro pole Bot šablony.
    - Zaškrtněte políčko potvrzení právních upozornění. Podmínky právních upozornění jsou níže na zaškrtávací políčko.

        ![Výběr služby bot](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. Potvrďte, že byla nasazena bot service.

    - Vyberte **oznámení** (ikona zvonku umístěné podél horního okraje na webu Azure portal). Oznámení se změní z **nasazení začalo** k **nasazení bylo úspěšné**.
    - Po oznámení změn **nasazení bylo úspěšné**vyberte **přejít k prostředku** na toto oznámení.

## <a name="chat-with-the-bot"></a>Chat s roboty
Výběr **přejít k prostředku** přejdete do okna prostředků bodu robotů také.

Jakmile je registrovaný robota, klikněte na tlačítko **testování ve Web Chat** a otevřete tak podokno Web Chat. Zadejte "hello" v Web Chat.

![Nástroj QnA bot webového chatu](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Robot odpoví "nastavte QnAKnowledgebaseId a QnASubscriptionKey v nastavení aplikace. Zjistěte, jak je na https://aka.ms/qnaabssetup". Tato odpověď potvrdí, že váš robot QnA přijal zprávu, ale neexistuje žádný nástroj QnA Maker znalostní báze knowledge base s ním spojená ještě. To udělejte v dalším kroku.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Připojte se k robota znalostní báze QnA Maker

1. Otevřít **nastavení aplikace** a upravit **QnAKnowledgebaseId**, **QnAAuthKey**a **QnAEndpointHostName** pole tak, aby obsahovala hodnoty znalostní báze QnA Maker.

    ![Nastavení aplikace](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. Získat vaše ID znalostní báze knowledge base, adresa url hostitele a klíče koncového bodu na kartě nastavení znalostní báze v https://qnamaker.ai.
    - Přihlaste se k [QnA Maker](https://qnamaker.ai)
    - Přejděte do znalostní báze
    - Klikněte na **nastavení** kartu
    - **Publikování** znalostní báze, pokud ještě neprovedli

    ![Nástroj QnA Maker hodnoty](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Pokud chcete připojit verze preview ve znalostní bázi s robotem QnA, nastavte hodnotu **Ocp-Apim-Subscription-Key** k **QnAAuthKey**. Nechte **QnAEndpointHostName** prázdný.

## <a name="test-the-bot"></a>Testování robota
Na webu Azure Portal, klikněte na **testování ve Web Chat** otestovat robota. 

![Nástroj QnA Maker robota](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Váš robot QnA nyní odpovědi ze znalostní báze.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Integrace nástroje QnA Maker a LUIS](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Další informace najdete v tématech

- [Správa znalostní báze](https://qnamaker.ai)
- [Umožňují robotům v různých kanálech](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
