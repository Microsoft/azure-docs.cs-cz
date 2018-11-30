---
title: Informace o monitorování sítě v Log Analytics | Dokumentace Microsoftu
description: Přehled řešení, včetně NPM, ke správě sítí přes cloud, místní a hybridní prostředí pro monitorování sítě.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 4107b292a606ea629e7de0c7be66621d699c5f42
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427382"
---
# <a name="network-monitoring-solutions"></a>Řešení pro monitorování sítě 

Azure nabízí celou řadu řešení pro monitorování síťových prostředků. Azure nabízí řešení a nástroje pro připojení k síti, stavu okruhů ExpressRoute, monitorovat a analyzovat síťový provoz v cloudu.

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

Network Performance Monitor (NPM) je sada funkcí, z nichž každý je zaměřený na monitorování stavu sítě, síťové připojení k vaší aplikace a poskytuje přehledy o výkonu vaší sítě. NPM je založené na cloudu a nabízí hybridní síť řešení monitorování, které monitoruje připojení mezi:
 
* Nasazení a v místním umístění v cloudu
* Více datových center a firemní pobočky
* Středisko kritické vícevrstvé aplikace/mikroslužby
* Umístění uživatele a webové aplikace (HTTP/HTTPs) 

Sledování výkonu, monitorování ExpressRoute a připojení k monitorování služby monitorování funkcí v rámci NPM a jsou popsané níže.

## <a name="performance-monitor"></a>Monitorování výkonu

Sledování výkonu je součástí NPM a je monitorování sítě pro cloud, hybridní a v místních prostředích. Můžete monitorovat připojení k síti napříč vzdálenými pobočkami a dočasnými kancelářemi, skladovými lokalitami, datovými centry a cloudy. Předtím, než si vaši zákazníci začnou stěžovat můžete detekovat problémy se sítí. Jsou klíčové výhody:

* Monitorování ztrát a latence mezi různými podsítěmi a nastavování upozornění
* Monitorování všech cest (včetně redundantní cesty) v síti
* Řešení potíží s problémy se sítí přechodné a bodu v čase, které se těžko replikují
* Určení konkrétního segmentu sítě, který je důvodem sníženého výkonu
* Monitorování stavu sítě bez nutnosti používat protokol SNMP

![Mapu NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Další informace naleznete v následujících článcích:

* [Konfigurace Network Performance Monitor Solution v Log Analytics](../azure-monitor/insights/network-performance-monitor.md) 
* [Případy použití](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Aktualizace produktu: [. února 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [. srpna 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute Monitor

NPM pro ExpressRoute nabízí komplexní ExpressRoute monitorování Azure privátní partnerské vztahy a partnerské vztahy Microsoft. Můžete monitorovat E2E připojení a výkon mezi vašimi pobočkami a Azure přes ExpressRoute. Jsou klíčové funkce:

* Automatické zjišťování okruhů ER přidružených k vašemu předplatnému
* Zjišťování topologie sítě v místním do vašich cloudových aplikací
* Plánování kapacity, analýza využití šířky pásma
* Monitorování a upozorňování pro primární a sekundární cesty
* Monitorování připojení k Azure službami, jako je Office 365, Dynamics 365... přes ExpressRoute
* Detekovat snížení výkonu připojení k virtuálním sítím

![Zobrazení přenosů geografické mapy napříč oblastmi](./media/network-monitoring-overview/expressroute-topology-map.png) 

Další informace najdete v následujících článcích:

* [Konfigurace Network Performance Monitor pro ExpressRoute](../expressroute/how-to-npm.md)
* [Blogový příspěvek](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitorování připojení služby

Pomocí monitorování připojení služby, teď můžete testu dostupnosti aplikací a zjišťování problémových míst výkonu v místní, operátora sítích a cloudu a soukromého datových center.

* Monitorování začátku do konce síťové připojení k aplikacím
* Doručování aplikací je možné korelovat s výkon sítě, detekci přesné umístění příslušného snížení na cestě mezi uživatelem a aplikace
* Test dostupnosti aplikace z více míst uživatelů po celém světě
* Určení latence a paketů ztráty v síti pro vaši oblast podnikání a k aplikacím SaaS
* Zjistit aktivní body v síti, které můžou způsobovat nízký výkon aplikace
* Monitorování připojení k aplikacím Office 365, pomocí integrovaných testů pro Microsoft Office 365, Dynamics 365, Skype pro firmy a další služby Microsoftu

Další informace najdete v následujících článcích:

* [Konfigurace Network Performance Monitor pro monitorování koncových bodů služby](https://aka.ms/applicationconnectivitymonitorguide)
* [Blogový příspěvek](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Analýza provozu
Analýza provozu je cloudové řešení, která poskytuje přehled o aktivitě uživatelů a aplikací v cloudových sítích. Poskytnout přehled o analýze protokolů toku NSG Flow:

* Mezi vaší sítí mezi Azure a Internet, veřejných cloudových oblastech, virtuálních sítí a podsítí
* Aplikace a protokoly ve vaší síti, bez nutnosti zjištění je snazší nebo vyhrazené tok kolekcí zařízení
* Hlavní vyjádření, přetížení aplikací, konverzace virtuálního počítače v cloudu, provoz hotspotům
* Zdroje a cíle provozu mezi virtuálními sítěmi, vzájemných vztahů mezi nejdůležitější obchodní služby a aplikace
* Zabezpečení – škodlivý provoz, porty otevřete na Internetu, aplikace nebo virtuální počítače pokusu o přístup k Internetu...
* Využití kapacity – pomáhá eliminovat problémy bylo potřeba zřizovat nebo nízkého využití sledování trendů využití brány VPN Gateway a dalších služeb

Analýza provozu získáte přístup k užitečné informace, které pomáhá auditovat aktivitu sítě vaší organizace, zabezpečených aplikací a dat, optimalizovat výkon úloh a zajistěte dodržování předpisů.

![Zobrazení přenosů geografické mapy napříč oblastmi](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Související odkazy:
* [Blogový příspěvek](https://aka.ms/trafficanalytics), [dokumentaci](https://aka.ms/trafficanalyticsdocs), [– nejčastější dotazy](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS Analytics
Určená pro Správce DNS, toto řešení shromažďuje, analyzuje a koreluje protokoly DNS a poskytuje zabezpečení, operací a přehledy související s výkonem.  Některé možnosti jsou:

* Identifikace klientů, které se pokoušejí přeložit škodlivé domény
* Identifikace zastaralých záznamů
* Přehled často doménový názvy a prahová klienti DNS
* Přehled o požadavku zatížení serverů DNS
* Monitorování selhání dynamické registrace DNS

![Řídicí panel DNS Analytics](./media/network-monitoring-overview/dns-analytics-overview.png) 

Související odkazy:
* [Blogový příspěvek](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [dokumentace](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Různé

* [Nový cenový model](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
