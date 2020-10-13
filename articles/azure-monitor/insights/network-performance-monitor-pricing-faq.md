---
title: Nejčastější dotazy týkající se cenové služby Azure Network Performance Monitor | Microsoft Docs
description: Nejčastější dotazy – Azure Network Performance Monitor
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: f44afd84c58c94c6a8d3e6145e8a4f66e0e2e782
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539648"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Cenové změny pro Azure Network Performance Monitor

Nastavili jsme vaše názory a nedávno jsme zavedli [nové cenové prostředí](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) pro různé služby monitorování v rámci Azure. Tento článek zachycuje cenové změny týkající se Azure [Network Performance Monitor](../../networking/network-monitoring-overview.md) (npm) ve snadno čitelném formátu otázek a odpovědí.

Network Performance Monitor se skládá ze tří součástí:
* [Monitorování výkonu](../../networking/network-monitoring-overview.md#performance-monitor)
* [Monitorování koncového bodu služby](../../networking/network-monitoring-overview.md)
* [Monitorování ExpressRoute](../../networking/network-monitoring-overview.md#expressroute-monitor)

Následující části vysvětlují cenové změny součástí NPM.

## <a name="performance-monitor"></a>Monitorování výkonu

**Jak bylo využití monitorování výkonu účtované ve starém modelu?**

Fakturace pro NPM vychází z využití a spotřeby dvou součástí:
* **Uzly**: všechny syntetické transakce pocházejí a končí v uzlech. Uzly se také označují jako agenti nebo agenti pro správu Microsoft.
* **Data**: výsledky různých testů sítě jsou uloženy v pracovním prostoru Log Analytics.

Ve starém modelu se vypočítala faktura na základě počtu uzlů a objemu vygenerovaných dat. 

**Jak se v rámci nového modelu účtují využití monitorování výkonu?**

Funkce sledování výkonu v NPM se teď účtuje na základě kombinace: 

* Monitorovaná propojení podsítí
* Objem dat

**Co je propojení podsítí?**

Sledování výkonu monitoruje připojení mezi dvěma nebo více lokalitami v síti. Připojení mezi skupinou uzlů nebo agenty v jedné podsíti a skupinou uzlů v jiné podsíti se nazývá propojení podsítě.

**Mám dvě podsítě (a a B) a mám několik agentů v každé podsíti. Sledování výkonu monitoruje připojení ze všech agentů v podsíti A na všechny agenty v podsíti B. Bude se mi účtovat na základě počtu připojení mezi podsítěmi?**

Ne. Pro účely fakturace se všechna připojení z podsítě A do podsítě B seskupují do jednoho propojení podsítí. Účtuje se vám jedno připojení. Sledování výkonu nadále monitoruje připojení mezi různými agenty v každé podsíti.

**Jaké jsou náklady na monitorování propojení podsítí?**

Informace o cenách monitorování jedné podsítě po celý měsíc najdete v části připojení k nástroji [příkazového](https://azure.microsoft.com/pricing/details/network-watcher/) pole.

**Jaké jsou poplatky za data, která monitor výkonu generuje?**

Poplatek za ingestování (nahrávání dat do Log Analytics pracovního prostoru v Azure Monitor, zpracování a indexování) je k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/) pro Log Analytics v části pro příjem dat. Poplatky za uchování dat (tj. data uchovávaná na možnosti zákazníka, i mimo první měsíc) jsou také k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/)v části uchovávání dat.


## <a name="expressroute-monitor"></a>Monitorování ExpressRoute

**Jaké jsou poplatky za využití monitorování ExpressRoute?**

Poplatky za monitorování ExpressRoute se účtují na základě objemu dat vygenerovaných během monitorování. Další informace najdete v části Co jsou poplatky za data, která monitor výkonu generuje.

**Používám monitorování ExpressRoute k monitorování více okruhů ExpressRoute. Účtují se mi poplatky na základě počtu monitorovaných okruhů?**

Neúčtují se vám žádné okruhy ani typ partnerského vztahu (například soukromý partnerský vztah, partnerský vztah Microsoftu). Účtují se vám poplatky podle objemu dat, jak je vysvětleno výše.

**Jaký je objem dat generovaných při ExpressRoute monitorování jednoho okruhu?**

Objem dat vygenerovaných měsíčně, pokud ExpressRoute sleduje připojení typu privátní partnerské vztahy, je následující:

|Percentil      |Data za měsíc (MB)|
| :---:          |           ---:|
|50 –<sup>tou</sup> |            192|
|60 –<sup>tou</sup> |            256|
|70 –<sup>tou</sup> |            360|
|80 –<sup>tou</sup> |            498|
|90 –<sup>tou</sup> |            870|
|95 –<sup>tou</sup> |           1560|


Podle této tabulky Zákazníci na 50. percentil platíte za 192 MB dat. V USD $2.30/GB po dobu prvního měsíce budou náklady za monitorování okruhu $0,43 USD (= 192 * 2,30/1024).

**Jaké jsou některé důvody pro variace objemu dat?**

Objem vygenerovaných dat monitorování závisí na několika faktorech, například:
* Počet agentů. Přesnost izolace chyb se zvyšuje o zvýšení počtu agentů.
* Počet směrování v síti.
* Počet cest mezi zdrojovým a cílovým umístěním.

Zákazníci s vyšším percentilem (v předchozí tabulce) obvykle monitorují své okruhy z několika Vantage bodů v místní síti. Více agentů je umístěno také hlouběji v síti a dále od hraničního směrovače poskytovatele služby. Agenti se často nacházejí na několika uživatelských webech, větvích a skříních v datových centrech.

## <a name="service-endpoint-monitor"></a>Monitorování koncového bodu služby

**Jaké jsou poplatky za využití monitorování koncového bodu služby?**

Poplatky za využití monitorování koncového bodu služby se vypočítávají na základě těchto údajů:
* Počet připojení
* Objem dat

**Co je připojení?**

Připojení je test dosažitelnosti jednoho koncového bodu (adresa URL nebo síťová služba) od jednoho agenta po celý měsíc. Například monitorování připojení k bing.com ze tří agentů představuje tři připojení.

**Jaké jsou náklady na monitorování koncového bodu služby?**

Náklady na monitorování koncového bodu v celém měsíci najdete v části [monitorování připojení](https://azure.microsoft.com/pricing/details/network-watcher/) . Poplatky za data jsou k dispozici na [stránce s cenami](https://azure.microsoft.com/pricing/details/log-analytics/) pro Log Analytics v části pro příjem dat.

## <a name="references"></a>Reference

[Nejčastější dotazy k log Analyticsm](https://azure.microsoft.com/pricing/details/log-analytics/)cenám: část s nejčastějšími dotazy obsahuje informace o cenách úrovně Free, ceny za uzel a další informace o cenách.
