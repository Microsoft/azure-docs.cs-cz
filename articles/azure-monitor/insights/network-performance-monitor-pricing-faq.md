---
title: Ceny za Azure Network Performance Monitor – nejčastější dotazy | Dokumentace Microsoftu
description: Časté otázky – Azure Network Performance monitoru
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: d2d1ca3e41de36d423de24fdbade8c17507642b5
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734296"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>O změně v cenách pro Azure Network Performance monitoru

Jsme naslouchali na vaši zpětnou vazbu a nedávno zavedli [nový cenový model prostředí](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) pro různé monitorování služby v Azure. Tento článek zachytává cenovou změny související s Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) ve formátu otázek a odpovědí – čtení.

Sledování výkonu sítě se skládá ze tří součástí:
* [Sledování výkonu](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitorování koncového bodu služby](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

Následující části popisují cenovou změny pro součásti NPM.

## <a name="performance-monitor"></a>Monitorování výkonu

**Jak se využití sledování výkonu ve starém modelu účtuje?**

Fakturace za NPM byl založen na využití a využití ze dvou částí:
* **Uzly**: Všechny syntetické transakce pocházejí a ukončovat platnost u uzlů. Uzly jsou také označovány jako agenty nebo agenty pro správu Microsoft.
* **Data**: Výsledky testů různé sítě jsou uloženy v úložišti Azure Log Analytics.

V části starý model se spočítala na základě počtu uzlů a objemu vygenerovaných dat faktury. 

**Jak se účtuje využití sledování výkonu v rámci nového modelu?**

Funkce monitorování výkonu v NPM teď fakturace je založená na kombinaci: 

* Monitorované odkazy na podsítě
* Objem dat

**Co je podsíť odkaz?**

Performance Monitor monitoruje připojení mezi dvěma nebo více umístění v síti. Připojení mezi skupinu uzlů nebo agenty na jednu podsíť a skupinu uzlů v jiné podsíti, se nazývá propojení podsítě.

**Můžu mít dvě podsítě: (A a B) a mám několik agentů v každé podsíti. Performance Monitor sleduje všechny agenty na podsíti A připojení ke všichni agenti v podsíti služby serveru B. Se mi účtovat na základě počtu připojení mezi podsíti?**

Ne. Pro účely fakturace všechna připojení z podsítě A podsíť B jsou seskupeny do jedno propojení podsítě. Bude se vám účtovat pro jedno připojení. Pokračuje v monitorování výkonu monitorování připojení mezi různé agenty v každé podsíti.

**Jaké jsou ceny za monitorování podsítě odkaz?**

Náklady na monitorování odkazem na jednu podsíť pro celý měsíc, najdete v článku [síť příkazů Ping](https://azure.microsoft.com/pricing/details/network-watcher/) oddílu.

**Co jsou poplatky za data, která generuje sledování výkonu?**

Poplatek za účelem ingestování datových (odesílání dat do služby Log Analytics, zpracování a indexování) je k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/) ke službě Log Analytics v části Ingestování. Poplatek za uchování dat (to znamená, data se uchovávají v možnost zákazníka, po prvním měsíci) je také k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/), v části uchování dat.


## <a name="expressroute-monitor"></a>ExpressRoute Monitor

**Co jsou poplatky za využití monitorování ExpressRoute?**

Poplatky za monitorování ExpressRoute se účtují na základě objemu dat vygenerovaných během monitorování. Další informace najdete v části "Jaké jsou poplatky za data, která generuje sledování výkonu?"

**Můžu pomocí ExpressRoute monitorování ke sledování víc okruhů ExpressRoute. Účtují se mi poplatky na základě počtu monitorované okruhy?**

Se vám neúčtují poplatky na základě počet okruhů nebo typ partnerského vztahu (například soukromý partnerský vztah, partnerský vztah Microsoftu). Můžete se účtuje podle objemu dat, jak bylo popsáno dříve.

**Co je objem dat, které jsou generovány, pokud monitoruje jeden okruh ExpressRoute?**

Objem dat vygenerovaných za měsíc, kdy ExpressRoute monitoruje privátní připojení s partnerským vztahem, vypadá takto:

|Percentil      |Dat za měsíc (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Podle této tabulky Zákazníci na 50. percentil platí za 192 MB na data. Ve 2,30 USD USD/GB na první měsíc, jsou náklady na monitorování okruhu USD $0.43 (= 192 * 2,30 / 1024).

**Jaké jsou některé důvody pro různé variace objem dat?**

Objem dat generované monitorování závisí na několika faktorech, jako například:
* Počet agentů. Přesnost izolaci chyb zvyšuje větší počet agentů.
* Počet segmentů směrování v síti.
* Počet cest mezi zdrojem a cílem.

Zákazníci na vyšší percentil (v předchozí tabulce) obvykle monitorovat své okruhy připojil z několika bodů vantage ve svojí místní síti. Více agentů jsou také umístěny hlouběji v síti, pokud chcete nastavit hraniční směrovač poskytovatele služeb. Agenti jsou často umístěné v několika lokalitách uživatele, větve a stojanů v datových centrech.

## <a name="service-endpoint-monitor"></a>Monitorování koncového bodu služby

**Co jsou poplatky za využití monitorování koncového bodu služby?**

Poplatky za využití monitorování koncového bodu služby se vypočítávají na základě:
* Počet připojení
* Objem dat

**Co je připojení?**

Připojení je test dostupnosti jeden koncový bod (adresa URL nebo síťové služby) z jednoho agenta pro celý měsíc. Například monitorování připojení k bing.com z tři agenti se považuje za tři připojení.

**Jaké jsou ceny za monitorování koncového bodu služby?**

Odkazovat [sledování připojení](https://azure.microsoft.com/pricing/details/network-watcher/) části nákladů na monitorování koncového bodu po celý měsíc. Cena za dat je k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/) ke službě Log Analytics v části Ingestování.

## <a name="references"></a>Odkazy

[Log Analytics, nejčastější dotazy týkající se cen](https://azure.microsoft.com/pricing/details/log-analytics/): V části Nejčastější dotazy se informace na úrovni free, jeden uzel ceny a další podrobnosti o cenách.

