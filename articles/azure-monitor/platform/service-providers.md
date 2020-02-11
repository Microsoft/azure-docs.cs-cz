---
title: Protokoly Azure Monitor pro poskytovatele služeb | Microsoft Docs
description: Protokoly Azure Monitor můžou pomáhat poskytovatelům spravovaných služeb (účastníci programu MSP), velkým podnikům, nezávislým dodavatelům softwaru a poskytovatelům hostitelských služeb spravovat a monitorovat servery v místní nebo cloudové infrastruktuře zákazníka.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: 1b5c181569ea569ecc4808284683501cb20f7bf5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117057"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Protokoly Azure Monitor pro poskytovatele služeb

Log Analytics pracovní prostory v Azure Monitor mohou pomáhat poskytovatelům spravovaných služeb (účastníci programu MSP), velkým společnostem, nezávislým výrobcům softwaru a poskytovatelům hostitelských služeb spravovat a monitorovat servery v místní nebo cloudové infrastruktuře zákazníka.

Velké podniky řada podobností s poskytovateli služeb, zejména v případě, že na centralizovaný tým IT, který zodpovídá za správu IT pro mnoho různých organizačních jednotek. Pro zjednodušení tento dokument používá *poskytovatele služeb* , ale stejné funkce jsou dostupné i pro podniky a další zákazníky.

Pro partnery a poskytovatele služeb, kteří jsou součástí programu [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , je Log Analytics v Azure monitor jedna ze služeb Azure dostupných v předplatných Azure CSP.

Log Analytics v Azure Monitor může také používat poskytovatel služeb, který spravuje prostředky zákazníka prostřednictvím funkce správy delegovaných prostředků Azure v [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="architectures-for-service-providers"></a>Architektury pro poskytovatele služeb

Log Analytics pracovní prostory poskytují metodu pro správce k řízení toku a izolaci dat [protokolu](data-platform-logs.md) a k vytvoření architektury, která řeší konkrétní obchodní potřeby. [Tento článek](design-logs-deployment.md) popisuje předpoklady pro návrh, nasazení a migraci pro pracovní prostor a článek [Správa přístupu](manage-access.md) popisuje, jak používat a spravovat oprávnění pro data protokolu. Poskytovatelé služeb mají další aspekty.

Existují tři možné architektury pro poskytovatele služeb týkající se pracovních prostorů Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. distribuované protokoly se ukládají v pracovních prostorech nacházejících se v tenantovi zákazníka.

V této architektuře pracovního prostoru se nasadí do tenanta zákazníka, který se používá pro všechny protokoly daného zákazníka.

Existují dva způsoby, jak můžou správci poskytovatele služeb získat přístup k pracovnímu prostoru Log Analytics v tenantovi zákazníka:

- Zákazník může přidat jednotlivé uživatele od poskytovatele služeb jako [Azure Active Directory uživatelů typu Host (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Správci poskytovatele služeb se budou muset přihlásit ke všem adresářům zákazníka v Azure Portal, aby měli přístup k těmto pracovním prostorům. To také vyžaduje, aby zákazníci mohli spravovat individuální přístup pro každého správce služby Service Provider.
- Pro zajištění větší škálovatelnosti a flexibility můžou poskytovatelé služeb využít možnost [správy delegovaných prostředků Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) ve [službě Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) pro přístup k tenantovi zákazníka. Pomocí této metody jsou správci poskytovatele služeb zahrnuti do skupiny uživatelů Azure AD v tenantovi poskytovatele služeb a tato skupina má přístup k této skupině během procesu připojování pro každého zákazníka. Tito správci pak můžou přístup k pracovním prostorům každého zákazníka z vlastního tenanta poskytovatele služeb, ale nemusí se přihlašovat jednotlivě k tenantovi jednotlivých zákazníků. Přístup k prostředkům pracovních prostorů Log Analytics vašich zákazníků tímto způsobem omezuje práci na straně zákazníka a usnadňuje shromažďování a analýzu dat napříč několika zákazníky spravovanými stejným poskytovatelem služeb prostřednictvím nástrojů, jako jsou například [sešity Azure monitor](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview). Další informace najdete v tématu [monitorování zákaznických prostředků ve velkém měřítku](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

Mezi výhody distribuované architektury patří:

* Zákazník může ověřit konkrétní úrovně oprávnění prostřednictvím [delegované správy prostředků Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)nebo může spravovat přístup k protokolům pomocí vlastního [přístupu založeného na rolích](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Protokoly je možné shromažďovat ze všech typů prostředků, nikoli jenom z dat virtuálních počítačů založených na agentech. Například protokolů auditu Azure.
* Každý zákazník může mít různá nastavení pro jejich pracovní prostor, jako jsou uchovávání a malá a velká data.
* Izolace mezi zákazníky pro regulační a dodržování předpisů.
* Poplatek za každý pracovní prostor se začne do předplatného zákazníka.

Mezi nevýhody distribuované architektury patří:

* Centralizované vizualizace a analýza dat napříč klienty zákazníka pomocí nástrojů, jako jsou Azure Monitor sešity, můžou mít za následek pomalejší prostředí, zejména při analýze dat napříč více než 50 + pracovními prostory.
* Pokud se zákazníkům pro správu delegovaných prostředků Azure nepřipojíte, musí se správci poskytovatele služeb zřídit v adresáři zákazníka a pro poskytovatele služeb je těžší spravovat velký počet zákaznických klientů najednou.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. centrální protokoly se ukládají v pracovním prostoru umístěném v tenantovi poskytovatele služeb.

V této architektuře protokoly nejsou uložené v tenantech zákazníka, ale pouze v centrálním umístění v rámci jednoho předplatného poskytovatele služeb. Agenty, které jsou nainstalovány na virtuálních počítačích zákazníků jsou nakonfigurovány k odeslání protokolů s jejich tento pracovní prostor pomocí ID a tajný klíč.

Výhody centralizované architektury jsou:

* Je snadné ke správě velkého počtu zákazníků a integrovat je do různých systémů back-endu.
* Poskytovatel služeb má úplné vlastnictví přes protokoly a různých artefaktů, jako je například funkce a uložené dotazy.
* Poskytovatel služeb můžete provádět analýzy na všech svých zákazníků.

Nevýhody centralizované architektury jsou:

* Tuto architekturu je možné použít pouze data virtuálního počítače založené na agentovi, nebudeme se zabývat PaaS, SaaS a Azure zdroje dat prostředků infrastruktury.
* Může být obtížné oddělit data mezi zákazníky, když jsou sloučeny do jednoho pracovního prostoru. Je jediným dobrým metodu počítače plně kvalifikovaný název domény (FQDN) nebo prostřednictvím předplatného Azure ID. 
* Všechna data ze všech zákazníků budou uloženy ve stejné oblasti pomocí faktury a stejné nastavení uchování a konfigurace.
* Prostředky infrastruktury Azure a PaaS služby, například Azure Diagnostics a protokoly auditování Azure vyžaduje pracovní prostor a být ve stejném tenantovi jako prostředek, proto, že aby nemohli odesílat protokoly do centrální pracovního prostoru.
* Všichni agenti virtuálních počítačů ze všech zákazníků budou ověřeni do centrálního pracovního prostoru pomocí stejného ID a klíče pracovního prostoru. Neexistuje žádná metoda blokování protokoly z konkrétního zákazníka, aniž by to ovlivnilo ostatní zákazníci.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. hybridní protokoly jsou uložené v pracovním prostoru umístěném v tenantovi zákazníka a některé z nich se najdou do centrálního umístění.

Třetí architektura poměr mezi těmito dvěma možnostmi. Je založen na první distribuovaná architektura, ve kterém jsou místní pro každého zákazníka protokoly, ale pomocí některé mechanismus, chcete-li vytvořit centrální úložiště protokolů. Část protokoly načítána do centrálního umístění pro generování sestav a analýzy. Tuto část může být malý počet datových typů nebo Souhrn aktivity, jako je například denní statistiky.

Existují dvě možnosti implementace protokolů v centrálním umístění:

1. Centrální pracovní prostor: poskytovatel služeb může ve svém tenantovi vytvořit pracovní prostor a použít skript, který využívá [rozhraní API pro dotazování](https://dev.loganalytics.io/) s [rozhraním API pro shromažďování dat](../../azure-monitor/platform/data-collector-api.md) k přenesení dat z různých pracovních prostorů do tohoto centrálního umístění. Další možností je jiný než skript, je použít [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI jako centrální umístění: Power BI se může chovat jako centrální umístění, když různé pracovní prostory exportují data do tohoto prostředí pomocí integrace mezi Log Analyticsm a [Power BIm](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Další kroky

* Automatizace vytváření a konfigurace pracovních prostorů pomocí [šablon Správce prostředků](template-workspace-configuration.md)

* Automatizace vytváření pracovních prostorů pomocí [PowerShellu](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Integrace s existujícími systémy pomocí [výstrah](../../azure-monitor/platform/alerts-overview.md)

* Generování souhrnných sestav pomocí [Power BI](../../azure-monitor/platform/powerbi.md)

* Zaveďte zákazníky do [správy delegovaných prostředků Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
