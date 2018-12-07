---
title: Log Analytics pro poskytovatele služeb | Dokumentace Microsoftu
description: Log Analytics může pomoct poskytovatelům spravovaných služeb (MSP) pro velké podniky, nezávislým výrobcům softwaru (ISV) a poskytovatelů hostitelských služeb, spravovat a monitorovat servery v jeho místní nebo cloudovou infrastrukturu.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: meirm
ms.component: ''
ms.openlocfilehash: c43b2e265903b119812d16ef4ef329393c725e71
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52992995"
---
# <a name="log-analytics-for-service-providers"></a>Log Analytics pro poskytovatele služeb
Log Analytics může pomoct, poskytovatelům spravovaných služeb (MSP), pro velké podniky, nezávislí výrobci softwaru (ISV) a poskytovatelů hostitelských služeb spravovat a monitorovat servery v jeho místní nebo cloudovou infrastrukturu. 

Velké podniky řada podobností s poskytovateli služeb, zejména v případě, že na centralizovaný tým IT, který zodpovídá za správu IT pro mnoho různých organizačních jednotek. Pro zjednodušení tento dokument používá termín *poskytovatele služeb* ale stejné funkce je také k dispozici pro podniky a ostatních zákazníků.

Pro partnery a poskytovatelé, kteří jsou součástí nástroje [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program, Log Analytics je součástí služby Azure, které je k dispozici v [předplatných Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architektury pro poskytovatele služeb

Pracovní prostory log Analytics poskytuje metodu pro správce k řízení toku a izolace protokolů a vytvořit protokol architektura, která řeší její konkrétní obchodní potřeby. [Tento článek](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) vysvětluje Obecné aspekty kolem pracovního prostoru Správa. Poskytovatelé služeb mají další aspekty.

Existují tři možné architektury pro poskytovatele služeb týkající se pracovních prostorů Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuované – protokoly se ukládají v pracovních prostorech umístěný v tenantovi zákazníka 

V této architektuře pracovního prostoru se nasadí do tenanta zákazníka, který se používá pro všechny protokoly daného zákazníka. Správci poskytovatele služeb je udělen přístup k této pracovní prostor pomocí [Azure Active Directory uživatele typu Host (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Přejděte do adresáře zákazníků na webu Azure Portal bude mít přístup k těmto pracovním prostorům bude mít správci poskytovatele služeb.

Výhody této architektury jsou:
* Zákazník může spravovat přístup k protokolům pomocí vlastních [přístupu podle rolí](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Každý zákazník může mít různá nastavení pro jejich pracovní prostor, jako jsou uchovávání a malá a velká data.
* Izolace mezi zákazníky pro regulační a dodržování předpisů.
* Poplatek za každý pracovní prostor se začne do předplatného zákazníka.
* Možné shromažďovat protokoly ze všech typů prostředků, ne jenom založené na agentovi. Například protokolů auditu Azure.

Nevýhody této architektury jsou:
* Je to obtížnější pro poskytovatele služby pro správu velkého počtu zákazníků klientů najednou.
* Zprostředkovatel Správci služeb mají zřídit v adresáři zákazníka.
* Poskytovatel služeb analyzovat data napříč svým zákazníkům.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Střed – protokoly se ukládají v pracovním prostoru, který je umístěný v tenantovi poskytovatele služby

V této architektuře protokoly nejsou uložené v tenantech zákazníka, ale pouze v centrálním umístění v rámci jednoho předplatného poskytovatele služeb. Agenty, které jsou nainstalovány na virtuálních počítačích zákazníků jsou nakonfigurovány k odeslání protokolů s jejich tento pracovní prostor pomocí ID a tajný klíč.

Výhody této architektury jsou:
* Je snadné ke správě velkého počtu zákazníků a integrovat je do různých systémů back-endu.
* Poskytovatel služeb má úplné vlastnictví přes protokoly a různých artefaktů, jako je například funkce a uložené dotazy.
* Poskytovatel služeb můžete provádět analýzy na všech svých zákazníků.

Nevýhody této architektury jsou:
* Tuto architekturu je možné použít pouze data virtuálního počítače založené na agentovi, nebudeme se zabývat PaaS, SaaS a Azure zdroje dat prostředků infrastruktury.
* Může být obtížné oddělit data mezi zákazníky, když jsou sloučeny do jednoho pracovního prostoru. Je jediným dobrým metodu počítače plně kvalifikovaný název domény (FQDN) nebo prostřednictvím předplatného Azure ID. 
* Všechna data ze všech zákazníků budou uloženy ve stejné oblasti pomocí faktury a stejné nastavení uchování a konfigurace.
* Prostředky infrastruktury Azure a PaaS služby, například Azure Diagnostics a protokoly auditování Azure vyžaduje pracovní prostor a být ve stejném tenantovi jako prostředek, proto, že aby nemohli odesílat protokoly do centrální pracovního prostoru.
* Všichni agenti virtuálního počítače od všech zákazníků bude ověřen na pracovním prostoru cental pomocí stejné ID pracovního prostoru a klíč. Neexistuje žádná metoda blokování protokoly z konkrétního zákazníka, aniž by to ovlivnilo ostatní zákazníci.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybridní - protokoly se ukládají v pracovním prostoru umístěný v tenantovi zákazníka a některé z nich se berou do centrálního umístění.

Třetí architektura poměr mezi těmito dvěma možnostmi. Je založen na první distribuovaná architektura, ve kterém jsou místní pro každého zákazníka protokoly, ale pomocí některé mechanismus, chcete-li vytvořit centrální úložiště protokolů. Část protokoly načítána do centrálního umístění pro generování sestav a analýzy. Tuto část může být malý počet datových typů nebo Souhrn aktivity, jako je například denní statistiky.

Existují dvě možnosti, jak implementovat centrální umístění ve službě Log Analytics:

1. Centrální pracovního prostoru: poskytovatel služeb můžete vytvořit pracovní prostor v jeho tenantovi a použít skript, který využívá [rozhraní API pro dotazy](https://dev.loganalytics.io/) s [rozhraní API kolekce dat](../../azure-monitor/platform/data-collector-api.md) k načítání dat z různých pracovních prostorů k této centrální umístění. Další možností, než skript, je použití [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI jako centrální umístění: Power BI může fungovat jako centrální umístění, pokud různé pracovní prostory export dat pomocí integraci mezi službou Log Analytics a [Power BI](../../azure-monitor/platform/powerbi.md). 


## <a name="next-steps"></a>Další kroky
* Automatizace vytváření a konfiguraci mezi pracovními prostory pomocí [šablon Resource Manageru](template-workspace-configuration.md)
* Automatizace vytváření pracovních prostorů pomocí [prostředí PowerShell](../../log-analytics/log-analytics-powershell-workspace-configuration.md) 
* Použití [výstrahy](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) integrovat se stávajícími systémy
* Pomocí souhrnné sestavy [Power BI](../../azure-monitor/platform/powerbi.md)
* Zkontrolujte proces [konfigurace Log Analytics a Power BI k monitorování více zákazníků CSP](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
