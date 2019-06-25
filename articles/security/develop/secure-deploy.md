---
title: Nasazení zabezpečených aplikací v Microsoft Azure
description: Tento článek popisuje osvědčené postupy, které byste měli zvážit během fáze uvolnění a odpověď z projektu webové aplikace.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e8249113ee65c28414c79f00c53d11596673434b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144448"
---
# <a name="deploy-secure-applications-on-azure"></a>Nasazení zabezpečené aplikace v Azure
V tomto článku Představujeme aktivity související se zabezpečením a ovládací prvky, které je třeba zvážit při nasazování aplikací pro cloud. Bezpečnostní otázky a koncepty, které je vhodné zvážit během fáze uvolnění a odpovědi Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) se vztahuje. Cílem je může pomoci určit činností a služeb Azure, které vám umožní nasadit více zabezpečenou webovou aplikaci.

Následující fáze SDL jsou popsané v tomto článku:

- Vydat
- Odpověď

## <a name="release"></a>Vydat
Fokus fáze vydání je readying projekt pro veřejné vydané verze.
To zahrnuje plánování způsoby, jak efektivně provádět úlohy údržby po vydání a řešit slabá místa zabezpečení, které mohou nastat později.

### <a name="check-your-applications-performance-before-you-launch"></a>Kontrola výkonu aplikace před spuštěním

Kontrola výkonu vaší aplikace předtím, než ji spustit nebo nasadit aktualizace do produkčního prostředí. Spuštění založené na cloudu [zátěžové testy](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) pomocí sady Visual Studio k zjišťování problémů s výkonem v aplikaci zlepšovat kvalitu nasazení, ujistěte se, že je vaše aplikace vždycky nahoru nebo k dispozici a že vaše aplikace dokáže zpracovat provoz pro uvedení na trh.

### <a name="install-a-web-application-firewall"></a>Instalace firewallu webových aplikací

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Společné tyto zneužití jsou útoky prostřednictvím injektáže SQL a útoky skriptování napříč weby. Zabraňuje těmto útokům v kódu aplikace může být náročné. To může vyžadovat pečlivou údržbu, opravy a monitorování na mnoha úrovních aplikační topologie. Centralizované WAF jednodušeji bezpečnostní správu jednodušší. Řešení WAF může také reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých jednotlivých webových aplikací.

[Waf služby Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/waf-overview) poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožení zabezpečení. WAF je na základě pravidel ze [základní sady pravidel OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9.

### <a name="create-an-incident-response-plan"></a>Vytvoření plánu reakcí na incidenty

Příprava plánu reakcí na incidenty je velmi důležité při řešení nové hrozby, které se můžou objevují v čase. Příprava plánu reakcí na incidenty zahrnuje identifikaci nouzové kontakty zabezpečení a zabezpečení pro kód, který je zděděno z jiných skupin v organizaci a pro licencované kód třetích stran plány údržby.

### <a name="conduct-a-final-security-review"></a>Provedení revize zabezpečení konečný

Záměrně zkontrolujete všechny aktivity související se zabezpečením, které se prováděly pomáhá zajistit připravenost pro verzi softwaru nebo aplikace. Zkontrolujte poslední zabezpečení (FSR) obvykle zahrnuje zkoumání modely hrozeb, výstupy nástrojů pro a proti brány kvality a panely chyb, které byly definované ve fázi, požadavky na výkon.

### <a name="certify-release-and-archive"></a>Certifikovat vydání a archivace

Certifikace software před vydání pomáhá zajistit, že jsou splněny požadavky na zabezpečení a ochrany osobních údajů. Archivace všechny relevantní dat je nezbytné k provádění úloh údržby po vydání. Archivace také pomáhá nižším dlouhodobé náklady spojené s zachovaného softwarového inženýrství.

## <a name="response"></a>Odpověď
Po vydání fáze odpovědi centra na vývojový tým může a dostupné správně reagovat na všechny sestavy vznikajícími hrozbami softwaru a ohrožení zabezpečení.

### <a name="execute-the-incident-response-plan"></a>Spuštění plánu reakcí na incidenty

Možnost k implementaci plánu reakcí na incidenty zaveden ve fázi vydání je nezbytné k ochraně zákazníků z ohrožení zabezpečení nebo ochranu osobních údajů softwaru, které se objevují.

### <a name="monitor-application-performance"></a>Monitorování výkonu aplikací

Průběžné monitorování vaší aplikace po nasazení potenciálně umožňuje detekovat problémy s výkonem, jakož i ohrožení zabezpečení.
Služby Azure, které pomáhají s monitorováním aplikací jsou:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba správu výkonu aplikací (APM) pro webové vývojáře ve více platformách. Slouží k monitorování živé webové aplikace. Application Insights automaticky zjišťuje anomálie výkonu. Obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé dělají s vaší aplikací. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomáhá zabránit, detekci a reakce na ně prostřednictvím zvýšené viditelnosti (a kontrolu nad) zabezpečení vašich prostředků Azure, včetně webových aplikací. Azure Security Center pomáhá zjišťovat hrozby, které jinak nevšimli. Funguje to různá řešení zabezpečení.

Bezplatná úroveň Security Center nabízí omezené zabezpečení vašich prostředků Azure pouze. [Security Center úrovně Standard](https://docs.microsoft.com/azure/security-center/security-center-onboarding) tyto možnosti rozšiřuje do místní prostředky i v jiných cloudech.
Security Center úrovně Standard vám pomůže:

  - Vyhledejte a opravte slabá místa zabezpečení.
  - Použití řízení přístupu a aplikací blokuje škodlivé aktivity.
  - Detekce hrozeb pomocí analýz a inteligentních funkcí.
  - Rychle reagujte v případě útoku.

## <a name="next-steps"></a>Další postup
V následujících článcích doporučujeme kontrolních mechanismů pro zabezpečení a aktivity, které vám pomohou navrhovat a vyvíjet bezpečné aplikace.

- [Návrh zabezpečených aplikací](secure-design.md)
- [Vývoj zabezpečených aplikací](secure-develop.md)
