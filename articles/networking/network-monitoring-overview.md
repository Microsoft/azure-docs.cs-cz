---
title: "O monitorování sítě v Log Analytics | Microsoft Docs"
description: "Přehled monitorování řešení, včetně NPM, pro správu sítě v cloudu, místní a hybridní prostředí sítě."
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 66e6b49dd3e3c7d0f2cbd7547b68733e1d4959ce
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="network-monitoring-solutions"></a>Řešení pro monitorování sítě 

Azure nabízí hostitele řešení ke sledování vaše síťové prostředky. Azure má řešení a nástroje pro připojení k síti, stav okruhy ExpressRoute, monitorovat a analyzovat síťový provoz v cloudu.

## <a name="network-performance-monitor-npm"></a>Sledování výkonu sítě (NPM)

Monitorování výkonu v síti (NPM) je sada funkcí, z nichž každá byla zaměřena monitorování stavu sítě, síťové připojení k vaší aplikace a poskytuje přehled o výkonu vaší sítě. NPM je založená na cloudu a poskytuje síť hybridní řešení monitorování, které monitoruje připojení mezi:
 
* Nasazení a místní umístění v cloudu
* Víc datových center a firemní pobočky
* Mise kritické vícevrstvé aplikace a micro – služby
* Umístění uživatele a webové aplikace (HTTP či HTTPs) 

## <a name="performance-monitor"></a>Monitorování výkonu

Sledování výkonu je součástí NPM a je monitorování sítě pro cloud, hybridní a místními prostředími. Připojení k síti můžete sledovat v rámci firemní pobočky ve vzdálených a pole pobočky, umístění úložiště, datovými centry a cloudy. Můžete zjistit problémy se síťovým před stížnost vaši uživatelé. Jsou klíčové výhody:

* Sledovat ztrátě a latence v rámci různých podsítí a nastavit upozornění
* Monitorovat všechny cesty (včetně redundantní cesty) v síti
* Řešení problémů přechodný a v daném okamžiku sítě, které je obtížné replikovat
* Určit konkrétní segmentu v síti, která je odpovědná za snížení výkonu
* Monitorování stavu sítě, bez nutnosti SNMP

![Mapy topologie NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Další informace zobrazení v následujících článcích:

* [Konfigurace řešení monitorování výkonu sítě v analýzy protokolů](../log-analytics/log-analytics-network-performance-monitor.md) 
* [Případy použití](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Aktualizace produktu: [února 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [2017 srpen](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Monitorování ExpressRoute

NPM pro ExpressRoute nabízí rozsáhlé sledování ExpressRoute pro privátní připojení partnerského vztahu. Můžete monitorovat E2E připojení a výkon mezi pobočkami a Azure prostřednictvím ExpressRoute. Klíčové funkce jsou:

* Automatické zjišťování ER okruhů spojené s vaším předplatným
* Zjišťování topologie sítě z místního pro cloudové aplikace
* Plánování, analýza využití kapacity
* Monitorování a výstrahy na primární i sekundární cesty
* Zjištění snížení připojení k virtuálním sítím

Další informace najdete v následujících článcích:

* [Konfigurace Network Performance Monitor pro ExpressRoute](../expressroute/how-to-npm.md)
* [příspěvek blogu](https://aka.ms/NPMExRmonitorGA)

## <a name="service-endpoint-monitor"></a>Monitorování koncového bodu služby

S monitorováním na koncový bod služby, teď můžete testování dostupnosti aplikací a zjistit kritická místa výkonu napříč místními, poskytovatel sítě a cloud a privátního datových centrech.

* Sledování začátku do konce síťové připojení k aplikacím
* Korelovat dodávání aplikací s výkon sítě, detekci přesné umístění snížení v cestě mezi uživatelem a aplikace
* Test dostupnosti aplikace z více umístění uživatele po celém světě
* Určení latenci a paket ztráty sítě pro vaše řádek obchodní a aplikací SaaS.
* Určení aktivní body v síti, který může být příčinou aplikace snížený výkon
* Monitorování dostupnosti aplikací Office 365, pomocí předdefinovaných testů pro Microsoft Office 365, Dynamics 365 Skype pro firmy a další služby Microsoftu

Další informace najdete v následujících článcích:

* [Konfigurace sítě monitorování výkonu pro monitorování koncových bodů služby](https://aka.ms/applicationconnectivitymonitorguide)
* [příspěvek blogu](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Analýza provozu
Analýza provozu je cloudové řešení, která poskytuje přehled o činnosti uživatelů a aplikací na vaše cloudové sítě. Skupina NSG toku protokoly se analyzují zajistit přehledy:

* Přenosy dat mezi vaší sítí mezi Azure a Internet, oblastí veřejného cloudu, virtuálních sítí a podsítí
* Aplikace a protokoly v síti, aniž by bylo nutné pro zjištění je snazší, nebo vyhrazený tok kolekce zařízení
* Provoz TOP talkers, chatty aplikace konverzace virtuálních počítačů v cloudu, aktivní body
* Zdroje a cíle provozu mezi virtuálními sítěmi, vzájemných vztahů mezi nejdůležitější obchodní služby a aplikace
* Zabezpečení – škodlivý přenos, porty, otevřete na Internetu, aplikace nebo virtuální počítače pokusu o přístup k Internetu...

Analýza provozu poskytuje jim s řešitelné informace, které pomáhá audit aktivitu sítě vaší organizace, zabezpečené aplikacím a datům, optimalizace výkonu zatížení a zůstat kompatibilní.

![Geografické mapy znázorňující provoz v oblastech](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Související odkazy:
* [Příspěvek blogu](https://aka.ms/trafficanalytics), [dokumentace](https://aka.ms/trafficanalyticsdocs), [– nejčastější dotazy](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS Analytics
Vytvořené pro Správce DNS, toto řešení shromažďuje, analyzuje a korelaci protokolů DNS zajistit bezpečnost, operace a výkonu související s statistiky.  Některé možnosti jsou:

* Identifikace klientů, které se pokusí přeložit na škodlivý domén
* Identifikace záznamů o prostředcích
* Přehled často dotazovaných doménách a klienty DNS, talkative
* Přehled požadavek zatížení serverů DNS
* Monitorování selhání dynamické registrace DNS

![Řídicí panel Analytics DNS](./media/network-monitoring-overview/dns-analytics-overview.png) 

Související odkazy:
* [Příspěvek blogu](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [dokumentace](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="next-steps"></a>Další kroky

* [Konfigurace programu Sledování výkonu sítě](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [Konfigurace Network Performance Monitor pro ExpressRoute](../expressroute/how-to-npm.md)
