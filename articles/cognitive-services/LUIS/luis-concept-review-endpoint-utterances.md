---
title: Zkontrolovat projev uživatele – LUIS
titleSuffix: Azure Cognitive Services
description: S aktivní učení, vaše revize koncového bodu projevy pro správný záměr a entitu. Služba LUIS zvolí projevy koncového bodu, které si není jistý.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 375d4b4e7c3fcafbdfde1ff447bedc3e16aff2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219937"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Koncepty pro povolení aktivního učení kontrolou projevy koncového bodu
Aktivní učení je jednou ze tří strategií pro zlepšení přesnosti predikce a nejjednodušší implementovat. S aktivní učení, vaše revize koncového bodu projevy pro správný záměr a entitu. Služba LUIS zvolí projevy koncového bodu, které si není jistý.

## <a name="what-is-active-learning"></a>Co je aktivní učení
Aktivní učení je dvoustupňový proces. Nejprve luis vybere projevy, které obdrží na koncovém bodu aplikace, které potřebují ověření. Druhý krok provádí vlastník aplikace nebo spolupracovník k ověření vybrané projevy pro [kontrolu](luis-how-to-review-endpoint-utterances.md), včetně správného záměru a všechny entity v rámci záměru. Po kontrole projevy, trénování a publikovat aplikaci znovu.

## <a name="which-utterances-are-on-the-review-list"></a>Které projevy jsou na seznamu recenzí
Služba LUIS přidá projevy do seznamu kontrol, když má hlavní záměr spouštění nízké skóre nebo jsou skóre dvou hlavních záměrů příliš blízko.

## <a name="single-pool-for-utterances-per-app"></a>Jeden fond pro projevy na aplikaci
**Seznam projevy koncového bodu revize** se nezmění na základě verze. Je jenom jeden fond promluv ke kontrole bez ohledu na to, jakou verzi promluvy právě editujete nebo jaká verze aplikace byla publikovaná v koncovém bodě.

## <a name="where-are-the-utterances-from"></a>Kde jsou projevy z
Projevy koncového bodu jsou převzaty z dotazů koncových uživatelů na koncovém bodu HTTP aplikace. Pokud vaše aplikace není publikována nebo ještě neobdržela požadavky na server, nemáte žádné projevy ke kontrole. Pokud jsou přijaty žádné požadavky na koncový bod pro konkrétní záměr nebo entitu, nemáte projevy ke kontrole, které je obsahují.

## <a name="schedule-review-periodically"></a>Pravidelně plánovat kontrolu
Revize navrhované projevy není nutné provádět každý den, ale by měla být součástí pravidelné údržby LUIS.

## <a name="delete-review-items-programmatically"></a>Programové odstranění položek recenze
Použijte **[rozhraní API pro odstranění neoznačených promluv.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** Před odstraněním zálohujte tyto projevy **[exportem souborů protokolu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="enable-active-learning"></a>Povolení aktivního učení

Chcete-li povolit aktivní učení, je nutné protokolovat dotazy uživatelů. Toho lze dosáhnout voláním [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) s parametrem a hodnotou řetězce dotazu. `log=true`

## <a name="next-steps"></a>Další kroky

* Naučte [se,](luis-how-to-review-endpoint-utterances.md) jak zkontrolovat projevy koncového bodu
