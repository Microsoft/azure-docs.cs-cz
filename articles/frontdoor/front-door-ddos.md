---
title: Přední vrátka Azure – ochrana DDoS
description: Tato stránka poskytuje informace o tom, jak vaše přední dveře Azure pomáhají chránit před útoky DDoS
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96350779"
---
# <a name="ddos-protection-on-front-door"></a>DDoS ochrana před dveřmi

Přední dveře Azure mají několik funkcí a vlastností, které vám pomůžou zabránit distribuovaným útokům na útoky DDoS (Denial of Service). Tyto funkce můžou útočníkům zabránit v tom, aby dosáhli vaší aplikace a ovlivnily dostupnost a výkon vaší aplikace.

## <a name="integration-with-azure-ddos-protection-basic"></a>Integrace s Azure DDoS Protection Basic

Přední dvířka jsou chráněná Azure DDoS Protection Basic. Ve výchozím nastavení je integrovaná do platformy front dveří a bez dalších nákladů. Úplný rozsah a kapacita globálně nasazené sítě front-The je zajištěná obrana proti běžným útokům síťové vrstvy prostřednictvím nepřetržitého monitorování provozu a zmírnění rizik v reálném čase. Základní ochrana DDoS chrání také před nejběžnějšími, často se vyskytujícími potřebami převodními dotazy na rozhraní DNS vrstvy 3 a 4, které cílí na veřejné koncové body. Tato služba má také prověřený záznam sledování v rámci ochrany podnikových a zákaznických služeb od Microsoftu po rozsáhlé útoky. Další informace najdete v tématu [Azure DDoS Protection](../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Blokování protokolu

Přední dvířka akceptují provoz pouze v protokolech HTTP a HTTPS a zpracovávají pouze platné žádosti se známou `Host` hlavičkou. Toto chování pomáhá zmírnit některé běžné typy útoků DDoS, včetně objemných útoků, které jsou rozdělené do různých protokolů a portů, útoků na zesílení DNS a útoků na poškození protokolu TCP.

## <a name="capacity-absorption"></a>Absorpce kapacity

Přední dvířka jsou rozsáhlě škálovatelná globálně distribuovaná služba. Máme spoustu zákazníků, včetně vlastních rozsáhlých cloudových produktů Microsoftu, které každou sekundu dostanou stovky tisíc požadavků. Přední dveře se nacházejí na hranici sítě Azure a absorbují a geograficky izolují rozsáhlé útoky na velké objemy svazků. To může zabránit škodlivému provozu v přechodu k dál, než je hranice sítě Azure.

## <a name="caching"></a>Ukládání do mezipaměti

[Možnosti ukládání do mezipaměti pro frontu](./front-door-caching.md) se dají použít k ochraně back-endu z velkých objemů provozu vygenerovaných útokem. Prostředky uložené v mezipaměti budou vráceny z hraničních uzlů front dveří, takže se nebudou předávány do vašeho back-endu. Dokonce i krátká doba vypršení platnosti mezipaměti (v sekundách nebo minutách) na dynamických odpovědích může výrazně snížit zatížení back-end služeb. Další informace o konceptech a vzorcích pro ukládání do mezipaměti najdete v tématu [požadavky na ukládání do](/azure/architecture/best-practices/caching) mezipaměti a model doplňování [mezipaměti](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Firewall webových aplikací (WAF)

[Firewall webových aplikací front (WAF)](../web-application-firewall/afds/afds-overview.md) je možné použít ke zmírnění mnoha různých typů útoků:

* Použití spravované sady pravidel poskytuje ochranu před řadou běžných útoků.
* Provoz mimo definovanou geografickou oblast nebo v rámci definované oblasti lze zablokovat nebo přesměrovat na statickou webovou stránku. Další informace najdete v tématu [geografické filtrování](../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* IP adresy a rozsahy, které identifikujete jako škodlivou, můžete zablokovat.
* Omezení rychlosti se dá použít k tomu, aby se zabránilo tomu, že IP adresy budou často volat služby.
* Můžete vytvářet [vlastní WAF pravidla](../web-application-firewall/afds/waf-front-door-custom-rules.md) pro automatické blokování a přenos útoků http nebo https, které mají známé signatury.

## <a name="for-further-protection"></a>Pro další ochranu

Pokud potřebujete další ochranu, můžete povolit [Azure DDoS Protection Standard](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) ve virtuální síti, kde jsou nasazené back-endy. Zákazníci s DDoS Protection Standard získají další výhody, jako je například ochrana nákladů, záruka SLA a přístup k odborníkům z týmu DDoS Rapid Response za účelem okamžité pomoci během útoku.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak nakonfigurovat [profil WAF na předních dveřích](front-door-waf.md). 
- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).