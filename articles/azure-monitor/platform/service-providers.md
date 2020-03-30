---
title: Protokoly monitorování Azure pro poskytovatele služeb | Dokumenty společnosti Microsoft
description: Protokoly monitorování Azure můžou poskytovatelům spravovaných služeb (MSPs), velkým podnikům, nezávislým dodavatelům softwaru (ISV) a poskytovatelům hostingových služeb spravovat a monitorovat servery v místní nebo cloudové infrastruktuře zákazníka.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658876"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Protokoly monitorování Azure pro poskytovatele služeb

Pracovní prostory Log Analytics ve službě Azure Monitor můžou pomoci poskytovatelům spravovaných služeb (MSP), velkým podnikům, nezávislým dodavatelům softwaru (ISV) a poskytovatelům hostingových služeb spravovat a monitorovat servery v místní nebo cloudové infrastruktuře zákazníka.

Velké podniky sdílejí mnoho podobností s poskytovateli služeb, zejména pokud existuje centralizovaný IT tým, který je zodpovědný za správu IT pro mnoho různých obchodních jednotek. Pro jednoduchost tento dokument používá termín *poskytovatel služeb,* ale stejné funkce jsou k dispozici také pro podniky a ostatní zákazníky.

Pro partnery a poskytovatele služeb, kteří jsou součástí programu [Zprostředkovatel cloudových řešení (CSP),](https://partner.microsoft.com/Solutions/cloud-reseller-overview) je Log Analytics v Azure Monitoru jednou ze služeb Azure dostupných v předplatných Azure CSP.

Log Analytics v Azure Monitoru může taky používat poskytovatel služeb, který spravuje prostředky zákazníků prostřednictvím funkce správy delegovaných prostředků Azure v [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="architectures-for-service-providers"></a>Architektury pro poskytovatele služeb

Pracovní prostory Log Analytics poskytují správci metodu k řízení toku a izolace dat [protokolu](data-platform-logs.md) a vytvoření architektury, která řeší jeho konkrétní obchodní potřeby. [Tento článek](design-logs-deployment.md) vysvětluje aspekty návrhu, nasazení a migrace pro pracovní prostor a článek [o přístupu ke správě](manage-access.md) popisuje, jak použít a spravovat oprávnění k protokolování dat. Poskytovatelé služeb mají další aspekty.

Existují tři možné architektury pro poskytovatele služeb týkající se pracovních prostorů Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuované – protokoly jsou uloženy v pracovních prostorech umístěných v klientovi zákazníka

V této architektuře je v tenantovi zákazníka nasazen pracovní prostor, který se používá pro všechny protokoly tohoto zákazníka.

Existují dva způsoby, jak mohou správci poskytovatelů služeb získat přístup k pracovnímu prostoru Log Analytics v klientovi zákazníka:

- Zákazník může přidat jednotlivé uživatele z poskytovatele služeb jako [uživatele typu Host služby Azure Active Directory (B2B).](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) Správci poskytovatelů služeb se budou muset přihlásit do adresáře každého zákazníka na webu Azure Portal, aby měli přístup k těmto pracovním prostorům. To také vyžaduje, aby zákazníci měli spravovat individuální přístup pro každého správce poskytovatele služeb.
- Pro větší škálovatelnost a flexibilitu můžou poskytovatelé služeb využít možnosti [správy delegovaných prostředků Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) Lighthouse v Azure [Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) pro přístup k tenantovi zákazníka. Pomocí této metody jsou správci poskytovatele služeb zahrnuti do skupiny uživatelů Azure AD v tenantovi poskytovatele služeb a této skupině je udělen přístup během procesu registrace pro každého zákazníka. Tito správci pak mohou přistupovat k pracovním prostorům každého zákazníka z vlastního tenanta poskytovatele služeb, místo aby se museli přihlašovat k klientovi každého zákazníka jednotlivě. Přístup k prostředkům pracovních prostorů Log Analytics vašich zákazníků tímto způsobem snižuje práci požadovanou na straně zákazníka a může usnadnit shromažďování a analýzu dat mezi více zákazníky spravovaným stejným poskytovatelem služeb prostřednictvím nástrojů, jako jsou [sešity Azure Monitor](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview). Další informace naleznete v [tématu Sledování zdrojů zákazníků ve velkém měřítku](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

Výhody distribuované architektury jsou:

* Zákazník může potvrdit konkrétní úrovně oprávnění prostřednictvím [azure delegované správy prostředků](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)nebo můžete spravovat přístup k protokolům pomocí vlastního [přístupu založeného na rolích](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Protokoly lze shromažďovat ze všech typů prostředků, nikoli pouze z dat virtuálních počítačů založených na agentovi. Například protokoly auditu Azure.
* Každý zákazník může mít různá nastavení pro svůj pracovní prostor, jako je uchovávání a omezení dat.
* Izolace mezi zákazníky pro regulaci a kompliancy.
* Poplatek za každý pracovní prostor bude zahrnut do předplatného zákazníka.

Nevýhody distribuované architektury jsou:

* Centrálně vizualizace a analýza dat napříč klienty zákazníků pomocí nástrojů, jako jsou sešity Azure Monitor, může mít za následek pomalejší prostředí , zejména při analýze dat ve více než 50+ pracovních prostorech.
* Pokud zákazníci nejsou na palubě pro azure delegované správy prostředků, musí být zřízena správci zprostředkovatele služeb v adresáři zákazníka a je těžší pro poskytovatele služeb spravovat velký počet klientů zákazníků najednou.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Centrální – protokoly jsou uloženy v pracovním prostoru umístěném v tenantovi poskytovatele služeb.

V této architektuře protokoly nejsou uloženy v klientech zákazníka, ale pouze v centrálním umístění v rámci jednoho z předplatného poskytovatele služeb. Agenti, kteří jsou nainstalováni na virtuálních počítačích zákazníka, jsou nakonfigurováni tak, aby odesílali své protokoly do tohoto pracovního prostoru pomocí ID pracovního prostoru a tajného klíče.

Výhody centralizované architektury jsou:

* Je snadné spravovat velký počet zákazníků a integrovat je do různých back-endových systémů.
* Poskytovatel služeb má plné vlastnictví přes protokoly a různé artefakty, jako jsou funkce a uložené dotazy.
* Poskytovatel služeb může provádět analýzy napříč všemi svými zákazníky.

Nevýhody centralizované architektury jsou:

* Tato architektura je použitelná jenom pro data virtuálních počítačů založených na agentovi, nebude zahrnovat zdroje dat PaaS, SaaS a Azure fabric.
* Může být obtížné oddělit data mezi zákazníky, když jsou sloučeni do jednoho pracovního prostoru. Jedinou dobrou metodou, jak to udělat, je použít plně kvalifikovaný název domény (FQDN) počítače nebo prostřednictvím ID předplatného Azure. 
* Všechna data od všech zákazníků budou uložena ve stejné oblasti s jednou fakturou a stejným nastavením uchovávání a konfigurace.
* Azure fabric a PaaS služby, jako je diagnostika Azure a protokoly auditu Azure vyžaduje, aby pracovní prostor byl ve stejném tenantovi jako prostředek, proto nemohou odesílat protokoly do centrálního pracovního prostoru.
* Všichni agenti virtuálních počítačů od všech zákazníků budou ověřeni do centrálního pracovního prostoru pomocí stejného ID pracovního prostoru a klíče. Neexistuje žádná metoda blokovat protokoly od konkrétního zákazníka bez přerušení ostatních zákazníků.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybridní - Protokoly jsou uloženy v pracovním prostoru umístěném v klientovi zákazníka a některé z nich jsou vytaženy do centrálního umístění.

Třetí architektura mix mezi těmito dvěma možnostmi. Je založen na první distribuované architektury, kde protokoly jsou místní pro každého zákazníka, ale pomocí některého mechanismu k vytvoření centrální úložiště protokolů. Část protokolů je vyžádána do centrálního umístění pro vytváření sestav a analýzy. Tato část může být malý počet datových typů nebo souhrn aktivity, jako jsou denní statistiky.

Existují dvě možnosti implementace protokolů v centrálním umístění:

1. Centrální pracovní prostor: Poskytovatel služeb může vytvořit pracovní prostor ve svém tenantovi a použít skript, který využívá [rozhraní API dotazu](https://dev.loganalytics.io/) s [rozhraním API pro shromažďování dat,](../../azure-monitor/platform/data-collector-api.md) aby data z různých pracovních prostorů do tohoto centrálního umístění. Další možností, než je skript, je použití [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI jako centrální umístění: Power BI může fungovat jako centrální umístění, když do něj různé pracovní prostory exportují data pomocí integrace mezi pracovním prostorem Log Analytics a [Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Další kroky

* Automatizace vytváření a konfigurace pracovních prostorů pomocí [šablon Správce prostředků](template-workspace-configuration.md)

* Automatizace vytváření pracovních prostorů pomocí [PowerShellu](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Integrace se stávajícími systémy pomocí [výstrah](../../azure-monitor/platform/alerts-overview.md)

* Generování souhrnných sestav pomocí [Power BI](../../azure-monitor/platform/powerbi.md)

* Zapalubí zákazníky do [Azure delegované správy prostředků](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
