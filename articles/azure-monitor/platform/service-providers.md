---
title: Azure Monitor pro poskytovatele služeb | Microsoft Docs
description: Azure Monitor může pomáhat poskytovatelům spravovaných služeb (účastníci programu MSP), velkým podnikům, nezávislým dodavatelům softwaru (ISV) a poskytovatelům hostitelských služeb spravovat a monitorovat servery v místní nebo cloudové infrastruktuře zákazníka.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 08/06/2019
ms.openlocfilehash: b0f25d01421edd329b03d8f2b7e1aafaa2ba67d5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932066"
---
# <a name="azure-monitor-for-service-providers"></a>Azure Monitor pro poskytovatele služeb
Log Analytics pracovní prostory v Azure Monitor mohou pomáhat poskytovatelům spravovaných služeb (účastníci programu MSP), velkým společnostem, nezávislým výrobcům softwaru a poskytovatelům hostitelských služeb spravovat a monitorovat servery v místní nebo cloudové infrastruktuře zákazníka. 

Velké podniky sdílí spoustu podobností s poskytovateli služeb, zejména v případě, že existuje centralizovaný tým IT, který je zodpovědný za jeho správu v mnoha různých obchodních jednotkách. Pro zjednodušení tento dokument používá *poskytovatele služeb* , ale stejné funkce jsou dostupné i pro podniky a další zákazníky.

Pro partnery a poskytovatele služeb, kteří jsou součástí programu [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , je Log Analytics v Azure monitor jedna ze služeb Azure dostupných v [předplatných Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architektury pro poskytovatele služeb

Log Analytics pracovní prostory poskytují metodu pro správce k řízení toku a izolaci dat [protokolu](data-platform-logs.md) a k vytvoření architektury, která řeší konkrétní obchodní potřeby. [Tento článek](design-logs-deployment.md) popisuje předpoklady pro návrh, nasazení a migraci pro pracovní prostor a článek [Správa přístupu](manage-access.md) popisuje, jak používat a spravovat oprávnění pro data protokolu. Poskytovatelé služeb mají další požadavky.

Existují tři možné architektury pro poskytovatele služeb týkající se Log Analyticsch pracovních prostorů:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. distribuované protokoly se ukládají v pracovních prostorech nacházejících se v tenantovi zákazníka. 

V této architektuře je pracovní prostor nasazený v tenantovi zákazníka, který se používá pro všechny protokoly daného zákazníka. Správcům poskytovatele služeb je udělen přístup k tomuto pracovnímu prostoru pomocí [Azure Active Directory uživatelů typu Host (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Správci poskytovatele služeb budou muset přejít na svůj adresář zákazníka v Azure Portal, aby mohli získat přístup k těmto pracovním prostorům.

Výhody této architektury:
* Zákazník může spravovat přístup k protokolům pomocí vlastního [přístupu založeného na rolích](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Každý zákazník může mít různá nastavení pro svůj pracovní prostor, jako je například uchovávání a capping dat.
* Izolace mezi zákazníky za zákonné a dodržování předpisů.
* Poplatky za každý pracovní prostor se zavádějí do předplatného zákazníka.
* Protokoly je možné shromažďovat ze všech typů prostředků, nikoli jenom pomocí agenta. Například protokoly auditu Azure.

Nevýhody této architektury jsou:
* Poskytovatel služeb je těžší spravovat velký počet zákaznických klientů najednou.
* Správce poskytovatele služeb musí být zřízen v adresáři zákazníka.
* Poskytovatel služby nemůže analyzovat data napříč svými zákazníky.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. centrální protokoly se ukládají v pracovním prostoru umístěném v tenantovi poskytovatele služeb.

V této architektuře se protokoly neukládají v klientech zákazníka, ale jenom v centrálním umístění v rámci jednoho z předplatných poskytovatele služeb. Agenti, kteří jsou nainstalováni na virtuálních počítačích zákazníka, jsou nakonfigurováni tak, aby odesílali své protokoly do tohoto pracovního prostoru pomocí ID a tajného klíče pracovního prostoru.

Výhody této architektury:
* Můžete snadno spravovat velký počet zákazníků a integrovat je do různých systémů back-endu.

* Poskytovatel služeb má plné vlastnictví přes protokoly a různé artefakty, jako jsou funkce a uložené dotazy.

* Poskytovatel služeb může provádět analýzy napříč všemi svými zákazníky.

Nevýhody této architektury jsou:
* Tato architektura se vztahuje jenom na data virtuálních počítačů založená na agentech. nepokrývá PaaS, SaaS a zdroje dat prostředků infrastruktury Azure.

* Oddělení dat mezi zákazníky může být obtížné, když se sloučí do jednoho pracovního prostoru. Jedinou dobrým způsobem, jak to udělat, je použít plně kvalifikovaný název domény (FQDN) počítače nebo ID předplatného Azure. 

* Všechna data všech zákazníků budou uložena ve stejné oblasti s jednou fakturou a stejným nastavením pro uchování a nastavení konfigurace.

* Služby Azure Fabric a PaaS Services, například Azure Diagnostics a protokoly auditu Azure, vyžadují, aby byl pracovní prostor ve stejném tenantovi jako prostředek, a proto nemůže protokoly odesílat do centrálního pracovního prostoru.

* Všichni agenti virtuálních počítačů ze všech zákazníků budou ověřeni do centrálního pracovního prostoru pomocí stejného ID a klíče pracovního prostoru. Neexistuje žádná metoda pro blokování protokolů od určitého zákazníka bez přerušení dalších zákazníků.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. hybridní protokoly jsou uložené v pracovním prostoru umístěném v tenantovi zákazníka a některé z nich se najdou do centrálního umístění.

Třetí architektura je kombinací obou možností. Vychází z první distribuované architektury, kde jsou protokoly místní pro každého zákazníka, ale používají nějaký mechanismus k vytvoření centrálního úložiště protokolů. Část protokolů se načte do centrálního umístění pro vytváření sestav a analýzy. Tato část může být malým počtem datových typů nebo souhrnem aktivity, jako jsou denní statistiky.

Existují dvě možnosti implementace protokolů v centrálním umístění:

1. Centrální pracovní prostor: poskytovatel služeb může ve svém tenantovi vytvořit pracovní prostor a použít skript, který využívá [rozhraní API pro dotazování](https://dev.loganalytics.io/) s [rozhraním API pro shromažďování dat](../../azure-monitor/platform/data-collector-api.md) k přenesení dat z různých pracovních prostorů do tohoto centrálního umístění. Další možností je jiný než skript, je použít [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI jako centrální umístění: Power BI se může chovat jako centrální umístění, když různé pracovní prostory exportují data do tohoto prostředí pomocí integrace mezi Log Analyticsm a [Power BIm](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Další kroky

* Automatizace vytváření a konfigurace pracovních prostorů pomocí [šablon Správce prostředků](template-workspace-configuration.md)

* Automatizace vytváření pracovních prostorů pomocí [PowerShellu](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Integrace s existujícími systémy pomocí [výstrah](../../azure-monitor/platform/alerts-overview.md)

* Generování souhrnných sestav pomocí [Power BI](../../azure-monitor/platform/powerbi.md)

* Kontrola procesu [konfigurace Log Analytics a Power BI pro monitorování více zákazníků CSP](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
