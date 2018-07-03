---
title: Log Analytics pro poskytovatele služeb | Dokumentace Microsoftu
description: Log Analytics může pomoct poskytovatelům spravovaných služeb (MSP) pro velké podniky, nezávislým výrobcům softwaru (ISV) a poskytovatelů hostitelských služeb, spravovat a monitorovat servery v jeho místní nebo cloudovou infrastrukturu.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: meirm
ms.component: na
ms.openlocfilehash: 13f36f67e76b75176940a0f36121be30ba27d519
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340861"
---
# <a name="log-analytics-features-for-service-providers"></a>Funkce log Analytics pro poskytovatele služeb
Log Analytics může pomoct, poskytovatelům spravovaných služeb (MSP), pro velké podniky, nezávislí výrobci softwaru (ISV) a poskytovatelů hostitelských služeb spravovat a monitorovat servery v jeho místní nebo cloudovou infrastrukturu. 

Velké podniky řada podobností s poskytovateli služeb, zejména v případě, že na centralizovaný tým IT, který zodpovídá za správu IT pro mnoho různých organizačních jednotek. Pro zjednodušení tento dokument používá termín *poskytovatele služeb* ale stejné funkce je také k dispozici pro podniky a ostatních zákazníků.

Pro partnery a poskytovatelé, kteří jsou součástí nástroje [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program, Log Analytics je součástí služby Azure, které je k dispozici v [předplatného Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architektury pro poskytovatele služeb

Pracovní prostory log Analytics poskytuje metodu pro správce k řízení toku a izolace protokolů a vytvořit protokol architektura, která řeší její konkrétní obchodní potřeby. [Tento článek](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access) vysvětluje Obecné aspekty kolem pracovního prostoru Správa. Poskytovatelé služeb mají další aspekty.

Existují tři možné architektury pro poskytovatele služeb týkající se pracovních prostorů Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuované – protokoly se ukládají v pracovních prostorech umístěný v tenantovi zákazníka 

V této architektuře pracovního prostoru se nasadí do tenanta zákazníka, který se používá pro všechny protokoly daného zákazníka. Správci poskytovatele služeb je udělen přístup k této pracovní prostor pomocí [Azure Active Directory uživatele typu Host (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b). Přejděte do adresáře zákazníka umožnit přístup k těmto pracovním prostorům na webu Azure portal bude mít správce poskytovatele služeb.

Výhody této architektury jsou:
* Zákazník může spravovat přístup k protokolům pomocí vlastních [přístupu podle rolí](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview).
* Každý zákazník může mít různá nastavení pro jejich pracovní prostor, jako jsou uchovávání a malá a velká data.
* Izolace mezi zákazníky pro regulační a dodržování předpisů.
* Poplatek za každý pracovní prostor se začne do předplatného zákazníka.
* Možné shromažďovat protokoly ze všech typů prostředků, ne jenom založené na agentovi. Například auditování Azure.

Nevýhody této architektury jsou:
* Je to obtížnější pro poskytovatele služby pro správu velkého počtu zákazníků klientů najednou.
* Zprostředkovatel Správci služeb mají zřídit v adresáři zákazníka.
* Poskytovatel služeb analyzovat data napříč svým zákazníkům.

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2. Střed – protokoly se ukládají v pracovním prostoru umístěný v tenantovi poskytovatele služby

V této architektuře protokoly nejsou uložené v tenantech zákazníka, ale pouze v centrálním umístění v rámci jednoho předplatného poskytovatele služeb. Agenty, které jsou nainstalovány na virtuálních počítačích zákazníků jsou nakonfigurovány k odeslání protokolů s jejich tento pracovní prostor pomocí ID a tajný klíč.

Výhody této architektury jsou:
* Je snadné ke správě velkého počtu zákazníků a integrovat je do různých systémů back-endu.
* Poskytovatel služeb má úplné vlastnictví přes protokoly a různých artefaktů, jako je například funkce a uložené dotazy.
* Poskytovatel služeb můžete provádět analýzy napříč všemi zákazníky.

Nevýhody této architektury jsou:
* Bude obtížné oddělit data mezi zákazníky. Jediným dobrým metodou k tomu je použít název domény počítače.
* Všechna data ze všech zákazníků budou uloženy ve stejné oblasti pomocí faktury a stejné nastavení uchování a konfigurace.
* Prostředky infrastruktury Azure a PaaS služby, například Azure Diagnostics a auditování Azure vyžaduje pracovní prostor a být ve stejném tenantovi jako prostředek tak, aby nemohli odesílat protokoly do centrální pracovního prostoru.
* Všichni agenti virtuálního počítače od všech zákazníků bude ověřen na pracovním prostoru cental pomocí stejné ID pracovního prostoru a klíč. Neexistuje žádná metoda blokování protokoly z konkrétního zákazníka, aniž by to ovlivnilo ostatní zákazníci.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybridní - protokoly se ukládají v pracovním prostoru umístěný v tenantovi zákazníka a některé z nich se berou do centrálního umístění.

Třetí architektura poměr mezi těmito dvěma možnostmi. Je založen na první distribuovaná architektura, ve kterém jsou místní pro každého zákazníka protokoly, ale pomocí některé mechanismus, chcete-li vytvořit centrální úložiště protokolů. Část protokoly načítána do centrálního umístění pro generování sestav a analýzy. Tuto část může být malý počet datových typů nebo Souhrn aktivity, jako je například denní statistiky.

Existují dvě možnosti, jak implementovat centrální umístění ve službě Log Analytics:

1. Centrální pracovního prostoru: poskytovatel služeb můžete vytvořit pracovní prostor v jeho tenantovi a použít skript, který využívá [rozhraní API pro dotazy](https://dev.loganalytics.io/) s [rozhraní API kolekce dat](log-analytics-data-collector-api.md) k načítání dat z různých pracovních prostorů k této centrální umístění. Další možností, než skript je použití [aplikace logiky Azure](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview).

2. Power BI jako centrální umístění: Power BI může fungovat jako centrální umístění, pokud různé pracovní prostory export dat pomocí integraci mezi službou Log Analytics a [Power BI](log-analytics-powerbi.md). 


## <a name="next-steps"></a>Další kroky
* Automatizace vytváření a konfiguraci mezi pracovními prostory pomocí [šablon Resource Manageru](log-analytics-template-workspace-configuration.md)
* Automatizace vytváření pracovních prostorů pomocí [prostředí PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Použití [výstrahy](log-analytics-alerts.md) integrovat se stávajícími systémy
* Pomocí souhrnné sestavy [Power BI](log-analytics-powerbi.md)

