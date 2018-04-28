---
title: Ceny – nejčastější dotazy pro sledování výkonu sítě Azure | Microsoft Docs
description: Nejčastější dotazy - sledování výkonu sítě Azure
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 1e7e43dc2e7ed386f8f77fd1ab186d2ff34af405
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Změny cenových pro sledování výkonu sítě Azure

Jsme naslouchali vaše připomínky a nedávno zavedená [nové cenách prostředí](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) pro různé sledování služby v Azure. Tento článek zachytává cenovou změny související s Azure [sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) ve formátu čtení otázku a odpověď.

Sledování výkonu sítě se skládá ze tří součástí:
* [Sledování výkonu](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitorování koncového bodu služby](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor)
* [Monitorování ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

Následující části popisují cenovou změny pro součásti NPM.

## <a name="performance-monitor"></a>Monitorování výkonu

**Jak se využití sledování výkonu účtují v původním modelu?**

Fakturace pro NPM byl na základě využití a spotřeba ze dvou částí:
* **Uzly**: všechny syntetické transakce pocházejí a ukončovat platnost u uzlů. Uzly jsou také označovány jako agentů nebo agenti pro správu Microsoft.
* **Data**: výsledky různé testy sítě jsou uloženy v úložišti Azure Log Analytics.

V rámci staré modelu se spočítala na základě počtu uzlů a objem dat generovaný kusovníku. 

**Jak je účtován využití sledování výkonu v rámci nového modelu?**

Funkce sledování výkonu v NPM se teď fakturuje založené na kombinaci: 

* Sledovat odkazy podsítě
* Objem dat

**Co je odkaz podsíť?**

Sledování výkonu monitoruje připojení mezi dvěma nebo více umístění v síti. Připojení mezi skupina uzlů nebo agenti v jedné podsíti a skupinu uzlů v jiné podsíti, se nazývá odkaz podsítě.

**Je nutné dvě podsítě (A a B) a několik agentů je nutné v jednotlivých podsítích. Sledování výkonu monitoruje připojení ze všech agentů v síti A k všechny agenty na podsíti B. I odečte podle počtu připojení mezi podsíť?**

Ne. Pro účely fakturace jsou všechna připojení z podsítě A podsíť B seskupeny dohromady na jednu podsíť odkaz. Fakturuje se jednoho připojení. Pokračuje v monitorování výkonu monitorování připojení mezi různé agenty v každé podsíti.

**Jaké jsou náklady pro monitorování odkaz podsíť?**

Náklady na monitorování odkaz jedné podsíti za každý měsíc, najdete v článku [Ping OK](https://azure.microsoft.com/pricing/details/network-watcher/) části.

**Jaké jsou poplatků za data, která generuje sledování výkonu?**

Zdarma pro přijímání (nahrání dat k analýze protokolů, zpracování a indexování) je k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/) pro analýzy protokolů, v části přijímat Data. Poplatek pro uchovávání dat (to znamená, data se uchovávají v možnost zákazníka po prvním měsíci) je také k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/), v části uchovávání dat.


## <a name="expressroute-monitor"></a>Monitorování ExpressRoute

**Jaké jsou poplatky za využití monitorování ExpressRoute?**

Poplatky za monitorování ExpressRoute se účtují podle objemu dat vygenerovaných během monitorování. Další informace najdete v části "Jaké jsou poplatků za data, která generuje sledování výkonu?"

**Používám ExpressRoute monitorování pro monitorování více okruhů ExpressRoute. Se mi strhla platba závislosti na počtu monitorovaných okruhy?**

Vám není účtován na základě buď počet okruhy nebo typ partnerského vztahu (například soukromého partnerského vztahu, partnerský vztah Microsoftu). Budou se vám účtovat podle objemu dat, jak je popsáno dříve.

**Co je objem dat, které jsou generovány, pokud monitoruje jeden okruh ExpressRoute?**

Objem dat generovaný za měsíc, kdy ExpressRoute monitoruje připojení soukromého partnerského vztahu, vypadá takto:

|Percentil      |Data za měsíc (MB)|
| :---:          |           ---:|
|50<sup>tý</sup> |            192|
|60<sup>tý</sup> |            256|
|70<sup>tý</sup> |            360|
|80<sup>tý</sup> |            498|
|90<sup>tý</sup> |            870|
|95<sup>tý</sup> |           1560|


Podle této tabulky Zákazníci na 50. percentil platit pro 192 MB dat. V $2.30 USD/GB pro první měsíc, náklady na monitorování okruh je USD $0.43 (= 192 * 2.30 / 1024).

**Jaké jsou některé důvody rozdíly v objemu dat?**

Objem dat generované monitorování závisí na několika faktorech, jako například:
* Počet agentů. Přesnost izolace chyb zvyšuje se zvýšením počtu nasazovaných agentů.
* Počet směrování v síti.
* Počet cest mezi zdrojovým a cílovým.

Zákazníci na vyšší percentily (v předchozí tabulce) obvykle monitorovat jejich okruhy z několika bodů vantage ve svojí místní síti. Více agentů jsou také umístěny hlubší v síti, pokud chcete nastavit hraniční směrovač poskytovatele služeb. Agenti jsou často umístěny v několika lokalitách uživatele, větve a stojany v datových centrech.

## <a name="service-endpoint-monitor"></a>Monitorování koncového bodu služby

**Jaké jsou poplatky za využití monitorování koncového bodu služby?**

Poplatky za využití monitorování koncového bodu služby se vypočítávají podle:
* počet připojení
* objem dat

**Co je připojení?**

Připojení je testu dostupnosti jeden koncový bod (adresa URL nebo síťové služby) z jednoho agenta pro celý měsíc. Například monitorování připojení do vyhledávače bing.com ze tří agentů se považuje za tři připojení.

**Jaké jsou náklady na monitorování koncového bodu služby?**

Odkazovat [monitorování připojení](https://azure.microsoft.com/pricing/details/network-watcher/) části nákladů na monitorování koncový bod pro celý měsíc. Zdarma pro data je k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/) pro analýzy protokolů, v části přijímat Data.

## <a name="references"></a>Odkazy

[Protokolu analýzy ceny – nejčastější dotazy](https://azure.microsoft.com/pricing/details/log-analytics/): Nejčastější dotazy týkající se část obsahuje informace o úroveň free na uzlu ceny a další podrobnosti o cenách.

