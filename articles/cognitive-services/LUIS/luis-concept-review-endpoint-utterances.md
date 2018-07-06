---
title: Zkontrolujte projevy koncový bod pro použití aktivního učení v Language Understanding (LUIS) – Azure | Dokumentace Microsoftu
description: Aktivní učení funkci s názvem "Zkontrolujte projevy koncový bod' pro zlepšení výkonu predikcí rychleji.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: e416eec29ff9f4ac96eabf11c87424abeba0c75b
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855713"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Povolit aktivní učení kontrolou projevy koncového bodu
Aktivní učení je jednou tři strategie zlepšit přesnost předpovědi a nejjednodušší implementace. 

## <a name="what-is-active-learning"></a>Co je aktivní učení
Aktivní učení je dvoustupňový proces. Služba LUIS nejprve vybere projevy, které dostane v koncovém bodu aplikace vyžadující ověření. Druhý krok se provádí pomocí vlastníka aplikace nebo spolupracovníka ověřit vybranou projevy pro [zkontrolujte](label-suggested-utterances.md), včetně správné záměr a všechny entity v rámci záměr. Po zkontrolování projevy, trénování a publikování aplikace znovu. 

## <a name="which-utterances-are-on-the-review-list"></a>Projevy, které jsou na seznamu revize
LUIS projevy přidá do seznamu kontrolu, když na začátek ohlásí záměr nízké skóre, nebo hodnocení horní dva příkazy jsou příliš zavřít. 

## <a name="single-pool-for-utterances-per-app"></a>Jeden fond pro projevy na aplikaci
**Zkontrolujte koncový bod projevy** seznamu nezmění závislosti na verzi. Existuje jeden fond projevy, které chcete zkontrolovat, bez ohledu na to, která verze utterance aktivně upravujete nebo která verze aplikace byla publikována na koncovém bodu. 

## <a name="where-are-the-utterances-from"></a>Kde jsou projevy z
Koncový bod projevy pocházejí ze dotazy koncového uživatele na koncový bod HTTP vaší aplikace. Pokud vaše aplikace není publikována nebo dosud nepřijal přístupů, není nutné jakékoli projevy ke kontrole. Pokud žádný koncový bod přístupů přijme pro specifické cílem nebo entity, není nutné ke kontrole projevy, které je obsahují. 

## <a name="schedule-review-periodically"></a>Naplánovat pravidelné kontroly
Kontrola navrhované projevy nemusí každý den provést, ale musí být součástí vaší pravidelné údržby služby LUIS. 

## <a name="delete-review-items-programmatically"></a>Odstranění revize položky prostřednictvím kódu programu
Pokud je vaše aplikace velké, můžete zkontrolovat některé projevy a odstranit zbývající ze seznamu prostřednictvím kódu programu. To se provádí tak, že nejprve [získávání](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) seznamu a potom [odstranění](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) projevy podle ID.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [zkontrolujte](Label-Suggested-Utterances.md) projevy koncového bodu
