---
title: Úložiště dat
titleSuffix: Language Understanding - Azure Cognitive Services
description: Služba LUIS ukládá data šifrovaná v úložišti Azure dat. odpovídající oblast učená klíčem.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: df8af9dc86e26a073b76bff33f35bad2ba32b5e8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105746"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Úložiště dat a odebírání ve službě Cognitive Services Language Understanding (LUIS)
Služba LUIS ukládá data šifrovaná v úložišti Azure dat. odpovídající oblast učená klíčem. Tato data jsou ukládána po dobu 30 dnů. 

## <a name="export-and-delete-app"></a>Exportovat a odstranit aplikaci
Uživatelé mají plnou kontrolu nad [export](luis-how-to-start-new-app.md#export-app) a [odstranění](luis-how-to-start-new-app.md#delete-app) aplikace. 

## <a name="utterances-in-an-intent"></a>Projevy v záměru
Odstranit projevy příklad používá pro školení [LUIS](luis-reference-regions.md). Pokud odstraníte utterance příklad z vaší aplikace LUIS, se odebere z webové služby LUIS a není k dispozici pro export.

## <a name="utterances-in-review"></a>Projevy v revizi
Projevy můžete odstranit ze seznamu uživatelů projevy, které navrhuje LUIS v  **[kontrolní koncový bod projevy stránku](luis-how-to-review-endoint-utt.md)**. Odstraňuje se z tohoto seznamu projevy brání jejich navržena, ale nedojde k jejich odstranění z protokolů.

## <a name="accounts"></a>Účty
Pokud odstraníte účet, se odstraní všechny aplikace, spolu s jejich příklad projevy a protokoly. Data se uchovávají po dobu 60 dní, než účet a data se trvale odstraní.

Odstraňuje se účet je k dispozici **nastavení** stránky. Vyberte název svého účtu v pravém horním navigačním panelu zobrazíte **nastavení** stránky.

## <a name="data-inactivity-as-an-expired-subscription"></a>Data nečinnosti jako předplatného s vypršelou platností
Pro účely uchovávání dat a odstranění neaktivních aplikace LUIS může _podle vlastního uvážení Microsoftu_ považovat za předplatné vypršelo. Aplikace se považuje za neaktivní, pokud za posledních 90 dní splňuje následující kritéria: 

* Zaznamenala **žádné** volání provedená do něj.
* Nebyl změněn.
* Není aktuální klíč přiřadili k němu.
* Přihlaste se k ní uživatel neměl.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o exportu a odstranění aplikace](luis-how-to-start-new-app.md)