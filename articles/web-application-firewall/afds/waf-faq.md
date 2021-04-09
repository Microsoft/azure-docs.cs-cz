---
title: Brána Firewall webových aplikací Azure na službě Azure front-dveří – Nejčastější dotazy
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se firewallu webových aplikací v Azure front-dveřích.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5c2763112b1aa2d58f5dc57cea72a3d0bdea961e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95545665"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Nejčastější dotazy k firewallu webových aplikací Azure na službě Azure front-dveří

Tento článek obsahuje odpovědi na běžné dotazy k firewallu webových aplikací Azure (WAF) ve funkcích a funkcích služby Azure front dveří. 

## <a name="what-is-azure-waf"></a>Co je Azure WAF?

Azure WAF je brána firewall webových aplikací, která pomáhá chránit webové aplikace před běžnými hrozbami, jako jsou například injektáže SQL, skriptování mezi weby a další webové zneužití. Můžete definovat zásady WAF, které se skládají z kombinace vlastních a spravovaných pravidel pro řízení přístupu k vašim webovým aplikacím.

Zásady Azure WAF je možné použít pro webové aplikace hostované v Application Gateway nebo na frontách Azure.

## <a name="what-is-waf-on-azure-front-door"></a>Co je WAF na Azure – přední dveře? 

Přední dvířka Azure je vysoce škálovatelná, globálně distribuovaná aplikace a síť pro doručování obsahu. Služba Azure WAF, která je integrovaná s předními dveřmi, zabrání útokům na dostupnost služby a cíle aplikace na hraničních zařízeních Azure. před přechodem do vaší virtuální sítě pak nabízí ochranu bez obětování výkonu.

## <a name="does-azure-waf-support-https"></a>Podporuje Azure WAF protokol HTTPS?

Přední dveře nabízí snižování zátěže TLS. WAF je nativně integrovaná s předními dveřmi a může žádost po dešifrování zkontrolovat.

## <a name="does-azure-waf-support-ipv6"></a>Podporuje Azure WAF protokol IPv6?

Ano. Můžete nakonfigurovat omezení IP adres pro IPv4 a IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Jak se spravují sady pravidel?

Náš postup se snažíme udržovat s měnícími se hrozbami. Po aktualizaci nového pravidla se přidá do výchozí sady pravidel s novým číslem verze.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Jaká je doba šíření, když provedete změnu v zásadách WAF?

Nasazení zásad WAF globálně obvykle trvá přibližně 5 minut a často se dokončí dřív.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Můžou se zásady WAF lišit pro různé oblasti?

Při integraci s předními dvířky je WAF globálním prostředkem. Stejná konfigurace se vztahuje na všechna umístění front dveří.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Návody omezit přístup k back-endu jenom z předních dveří?

Můžete nakonfigurovat seznam IP Access Control v back-endu tak, aby umožňoval pouze odchozí rozsahy IP adres na front-endu a odepřít veškerý přímý přístup z Internetu. Značky služeb jsou podporované pro použití ve vaší virtuální síti. Kromě toho můžete ověřit, jestli je pro vaši webovou aplikaci platné pole hlavička protokolu HTTP s přesměrováním X.

## <a name="which-azure-waf-options-should-i-choose"></a>Které možnosti Azure WAF mám zvolit?

Při použití zásad WAF v Azure jsou k dispozici dvě možnosti. WAF s Azure front-Dvířks je globálně distribuované řešení zabezpečení Edge. WAF s Application Gateway je místní vyhrazené řešení. Doporučujeme zvolit řešení na základě celkových požadavků na výkon a zabezpečení. Další informace najdete v tématu [Vyrovnávání zatížení s využitím sady pro doručování aplikací v Azure](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md).

## <a name="whats-the-recommended-approach-to-enabling-waf-on-front-door"></a>Jaký je doporučený přístup k povolení WAF na předních dveřích?

Pokud WAF povolíte u existující aplikace, je běžné mít falešně pozitivní detekci, kde pravidla WAF zjišťují legitimní provoz jako hrozbu. K minimalizaci rizika dopadu na uživatele doporučujeme následující postup:

* Povolte WAF v režimu [ **detekce**](./waf-front-door-create-portal.md#change-mode) , abyste zajistili, že WAF při práci s tímto procesem neblokuje požadavky.
  > [!IMPORTANT]
  > Tento postup popisuje, jak povolit WAF v novém nebo existujícím řešení, pokud je vaše priorita minimalizována pro uživatele vaší aplikace. V případě útoku nebo bezprostřední hrozby možná budete chtít místo toho nasadit WAF do režimu **prevence** a pomocí procesu optimalizace monitorovat a ladit WAF v průběhu času. Tím pravděpodobně dojde k zablokování některých legitimních přenosů, což je důvod, proč to doporučujeme jenom v případě, že máte hrozbu.
* Postupujte podle našich [pokynů pro optimalizaci WAF](./waf-front-door-tuning.md). Tento proces vyžaduje, abyste povolili protokolování diagnostiky, zkontrolovali protokoly pravidelně a přidali vyloučení pravidel a další zmírnění rizik.
* Tento celý proces zopakujte a pravidelně kontrolujte protokoly, dokud nebudete přesvědčeni, že nebude zablokovaný žádný oprávněný provoz. Celý proces může trvat několik týdnů. V ideálním případě byste měli po každé změně provedeného ladění zobrazit méně falešně pozitivních detekcí.
* Nakonec povolte WAF v **režimu prevence**.
* I když používáte WAF v produkčním prostředí, měli byste monitorování protokolů sledovat, abyste identifikovali jiné falešně pozitivní detekce. Pravidelné kontroly protokolů vám také pomůžou identifikovat všechny reálné pokusy o útokech, které byly zablokovány.

## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Podporujete stejné funkce WAF na všech integrovaných platformách?

V současné době se pravidla ModSecch počítačových 2.2.9, počítačových rezervačních 3,0 a počítačových 3,1 podporují jenom s WAF na Application Gateway. Pravidla omezení četnosti, geografického filtrování a spravovaného výchozího pravidla sady Azure jsou podporovaná jenom s WAF na frontách Azure na předních dveřích.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Je ochrana DDoS integrovaná s předními dvířky? 

V případě globálně distribuovaných na okrajích sítě Azure můžou přední dveře Azure absorbovat a geograficky izolovat útoky na velké objemy svazků. Můžete vytvořit vlastní zásady WAF a automaticky tak blokovat a omezit útoky na požadavky HTTP (s), které mají známé signatury. Další informace můžete povolit DDoS Protection Standard ve virtuální síti, kde jsou nasazené back-endy. Zákazníci s Azure DDoS Protection Standard získají další výhody, jako je ochrana nákladů, záruka SLA a přístup k odborníkům z týmu DDoS Rapid Response pro zajištění okamžité pomoci během útoku. Další informace najdete v tématu [DDoS Protection na předních dveřích](../../frontdoor/front-door-ddos.md).

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Proč se do back-endu serveru předají další požadavky nad prahovou hodnotou nakonfigurovanou pro pravidlo pro omezení přenosové rychlosti?

Pravidlo omezení přenosové rychlosti může omezit neobvykle vysoký provoz z jakékoli IP adresy klienta. Prahovou hodnotu můžete nakonfigurovat u počtu webových požadavků povolených z IP adresy klienta během jedné minuty nebo pěti minut. Pro řízení detailních sazeb se dá omezení četnosti kombinovat s dalšími podmínkami shody, jako je odpovídající parametr HTTP (S). 

Žádosti ze stejného klienta často dorazí na stejný server front-dveří. V takovém případě uvidíte další požadavky nad prahovou hodnotou, která se okamžitě zablokuje. 

Je ale možné, že žádosti ze stejného klienta dorazí na jiný server front-dveří, který ještě neaktualizoval čítač limitu četnosti. Klient například může pro každý požadavek otevřít nové připojení a prahová hodnota je nízká. V takovém případě by první požadavek na nový server front-dveří mohl projít kontrolu limitu četnosti. Mezní hodnota omezení četnosti je obvykle nastavena na vysoká, aby se zabránilo útokům DOS (Denial of Service) z jakékoli IP adresy klienta. V případě velmi nízké prahové hodnoty se mohou zobrazit další požadavky nad prahovou hodnotou získat.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [bráně firewall webových aplikací Azure](../overview.md).
- Přečtěte si další informace o [službě Azure front-dveří](../../frontdoor/front-door-overview.md).