---
title: Nejčastější dotazy k ocenění pro sledování výkonu sítě Azure | Dokumenty společnosti Microsoft
description: Nejčastější dotazy – Azure Network Performance Monitor
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: c5a80c7ff6d08ce601a6c2bbb0db0ef866d3b425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654388"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Změny cen pro sledování výkonu sítě Azure

Vyslechli jsme vaši zpětnou vazbu a nedávno jsme představili [nové cenové prostředí](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) pro různé monitorovací služby v azure. Tento článek zachycuje změny cen související s [Azure Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) ve snadno čitelném formátu otázek a odpovědí.

Sledování výkonu sítě se skládá ze tří součástí:
* [Monitorování výkonu](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor koncového bodu služby](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

V následujících částech jsou vysvětleny změny cen pro součásti NPM.

## <a name="performance-monitor"></a>Monitorování výkonu

**Jak se ve starém modelu fakturovalo využití sledování výkonu?**

Fakturace pro NPM byla založena na využití a spotřebě dvou složek:
* **Uzly**: Všechny syntetické transakce pocházejí a končí v uzlech. Uzly jsou také označovány jako agenti nebo agenti správy společnosti Microsoft.
* **Data**: Výsledky různých síťových testů jsou uloženy v pracovním prostoru Log Analytics.

Podle starého modelu byla faktura vypočítána na základě počtu uzlů a objemu generovaných dat. 

**Jak se v rámci nového modelu účtuje využití monitoru výkonu?**

Funkce Sledování výkonu v npm se nyní účtuje na základě kombinace: 

* Sledované odkazy podsítí
* Objem dat

**Co je propojení podsítě?**

Sledování výkonu monitoruje připojení mezi dvěma nebo více umístěními v síti. Připojení mezi skupinou uzlů nebo agentů v jedné podsíti a skupinou uzlů v jiné podsíti se nazývá propojení podsítě.

**Mám dvě podsítě (A a B) a v každé podsíti mám několik agentů. Sledování výkonu monitoruje připojení od všech agentů v podsíti A ke všem agentům v podsíti B. Bude mi účtován poplatek na základě počtu připojení mezi podsítěmi?**

Ne. Pro účely fakturace jsou všechna připojení od podsítě A k podsíti B seskupena do jednoho propojení podsítě. Účtují vám jedno připojení. Sledování výkonu nadále monitoruje připojení mezi různými agenty v každé podsíti.

**Jaké jsou náklady na sledování propojení podsítě?**

Náklady na sledování jednoho odkazu podsítě za celý měsíc najdete v části [Ping Mesh.](https://azure.microsoft.com/pricing/details/network-watcher/)

**Jaké jsou poplatky za data, která generuje sledování výkonu?**

Poplatek za ingestování (nahrávání dat do pracovního prostoru Log Analytics v Azure Monitoru, zpracování a indexování) je k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/) pro Log Analytics v části Ingestion data. Poplatek za uchovávání dat (tj. údaje uchovávané podle uvážení zákazníka po prvním měsíci) je také k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/)v části Uchovávání údajů.


## <a name="expressroute-monitor"></a>ExpressRoute Monitor

**Jaké jsou poplatky za používání expressroute monitoru?**

Poplatky za ExpressRoute Monitor se účtují na základě objemu dat generovaných během monitorování. Další informace naleznete v tématu "Jaké jsou poplatky za data, která generuje sledování výkonu?"

**Používám ExpressRoute Monitor pro sledování více expressroute obvodů. Účtují se mi poplatky na základě počtu sledovaných obvodů?**

Neúčtují se vám poplatky na základě počtu okruhů ani typu partnerského vztahu (například privátní partnerský vztah, partnerský vztah Microsoftu). Poplatky vám budou účtovány na základě objemu dat, jak bylo vysvětleno výše.

**Jaký je objem dat generovaných, když ExpressRoute monitoruje jeden okruh?**

Objem dat generovaných za měsíc, když ExpressRoute monitoruje připojení soukromého partnerského vztahu, je následující:

|Percentil      |Data/měsíc (MB)|
| :---:          |           ---:|
|<sup>50.</sup> |            192|
|<sup>60.</sup> |            256|
|<sup>70.</sup> |            360|
|<sup>80.</sup> |            498|
|<sup>90.</sup> |            870|
|<sup>95.</sup> |           1560|


Podle této tabulky platí zákazníci na 50. Na USD $ 2.30 / GB za první měsíc, náklady na monitorování okruhu je USD $ 0.43 (= 192 * 2.30 / 1024).

**Jaké jsou některé důvody pro změny objemu údajů?**

Objem získaných dat monitorování závisí na několika faktorech, jako jsou:
* Počet agentů. Přesnost izolace poruch se zvyšuje se zvýšením počtu agentů.
* Počet směrování v síti.
* Počet cest mezi zdrojem a cílem.

Zákazníci na vyšších percentilech (v předchozí tabulce) obvykle sledují své obvody z několika výhodných míst v místní síti. Více agentů je také umístěno hlouběji v síti, dále od hraničního směrovače poskytovatele služeb. Agenti jsou často umístěny na několika uživatelských serverech, pobočkách a rackech v datových centrech.

## <a name="service-endpoint-monitor"></a>Monitor koncového bodu služby

**Jaké jsou poplatky za používání sledování koncového bodu služby?**

Poplatky za využití sledování koncového bodu služby se počítají na základě:
* Počet připojení
* Objem dat

**Co je to spojení?**

Připojení je test dosažitelnosti k jednomu koncovému bodu (URL nebo síťové službě) od jednoho agenta za celý měsíc. Například sledování připojení k bing.com ze tří agentů představuje tři připojení.

**Jaké jsou náklady na sledování koncového bodu služby?**

Náklady na monitorování koncového bodu za celý měsíc naleznete v části [Monitorování připojení.](https://azure.microsoft.com/pricing/details/network-watcher/) Poplatek za data je k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/) pro Log Analytics v části Ingestion dat.

## <a name="references"></a>Odkazy

[Nejčastější dotazy k cenám log Analytics:](https://azure.microsoft.com/pricing/details/log-analytics/)V části Nejčastější dotazy jsou uvedeny informace o bezplatné úrovni, cenách podle uzlů a dalších podrobnostech o cenách.

