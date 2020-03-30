---
title: Nasazení zabezpečených aplikací v Microsoft Azure
description: Tento článek popisuje osvědčené postupy, které je třeba zvážit během fází vydání a odezvy projektu webové aplikace.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934874"
---
# <a name="deploy-secure-applications-on-azure"></a>Nasazení zabezpečených aplikací v Azure
V tomto článku uvádíme aktivity zabezpečení a ovládací prvky, které je třeba zvážit při nasazování aplikací pro cloud. Bezpečnostní otázky a koncepty, které je třeba zvážit během fází vydání a odezvy životního cyklu vývoje zabezpečení společnosti Microsoft [(SDL),](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) jsou zahrnuty. Cílem je pomoci vám definovat aktivity a služby Azure, které můžete použít k nasazení bezpečnější aplikace.

Následující fáze SDL jsou uvedeny v tomto článku:

- Vydat
- Odpověď

## <a name="release"></a>Vydat
Těžištěm fáze vydání je příprava projektu pro veřejné vydání.
To zahrnuje plánování způsobů, jak efektivně provádět servisní úlohy po vydání a řešit chyby zabezpečení, ke kterým může dojít později.

### <a name="check-your-applications-performance-before-you-launch"></a>Zkontrolujte výkon aplikace před spuštěním

Před spuštěním aplikace nebo nasazením aktualizací do produkčního prostředí zkontrolujte výkon aplikace. Spusťte cloudové [zátěžové testy](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) pomocí sady Visual Studio, abyste našli problémy s výkonem ve vaší aplikaci, zlepšili kvalitu nasazení, ujistili se, že vaše aplikace je vždy nahoře nebo dostupná a že vaše aplikace zvládne provoz pro vaše spuštění.

### <a name="install-a-web-application-firewall"></a>Instalace brány firewall webové aplikace

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Běžné mezi tyto zneužití jsou útoky injektáže SQL a útoky skriptování mezi weby. Zabránění těmto útokům v kódu aplikace může být náročné. To může vyžadovat přísnou údržbu, opravy a monitorování v mnoha vrstvách topologie aplikace. Centralizovaný WAF pomáhá zjednodušit správu zabezpečení. Řešení WAF může také reagovat na ohrožení zabezpečení tím, že opravuje známou chybu zabezpečení v centrálním umístění oproti zabezpečení jednotlivých webových aplikací.

[Azure Application Gateway WAF](../../application-gateway/waf-overview.md) poskytuje centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení. WAF je založen na pravidlech ze [základních sad pravidel OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9.

### <a name="create-an-incident-response-plan"></a>Vytvoření plánu reakcí na incidenty

Příprava plánu reakce na incidenty je zásadní pro řešení nových hrozeb, které se mohou časem objevit. Příprava plánu reakce na incidentzahrnuje identifikaci vhodných nouzových kontaktů zabezpečení a vytvoření plánů údržby zabezpečení pro kód, který je zděděn od jiných skupin v organizaci a pro licencovaný kód třetí strany.

### <a name="conduct-a-final-security-review"></a>Proveďte závěrečnou kontrolu zabezpečení

Záměrná kontrola všech provedených aktivit zabezpečení pomáhá zajistit připravenost pro vydání softwaru nebo aplikaci. Závěrečná kontrola zabezpečení (FSR) obvykle zahrnuje zkoumání modelů hrozeb, výstupů nástrojů a výkonu proti vratům kvality a panelům chyb, které byly definovány ve fázi požadavků.

### <a name="certify-release-and-archive"></a>Certifikovat vydání a archivaci

Certifikace softwaru před vydáním pomáhá zajistit splnění požadavků na zabezpečení a ochranu osobních údajů. Archivace všech relevantních dat je nezbytná pro provádění servisních úloh po uvolnění. Archivace také pomáhá snížit dlouhodobé náklady spojené s trvalým softwarovým inženýrstvím.

## <a name="response"></a>Odpověď
Fáze odezvy po vydání se soustředí na to, aby vývojový tým byl schopen a dostupný, aby mohl odpovídajícím způsobem reagovat na jakékoli zprávy o vznikajících softwarových hrozbách a zranitelných místech.

### <a name="execute-the-incident-response-plan"></a>Provedení plánu reakce na incidenty

Možnost implementovat plán reakce na incidenty zavedený ve fázi vydání je nezbytná pro ochranu zákazníků před zabezpečením softwaru nebo chybami zabezpečení osobních údajů, které se objeví.

### <a name="monitor-application-performance"></a>Monitorování výkonu aplikací

Průběžné monitorování vaší aplikace po nasazení potenciálně pomáhá odhalit problémy s výkonem a chybzabezpečení.
Služby Azure, které pomáhají s monitorováním aplikací, jsou:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) je rozšiřitelná služba Správy výkonu aplikací (APM) pro webové vývojáře na více platformách. Slouží k monitorování živé webové aplikace. Application Insights automaticky detekuje anomálie výkonu. Obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé s vaší aplikací skutečně dělají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-intro.md) vám pomůže předcházet, zjišťovat a reagovat na hrozby se zvýšeným přehledem (a kontrolou) zabezpečení vašich prostředků Azure, včetně webových aplikací. Azure Security Center pomáhá detekovat hrozby, které by jinak mohly zůstat bez povšimnutí. Pracuje s různými bezpečnostními řešeními.

Úroveň Free centra zabezpečení nabízí omezené zabezpečení pouze pro vaše prostředky Azure. [Úroveň Standard Security Center](../../security-center/security-center-onboarding.md) rozšiřuje tyto možnosti na místní prostředky a další cloudy.
Security Center Standard vám pomůže:

  - Vyhledejte a opravte slabá místa zabezpečení.
  - Chcete-li blokovat škodlivou aktivitu, použijte ovládací prvky přístupu a aplikací.
  - Detekujte hrozby pomocí analýzy a inteligence.
  - Reagujte rychle, když jste v útoku.

## <a name="next-steps"></a>Další kroky
V následujících článcích doporučujeme ovládací prvky zabezpečení a aktivity, které vám pomohou navrhovat a vyvíjet zabezpečené aplikace.

- [Navrhujte zabezpečené aplikace](secure-design.md)
- [Vývoj zabezpečených aplikací](secure-develop.md)
