---
title: Brána firewall webových aplikací Azure – nejčastější dotazy
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se brány firewall webových aplikací na Azure Front Door
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: fefbb038cec0c061f1fd191a77164880372555a0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314321"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Nejčastější dotazy týkající se brány firewall webových aplikací Azure ve službě Azure Front Door Service

Tento článek odpovídá na běžné otázky týkající se funkcí a funkcí brány firewall webových aplikací Azure (WAF). 

## <a name="what-is-azure-waf"></a>Co je Azure WAF?

Azure WAF je brána firewall webových aplikací, která pomáhá chránit webové aplikace před běžnými hrozbami, jako je vkládání SQL, skriptování mezi weby a další webové zneužití. Můžete definovat zásadu WAF skládající se z kombinace vlastních a spravovaných pravidel pro řízení přístupu k webovým aplikacím.

Zásady Azure WAF lze použít pro webové aplikace hostované na application gateway nebo Azure Front Doors.

## <a name="what-is-waf-on-azure-front-door"></a>Co je WAF na předních dveřích Azure? 

Azure Front Door je vysoce škálovatelná globálně distribuovaná síť aplikací a obsahu. Azure WAF, když je integrován s front door, zastaví odmítnutí služby a cílené útoky aplikací na okraji sítě Azure, v blízkosti zdrojů útoků před vstupem do vaší virtuální sítě, nabízí ochranu bez obětování výkonu.

## <a name="does-azure-waf-support-https"></a>Podporuje Azure WAF protokol HTTPS?

Přední dveře nabízejí vykládku TLS. WAF je nativně integrován s předními dveřmi a může zkontrolovat požadavek po jeho dešifrování.

## <a name="does-azure-waf-support-ipv6"></a>Podporuje Azure WAF IPv6?

Ano. Omezení protokolu IP můžete nakonfigurovat pro protokoly IPv4 a IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Jak aktuální jsou spravované sady pravidel?

Snažíme se držet krok s měnící se hrozbou krajiny. Po aktualizaci nového pravidla se přidá do výchozí sady pravidel s novým číslem verze.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Jaký je čas šíření, pokud změním své zásady WAF?

Nasazení zásad WAF globálně obvykle trvá přibližně 5 minut a často skončí dříve.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Mohou být zásady WAF pro různé oblasti odlišné?

Při integraci s předními dveřmi je WAF globálním zdrojem. Stejná konfigurace platí ve všech umístěních předních dveří.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Jak mohu omezit přístup k mému back-endu pouze z předních dveří?

Seznam řízení přístupu k ip v back-endu můžete nakonfigurovat tak, aby umožňoval pouze rozsahy odchozích IP adres předních dveří a odepřel jakýkoli přímý přístup z Internetu. Značky služeb jsou podporovány pro použití ve vaší virtuální síti. Kromě toho můžete ověřit, zda je pole hlavičky HTTP hostitele X-Forwarded platné pro vaši webovou aplikaci.

## <a name="which-azure-waf-options-should-i-choose"></a>Které možnosti Azure WAF si mám vybrat?

Existují dvě možnosti při použití zásad WAF v Azure. WAF s Azure Front Door je globálně distribuované řešení zabezpečení na okraji. WAF s aplikační bránou je regionální, vyhrazené řešení. Doporučujeme zvolit řešení na základě vašich celkových požadavků na výkon a zabezpečení. Další informace najdete v [tématu Vyrovnávání zatížení s sadou pro doručování aplikací Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Podporujete stejné funkce WAF na všech integrovaných platformách?

V současné době jsou pravidla ModSec CRS 2.2.9, CRS 3.0 a CRS 3.1 podporována pouze pomocí waf v aplikační bráně. Omezení rychlosti, geografické filtrování a pravidla sady výchozích pravidel spravované Azure jsou podporované jenom s WAF na Azure Front Door.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Je ochrana DDoS integrována s předními dveřmi? 

Azure Front Door, globálně distribuované na okrajích sítě Azure, můžou absorbovat a geograficky izolovat útoky velkého objemu. Můžete vytvořit vlastní zásady WAF pro automatické blokování a omezení útoků http(s), které mají známé podpisy. Další další můžete povolit Standard ochrany DDoS na virtuální síti, kde jsou nasazeny back-endy. Zákazníci azure ddos protection standard udělují další výhody, včetně ochrany nákladů, záruky SLA a přístupu k odborníkům z Týmu rychlé reakce DDoS, kteří vám během útoku pomohou.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Proč se další požadavky nad prahovou hodnotu nakonfigurovanou pro pravidlo limitu rychlosti předávají back-endovému serveru?

Pravidlo omezení rychlosti může omezit abnormálně vysoký provoz z libovolné ip adresy klienta. Můžete nakonfigurovat prahovou hodnotu na počet webových požadavků povolených z IP adresy klienta během jedné minuty nebo pět minut trvání. Pro granulární řízení rychlosti omezení rychlosti lze kombinovat s dalšími podmínkami shody, jako je například párování parametrů HTTP(S). 

Požadavky od stejného klienta často dorazí na stejný server Front Door. V takovém případě se zobrazí další požadavky nad prahovou hodnotou okamžitě blokovány. 

Je však možné, že požadavky od stejného klienta mohou být doručeny na jiný server front door, který ještě neaktualizoval čítač limitu rychlosti. Klient může například otevřít nové připojení pro každý požadavek a prahová hodnota je nízká. V takovém případě by první požadavek na nový server Front Door prošel kontrolou limitu rychlosti. Prahová hodnota limitu rychlosti je obvykle nastavena jako vysoká, aby se bránila útokům odmítnutí služby z libovolné IP adresy klienta. Pro velmi nízkou prahovou hodnotu se mohou zobrazit další požadavky nad prahovou hodnotou projít.

## <a name="next-steps"></a>Další kroky

- Přečtěte si informace o [bráně firewall webových aplikací Azure](../overview.md).
- Další informace o [Azure Front Door](../../frontdoor/front-door-overview.md).
