---
title: Azure Monitor pro poskytovatele služeb | Microsoft Docs
description: Azure Monitor může pomáhat poskytovatelům spravovaných služeb (účastníci programu MSP), velkým podnikům, nezávislým dodavatelům softwaru (ISV) a poskytovatelům hostitelských služeb spravovat a monitorovat servery v místní nebo cloudové infrastruktuře zákazníka.
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
ms.date: 08/06/2019
ms.author: meirm
ms.openlocfilehash: 971757a4778dd50be486bead0c50fd6b3a25002e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839271"
---
# <a name="azure-monitor-for-service-providers"></a>Azure Monitor pro poskytovatele služeb
Log Analytics pracovní prostory v Azure Monitor mohou pomáhat poskytovatelům spravovaných služeb (účastníci programu MSP), velkým společnostem, nezávislým výrobcům softwaru a poskytovatelům hostitelských služeb spravovat a monitorovat servery v místní nebo cloudové infrastruktuře zákazníka. 

Velké podniky řada podobností s poskytovateli služeb, zejména v případě, že na centralizovaný tým IT, který zodpovídá za správu IT pro mnoho různých organizačních jednotek. Pro zjednodušení tento dokument používá termín *poskytovatele služeb* ale stejné funkce je také k dispozici pro podniky a ostatních zákazníků.

Pro partnery a poskytovatele služeb, kteří jsou součástí programu [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , je Log Analytics v Azure monitor jedna ze služeb Azure dostupných v předplatných [Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architektury pro poskytovatele služeb

Log Analytics pracovní prostory poskytují metodu pro správce k řízení toku a izolaci dat [protokolu](data-platform-logs.md) a k vytvoření architektury, která řeší konkrétní obchodní potřeby. [Tento článek](design-logs-deployment.md) popisuje předpoklady pro návrh, nasazení a migraci pro pracovní prostor a článek [Správa přístupu](manage-access.md) popisuje, jak používat a spravovat oprávnění pro data protokolu. Poskytovatelé služeb mají další aspekty.

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

* Všichni agenti virtuálních počítačů ze všech zákazníků budou ověřeni do centrálního pracovního prostoru pomocí stejného ID a klíče pracovního prostoru. Neexistuje žádná metoda blokování protokoly z konkrétního zákazníka, aniž by to ovlivnilo ostatní zákazníci.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybridní - protokoly se ukládají v pracovním prostoru umístěný v tenantovi zákazníka a některé z nich se berou do centrálního umístění.

Třetí architektura poměr mezi těmito dvěma možnostmi. Je založen na první distribuovaná architektura, ve kterém jsou místní pro každého zákazníka protokoly, ale pomocí některé mechanismus, chcete-li vytvořit centrální úložiště protokolů. Část protokoly načítána do centrálního umístění pro generování sestav a analýzy. Tuto část může být malý počet datových typů nebo Souhrn aktivity, jako je například denní statistiky.

Existují dvě možnosti implementace protokolů v centrálním umístění:

1. Centrální pracovní prostor: Poskytovatel služeb může ve svém tenantovi vytvořit pracovní prostor a použít skript, který využívá [rozhraní API](https://dev.loganalytics.io/) pro dotazování s [rozhraním API pro shromažďování dat](../../azure-monitor/platform/data-collector-api.md) k přenesení dat z různých pracovních prostorů do tohoto centrálního umístění. Další možností, než skript, je použití [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI jako centrální umístění: Power BI může fungovat jako centrální umístění, když různé pracovní prostory exportují data do IT pomocí integrace mezi pracovním prostorem Log Analytics a [Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Další postup

* Automatizace vytváření a konfiguraci mezi pracovními prostory pomocí [šablon Resource Manageru](template-workspace-configuration.md)

* Automatizace vytváření pracovních prostorů pomocí [prostředí PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Použití [výstrahy](../../azure-monitor/platform/alerts-overview.md) integrovat se stávajícími systémy

* Pomocí souhrnné sestavy [Power BI](../../azure-monitor/platform/powerbi.md)

* Zkontrolujte proces [konfigurace Log Analytics a Power BI k monitorování více zákazníků CSP](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
