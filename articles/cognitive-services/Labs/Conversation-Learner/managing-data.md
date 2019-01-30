---
title: Správa dat uživatele s použitím konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o správě dat uživatele s Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83c7e808e48733487e84d668236cdb327c21c44c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249074"
---
# <a name="managing-user-data"></a>Správa dat uživatele

Tato stránka popisuje, co protokoly služeb cloudu konverzace Learner při provádění dialogová okna s koncovými uživateli.  Také popisuje, jak přidružit protokoly Learner konverzace s ID uživatelů, tak, že můžete načíst nebo odstranit všechny protokoly, které jsou spojeny s konkrétním uživatelem.

## <a name="overview-of-end-user-data-logging"></a>Přehled protokolování dat koncových uživatelů

Ve výchozím nastavení cloudové službě konverzace Learner protokoly interakcí mezi koncovým uživatelům a váš robot.  Tyto protokoly jsou důležité pro zlepšení vašeho robota, umožní vám identifikovat případech, kdy váš robot extrahovat nesprávné entity nebo vybrané nesprávné akce.  Když přejdete na stránku "Protokolu dialogů" uživatelské rozhraní, opravy a ukládání tohoto dialogového okna opravený jako dialogové okno Nový trénování pak lze opravit tyto chyby. Další informace najdete v kurzu o "Protokolu dialozích."

## <a name="how-to-disable-logging"></a>Zákaz protokolování

Můžete řídit, jestli konverzace s koncovými uživateli se na stránce "Nastavení" pro váš model Learner konverzace.  Je zaškrtávací políčko pro "Protokolu konverzací."  Zrušením zaškrtnutí tohoto políčka, se nebudou protokolovat konverzace s koncovými uživateli.

## <a name="what-is-logged"></a>Co je protokolováno 

V dialogových oknech protokolu konverzace Learner ukládá vstup uživatele, hodnoty entit, vybrané akce a časová razítka pro každý na řadě vy.  Tyto protokoly jsou uloženy pro určitou dobu a potom je zahozen (podrobnosti najdete na stránce nápovědy na "výchozí hodnoty a hranice").  

Konverzace Learner vytvoří jedinečné ID pro každého přihlášeného dialogového okna.  Konverzace Learner nemá *není* ukládání identifikátor uživatele pomocí protokolu dialogová okna.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Dialogová okna přidružení protokolu s ID uživatele

Často je potřeba mít možnost zaznamenané dialogová okna přidružit ID uživatele – například, bude moct načíst nebo odstranit zaznamenané dialogová okna od konkrétního uživatele.  Konverzace Learner neukládá identifikátoru uživatele, musí toto přidružení zachovat kódem pro vývojáře.  

K vytvoření tohoto mapování, získejte ID dialogu zaznamenané v `EntityDetectionCallback`; v úložišti svého robota, uložit přidružení mezi ID uživatele a toto dialogové okno Protokolované.  

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

V každé volání rozhraní HTTP přidejte následující hlavičky:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

kde `<LUIS_AUTHORING_KEY>` je LUIS vytváření klíč se používá pro přístup k aplikacím Learner vaší konverzace.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Získání nezpracovaných dat pro přihlášeného dialogového okna

K získání nezpracovaných dat pro dialogové okno Protokol, můžete použít toto volání HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Kde `<appId>` je identifikátor GUID pro tento model Learner konverzace a `<logDialgoId>` je ID protokolu dialogové okno, které chcete načíst.  

> [!NOTE]
> Dialogová okna protokolu může upravit vývojář a pak uloženy jako trénování dialogová okna.  Když to uděláte, konverzace Learner ukládá ID dialogu "zdroj" protokolu s dialogovým oknem trénování.  Kromě toho trénování dialogového okna je možné "větvit" v uživatelském rozhraní; Pokud dialogové okno trénovat na základě modelu má ID přidružena zdrojová protokolu dialogové okno, pak větve z dialogového okna trénování budou označeny se stejným ID. protokolu dialogového okna

Získat všechna dialogová okna trénování, které byly získány z dialogového okna protokolu, postupujte podle těchto kroků.

Nejdřív načtěte všechna dialogová okna trénovat na základě modelu:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Kde `<appId>` je identifikátor GUID pro tento model Learner konverzace.  

Vrátí všechna dialogová okna trénování.  Hledat v seznamu pro přidružený `sourceLogDialogId`a Poznámka: přidruženou `trainDialogId`. 

Do jediné trénování dialogové okno podle ID:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Kde `<appId>` je identifikátor GUID pro tento model Learner konverzace a `<trainDialogId>` je ID trénování dialogového okna, které chcete načíst.  

## <a name="how-to-delete-a-logged-dialog"></a>Jak odstranit zaznamenané dialogového okna

Pokud chcete odstranit dialogové okno Protokol zadané jeho ID, můžete použít toto volání HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Kde `<appId>` je identifikátor GUID pro tento model Learner konverzace a `<logDialogId>` je ID dialogové okno protokolu, které chcete odstranit. 

Pokud chcete odstranit dialogové okno trénování zadané jeho ID, můžete použít toto volání HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Kde `<appId>` je identifikátor GUID pro tento model Learner konverzace a `<trainDialogId>` je ID dialogu trénování chcete odstranit. 
