---
title: Firewall webových aplikací – – nejčastější dotazy
description: Tato stránka nabízí odpovědi na nejčastější dotazy týkající se Azure branou služby
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 05d01851d0a3dc9df6c396e862ce93defd957c70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461965"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Nejčastější dotazy ke službě Azure web application firewall

Tento článek obsahuje odpovědi na běžné otázky o Azure web application firewall (WAF) funkce a funkce. 

## <a name="what-is-azure-waf"></a>Co je Azure WAF?

Azure WAF je firewall webových aplikací, který pomáhá chránit vaše webové aplikace z běžné hrozby, jako jsou SQL injektáž, skriptování napříč weby a další typy útoků. Můžete definovat zásady WAF, který se skládá z kombinace vlastního a spravované pravidla pro řízení přístupu k webovým aplikacím.

Zásady služby Azure WAF lze použít pro webové aplikace hostované na službách Application Gateway nebo branou Azure.

## <a name="what-is-waf-for-azure-front-door-service"></a>Co je WAF branou služby Azure? 

Branou Azure je vysoce škálovatelné a globálně distribuované aplikace a síť pro doručování obsahu. Azure WAF, při integraci s branou, přestane denial-of-service a cílených útoků aplikace na hranici sítě Azure, blízko útoku zdroje před vstupem vaší virtuální síti, nabízí ochranu bez omezení výkonu.

## <a name="how-will-i-be-charged-for-azure-waf-for-front-door"></a>Jak se mi účtovat waf služby Azure pro branou?
Ve verzi public preview WAF využití za branou neúčtují žádné poplatky. Všimněte si, že je poplatek za branou navíc. Podívejte se na ceny služby branou [tady](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="does-azure-waf-support-https"></a>Podporuje Azure WAF HTTPS?

Nabízí služba branou snižování zátěže protokolu SSL. WAF je nativně integrováno s branou a můžete si prohlédnout žádost po vrácením se dešifrují.

## <a name="does-azure-waf-support-ipv6"></a>Podporuje Azure WAF IPv6?

Ano. Můžete nakonfigurovat omezení IP adres pro IPv4 a IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Zprávu o aktuálnosti jsou sady pravidel spravovaná?

Můžeme ale snažit co nejlépe udržovat tempo s měnícími světě hrozeb. Jakmile se aktualizuje nové pravidlo, přidá se do výchozí sada pravidel se nové číslo verze.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Co je doba rozšíření, pokud Moje zásad WAF udělám nějakou změnu?

Nasazení zásady WAF globálně obvykle trvá přibližně 5 minut a často dokončí dříve.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Mohou být různé pro různé oblasti WAF zásady?

Při integraci s branou služby WAF je globální prostředek. Stejné konfigurace platí ve všech umístěních branou.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Jak omezit přístup do své back endu na pouze z branou?

Můžete nakonfigurovat seznam řízení přístupu IP v back endu k povolení pro pouze branou odchozí rozsahy IP adres nebo zamítnutí přímý přístup z Internetu. Značky služeb se podporují pro použití ve vaší virtuální síti. Kromě toho můžete ověřit, že pole hlavičky HTTP X předané hostiteli je platný pro vaši webovou aplikaci.




## <a name="which-azure-waf-options-should-i-choose"></a>Jaké možnosti Azure WAF mám zvolit?

Existují dvě možnosti při používání WAF zásad v Azure. WAF s branou Azure je globálně distribuovaná, hraniční řešení zabezpečení. WAF pomocí služby Application Gateway je vyhrazené místní řešení. Doporučujeme, abyste že si vyberete řešení na základě požadavků celkový výkon a zabezpečení. Další informace najdete v tématu [Vyrovnávání zatížení se sadou Azure application delivery](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Podporujete stejné funkce WAF na všech platformách, integrované?

V současné době pravidel CRS 3.0 a ModSec CRS 2.2.9 jsou podporovaná jenom s WAF ve službě Application Gateway. Omezení rychlosti, geografické filtrování a Azure spravované výchozí sada pravidel pravidla jsou podporovány pouze s WAF na Azure branou.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Je služba DDoS protection integrována s branou? 

Globálně distribuované na hranách síť Azure a Azure branou vyrovnat se s a geograficky izolovat velké objemy útoky. Vlastní zásady WAF automaticky blokovat a míry můžete vytvořit útoků limit http (s), které znají podpisy. Další informace, můžete povolit před útoky DDoS Protection standardní ve virtuální síti, ve které jsou nasazené back EndY. Azure DDoS Protection standardní zákazníci získají další výhody, včetně ochrana nákladů, se zárukou smlouvy SLA a přístup k odborníkům na od týmu služby DDoS rychlé odpovědi nápovědu k okamžité během útoku. 

## <a name="next-steps"></a>Další postup

- Další informace o [firewallu webových aplikací](waf-overview.md).
- Další informace o [Azure branou](front-door-overview.md).
