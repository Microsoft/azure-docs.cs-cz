---
title: Ceny – nejčastější dotazy pro sledování výkonu sítě Azure | Microsoft Docs
description: Nejčastější dotazy - sledování výkonu síť Azure
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
ms.openlocfilehash: 5b2335ee2584af07ed23ce87be92a869f3a07ba1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Změny cenových pro sledování výkonu síť Azure

Jsme byla naslouchání vaše připomínky a nedávno zavedly [nové cenách prostředí](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), pro různé sledování služby v Azure.

Tento dokument zachytává cenovou změny související s Azure [sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM), v žádném snadno čitelný otázku a odpověď formátu.

Sledování výkonu sítě se skládá ze tří součástí:
* [Sledování výkonu](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitorování koncový bod služby](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor) a
* [ExpressRoute Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

Následující části vysvětluje cenové změny pro výše uvedené součásti.

## <a name="performance-monitor-pm"></a>Sledování výkonu (odp)

**Jak se využití sledování výkonu účtují v původním modelu?**

Fakturace pro NPM bylo založeno na nebo využívání ze dvou částí:
* Uzly: Všechny syntetické transakce pocházejí a ukončovat platnost u uzlů. Uzly jsou také označovány jako agentů nebo MMA (Microsoft agenti pro správu).
* Data: Výsledky různé testy sítě jsou uložené v úložišti analýzy protokolů.

V rámci staré modelu se spočítala na základě počtu uzlů a objem dat generovaný kusovníku. 

**Jak je použití nástroje Sledování výkonu, účtovat v rámci nového modelu?**

V kombinaci se teď fakturuje funkci sledování výkonu v NPM: 

* podsíť odkazy monitorovat a
* Datový svazek

**Co je odkaz podsíť?**

Sledování výkonu monitoruje připojení mezi dvěma nebo více umístění v síti.  Připojení mezi skupinou uzly nebo agentů v jedné podsíti a skupinou uzlů v jiné podsíti, se označuje jako podsíť odkaz.

**Mám dvě podsítě (podsíť A a B) a mít několik agentů v jednotlivých podsítích.  Sledování výkonu monitoruje připojení ze všech agentů v síti A k všechny agenty na podsíti B.  I odečte podle počtu připojení mezi podsíť?**

Ne. Pro účely fakturace všechna připojení z podsítě A podsíť B se seskupují do jedné podsítě odkaz a vám fakturují jednoho připojení.  Sledování výkonu bude nadále monitorovat připojení mezi různé agenty v každé podsíti.

**Jaké jsou náklady pro monitorování odkaz podsíť?**

Naleznete v části s názvem [Ping OK](https://azure.microsoft.com/pricing/details/network-watcher/) nákladů na monitorování odkaz jedné podsíti za každý měsíc.

**Jaké jsou poplatků za data generována sledování výkonu?**

Zdarma pro přijímání (nahrání dat k analýze protokolů, zpracování a indexování) je k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/) pro analýzu protokolu.  (Část: přijímání dat).

Poplatek pro uchovávání dat (to znamená, data se uchovávají v možnost zákazníka po prvním měsíci) je také k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/).  (Část: uchovávání dat).


## <a name="expressroute-monitor-erm"></a>Monitorování ExpressRoute (ERM)

**Jaké jsou poplatky za využití monitorování ExpressRoute?**

Poplatky za monitorování ExpressRoute se účtují podle objemu dat vygenerovaných během monitorování.   Odkazovat na otázku "Co jsou poplatků za data generována sledování výkonu?" Další informace.

**Používám ERM k monitorování více okruhů ExpressRoute. Se mi strhla platba závislosti na počtu monitorovaných okruhy?**

Vám není účtován na základě buď počet okruhy nebo typ partnerského vztahu (například soukromého partnerského vztahu, partnerský vztah Microsoftu).  Budou se účtovat na objemu dat, jak je popsáno výše.

**Co je objem dat generovaný, při monitorování jeden okruh?**

Objem dat generovaný za měsíc, když monitorování soukromého partnerského vztahu připojení je následující:

|Percentil      |Data za měsíc (MB)|
| :---:          |           ---:|
|50<sup>tý</sup> |            192|
|60<sup>tý</sup> |            256|
|70<sup>tý</sup> |            360|
|80<sup>tý</sup> |            498|
|90<sup>tý</sup> |            870|
|95<sup>tý</sup> |           1560|


Na výše uvedené tabulce Zákazníci na 50. percentil platit pro 192 MB dat. V $2.30 USD/GB pro první měsíc, náklady na monitorování okruh je USD 0.43 (= 192 * 2.30 / 1024) pro první měsíc.

**Jaké jsou některé důvody pro rozdíly v objemu dat?**

Objem dat generované monitorování závisí na několika faktorech, jako:
* počet agentů - přesnost izolace chyb zvyšuje se zvýšením počtu agentů
* počet směrování v síti
* Počet cest mezi zdrojovým a cílovým

Zákazníci na vyšší percentily (v předchozí tabulce), obvykle sledovat jejich okruhy z několika bodů vantage ve svojí místní síti.  Více agentů jsou také umístěny hlubší v síti, pokud chcete nastavit hraniční směrovač poskytovatele služeb. Agenti často umístěny v několika lokalitách uživatele, větve a stojany v datových centrech.

## <a name="service-endpoint-monitor-sepm"></a>Monitorování koncového bodu služby (SEPM)

**Jaké jsou poplatky za využití monitorování koncového bodu služby?**

Poplatky za využití monitorování koncového bodu služby se vypočítávají podle:
* počet připojení
* objem dat

**Co je připojení?**

Připojení je testu dostupnosti jeden koncový bod (adresa URL nebo síťové služby) z jednoho agenta pro celý měsíc. Například monitorování připojení do vyhledávače bing.com ze tří agentů se považuje za tři připojení.

**Jaké jsou náklady na monitorování koncového bodu služby?**

- Odkazovat [monitorování připojení](https://azure.microsoft.com/pricing/details/network-watcher/) části nákladů na monitorování koncový bod pro celý měsíc.
- Zdarma pro data je k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/) pro analýzu protokolu.  (Část: přijímání dat).

## <a name="references"></a>Odkazy

- [Protokolu analýzy ceny – nejčastější dotazy](https://azure.microsoft.com/pricing/details/log-analytics/) – nejčastější dotazy týkající se část obsahuje informace o úroveň free, na každém uzlu cenovou atd.

