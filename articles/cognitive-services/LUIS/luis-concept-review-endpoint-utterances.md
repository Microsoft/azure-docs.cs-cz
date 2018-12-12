---
title: Projděte si uživatel projevy
titleSuffix: Language Understanding - Azure Cognitive Services
description: Aktivní učení, váš koncový bod projevy revize pro správné záměr a entity. Služba LUIS zvolí projevy koncový bod, je jistí, jaké.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 10/07/2018
ms.author: diberry
ms.openlocfilehash: ca8a6b05722ac2edc91d7459c64c2bdb312a7016
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080958"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Koncepty pro povolení aktivně učit kontrolou projevy koncového bodu
Aktivní učení je jednou tři strategie zlepšit přesnost předpovědi a nejjednodušší implementace. Aktivní učení, váš koncový bod projevy revize pro správné záměr a entity. Služba LUIS zvolí projevy koncový bod, je jistí, jaké.

## <a name="what-is-active-learning"></a>Co je aktivní učení
Aktivní učení je dvoustupňový proces. Služba LUIS nejprve vybere projevy, které dostane v koncovém bodu aplikace vyžadující ověření. Druhý krok se provádí pomocí vlastníka aplikace nebo spolupracovníka ověřit vybranou projevy pro [zkontrolujte](luis-how-to-review-endoint-utt.md), včetně správné záměr a všechny entity v rámci záměr. Po zkontrolování projevy, trénování a publikování aplikace znovu. 

## <a name="which-utterances-are-on-the-review-list"></a>Projevy, které jsou na seznamu revize
LUIS projevy přidá do seznamu kontrolu, když na začátek ohlásí záměr nízké skóre, nebo hodnocení horní dva příkazy jsou příliš zavřít. 

## <a name="single-pool-for-utterances-per-app"></a>Jeden fond pro projevy na aplikaci
**Zkontrolujte koncový bod projevy** seznamu nezmění závislosti na verzi. Je jenom jeden fond promluv ke kontrole bez ohledu na to, jakou verzi promluvy právě editujete nebo jaká verze aplikace byla publikovaná v koncovém bodě. 

## <a name="where-are-the-utterances-from"></a>Kde jsou projevy z
Koncový bod projevy pocházejí ze dotazy koncového uživatele na koncový bod HTTP vaší aplikace. Pokud vaše aplikace není publikována nebo dosud nepřijal přístupů, není nutné jakékoli projevy ke kontrole. Pokud žádný koncový bod přístupů přijme pro specifické cílem nebo entity, není nutné ke kontrole projevy, které je obsahují. 

## <a name="schedule-review-periodically"></a>Naplánovat pravidelné kontroly
Kontrola navrhované projevy nemusí každý den provést, ale musí být součástí vaší pravidelné údržby služby LUIS. 

## <a name="delete-review-items-programmatically"></a>Odstranění revize položky prostřednictvím kódu programu
Použití **[odstranit označeny projevy](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** rozhraní API. Zálohování těchto projevů před odstraněním podle  **[export souborů protokolu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [zkontrolujte](luis-how-to-review-endoint-utt.md) projevy koncového bodu
