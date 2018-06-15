---
title: Zkontrolujte utterances koncový bod používat v jazyk Principy (LEOŠ) – Azure active learning | Microsoft Docs
description: Použít funkci active learning s názvem "Zkontrolujte utterances koncový bod, ke zlepšení výkonu předpovědi rychlejší.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: b9672e8e63fb601d4411a342b7f3c00e30f9e002
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35346014"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Povolit aktivní learning kontrolou utterances koncový bod
Aktivní learning je jedním z tři strategie zlepšit přesnost předpovědi a nejjednodušší k implementaci. 

## <a name="what-is-active-learning"></a>Co je aktivní learning
Aktivní learning je ve dvou krocích. Nejprve LEOŠ vybere utterances, které obdrží na koncovém bodu aplikace vyžadující ověření. Druhý krok se provádí pomocí vlastníka aplikace nebo spolupracovník ověřit vybranou utterances pro [zkontrolujte](label-suggested-utterances.md), včetně správné záměr a všechny entity v rámci záměr. Po zkontrolování utterances, školení a publikujte aplikaci znovu. 

## <a name="which-utterances-are-on-the-review-list"></a>Které utterances jsou v seznamu revize
LEOŠ utterances přidá do seznamu kontrolní, kdy je horní ohlásí záměr nízké skóre nebo se příliš nejvyšší dvě záměry skóre. 

## <a name="where-are-the-utterances-from"></a>Kde jsou utterances z
Koncový bod utterances jsou převzaty z koncového uživatele dotazy na koncový bod HTTP aplikace. Pokud vaše aplikace není publikována nebo ještě neobdržel přístupů, nemáte žádné utterances ke kontrole. Pokud žádný koncový bod přístupů přijme pro konkrétní záměr nebo entity, není nutné utterances ke kontrole, které je obsahují. 

## <a name="schedule-review-periodically"></a>Zkontrolujte plán pravidelně
Kontrola navrhované utterances není nutné provádět každý den, ale měl by být součástí vaší pravidelné údržby LEOŠ. 

## <a name="delete-review-items-programmatically"></a>Odstranění revize položky prostřednictvím kódu programu
Pokud je vaše aplikace velký, můžete se rozhodnout a zkontrolujte některé utterances odstranit rest ze seznamu prostřednictvím kódu programu. K tomu je potřeba první [získávání](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) seznamu a potom [odstraňování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) utterances podle ID.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [zkontrolujte](Label-Suggested-Utterances.md) utterances koncový bod
