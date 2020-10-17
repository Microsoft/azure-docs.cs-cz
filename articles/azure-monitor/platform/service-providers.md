---
title: Protokoly Azure Monitor pro poskytovatele služeb | Microsoft Docs
description: Protokoly Azure Monitor můžou pomáhat poskytovatelům spravovaných služeb (účastníci programu MSP), velkým podnikům, nezávislým dodavatelům softwaru a poskytovatelům hostitelských služeb spravovat a monitorovat servery v místní nebo cloudové infrastruktuře zákazníka.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: d92dd191c8bfe590f6dab392ff679e5d7712ae6c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143861"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Protokoly Azure Monitor pro poskytovatele služeb

Log Analytics pracovní prostory v Azure Monitor mohou pomáhat poskytovatelům spravovaných služeb (účastníci programu MSP), velkým společnostem, nezávislým výrobcům softwaru a poskytovatelům hostitelských služeb spravovat a monitorovat servery v místní nebo cloudové infrastruktuře zákazníka.

Velké podniky sdílí spoustu podobností s poskytovateli služeb, zejména v případě, že existuje centralizovaný tým IT, který je zodpovědný za jeho správu v mnoha různých obchodních jednotkách. Pro zjednodušení tento dokument používá *poskytovatele služeb* , ale stejné funkce jsou dostupné i pro podniky a další zákazníky.

Pro partnery a poskytovatele služeb, kteří jsou součástí programu [Cloud Solution Provider (CSP)](https://partner.microsoft.com/membership/cloud-solution-provider) , je Log Analytics v Azure monitor jedna ze služeb Azure dostupných v předplatných Azure CSP.

Log Analytics v Azure Monitor může také používat poskytovatel služeb, který spravuje prostředky zákazníka prostřednictvím funkce správy delegovaných prostředků Azure v [Azure Lighthouse](../../lighthouse/overview.md).

## <a name="architectures-for-service-providers"></a>Architektury pro poskytovatele služeb

Log Analytics pracovní prostory poskytují metodu pro správce k řízení toku a izolaci dat [protokolu](data-platform-logs.md) a k vytvoření architektury, která řeší konkrétní obchodní potřeby. [Tento článek](design-logs-deployment.md) popisuje předpoklady pro návrh, nasazení a migraci pro pracovní prostor a článek [Správa přístupu](manage-access.md) popisuje, jak používat a spravovat oprávnění pro data protokolu. Poskytovatelé služeb mají další požadavky.

Existují tři možné architektury pro poskytovatele služeb týkající se Log Analyticsch pracovních prostorů:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. distribuované protokoly se ukládají v pracovních prostorech nacházejících se v tenantovi zákazníka.

V této architektuře je pracovní prostor nasazený v tenantovi zákazníka, který se používá pro všechny protokoly daného zákazníka.

Existují dva způsoby, jak můžou správci poskytovatele služeb získat přístup k pracovnímu prostoru Log Analytics v tenantovi zákazníka:

- Zákazník může přidat jednotlivé uživatele od poskytovatele služeb jako [Azure Active Directory uživatelů typu Host (B2B)](../../active-directory/external-identities/what-is-b2b.md). Správci poskytovatele služeb se budou muset přihlásit ke všem adresářům zákazníka v Azure Portal, aby měli přístup k těmto pracovním prostorům. To také vyžaduje, aby zákazníci mohli spravovat individuální přístup pro každého správce služby Service Provider.
- Pro zajištění větší škálovatelnosti a flexibility můžou poskytovatelé služeb využít možnost [správy delegovaných prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md) ve [službě Azure Lighthouse](../../lighthouse/overview.md) pro přístup k tenantovi zákazníka. Pomocí této metody jsou správci poskytovatele služeb zahrnuti do skupiny uživatelů Azure AD v tenantovi poskytovatele služeb a tato skupina má přístup k této skupině během procesu připojování pro každého zákazníka. Tito správci pak můžou přístup k pracovním prostorům každého zákazníka z vlastního tenanta poskytovatele služeb, ale nemusí se přihlašovat jednotlivě k tenantovi jednotlivých zákazníků. Přístup k prostředkům pracovních prostorů Log Analytics vašich zákazníků tímto způsobem omezuje práci na straně zákazníka a usnadňuje shromažďování a analýzu dat napříč několika zákazníky spravovanými stejným poskytovatelem služeb prostřednictvím nástrojů, jako jsou například [sešity Azure monitor](./workbooks-overview.md). Další informace najdete v tématu [monitorování zákaznických prostředků ve velkém měřítku](../../lighthouse/how-to/monitor-at-scale.md).

Mezi výhody distribuované architektury patří:

* Zákazník může ověřit konkrétní úrovně oprávnění prostřednictvím [delegované správy prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md)nebo může spravovat přístup k protokolům pomocí vlastního [řízení přístupu založeného na rolích Azure (Azure RBAC)](../../role-based-access-control/overview.md).
* Protokoly je možné shromažďovat ze všech typů prostředků, nikoli jenom z dat virtuálních počítačů založených na agentech. Například protokoly auditu Azure.
* Každý zákazník může mít různá nastavení pro svůj pracovní prostor, jako je například uchovávání a capping dat.
* Izolace mezi zákazníky za zákonné a dodržování předpisů.
* Poplatky za každý pracovní prostor se zavádějí do předplatného zákazníka.

Mezi nevýhody distribuované architektury patří:

* Centralizované vizualizace a analýza dat napříč klienty zákazníka pomocí nástrojů, jako jsou Azure Monitor sešity, můžou mít za následek pomalejší prostředí, zejména při analýze dat napříč více než 50 + pracovními prostory.
* Pokud se zákazníkům pro správu delegovaných prostředků Azure nepřipojíte, musí se správci poskytovatele služeb zřídit v adresáři zákazníka a pro poskytovatele služeb je těžší spravovat velký počet zákaznických klientů najednou.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. centrální protokoly se ukládají v pracovním prostoru umístěném v tenantovi poskytovatele služeb.

V této architektuře se protokoly neukládají v klientech zákazníka, ale jenom v centrálním umístění v rámci jednoho z předplatných poskytovatele služeb. Agenti, kteří jsou nainstalováni na virtuálních počítačích zákazníka, jsou nakonfigurováni tak, aby odesílali své protokoly do tohoto pracovního prostoru pomocí ID a tajného klíče pracovního prostoru.

Výhody centralizované architektury jsou:

* Můžete snadno spravovat velký počet zákazníků a integrovat je do různých systémů back-endu.
* Poskytovatel služeb má plné vlastnictví přes protokoly a různé artefakty, jako jsou funkce a uložené dotazy.
* Poskytovatel služeb může provádět analýzy napříč všemi svými zákazníky.

Nevýhody centralizované architektury jsou:

* Tato architektura se vztahuje jenom na data virtuálních počítačů založená na agentech. nepokrývá PaaS, SaaS a zdroje dat prostředků infrastruktury Azure.
* Oddělení dat mezi zákazníky může být obtížné, když se sloučí do jednoho pracovního prostoru. Jedinou dobrým způsobem, jak to udělat, je použít plně kvalifikovaný název domény (FQDN) počítače nebo ID předplatného Azure.
* Všechna data všech zákazníků budou uložena ve stejné oblasti s jednou fakturou a stejným nastavením pro uchování a nastavení konfigurace.
* Služby Azure Fabric a PaaS Services, například Azure Diagnostics a protokoly auditu Azure, vyžadují, aby byl pracovní prostor ve stejném tenantovi jako prostředek, a proto nemůže protokoly odesílat do centrálního pracovního prostoru.
* Všichni agenti virtuálních počítačů ze všech zákazníků budou ověřeni do centrálního pracovního prostoru pomocí stejného ID a klíče pracovního prostoru. Neexistuje žádná metoda pro blokování protokolů od určitého zákazníka bez přerušení dalších zákazníků.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. hybridní protokoly jsou uložené v pracovním prostoru umístěném v tenantovi zákazníka a některé z nich se najdou do centrálního umístění.

Třetí architektura je kombinací obou možností. Vychází z první distribuované architektury, kde jsou protokoly místní pro každého zákazníka, ale používají nějaký mechanismus k vytvoření centrálního úložiště protokolů. Část protokolů se načte do centrálního umístění pro vytváření sestav a analýzy. Tato část může být malým počtem datových typů nebo souhrnem aktivity, jako jsou denní statistiky.

Existují dvě možnosti implementace protokolů v centrálním umístění:

1. Centrální pracovní prostor: poskytovatel služeb může ve svém tenantovi vytvořit pracovní prostor a použít skript, který využívá [rozhraní API pro dotazování](https://dev.loganalytics.io/) s [rozhraním API pro shromažďování dat](./data-collector-api.md) k přenesení dat z různých pracovních prostorů do tohoto centrálního umístění. Další možností je jiný než skript, je použít [Azure Logic Apps](../../logic-apps/logic-apps-overview.md).

2. Power BI jako centrální umístění: Power BI se může chovat jako centrální umístění, když různé pracovní prostory exportují data do tohoto prostředí pomocí integrace mezi Log Analyticsm a [Power BIm](./powerbi.md).

## <a name="next-steps"></a>Další kroky

* Automatizace vytváření a konfigurace pracovních prostorů pomocí [šablon Správce prostředků](../samples/resource-manager-workspace.md)

* Automatizace vytváření pracovních prostorů pomocí [PowerShellu](./powershell-workspace-configuration.md)

* Integrace s existujícími systémy pomocí [výstrah](./alerts-overview.md)

* Generování souhrnných sestav pomocí [Power BI](./powerbi.md)

* Zaveďte zákazníky do [správy delegovaných prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).