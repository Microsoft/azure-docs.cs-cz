---
title: Plánování sestav & monitorování nasazení – Azure AD
description: Popisuje, jak plánovat a provádět implmentování vykazování a monitorování.
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
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232110"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Plánování nasazení sestav a monitorování služby Azure Active Directory

Vaše řešení pro vytváření sestav a monitorování služby Azure Active Directory (Azure AD) závisí na vašich právních, bezpečnostních a provozních požadavcích a na vašem stávajícím prostředí a procesech. Tento článek představuje různé možnosti návrhu a provede vás správnou strategií nasazení.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Výhody vytváření sestav a monitorování azure ad

Sestavy Azure AD poskytuje komplexní zobrazení a protokoly aktivity Azure AD ve vašem prostředí, včetně přihlášení události, události auditu a změny vašeho adresáře.

Poskytnutá data vám umožní:

* určit, jak se vaše aplikace a služby používají.

* potenciální rizika ovlivňující zdraví vašeho životního prostředí.

* řešení problémů, které uživatelům brání v tom, aby svou práci odváděli.

* získejte přehledy tím, že uvidíte události auditu změn v adresáři Azure AD.

> [!IMPORTANT]
> Monitorování Azure AD umožňuje směrovat protokoly generované sestavy Azure AD do různých cílových systémů. Následně je můžete ukládat pro účely dlouhodobého používání nebo integrovat s nástroji pro správu akcí a informací o zabezpečení (SIEM) třetích stran a získat tak přehled o vašem prostředí.

Pomocí monitorování Azure AD můžete směrovat protokoly na:

* účet úložiště Azure pro účely archivace.
* Protokoly Azure Monitor, dříve známé jako pracovní prostor Azure Log Analytics, kde můžete analyzovat data, vytvářet řídicí panely a upozorňovat na konkrétní události.
* Centrum událostí Azure, kde můžete integrovat s vašimi stávajícími nástroji SIEM, jako je Splunk, Sumologic nebo QRadar.

> [!NOTE]
Nedávno jsme začali používat termín Azure Monitor protokoly namísto Log Analytics. Data protokolu jsou stále uložena v pracovním prostoru Log Analytics a jsou stále shromažďována a analyzována stejnou službou Log Analytics. Aktualizujeme terminologii tak, aby lépe odrážela roli [protokolů v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection). Podrobnosti najdete v [tématu změny terminologie Azure Monitoru.](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand)

[Další informace o zásadách uchovávání zpráv v sestavách](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licencování a předpoklady pro vytváření sestav a monitorování Azure AD

Budete potřebovat licenci Azure AD premium pro přístup k protokolům přihlášení Azure AD.

Podrobné informace o funkcích a licencích v [průvodci cenami služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

K nasazení monitorování a vytváření sestav služby Azure AD budete potřebovat uživatele, který je globálním správcem nebo správcem zabezpečení pro klienta Azure AD.

V závislosti na konečném cíli dat protokolu budete potřebovat jednu z následujících možností:

* Účet úložiště Azure, pro který máte oprávnění ListKeys. Doporučujeme použít obecný účet úložiště, ne účet úložiště objektů blob. Informace o cenách úložiště najdete v [cenové kalkulačce služby Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Obor názvů Azure Event Hubs pro integraci s řešeními SIEM třetích stran.

* Pracovní prostor Azure Log Analytics pro odesílání protokolů do protokolů Azure Monitor.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Plánování projektu nasazení azure reportingu a monitorování

V tomto projektu definujete cílové skupiny, které budou využívat a monitorovat sestavy, a definujete architekturu monitorování Azure AD.

### <a name="engage-the-right-stakeholders"></a>Zapojte správné zúčastněné strany

Když technologické projekty selžou, obvykle tak činí kvůli neodpovídajícím očekáváním ohledně dopadu, výsledků a odpovědností. Chcete-li se těmto nástrahám vyhnout, [ujistěte se, že zapojujete správné zúčastněné strany](https://aka.ms/deploymentplans). Rovněž zajistit, aby role zúčastněných stran v projektu byly dobře pochopeny dokumentováním zúčastněných stran a jejich vstupů a odpovědnosti za projekt.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je rozhodující pro úspěch každé nové služby. Proaktivně komunikujte s uživateli o tom, jak se jejich prostředí změní, kdy se změní a jak získat podporu, pokud se u nich vyskytnou problémy.

### <a name="document-your-current-infrastructure-and-policies"></a>Dokumentujte aktuální infrastrukturu a zásady

Vaše aktuální infrastruktura a zásady budou řídit váš návrh vykazování a monitorování. Ujistěte se, že víte,

* Jaké, pokud existuje, siem nástroje, které používáte.

* Vaše infrastruktura Azure, včetně stávajících účtů úložiště a monitorování, které se používají.

* Zásady uchovávání informací v organizaci pro protokoly, včetně všech požadovaných příslušných architektur dodržování předpisů. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Plánování nasazení sestav a monitorování Azure AD

Vytváření sestav a monitorování se používají ke splnění vašich obchodních požadavků, získání přehledu o vzorcích využití a zvýšení stavu zabezpečení vaší organizace.

### <a name="business-use-cases"></a>Obchodní případy použití

* Vyžadováno pro řešení pro splnění obchodních potřeb
* Je hezké, že musím splnit obchodní potřeby
* Neuvedeno

|Oblast |Popis |
|-|-|
|Uchovávání| **Uchovávání protokolu delší než 30 dní**. Z důvodu právních nebo obchodních požadavků je nutné ukládat protokoly auditu a přihlásit protokoly Azure AD delší než 30 dní. |
|Analýza| **Protokoly musí být prohledávatelné**. Uložené protokoly musí být prohledávatelné pomocí analytických nástrojů. |
| Operational Insights| **Přehledy pro různé týmy**. Potřeba poskytnout přístup různým uživatelům, aby získali provozní přehledy, jako je využití aplikací, chyby přihlášení, samoobslužné využití, trendy atd. |
| Přehledy zabezpečení| **Přehledy pro různé týmy**. Potřeba poskytnout přístup různým uživatelům, aby získali provozní přehledy, jako je využití aplikací, chyby přihlášení, samoobslužné využití, trendy atd. |
| Integrace do systémů SIEM      | **integrace SIEM**. Potřeba integrovat a streamovat protokoly přihlášení Azure AD a protokoly auditu do stávajících systémů SIEM. |

### <a name="choose-a-monitoring-solution-architecture"></a>Zvolte architekturu monitorovacího řešení

Díky monitorování Azure AD můžete směrovat protokoly aktivit Azure AD do systému, který nejlépe vyhovuje vašim obchodním potřebám. Pak je můžete uchovávat pro dlouhodobé vytváření sestav a analýzy, abyste získali přehled o vašem prostředí a integrovali je s nástroji SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Vývojový diagram rozhodnutí![Obrázek znázorňující, co je popsáno v následujících částech](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Archivovat protokoly v účtu úložiště

Směrováním protokolů do účtu úložiště Azure je můžete uchovávat déle, než je výchozí doba uchovávání uvedená v našich [zásadách uchovávání informací](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention). Tuto metodu použijte, pokud potřebujete archivovat protokoly, ale nepotřebujete je integrovat se systémem SIEM a nepotřebujete průběžné dotazy a analýzy. Stále můžete vyhledávat na vyžádání.

Informace o [směrování dat do účtu úložiště](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Odesílání protokolů do protokolů Azure Monitoru

[Protokoly Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) upevnit data monitorování z různých zdrojů. Poskytuje také dotazovací jazyk a analytický modul, který vám poskytuje přehled o fungování vašich aplikací a využití prostředků. Odesláním protokolů aktivit Azure AD do protokolů Azure Monitoru můžete rychle načítat, monitorovat a upozorňovat na shromážděná data. Tuto metodu použijte, pokud nemáte existující řešení SIEM, které chcete odeslat data přímo, ale chcete dotazy a analýzy. Jakmile jsou vaše data v protokolech Azure Monitor, pak je můžete odeslat do centra událostí a odtud do SIEM, pokud chcete.

Přečtěte si, jak [odesílat data do protokolů Azure Monitoru](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Můžete také nainstalovat předem připravená zobrazení pro protokoly aktivit Azure AD ke sledování běžných scénářů zahrnujících události přihlášení a auditování.

Zjistěte, jak [nainstalovat a používat zobrazení analýzy protokolů pro protokoly aktivit Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Streamování protokolů do centra událostí Azure

Protokoly směrování do centra událostí Azure umožňují integraci s nástroji SIEM třetích stran. Tato integrace umožňuje kombinovat data protokolu aktivit Azure AD s dalšími daty spravovanými vaším SIEM, abyste poskytli bohatší přehled o vašem prostředí. 

Informace o [streamování protokolů do centra událostí](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Plánování operací a zabezpečení pro vytváření sestav a monitorování azure ad

Zúčastněné strany potřebují přístup k protokolům Azure AD získat provozní přehledy. Mezi pravděpodobné uživatele patří členové týmu zabezpečení, interní nebo externí auditoři a operační tým správy identit a přístupu.

Role Azure AD umožňují delegovat možnost konfigurace a zobrazení sestav Azure AD na základě vaší role. Určete, kdo ve vaší organizaci potřebuje oprávnění ke čtení sestav Azure AD a jaká role by pro ně byla vhodná. 

Následující role můžou číst sestavy Azure AD:

* Globální správce

* Správce zabezpečení

* Čtečka zabezpečení

* Čtečka sestav

Přečtěte si další informace o [rolích pro správu Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

*Vždy použijte koncept nejnižších oprávnění, abyste snížili riziko ohrožení zabezpečení účtu*. Zvažte implementaci [správy privilegovaných identit](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) k dalšímu zabezpečení vaší organizace.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Nasazení sestav a monitorování Azure AD

V závislosti na rozhodnutích, která jste učinili dříve pomocí výše uvedených pokynů k návrhu, vás tato část provede dokumentací o různých možnostech nasazení.

### <a name="consume-and-archive-azure-ad-logs"></a>Využití a archivace protokolů Azure AD

[Vyhledání sestav aktivit na webu Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Archivujte protokoly Azure AD na účet úložiště Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Implementace monitorování a analýzy

[Odesílání protokolů do Služby Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Instalace a použití zobrazení analýzy protokolů pro Službu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Analýza protokolů aktivit Azure AD pomocí protokolů Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Interpretace schématu protokolů auditu v Azure Monitoru](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Interpretace schématu protokolů přihlášení v Azure Monitoru](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Streamování protokolů Azure AD do centra událostí Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrace protokolů Azure AD s nástrojem Splunk pomocí služby Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Integrace protokolů Azure AD s nástrojem SumoLogic pomocí služby Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>Další kroky

Zvažte implementaci [správy privilegovaných identit](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

Zvažte implementaci [řízení přístupu na základě rolí (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
