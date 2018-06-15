---
title: Vytvoření QnA robota robota službě Azure - kognitivní služeb Azure | Microsoft Docs
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: f2721dfa9a0922ee4a5af1eb66fd4506feb94d28
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343877"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Vytvořit QnA robota se službou Azure robota
Tento kurz vás provede procesem vytváření QnA robota službou Azure robota na portálu Azure.

## <a name="prerequisite"></a>Požadavek
Než vytvoříte, postupujte podle kroků v [vytvořit znalostní bázi]() vytvoření služby QnA Maker s otázky a odpovědi.

Robota odpoví na dotazy z znalostní bázi knowledge base, kterou jste vytvořili pomocí QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Vytvoření QnA robota
1. V [portál Azure](https://portal.azure.com), vyberte **vytvořit** nový prostředek v nabídce okna a potom vyberte **zobrazit všechny**.

    ![vytvoření služby robota](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. Do vyhledávacího pole Hledat **webové aplikace robota**.

    ![Výběr robota služby](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. V **okno služby robota**, zadejte požadované informace a vyberte **vytvořit**. Tím se vytvoří a nasadí službu robota s QnAMakerDialog do Azure.

    - Nastavit **název aplikace** na název vaší robota. Název se používá jako subdoménou, když vaše robota je nasazená do cloudu (například mynotesbot.azurewebsites.net).
    - Vyberte předplatné, skupinu prostředků, plán služby App service a umístění.
    - Vyberte **otázku a odpověď** šablony (Node.js nebo C#) pro pole robota šablony.
    - Zaškrtněte políčko potvrzení právní upozornění. Právní upozornění. podmínky jsou uvedeny níže políčko.

        ![Výběr robota služby](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. Potvrďte, že byla nasazena službu robota.

    - Vyberte **oznámení** (na ikonu zvonku umístěné podél horního okraje portálu Azure). Oznámení se změní ze **nasazení začalo** k **nasazení bylo úspěšné**.
    - Po oznámení změn na **nasazení bylo úspěšné**, vyberte **přejděte k prostředku** na tomto oznámení.

## <a name="chat-with-the-bot"></a>Chat s robota
Výběr **přejděte k prostředku** přejdete do okna prostředků robota.

Po registraci robota klikněte na tlačítko **Test v webového chatu** otevřete podokno webového chatu. Zadejte "hello" v webového chatu.

![QnA robota webového chatu](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Robota odpoví "nastavte QnAKnowledgebaseId a QnASubscriptionKey v nastavení aplikace. Další informace o získání je v https://aka.ms/qnaabssetup". Tato odezva potvrdí vaší QnA robota přijal zprávu, že neexistuje žádná znalostní báze QnA Maker ještě s ním spojená. To udělat v dalším kroku.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Připojení k robota QnA Maker znalostní báze

1. Otevřete **nastavení aplikace** a upravit **QnAKnowledgebaseId**, **QnAAuthKey**a **QnAEndpointHostName** pole tak, aby obsahovala hodnoty QnA Maker znalostní báze.

    ![Nastavení aplikace](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. Získání vaše ID znalostní báze knowledge base, hostitelské adresy url a klíč koncového bodu na kartě nastavení znalostní báze v https://qnamaker.ai.
    - Přihlaste se k [QnA Maker](https://qnamaker.ai)
    - Přejděte do znalostní báze
    - Klikněte na **nastavení** karta
    - **Publikování** znalostní báze, pokud není neudělali

    ![Hodnoty QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Pokud se chcete připojit verze preview znalostní báze s robota QnA, nastavte hodnotu **Ocp-Apim-Subscription-Key** k **QnAAuthKey**. Ponechte **QnAEndpointHostName** prázdný.

## <a name="test-the-bot"></a>Testovací robota
Na portálu Azure klikněte na **testů ve webové Chat** k testování robota. 

![Robota QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Vaše QnA robota teď odpovědi ze znalostní báze.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Integrovat QnA Maker a LEOŠ](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Další informace najdete v tématech

- [Správa znalostní báze](https://qnamaker.ai)
- [Povolit vaší robota v různé kanály](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
