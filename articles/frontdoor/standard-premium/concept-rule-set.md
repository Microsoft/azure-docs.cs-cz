---
title: 'Přední dvířka Azure: sada pravidel'
description: Tento článek poskytuje přehled funkce sady pravidel pro službu Azure front-Premium Standard/Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e3e5333b339101676582cec03dbb960148d59b56
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067550"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Co je pravidlo sady pro Azure front-end Standard/Premium (Preview)?

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Sada pravidel je přizpůsobený modul pravidel, který seskupuje kombinaci pravidel do jedné sady. Můžete přidružit sadu pravidel s více trasami. Sada pravidel umožňuje přizpůsobit způsob zpracování požadavků na hranici a způsob, jakým tyto požadavky zpracovávají přední dveře Azure.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="common-supported-scenarios"></a>Běžné podporované scénáře

* Implementace hlaviček zabezpečení pro zabránění ohrožení zabezpečení založeného na prohlížeči, jako je HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-frame-Options a záhlaví Access-Control-Allow-Origin pro scénáře sdílení prostředků mezi zdroji (CORS). Atributy založené na zabezpečení je také možné definovat pomocí souborů cookie.

* Směrování požadavků na mobilní nebo desktopové verze vaší aplikace na základě typu klientského zařízení.

* Použití schopností přesměrování k vrácení 301, 302, 307 a 308 přesměrování na klienta, aby se směroval na nové názvy hostitelů, cesty, řetězce dotazů nebo protokoly.

* Dynamicky Upravte konfiguraci ukládání do mezipaměti trasy na základě příchozích požadavků.

* Přepište cestu adresy URL požadavku a přepošle požadavek příslušnému zdroji v nakonfigurované skupině původu.

* Umožňuje přidat, upravit nebo odebrat hlavičku žádosti nebo odpovědi pro skrytí citlivých informací nebo zaznamenání důležitých informací prostřednictvím hlaviček.

* Podporuje proměnné serveru pro dynamické změny hlaviček žádostí a odpovědí nebo cest pro přepis adres URL/řetězců dotazů, například při načtení nové stránky nebo při odeslání formuláře. Proměnná serveru se v současné době podporuje jenom u **[akcí sady pravidel](concept-rule-set-actions.md)** .

## <a name="architecture"></a>Architektura

Sada pravidel zpracovává požadavky na hraničních zařízeních. Když požadavek dorazí na koncový bod služby Azure front-end Standard/Premium, WAF se spustí jako první a pak se provede nastavení nakonfigurovaná v cestě. Mezi tato nastavení patří sada pravidel přidružená k trase. Sady pravidel jsou zpracovávány shora dolů v trase. Totéž platí pro pravidla v rámci sady pravidel. Aby se daly provést všechny akce v každém pravidle, musí být splněné všechny podmínky shody v rámci pravidla. Pokud požadavek neodpovídá žádné z podmínek v konfiguraci sady pravidel, spustí se pouze konfigurace v trase.

Pokud je zaškrtnuté políčko **zastavit vyhodnocení zbývajících pravidel** , pak se nespustí všechny zbývající sady pravidel přidružené k trase.  

### <a name="example"></a>Příklad

V následujícím diagramu se zásady WAF spustí jako první. Sada pravidel se nakonfiguruje tak, aby přidala hlavičku odpovědi. Pak záhlaví změní maximální stáří řízení mezipaměti, pokud je splněna podmínka shody.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="Diagram znázorňující architekturu sady pravidel" lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>Terminologie

Pomocí sady pravidel front-end pro Azure můžete vytvořit kombinaci konfigurace sady pravidel, z nichž každá se skládá ze sady pravidel. Následující řádky jsou užitečné v terminologií při konfiguraci sady pravidel.

Další omezení kvóty najdete v tématu [omezení a kvóty předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

* *Sada pravidel*: sada pravidel, která je přidružena k jedné nebo více [trasám](concept-route.md).

* *Pravidlo sady pravidel*: pravidlo složené z až 10 podmínek shody a 5 akcí. Pravidla jsou místní pro sadu pravidel a nelze je exportovat pro použití v rámci sad pravidel. Uživatelé můžou vytvořit stejné pravidlo v několika sadách pravidel.

* *Podmínka shody*: existuje mnoho podmínek shody, které lze využít k analýze příchozích požadavků. Pravidlo může obsahovat až 10 podmínek shody. Podmínky shody jsou vyhodnocovány pomocí operátoru **and** . *Regulární výraz je podporován v podmínkách*. Úplný seznam podmínek shody najdete v [podmínkách shody sady pravidel](concept-rule-set-match-conditions.md).

* *Akce*: akce určují, jak AFD zpracovává příchozí požadavky na základě podmínek porovnání. Můžete upravit chování ukládání do mezipaměti, upravit hlavičky žádosti a hlavičky odpovědí, provést přepsání adresy URL a přesměrování adresy URL. *Proměnné serveru jsou podporovány při akci*. Pravidlo může obsahovat až 10 podmínek shody. Je možné najít úplný seznam akcí, které lze provést v rámci [akcí sady pravidel](concept-rule-set-actions.md).

## <a name="arm-template-support"></a>Podpora šablon ARM

Sady pravidel lze konfigurovat pomocí Azure Resource Manager šablon. [Podívejte se na příklad šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set). Chování můžete přizpůsobit pomocí fragmentů JSON nebo bicep, které jsou uvedené v příkladech dokumentace pro podmínky a [Akce](concept-rule-set-actions.md) [shody](concept-rule-set-match-conditions.md) .

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [vytvořit frontu Standard/Premium](create-front-door-portal.md).
* Naučte se konfigurovat svoji první [sadu pravidel](how-to-configure-rule-set.md).
