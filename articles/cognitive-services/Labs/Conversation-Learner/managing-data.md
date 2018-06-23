---
title: Správa dat uživatele s konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Zjistěte, jak spravovat data uživatele s student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8d42f903559a1e07b42ded33972be4b552f21b5e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343229"
---
# <a name="managing-user-data"></a>Správa dat uživatele

Tato stránka popisuje, co protokoly cloudové službě konverzace student při provádění dialogová okna s koncovými uživateli.  Také popisuje, jak student konverzace protokoly přidružit ID uživatele, aby můžete načíst nebo odstranit všechny protokoly, které jsou přidružené k uživateli.

## <a name="overview-of-end-user-data-logging"></a>Přehled protokolování dat koncového uživatele

Ve výchozím nastavení zaznamená cloudové službě konverzace student interakce mezi koncovým uživatelům a vaše robota.  Tyto protokoly jsou důležité pro zlepšení vaší robota umožňuje identifikovat případech, kdy váš robota extrahovat nesprávný entity nebo vybrali nesprávný akce.  Tyto chyby může být vyřešen pak přejdete na stránku "Protokolu v dialogových oknech" uživatelského rozhraní, provádění oprav a ukládání toto dialogové okno opravené jako dialogové okno Nový train. Další informace najdete v tématu kurz týkající se "Protokolu dialogová okna."

## <a name="how-to-disable-logging"></a>Postup zakázání protokolování

Můžete určit, zda konverzace s koncoví uživatelé jsou na stránce "Nastavení" pro vaši aplikaci student konverzace.  Je zaškrtávací políčko pro "Protokolu konverzace."  Zrušením zaškrtnutí tohoto políčka, se nebudou protokolovat konverzace s koncovými uživateli.

## <a name="what-is-logged"></a>Co je protokolováno 

V dialogových oknech protokolu ukládá konverzace student uživatelský vstup, hodnot entity, vybrané akce a časová razítka pro každý zapnout.  Tyto protokoly jsou uložené dobu a potom je zahozen (podrobnosti najdete v části na stránce nápovědy na "Výchozí hodnota a hranice").  

Konverzace student vytvoří jedinečné ID pro každého přihlášeného dialogové okno.  Konverzace student nepodporuje *není* ukládání identifikátoru uživatele pomocí protokolu dialogová okna.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Dialogová okna přidružení protokolu s ID uživatele

Často je důležité, abyste mohli zaznamenané dialogová okna přidružit ID uživatel – třeba, aby možné načíst nebo odstranit zaznamenané dialogová okna z určitého uživatele.  Vzhledem k tomu, že konverzace student neukládá identifikátoru uživatele, je třeba zachovat kódem vývojáře pro toto přidružení.  

K vytvoření tohoto mapování získat číslo ID dialogu zaznamenané v `EntityDetectionCallback`; v úložišti vašeho robota uložit přidružení mezi ID uživatele a toto dialogové okno protokolu.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific datastore, store an association
    // bewteen your user identifier and this session ID.
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

## <a name="headers-for-http-calls"></a>Hlavičky pro volání protokolu HTTP

V každé níže volání protokolu HTTP přidejte následující hlavičky:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

kde `<LUIS_AUTHORING_KEY>` je LEOŠ vytváření klíč používaný pro přístup k aplikacím student vaší konverzace.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Jak získat nezpracovaná data pro přihlášeného dialogové okno

Pokud chcete získat nezpracovaná data pro dialogové okno protokolu, můžete toto volání protokolu HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Kde `<appId>` je identifikátor GUID pro tuto aplikaci student konverzace a `<logDialgoId>` je ID protokolu dialogového okna můžete obnovit.  

Všimněte si, že dialogová okna protokolu může být upravená vývojář a pak uloženy jako cvičení dialogová okna.  Když to uděláte, ukládá konverzace student ID dialogu "zdroj" protokolu s dialogu vlaku.  Další zobrazí se dialogové okno train můžete "vytvořit větve" v uživatelském rozhraní; Pokud train dialog má ID přidružena zdrojová protokolu dialogové okno, pak větví z tohoto dialogového okna train budou označeny se stejným ID. protokolu dialogové okno

Pokud chcete získat všechna dialogová okna train, které byly získány z dialogového okna protokolu, postupujte podle těchto kroků.

Nejdřív načtěte všechna dialogová okna train:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Kde `<appId>` je identifikátor GUID pro tuto aplikaci student konverzace.  

Tento příkaz vrátí všechna dialogová okna vlaku.  Hledat v seznamu pro přidruženého `sourceLogDialogId`a poznamenejte si přidruženého `trainDialogId`. 

Na jednu cvičení dialogové okno s ID:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Kde `<appId>` je identifikátor GUID pro tuto aplikaci student konverzace a `<trainDialogId>` je ID train dialogového okna můžete obnovit.  

## <a name="how-to-delete-a-logged-dialog"></a>Postup odstranění zaznamenané dialogové okno

Pokud chcete odstranit dialogové okno protokolu zadané jeho ID, můžete toto volání protokolu HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Kde `<appId>` je identifikátor GUID pro tuto aplikaci student konverzace a `<logDialogId>` je ID dialogu protokolu, který chcete odstranit. 

Pokud chcete odstranit dialogové okno train zadané jeho ID, můžete toto volání protokolu HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Kde `<appId>` je identifikátor GUID pro tuto aplikaci student konverzace a `<trainDialogId>` je ID train dialogové okno, které chcete odstranit. 
