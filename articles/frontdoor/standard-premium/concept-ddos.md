---
title: 'Přední dveře Azure: ochrana DDoS'
description: Tato stránka poskytuje informace o tom, jakým způsobem Azure front-end Standard/Premium pomáhá chránit před útoky DDoS.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099238"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>DDoS Protection na Azure front-end Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Přední dveře Azure mají několik funkcí a vlastností, které vám pomůžou zabránit distribuovaným útokům na útoky DDoS (Denial of Service). Tyto funkce můžou útočníkům zabránit v tom, aby dosáhli vaší aplikace a ovlivnily dostupnost a výkon vaší aplikace.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="integration-with-azure-ddos-protection-basic"></a>Integrace s Azure DDoS Protection Basic

Přední dvířka jsou chráněná Azure DDoS Protection Basic. Tato funkce je ve výchozím nastavení integrovaná do platformy front dveří a bez dalších poplatků. Úplný rozsah a kapacita globálně nasazené sítě front-The je zajištěná obrana proti běžným útokům síťové vrstvy prostřednictvím nepřetržitého monitorování provozu a zmírnění rizik v reálném čase. Základní ochrana DDoS chrání také před nejběžnějšími, často se vyskytujícími potřebami převodními dotazy na rozhraní DNS vrstvy 3 a 4, které cílí na veřejné koncové body. Tato služba má také prověřený záznam sledování v rámci ochrany podnikových a zákaznických služeb od Microsoftu po rozsáhlé útoky. Další informace najdete v tématu [Azure DDoS Protection](../../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Blokování protokolu

Přední dvířka akceptují provoz pouze v protokolech HTTP a HTTPS a zpracovávají pouze platné žádosti se známou `Host` hlavičkou. Toto chování pomáhá zmírnit některé běžné typy útoků DDoS, včetně objemných útoků, které jsou rozdělené do různých protokolů a portů, útoků na zesílení DNS a útoků na poškození protokolu TCP.

## <a name="capacity-absorption"></a>Absorpce kapacity

Přední dvířka jsou rozsáhlě škálovatelná globálně distribuovaná služba. Máme spoustu zákazníků, včetně vlastních rozsáhlých cloudových produktů Microsoftu, které každou sekundu dostanou stovky tisíc požadavků. Přední dveře jsou umístěné na hranici sítě v Azure, při absorpci a geograficky izolaci velkých útoků na svazek. To může zabránit škodlivému provozu v přechodu k dál, než je hranice sítě Azure.

## <a name="caching"></a>Ukládání do mezipaměti

[Možnosti ukládání do mezipaměti pro frontu](concept-caching.md) se dají použít k ochraně back-endu z velkých objemů provozu vygenerovaných útokem. Prostředky uložené v mezipaměti budou vráceny z hraničních uzlů front dveří, takže se nebudou předávány do vašeho back-endu. Dokonce i krátká doba vypršení platnosti mezipaměti (v sekundách nebo minutách) na dynamických odpovědích může výrazně snížit zatížení back-end služeb. Další informace o konceptech a vzorcích pro ukládání do mezipaměti najdete v tématu [požadavky na ukládání do](/azure/architecture/best-practices/caching) mezipaměti a model doplňování [mezipaměti](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Firewall webových aplikací (WAF)

[Firewall webových aplikací (WAF) pro frontu](../../web-application-firewall/afds/afds-overview.md) se dá použít k omezení mnoha různých typů útoků:

* Použití spravované sady pravidel poskytuje ochranu před mnoha běžnými útoky.
* Provoz mimo definovanou geografickou oblast nebo v rámci definované oblasti lze zablokovat nebo přesměrovat na statickou webovou stránku. Další informace najdete v tématu [geografické filtrování](../../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* IP adresy a rozsahy, které identifikujete jako škodlivou, můžete zablokovat.
* Omezení rychlosti se dá použít k tomu, aby se zabránilo tomu, že IP adresy budou často volat služby.
* Můžete vytvářet [vlastní WAF pravidla](../../web-application-firewall/afds/waf-front-door-custom-rules.md) pro automatické blokování a přenos útoků http nebo https, které mají známé signatury.

## <a name="for-further-protection"></a>Pro další ochranu

Pokud potřebujete další ochranu, můžete povolit [Azure DDoS Protection Standard](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) ve virtuální síti, kde jsou nasazené back-endy. Zákazníci s DDoS Protection Standard získají další výhody, včetně:

* Ochrana nákladů
* Záruka SLA
* Přístup k odborníkům z týmu DDoS Rapid Response za účelem okamžité pomoci během útoku.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit frontu Standard/Premium](create-front-door-portal.md).
