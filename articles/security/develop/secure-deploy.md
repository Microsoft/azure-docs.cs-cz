---
title: Nasazení zabezpečených aplikací na Microsoft Azure
description: Tento článek popisuje osvědčené postupy, které je třeba vzít v úvahu během fází vydání a odezvy projektu webové aplikace.
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
ms.openlocfilehash: 4c71ddbf1d2b435697b2707acf0b1262f2c5dc31
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517185"
---
# <a name="deploy-secure-applications-on-azure"></a>Nasazení zabezpečených aplikací v Azure
V tomto článku jsou uvedeny bezpečnostní aktivity a ovládací prvky, které je potřeba vzít v úvahu při nasazování aplikací pro Cloud. Pojednává o bezpečnostních otázkách a konceptech, které je potřeba zvážit během fází vydávání verzí a odpovědí v oblasti [SDL (Microsoft Security Development Lifecycle)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) . Cílem je pomáhat vám definovat aktivity a služby Azure, které můžete použít k nasazení bezpečnější aplikace.

V tomto článku jsou uvedené tyto fáze SDL:

- Vydat
- Odpověď

## <a name="release"></a>Vydat
Fáze vydané verze je připravená na projekt pro veřejnou verzi.
To zahrnuje způsoby plánování, které umožňují efektivně provádět úlohy údržby po vydaných verzích a řešit bezpečnostní chyby zabezpečení, ke kterým může dojít později.

### <a name="check-your-applications-performance-before-you-launch"></a>Před spuštěním ověřte výkon vaší aplikace.

Před spuštěním nebo nasazením aktualizací do produkčního prostředí ověřte výkon vaší aplikace. Spusťte cloudové [zátěžové testy](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) pomocí sady Visual Studio, abyste zjistili problémy s výkonem ve vaší aplikaci, Vylepšete kvalitu nasazení, zajistěte, aby byla aplikace vždy dostupná nebo dostupná a aby mohla vaše aplikace zpracovat provoz pro vaše spuštění.

### <a name="install-a-web-application-firewall"></a>Instalace firewallu webových aplikací

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tato zneužití patří mezi ně útoky prostřednictvím injektáže SQL a útoky skriptování napříč weby. Zabránění těmto útokům v kódu aplikace může být náročné. Může vyžadovat přísnou údržbu, opravy a monitorování v mnoha vrstvách topologie aplikace. Centralizované WAF pomáhá zjednodušit správu zabezpečení. Řešení WAF může také reagovat na bezpečnostní hrozbu tím, že opraví známou chybu zabezpečení v centrálním umístění a zabezpečí každou jednotlivou webovou aplikaci.

[Azure Application Gateway WAF](../../web-application-firewall/ag/ag-overview.md) poskytuje centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení. WAF je založen na pravidlech ze [sad pravidel OWASP core](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 nebo 2.2.9.

### <a name="create-an-incident-response-plan"></a>Vytvoření plánu reakcí na incidenty

Příprava plánu reakce na incidenty je zásadní, aby vám pomohla řešit nové hrozby, které by se mohly v průběhu času vylepšovat. Příprava plánu reakce na incidenty zahrnuje identifikaci příslušných nouzových kontaktů zabezpečení a vytváření plánů údržby zabezpečení pro kód, který je zděděný z jiných skupin v organizaci a pro licencovaný kód třetí strany.

### <a name="conduct-a-final-security-review"></a>Provést závěrečnou kontrolu zabezpečení

Úmyslné prověřování všech provedených aktivit zabezpečení pomáhá zajistit připravenost pro vydání softwaru nebo aplikaci. Poslední revize zabezpečení (FSR) obvykle zahrnuje prověřování modelů hrozeb, výstupů nástrojů a výkonu proti branám kvality a chybám, které byly definovány ve fázi požadavků.

### <a name="certify-release-and-archive"></a>Certifikovat vydání a archivace

Certifikace softwaru před vydáním pomáhá zajistit splnění požadavků na zabezpečení a ochranu osobních údajů. Archivace všech relevantních dat je zásadní pro provádění úloh údržby po vydání. Archivace také pomáhá snižovat dlouhodobé náklady spojené s udržitelným softwarovým inženýry.

## <a name="response"></a>Odpověď
Fáze vydaných odpovědí na vydanou verzi ve vývojovém týmu jsou schopná a dostupná tak, aby odpovídala odpovídajícím zprávám vznikajícím hrozbám a ohrožením softwaru.

### <a name="execute-the-incident-response-plan"></a>Spustit plán reakce na incidenty

Schopnost implementovat plán reakce na incidenty, zřízený ve fázi vydávání verzí, je zásadní pro pomoc při ochraně zákazníků před zabezpečením softwaru nebo zjištěnými chybami zabezpečení ochrany osobních údajů.

### <a name="monitor-application-performance"></a>Monitorování výkonu aplikací

Průběžné monitorování aplikace po jejím nasazení vám pomůže detekovat problémy s výkonem a také chyby zabezpečení.
Služby Azure, které pomáhají s monitorováním aplikací, jsou:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) je rozšiřitelná služba správy výkonu aplikací (APM) pro webové vývojáře na různých platformách. Slouží k monitorování živé webové aplikace. Application Insights automaticky detekuje anomálie výkonu. Obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé s vaší aplikací skutečně dělají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure, včetně webových aplikací, a kontrolu nad nimi. Azure Security Center pomáhá detekovat hrozby, které by jinak neinformovaly. Funguje s různými řešeními zabezpečení.

Úroveň Free Security Center nabízí jenom omezené zabezpečení pro vaše prostředky Azure. [Úroveň Standard Security Center](../../security-center/security-center-get-started.md) rozšiřuje tyto možnosti na místní prostředky a další cloudy.
Security Center Standard vám pomůže:

  - Najděte a opravte slabá místa zabezpečení.
  - Použijte ovládací prvky přístupu a aplikace k blokování škodlivých aktivit.
  - Detekuje hrozby pomocí analýz a inteligentních informací.
  - Rychlá reakce v případě útoku

## <a name="next-steps"></a>Další kroky
V následujících článcích doporučujeme kontrolu zabezpečení a aktivity, které vám pomůžou navrhovat a vyvíjet zabezpečené aplikace.

- [Návrh zabezpečených aplikací](secure-design.md)
- [Vývoj zabezpečených aplikací](secure-develop.md)