---
title: Úložiště dat – LUIS
titleSuffix: Azure Cognitive Services
description: Služba LUIS ukládá data šifrovaná v úložišti Azure dat. odpovídající oblast učená klíčem.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390093"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Úložiště dat a odebírání ve službě Cognitive Services Language Understanding (LUIS)
Služba LUIS ukládá data šifrovaná v úložišti Azure dat. odpovídající oblast učená klíčem. Tato data jsou ukládána po dobu 30 dnů. 

## <a name="export-and-delete-app"></a>Exportovat a odstranit aplikaci
Uživatelé mají plnou kontrolu nad [exportem](luis-how-to-start-new-app.md#export-app) a [odstraněním](luis-how-to-start-new-app.md#delete-app) aplikace. 

## <a name="utterances"></a>Projevy

Projevy může být uložená na dvou různých místech. 

* Během **procesu vytváření obsahu**jsou projevy vytvořeny a uloženy v záměru. Projevy v záměrech se vyžadují pro úspěšnou aplikaci LUIS. Jakmile se aplikace publikuje a přijme dotazy v koncovém bodu, dotaz na žádost koncového bodu (QueryString) `log=false`určuje, jestli je uložený koncový bod utterance. Pokud je koncový bod uložený, bude se jednat o součást aktivního výukového projevyu, která se nachází v části **Build** na portálu, v části **Kontrola koncového bodu projevy** . 
* Když provedete **kontrolu koncového bodu projevy**a do záměru přidáte utterance, utterance se už neukládá jako součást koncového bodu projevy, který se má zkontrolovat. Přidá se do záměrů aplikace. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Odstranění příkladu projevy z záměru

Odstraňte příklad projevy, který se používá pro školení [Luis](luis-reference-regions.md). Pokud odstraníte utterance příklad z vaší aplikace LUIS, se odebere z webové služby LUIS a není k dispozici pro export.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Odstranit projevy v recenzi z aktivního učení

Projevy je možné odstranit ze seznamu uživatelských projevy, který LUIS navrhuje na **[stránce zkontrolovat koncový bod pro projevy](luis-how-to-review-endpoint-utterances.md)** . Odstraňuje se z tohoto seznamu projevy brání jejich navržena, ale nedojde k jejich odstranění z protokolů.

Pokud nechcete aktivní výukové projevy, můžete [zakázat aktivní učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Zakázáním aktivního učení také zakážete protokolování.

### <a name="disable-logging-utterances"></a>Zakázat protokolování projevy
[Zakázáním aktivního učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning) zakážete protokolování.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Odstranění účtu
Pokud odstraníte účet, se odstraní všechny aplikace, spolu s jejich příklad projevy a protokoly. Data se uchovávají po dobu 60 dní, než účet a data se trvale odstraní.

Odstranění účtu je k dispozici na stránce **Nastavení** . Na stránce **Nastavení** se dostanete tak, že vyberete název svého účtu v pravém horním navigačním panelu.

## <a name="data-inactivity-as-an-expired-subscription"></a>Data nečinnosti jako předplatného s vypršelou platností
Pro účely uchovávání a odstranění dat může být neaktivní aplikace LUIS na _uvážení společnosti Microsoft_ považována za předplatné s vypršenou platností. Aplikace se považuje za neaktivní, pokud za posledních 90 dní splňuje následující kritéria: 

* Na ni se neudělala **žádná** volání.
* Nebyl změněn.
* Není aktuální klíč přiřadili k němu.
* Přihlaste se k ní uživatel neměl.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o exportu a odstraňování aplikace](luis-how-to-start-new-app.md)
