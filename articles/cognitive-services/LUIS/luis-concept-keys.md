---
title: Pochopení LEOŠ klíče - Azure | Microsoft Docs
description: Klávesami znalosti jazyka (LEOŠ) vytvářet aplikace a dotazovat se vaše endpoing.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/23/2018
ms.author: v-geberr
ms.openlocfilehash: bf131877080f46781e74991ef627922384cc4c08
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343818"
---
# <a name="keys-in-luis"></a>Klíče v LEOŠ
LEOŠ používá dva klíče: [vytváření](#programmatic-key) a [koncový bod](#endpoint-key). Vytváření klíč je vytvořen automaticky při vytvoření účtu LEOŠ. Až budete připraveni k publikování aplikace LEOŠ, budete muset [vytvořit klíč koncového bodu](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [ji přiřadit](Manage-keys.md#assign-endpoint-key) do vaší aplikace LEOŠ a [pomocí dotazu koncový bod](#use-endpoint-key-in-query). 

|Klíč|Účel|
|--|--|
|[Vytváření klíče](#programmatic-key)|Vytváření, publikování, správu spolupracovníci, Správa verzí|
|[Klíč koncového bodu](#endpoint-key)| Dotazování|

Je důležité k vytváření aplikací LEOŠ v [oblasti](luis-reference-regions.md#publishing-regions) také místo publikování a dotazu.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Vytváření klíče

Vytváření klíč, také známé jako klíč starter, se vytvoří automaticky při vytvoření účtu LEOŠ a je zdarma. Máte jeden klíč pro tvorbu napříč aplikace LEOŠ pro každý vytváření [oblast](luis-reference-regions.md). Vytváření klíč je k dispozici pro vytvoření aplikace LEOŠ nebo k testování dotazů koncový bod. 

Vyhledání klíče pro tvorbu, přihlaste se k [LEOŠ] [ LUIS] a klikněte na název účtu v pravém horním navigačním panelu otevřete **nastavení účtu**.

![vytváření klíče](./media/luis-concept-keys/programatic-key.png)

Pokud chcete provést **produkční koncový bod dotazy**, vytvořit Azure [LEOŠ předplatné](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Pro usnadnění práce řadu ukázky použít klíč pro vytváření obsahu vzhledem k tomu, že poskytuje několik volání koncového bodu v jeho [kvóty](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Klíč koncového bodu
 Když potřebujete **produkční koncový bod dotazy**, vytvoření [klíč LEOŠ](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) na portálu Azure. Mějte na paměti, název sloužící k vytvoření klíče, je nutné při přidání klíče do aplikace...

Po dokončení procesu předplatné LEOŠ [přidat klíč](Manage-keys.md#assign-endpoint-key) aplikace na **publikovat** stránky. 

Klíč koncového bodu umožňuje kvótu přístupů koncový bod na základě plánu využití, kterou jste zadali při vytváření klíče. V tématu [kognitivní ceny služby](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) informace o cenách.

Klíč koncového bodu lze použít pro všechny aplikace LEOŠ nebo pro konkrétní aplikace LEOŠ. 

Nepoužívejte pro vytváření aplikací LEOŠ klíč koncového bodu. 

## <a name="use-endpoint-key-in-query"></a>Použijte klíč koncového bodu v dotazu
Koncový bod LEOŠ přijímá dva styly dotazu, používejte klíče, ale koncový bod na různých místech:

|Příkaz|Příklad adresy url a klíč umístění|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn na indikátory<br><br>hodnotu řetězce dotazu pro `subscription-key`<br><br>Změnit hodnotu dotazu váš koncový bod pro `subscription-key` z vytváření klíče (starter), na nový klíč koncový bod, chcete-li použít míra klíče kvót LEOŠ koncový bod. Pokud klíč, vytvořte a přiřaďte klíč, ale neměňte hodnotu dotazu koncového bodu pro klíč předplatného se, že nepoužíváte kvóty klíče vašeho koncového bodu.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a<br><br> Hodnota hlavičky pro `Ocp-Apim-Subscription-Key`<br><br>Změnit hodnotu dotazu váš koncový bod pro `Ocp-Apim-Subscription-Key` z vytváření klíče (starter), na nový klíč koncový bod, chcete-li použít míra klíče kvót LEOŠ koncový bod. Pokud vytvoříte klíč a přiřadit klíč, ale neměňte hodnotu dotazu koncového bodu pro `Ocp-Apim-Subscription-Key`, že nepoužíváte kvóty klíče vašeho koncového bodu.|

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Využití rozhraní API Ocp-Apim-Subscription-Key
Rozhraní API LEOŠ použít záhlaví, `Ocp-Apim-Subscription-Key`. Název hlavičky nezmění podle které klíč a sadu rozhraní API, kterou používáte. Nastavte hlavičku ke klíči pro vytváření obsahu pro rozhraní API pro vytváření obsahu. Pokud používáte koncový bod, nastavte záhlaví na klíč koncového bodu. 

Nelze předat klíč koncový bod rozhraní API pro vytváření obsahu. Pokud tak učiníte, získáte 401 Chyba: přístup byl odepřen z důvodu neplatný odběr klíč. 

## <a name="key-limits"></a>Omezuje klíč
V tématu [klíče omezení](luis-boundaries.md#key-limits) a [oblastí Azure](luis-reference-regions.md). Vytváření klíč je volné a využité pro vytváření obsahu. Klíč předplatného LEOŠ obsahuje volné vrstvy, ale musí být vytvořené vámi a přidružené k vaší aplikaci LEOŠ na **publikovat** stránky. Nelze zadat pro vytváření, ale pouze na dotazy koncový bod.

Publikování oblastí se liší od vytváření oblastí. Ujistěte se, že vytvoříte aplikaci v vytváření oblast odpovídající k publikování oblast, kterou chcete.

## <a name="key-limit-errors"></a>Chyby klíčů limit
Pokud překročíte vaší za druhé kvótu, obdržíte chybu HTTP 429. Pokud překročíte vaší za měsíc kvótu, obdržíte chybu HTTP 403. Opravte tyto chyby získáním LEOŠ [koncový bod](#endpoint-key) klíč, [přiřazení](Manage-keys.md#assign-endpoint-key) klíč do aplikace na **publikovat** stránky [LEOŠ] [ LUIS] webu.

## <a name="next-steps"></a>Další postup

* Další informace [koncepty](Manage-Keys.md#assign-endpoint-key) o vytváření a koncového bodu klíče.

[LUIS]:luis-reference-regions.md#luis-website
