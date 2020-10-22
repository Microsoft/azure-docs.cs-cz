---
title: Plánování sestav & nasazení monitorování – Azure AD
description: Popisuje, jak naplánovat a spustit implementaci generování sestav a monitorování.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 094ecd88c8b493d44b756d03d700b43cbcba1ee9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92362395"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Plánování nasazení Azure Active Directory vytváření sestav a monitorování

Řešení pro vytváření sestav a monitorování pro Azure Active Directory (Azure AD) závisí na vašich právních a provozních požadavcích a na stávajícím prostředí a procesech. Tento článek nabízí různé možnosti návrhu a provede vás se správnou strategií nasazení.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Výhody generování sestav a monitorování Azure AD

Vytváření sestav Azure AD poskytuje komplexní zobrazení a protokoly aktivity Azure AD ve vašem prostředí, včetně událostí přihlášení, událostí auditu a změn v adresáři.

Poskytnutá data vám umožní:

* Určete, jak se budou vaše aplikace a služby používat.

* detekuje potenciální rizika, která mají vliv na stav vašeho prostředí.

* řešení problémů, které uživatelům brání v práci.

* Získejte přehledy na základě událostí auditu změn v adresáři Azure AD.

> [!IMPORTANT]
> Azure AD Monitoring umožňuje směrovat protokoly generované vytvářením sestav Azure AD do různých cílových systémů. Následně je můžete ukládat pro účely dlouhodobého používání nebo integrovat s nástroji pro správu akcí a informací o zabezpečení (SIEM) třetích stran a získat tak přehled o vašem prostředí.

S monitorováním Azure AD můžete směrovat protokoly do:

* účet služby Azure Storage pro účely archivace.
* Protokoly Azure Monitor, dříve označované jako Azure Log Analytics Workspace, kde můžete analyzovat data, vytvářet řídicí panely a upozorňovat na konkrétní události.
* centrum událostí Azure, kde se můžete integrovat s existujícími nástroji SIEM, jako je Splunk, SumoLogic nebo QRadar.

> [!NOTE]
Nedávno jsme začali používat místo Log Analytics Azure Monitor protokoly. Data protokolu se pořád ukládají do Log Analyticsho pracovního prostoru a pořád se shromažďují a analyzují pomocí stejné služby Log Analytics. Aktualizujeme terminologii, aby lépe odrážela roli [protokolů v Azure monitor](../../azure-monitor/platform/data-platform.md). Podrobnosti najdete v tématu [Azure monitor změny terminologie](../../azure-monitor/terminology.md) .

[Přečtěte si další informace o zásadách uchovávání sestav](./reference-reports-data-retention.md).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licencování a předpoklady pro Azure AD Reporting a monitoring

Pro přístup k protokolům přihlášení Azure AD budete potřebovat licenci Azure AD Premium.

Podrobné informace o funkcích a licencování najdete v [Azure Active Directory cenové příručce](https://azure.microsoft.com/pricing/details/active-directory/).

K nasazení monitorování a vytváření sestav Azure AD budete potřebovat uživatele, který je globálním správcem nebo správcem zabezpečení pro tenanta Azure AD.

V závislosti na konečném cíli dat protokolu budete potřebovat jednu z následujících možností:

* Účet úložiště Azure, pro který máte oprávnění ListKeys. Doporučujeme použít obecný účet úložiště, ne účet úložiště objektů blob. Informace o cenách úložiště najdete v [cenové kalkulačce služby Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Obor názvů Azure Event Hubs, který se má integrovat s řešeními SIEM třetích stran.

* Pracovní prostor Azure Log Analytics k odesílání protokolů do protokolů Azure Monitor.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Plánování projektu nasazení služby Azure Reporting a monitoring

V tomto projektu definujete cílové skupiny, které budou využívat a monitorovat sestavy, a definujete svou architekturu monitorování Azure AD.

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, obvykle to vznikne z důvodu neshodných očekávání na dopad, výsledky a zodpovědnosti. Abyste se vyhnuli těmto nástrah, [Ujistěte se, že jste připraví správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md). Také zajistěte, aby se role účastníků v projektu dobře pochopily tím, že dokumentují zúčastněné strany a jejich vstup a accountabilities projektu.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikujte s vašimi uživateli, jak se změní, když se změní, a jak získat podporu, pokud se jim setkávají problémy.

### <a name="document-your-current-infrastructure-and-policies"></a>Dokumentování aktuální infrastruktury a zásad

Vaše aktuální infrastruktura a zásady budou řídit vytváření sestav a monitorování. Ujistěte se, že víte

* Co v některých SIEMch nástrojích, které používáte.

* Vaše infrastruktura Azure, včetně stávajících účtů úložiště a monitorování, se používá.

* Zásady uchovávání informací vaší organizace pro protokoly, včetně požadovaných potřebných rozhraní dodržování předpisů. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Plánování nasazení služby Azure AD Reporting a monitoring

Vytváření sestav a monitorování se používá ke splnění vašich obchodních požadavků, získání přehledu o vzorcích využití a zvýšení zabezpečení stav vaší organizace.

### <a name="business-use-cases"></a>Případy použití firmy

* Vyžadováno pro řešení pro splnění obchodních potřeb
* Skvělé vyhovět potřebám podniku
* Nelze použít

|Plošný |Popis |
|-|-|
|Uchovávání| **Protokolujte uchovávání více než 30 dní**. Vzhledem k právním nebo podnikovým požadavkům se vyžaduje ukládání protokolů auditu a přihlášení do protokolů služby Azure AD déle než 30 dní. |
|Analýzy| **Protokoly musí být prohledávatelné**. Uložené protokoly musí být prohledávatelné pomocí analytických nástrojů. |
| Operational Insights| **Přehledy pro různé týmy**. Potřeba udělit přístup různým uživatelům, aby získali provozní přehledy, jako je použití aplikací, chyby při přihlašování, samoobslužné používání, trendy atd. |
| Přehledy zabezpečení| **Přehledy pro různé týmy**. Potřeba udělit přístup různým uživatelům, aby získali provozní přehledy, jako je použití aplikací, chyby při přihlašování, samoobslužné používání, trendy atd. |
| Integrace v SIEM systémech      | **Integrace Siem**. Nutnost integrace a streamování protokolů přihlášení a auditu Azure AD do stávajících SIEM systémů. |

### <a name="choose-a-monitoring-solution-architecture"></a>Výběr architektury řešení monitorování

Díky monitorování Azure AD můžete směrovat protokoly aktivit Azure AD do systému, který nejlépe vyhovuje vašim obchodním potřebám. Pak je můžete zachovat pro dlouhodobé generování sestav a analýzu, abyste získali přehled o vašem prostředí a mohli je integrovat s SIEM nástroji.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Diagram toku rozhodnutí![Obrázek, který ukazuje, co je popsáno v dalších částech](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Archivace protokolů v účtu úložiště

Směrováním protokolů do účtu služby Azure Storage je můžete uchovat po dobu delší, než je výchozí doba uchování, jak je uvedeno v našich [zásadách uchovávání informací](./reference-reports-data-retention.md). Tuto metodu použijte, pokud potřebujete archivovat protokoly, ale nemusíte je integrovat se systémem SIEM a nepotřebujete průběžné dotazy a analýzu. Pořád můžete provádět hledání na vyžádání.

Informace o [směrování dat do účtu úložiště](./quickstart-azure-monitor-route-logs-to-storage-account.md).

#### <a name="send-logs-to-azure-monitor-logs"></a>Odeslat protokoly do protokolů Azure Monitor

[Protokoly Azure monitor](../../azure-monitor/log-query/log-query-overview.md) slučují data monitorování z různých zdrojů. Poskytuje také dotazovací jazyk a analytický modul, který vám poskytne přehled o provozu aplikací a používání prostředků. Odesláním protokolů aktivit Azure AD do Azure Monitor protokolů můžete shromažďovat, monitorovat a upozorňovat na shromážděná data. Tuto metodu použijte, pokud nemáte existující řešení SIEM, ve kterém chcete data odesílat přímo, ale chcete dotazy a analýzy. Jakmile budou data v Azure Monitor protokoly, můžete je odeslat do centra událostí a odtud do SIEM, pokud chcete.

Naučte se [odesílat data do protokolů Azure monitor](./howto-integrate-activity-logs-with-log-analytics.md).

K monitorování běžných scénářů, které zahrnují události přihlášení a auditu, můžete také nainstalovat předem vytvořená zobrazení protokolů aktivit služby Azure AD.

Naučte se [instalovat a používat zobrazení Log Analytics pro protokoly aktivit Azure AD](./howto-install-use-log-analytics-views.md).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Streamování protokolů do vašeho centra událostí Azure

Směrování protokolů do centra událostí Azure umožňuje integraci s nástroji SIEM třetích stran. Tato integrace umožňuje kombinovat data protokolu aktivit Azure AD s jinými daty spravovanými vaším SIEM a poskytnout tak lepší přehled o vašem prostředí. 

Informace o [streamování protokolů do centra událostí](./tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Plánování operací a zabezpečení pro Azure AD Reporting a monitoring

Účastníci potřebují získat přístup k protokolům Azure AD, abyste získali Operational Insights. Pravděpodobnými uživateli jsou členové týmu zabezpečení, interní nebo externí auditor a provozní tým pro správu identit a přístupu.

Role Azure AD umožňují delegovat možnost konfigurovat a zobrazovat sestavy služby Azure AD na základě vaší role. Určete, kdo ve vaší organizaci potřebuje oprávnění ke čtení sestav Azure AD a jaká role by byla pro ně vhodná. 

Sestavy služby Azure AD mohou číst následující role:

* Globální správce

* Správce zabezpečení

* Čtenář zabezpečení

* Čtenář sestav

Přečtěte si další informace o [rolích pro správu Azure AD](../roles/permissions-reference.md).

*Vždy používejte koncept nejnižších oprávnění ke snížení rizika ohrožení bezpečnosti účtu*. Zvažte implementaci [Privileged Identity Management](../privileged-identity-management/pim-configure.md) k dalšímu zabezpečení vaší organizace.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Nasazení služby Azure AD Reporting a monitoring

V závislosti na tom, jaká rozhodnutí jste provedli dříve pomocí výše uvedeného Průvodce návrhem, vás tato část vás provede v dokumentaci k různým možnostem nasazení.

### <a name="consume-and-archive-azure-ad-logs"></a>Využívání a archivace protokolů služby Azure AD

[Vyhledání sestav aktivit na webu Azure Portal](./howto-find-activity-reports.md)

[Archivace protokolů služby Azure AD na účet Azure Storage](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>Implementace monitorování a analýzy

[Odeslat protokoly do Azure Monitor](./howto-integrate-activity-logs-with-log-analytics.md)

[Instalace a použití zobrazení Log Analytics pro Azure Active Directory](./howto-install-use-log-analytics-views.md)

[Analýza protokolů aktivit Azure AD pomocí protokolů Azure Monitor](./howto-analyze-activity-logs-log-analytics.md)

* [Interpretace schématu protokolů auditu v Azure Monitor](./reference-azure-monitor-audit-log-schema.md)

* [Interpretace schématu přihlášení v Azure Monitor](./reference-azure-monitor-sign-ins-log-schema.md)

 * [Streamování protokolů Azure AD do centra událostí Azure](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [Integrace protokolů Azure AD s nástrojem Splunk pomocí služby Azure Monitor](./howto-integrate-activity-logs-with-splunk.md)

* [Integrace protokolů Azure AD s nástrojem SumoLogic pomocí služby Azure Monitor](./howto-integrate-activity-logs-with-sumologic.md)

 

 

## <a name="next-steps"></a>Další kroky

Zvažte implementaci [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 

Zvažte implementaci [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md) .