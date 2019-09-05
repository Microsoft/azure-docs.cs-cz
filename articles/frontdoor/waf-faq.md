---
title: Brána Firewall webových aplikací Azure – Nejčastější dotazy
description: Tato stránka obsahuje odpovědi na nejčastější dotazy týkající se služby Azure front-dveří.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: f7d205920704ce8aedf3b2e3c07bd429b3c64964
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375348"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Nejčastější dotazy k bráně firewall webových aplikací Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se funkcí a funkcí firewallu webových aplikací v Azure (WAF). 

## <a name="what-is-azure-waf"></a>Co je Azure WAF?

Azure WAF je brána firewall webových aplikací, která pomáhá chránit webové aplikace před běžnými hrozbami, jako jsou například injektáže SQL, skriptování mezi weby a další webové útoky. Můžete definovat zásady WAF, které se skládají z kombinace vlastních a spravovaných pravidel pro řízení přístupu k vašim webovým aplikacím.

Zásady Azure WAF je možné použít u webových aplikací hostovaných na službě Application Gateway nebo Azure front-dveří.

## <a name="what-is-waf-for-azure-front-door-service"></a>Co je WAF pro službu Azure front-dveří? 

Přední dvířka Azure je vysoce škálovatelná, globálně distribuovaná aplikace a síť pro doručování obsahu. Služba Azure WAF, která je integrovaná s předními dveřmi, zabrání útokům na dostupnost služby a cíle aplikace na hraničních zařízeních Azure. před přechodem do vaší virtuální sítě pak nabízí ochranu bez obětování výkonu.

## <a name="does-azure-waf-support-https"></a>Podporuje Azure WAF protokol HTTPS?

Služba front-dveří nabízí snižování zátěže SSL. WAF je nativně integrovaná s předními dveřmi a může žádost po dešifrování zkontrolovat.

## <a name="does-azure-waf-support-ipv6"></a>Podporuje Azure WAF protokol IPv6?

Ano. Můžete nakonfigurovat omezení IP adres pro IPv4 a IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Jak se spravují sady pravidel?

Náš postup se snažíme udržovat s měnícími se hrozbami. Po aktualizaci nového pravidla se přidá do výchozí sady pravidel s novým číslem verze.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Jaká je doba šíření, když provedete změnu v zásadách WAF?

Nasazení zásad WAF globálně obvykle trvá přibližně 5 minut a často se dokončí dřív.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Můžou se zásady WAF lišit pro různé oblasti?

Při integraci s front-Dvířk Service je WAF globálním prostředkem. Stejná konfigurace se vztahuje na všechna umístění front dveří.
 

## <a name="which-azure-waf-options-should-i-choose"></a>Které možnosti Azure WAF mám zvolit?

Při použití zásad WAF v Azure jsou k dispozici dvě možnosti. WAF s Azure front-Dvířks je globálně distribuované řešení zabezpečení Edge. WAF s Application Gateway je místní vyhrazené řešení. Doporučujeme zvolit řešení na základě celkových požadavků na výkon a zabezpečení. Další informace najdete v tématu [Vyrovnávání zatížení s využitím sady pro doručování aplikací v Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Podporujete stejné funkce WAF na všech integrovaných platformách?

V současné době se pravidla ModSecch počítačových 2.2.9 a počítačových systémů 3,0 podporují jenom s WAF v Application Gateway. Pravidla omezení četnosti, geografického filtrování a spravovaného výchozího pravidla sady Azure jsou podporovaná jenom s WAF v Azure front-dveřích.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Je ochrana DDoS integrovaná s předními dvířky? 

V případě globálně distribuovaných na okrajích sítě Azure můžou přední dveře Azure absorbovat a geograficky izolovat útoky na velké objemy svazků. Můžete vytvořit vlastní zásady WAF a automaticky tak blokovat a omezit útoky na požadavky HTTP (s), které mají známé signatury. Další informace můžete povolit DDoS Protection Standard ve virtuální síti, kde jsou nasazené back-endy. Zákazníci s Azure DDoS Protection Standard získají další výhody, jako je ochrana nákladů, záruka SLA a přístup k odborníkům z týmu DDoS Rapid Response pro zajištění okamžité pomoci během útoku. 

Doporučujeme, abyste v produkčním prostředí uzamknul své back-endy, aby se snížila plocha pro útok na DDoS. Přečtěte si téma [návody uzamčení přístupu k back-endu pouze do front-endu Azure?](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)


## <a name="next-steps"></a>Další postup

- Přečtěte si o [bráně firewall webových aplikací Azure](waf-overview.md).
- Přečtěte si další informace o [službě Azure front-dveří](front-door-overview.md).
