---
title: Kontrola uživatele utterance-LUIS
description: Díky aktivnímu učení se ke správnému záměru a entitě projevy koncový bod kontroly. LUIS zvolí koncový bod projevy, na který si nejste jistí.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 82f228d5e6f801539c549e16faea371782ad4b59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316439"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Koncepty pro povolení aktivního učení pomocí kontroly projevy koncového bodu
Aktivní učení je jedna ze tří strategií pro zlepšení přesnosti předpovědi a nejjednodušší implementace. Díky aktivnímu učení se ke správnému záměru a entitě projevy koncový bod kontroly. LUIS zvolí koncový bod projevy, na který si nejste jistí.

## <a name="what-is-active-learning"></a>Co je aktivní učení
Aktivní učení je proces se dvěma kroky. Nejprve LUIS vybere projevy, který obdrží na koncovém bodu aplikace, který vyžaduje ověření. Druhý krok provádí vlastník aplikace nebo spolupracovníka, aby ověřil vybraný projevy k [revizi](luis-how-to-review-endpoint-utterances.md), včetně správného záměru a všech entit v rámci záměru. Po zkontrolování projevyu spusťte znovu výuku a publikujte aplikaci.

## <a name="which-utterances-are-on-the-review-list"></a>Které projevy jsou v seznamu kontroly
LUIS přidá projevy do seznamu revizí, pokud má vrchní záměr vysoké skóre nebo jsou skóre prvních dvou záměrů příliš blízko.

## <a name="single-pool-for-utterances-per-app"></a>Jeden fond pro projevy na aplikaci
V závislosti na verzi se nemění seznam **projevyy bodu revize** . Je jenom jeden fond promluv ke kontrole bez ohledu na to, jakou verzi promluvy právě editujete nebo jaká verze aplikace byla publikovaná v koncovém bodě.

V [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9)je název verze povinný a musí v aplikaci existovat, ale nepoužívá se nad tímto ověřením. Projevy revize platí pro celou aplikaci. Pokud projevy odeberete z jedné _verze_, budou ovlivněny všechny verze.

## <a name="where-are-the-utterances-from"></a>Kde se nachází projevy
Koncový bod projevy se provádí z dotazů koncových uživatelů na koncovém bodu HTTP aplikace. Pokud vaše aplikace není publikovaná nebo ještě neobdržela vaše přístupy, nemáte žádné projevy ke kontrole. Pokud nejsou přijímány žádné přístupy koncového bodu pro určitý záměr nebo entitu, nemáte projevy k revizi, která je obsahuje.

## <a name="schedule-review-periodically"></a>Pravidelná kontrola plánu
Kontrola navrhovaných projevy se nemusí provádět každý den, ale měla by být součástí běžné údržby LUIS.

## <a name="delete-review-items-programmatically"></a>Odstranění položek kontroly programově
Použijte **[odstranění neoznačeného rozhraní projevy](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API. Tyto projevy zálohujte před odstraněním jejich **[exportováním soubory protokolu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="enable-active-learning"></a>Povolit aktivní učení

Chcete-li povolit aktivní učení, je nutné protokolovat uživatelské dotazy. Toho je možné dosáhnout voláním [dotazu koncového bodu](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) s `log=true` parametrem a hodnotou QueryString.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [zkontrolovat](luis-how-to-review-endpoint-utterances.md) projevya koncového bodu
