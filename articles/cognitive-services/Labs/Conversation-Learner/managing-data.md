---
title: Správa uživatelských dat pomocí Conversation Learner-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se spravovat data uživatelů pomocí Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 857e899764d284e2d78f1172fa8eeac04c57d618
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705198"
---
# <a name="managing-user-data"></a>Správa uživatelských dat

Tato stránka popisuje, co Conversation Learner protokoly cloudových služeb při provádění dialogových oken s koncovými uživateli.  Také popisuje, jak přidružit protokoly Conversation Learner s ID uživatelů, abyste mohli načíst nebo odstranit všechny protokoly přidružené ke konkrétnímu uživateli.

## <a name="overview-of-end-user-data-logging"></a>Přehled protokolování dat koncových uživatelů

Ve výchozím nastavení protokoluje cloudová služba Conversation Learner interakce mezi koncovými uživateli a robotem.  Tyto protokoly jsou důležité pro vylepšení robota, což vám umožní identifikovat případy, kde robot extrahovali nesprávnou entitu nebo vybrali nesprávnou akci.  Tyto chyby se pak dají opravit tak, že na stránce "dialogová okna protokolu" v uživatelském rozhraní kliknete, provedete opravy a uložíte tuto opravenou dialogovou sadu jako nový vlakový dialog. Další informace najdete v kurzu "dialogová okna protokolu".

## <a name="how-to-disable-logging"></a>Postup zakázání protokolování

Můžete řídit, jestli budou konverzace s koncovými uživateli na stránce nastavení pro model Conversation Learner.  Pro "log konverzace" existuje Zaškrtávací políčko.  Když toto políčko zrušíte, konverzace s koncovými uživateli se nebudou protokolovat.

## <a name="what-is-logged"></a>Co je protokolováno 

V dialogových oknech protokolu Conversation Learner ukládá vstup uživatele, hodnoty entit, vybrané akce a časová razítka pro každé vypnutí.  Tyto protokoly se ukládají po určitou dobu a pak se zahodí (podrobnosti najdete na stránce s informacemi o výchozí hodnotě a hranicích).  

Conversation Learner pro každé protokolované dialogové okno vytvoří jedinečné ID.  Conversation Learner *neukládá identifikátor* uživatele s protokolovanými dialogy.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Přidružení protokolovaných dialogových oken s ID uživatele

Často je důležité, aby bylo možné přidružit přihlášená dialogová okna s ID uživatele, například aby bylo možné načíst nebo odstranit přihlášené dialogy od určitého uživatele.  Vzhledem k tomu, že Conversation Learner neukládá identifikátor uživatele, je nutné toto přidružení udržovat v kódu vývojáře.  

Pokud chcete vytvořit toto mapování, Získejte ID přihlášeného dialogu v `EntityDetectionCallback`. potom v úložišti robota uložte přidružení mezi ID uživatele a tímto protokolem.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>Hlavičky pro volání HTTP

V každém volání HTTP přidejte následující hlavičku:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

kde `<LUIS_AUTHORING_KEY>` je Luis Authoring Key, který slouží k přístupu k aplikacím Conversation Learner.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Jak získat nezpracovaná data pro protokolovaný dialog

Chcete-li získat nezpracovaná data pro dialogové okno protokolu, můžete použít toto volání HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Kde `<appId>` je identifikátor GUID tohoto Conversation Learner modelu a `<logDialgoId>` je ID dialogu protokolu, který chcete načíst.  

> [!NOTE]
> Dialogová okna protokolu může upravit vývojář a pak se ukládají jako výuková dialogová okna.  Po dokončení Conversation Learner ukládá do dialogového okna výuka ID dialogového okna protokolu "Source".  Kromě toho může být dialogové okno s výukou v uživatelském rozhraní "větvení". Pokud má dialogové okno vlaku přidružené ID dialogu zdrojového protokolu, budou se větve z tohoto dialogového okna tohoto vlaku označovat stejným ID dialogu protokolu.

Pokud chcete získat všechna dialogová okna, která byla odvozena z dialogu protokolu, postupujte podle těchto kroků.

Nejdřív načtěte všechna dialogová okna s výukou:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Kde `<appId>` je identifikátor GUID tohoto Conversation Learnerho modelu.  

Vrátí všechny dialogy s výukou.  Vyhledejte v tomto seznamu přidružená `sourceLogDialogId`a Všimněte si přidruženého `trainDialogId`. 

Do jednoho výukového dialogového okna podle ID:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Kde `<appId>` je identifikátor GUID tohoto Conversation Learner modelu a `<trainDialogId>` je ID dialogového okna s vlakem, který chcete načíst.  

## <a name="how-to-delete-a-logged-dialog"></a>Postup odstranění protokolovaného dialogu

Pokud chcete odstranit dialogové okno protokolu s daným ID, můžete použít toto volání HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Kde `<appId>` je identifikátor GUID tohoto Conversation Learner modelu a `<logDialogId>` je ID dialogu protokolu, který chcete odstranit. 

Pokud chcete odstranit dialogové okno výuka s daným ID, můžete použít toto volání HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Kde `<appId>` je identifikátor GUID tohoto Conversation Learner modelu a `<trainDialogId>` je ID dialogového okna s vlakem, které chcete odstranit. 
