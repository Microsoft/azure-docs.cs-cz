---
title: Informace o monitorování sítě v protokolech Azure Monitor | Microsoft Docs
description: Přehled řešení monitorování sítě, včetně NPM, pro správu sítí napříč cloudem, místními i hybridními prostředími.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: a92c6789723dc42ac8f3a9c471e494079dc19328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98233436"
---
# <a name="network-monitoring-solutions"></a>Řešení monitorování sítě 

Azure nabízí hostitele řešení pro monitorování síťových prostředků. Azure nabízí řešení a pomůcky pro monitorování připojení k síti, stav okruhu ExpressRoute a analýzu síťového provozu v cloudu.

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

Network Performance Monitor (NPM) je sada funkcí, z nichž každá je zaměřená na monitorování stavu vaší sítě, síťové připojení k vašim aplikacím a poskytuje přehled o výkonu vaší sítě. NPM je cloudová a poskytuje řešení pro monitorování hybridní sítě, které monitoruje konektivitu mezi:
 
* Cloudová nasazení a místní umístění
* Několik datových center a poboček
* Klíčové vysoce vícevrstvé aplikace/mikroslužby
* Uživatelská umístění a webové aplikace (HTTP/HTTPs) 

Sledování výkonu, monitorování ExpressRoute a monitorování připojení služby jsou možnosti monitorování v rámci NPM a jsou popsány níže.

## <a name="performance-monitor"></a>Monitorování výkonu

Sledování výkonu je součástí NPM a je monitorování sítě pro cloudová, hybridní a místní prostředí. Můžete monitorovat síťové připojení mezi vzdálenými pobočkami a pobočkami, umístěními úložišť, datovými centry a cloudy. Můžete zjistit problémy se sítí, než si uživatelé vystěžují stížnost. Mezi klíčové výhody patří:

* Monitorování ztrát a latence v různých podsítích a nastavení výstrah
* Monitorovat všechny cesty (včetně redundantních cest) v síti
* Řešení přechodných a nevyřešených problémů se sítí, které se obtížně replikují
* Určení konkrétního segmentu sítě, který je zodpovědný za snížený výkon
* Monitoruje stav sítě bez nutnosti SNMP.

![Mapa topologie NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Další informace najdete v následujících článcích:

* [Konfigurace řešení Network Performance Monitor v protokolech Azure Monitor](../azure-monitor/insights/network-performance-monitor.md) 
* [Případy použití](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor)
* Aktualizace produktu:
  * [Únor 2017](/archive/blogs/msoms/oms-network-performance-monitor-is-now-generally-available)
  * [Srpen 2017](/archive/blogs/msoms/improvements-to-oms-network-performance-monitor)

## <a name="expressroute-monitor"></a>Monitorování ExpressRoute

NPM for ExpressRoute nabízí komplexní monitorování ExpressRoute pro privátní partnerské vztahy Azure a připojení partnerského vztahu Microsoftu. Můžete monitorovat připojení a výkon E2E mezi firemními pobočkami a Azure prostřednictvím ExpressRoute. Klíčové možnosti:

* Automatické rozpoznávání okruhů ER přidružených k vašemu předplatnému
* Detekce síťové topologie z místního prostředí do cloudových aplikací
* Plánování kapacity, analýza využití šířky pásma
* Monitorování a upozorňování na primární i sekundární cestě
* Monitorování připojení ke službám Azure, jako je Microsoft 365, Dynamics 365,... přes ExpressRoute
* Zjištění degradace připojení k virtuální sítě

![Geografické mapování znázorňující přenos napříč oblastmi](./media/network-monitoring-overview/expressroute-topology-map.png) 

Další informace najdete v následujících článcích:

* [Konfigurace Network Performance Monitoru pro ExpressRoute](../expressroute/how-to-npm.md)
* [Příspěvek na blogu](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitorování připojení služby

Díky monitorování připojení služby teď můžete testovat dostupnost aplikací a detekovat kritická místa výkonu v místních sítích, sítích dopravců a v cloudových nebo privátních datových centrech.

* Monitorování kompletního síťového připojení k aplikacím
* Korelace doručování aplikací s výkonem sítě, detekce přesného umístění rozkladu podél cesty mezi uživatelem a aplikací
* Test dosažitelnosti aplikace z více umístění uživatelů po celém světě
* Určení latence sítě a ztráty paketů pro vaše obchodní a SaaS aplikace
* Určení aktivních bodů v síti, které mohou způsobovat špatný výkon aplikace
* Monitorování dostupnosti pro Microsoft 365 aplikací pomocí integrovaných testů pro Microsoft 365, Dynamics 365, Skype pro firmy a další služby Microsoftu

Další informace najdete v následujících článcích:

* [Konfigurace Network Performance Monitor pro monitorování koncových bodů služby](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Příspěvek na blogu](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Analýza provozu
Analýza provozu je cloudové řešení, které poskytuje přehled o aktivitách uživatelů a aplikací v cloudových sítích. Protokoly toku NSG se analyzují tak, aby poskytovaly přehledy:

* Přenos toků mezi vašimi sítěmi mezi Azure a internetem, oblastmi veřejných cloudů, virtuální sítě a podsítěmi
* Aplikace a protokoly ve vaší síti, bez potřeby pro sledování a zařízení sběrače vyhrazeného toku
* Hlavní mluvčí, aplikace v konverzaci, konverzace virtuálních počítačů v cloudu, provozní hotspoty
* Zdroje a cíle provozu mezi virtuální sítě, mezi důležitými obchodními službami a aplikacemi
* Zabezpečení – škodlivý provoz, porty otevřené pro Internet, aplikace nebo virtuální počítače, které se pokoušejí o přístup k Internetu...
* Využití kapacity – pomáhá eliminovat problémy při nadměrném zřizování nebo nevyužití Díky monitorování trendů využití bran VPN a dalších služeb.

Analýza provozu vám umožní získat užitečné informace, které vám pomůžou auditovat síťové aktivity vaší organizace, zabezpečit aplikace a data a optimalizovat výkon úloh a udržet si vyhovující předpisy.

![Geografické mapování znázorňující provoz napříč oblastmi 2](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Související odkazy:
* [Blogový příspěvek](https://aka.ms/trafficanalytics), [dokumentace](../network-watcher/traffic-analytics.md), [Nejčastější dotazy](../network-watcher/traffic-analytics-faq.md)

## <a name="dns-analytics"></a>DNS Analytics
Toto řešení je vytvořené pro Správce DNS, shromažďuje, analyzuje a koreluje protokoly DNS za účelem poskytování zabezpečení, operací a přehledů souvisejících s výkonem.  Mezi tyto možnosti patří:

* Identifikace klientů, kteří se pokoušejí přeložit na škodlivé domény
* Identifikace zastaralých záznamů prostředků
* Přehled často dotazovaných názvů domén a prahová klientů DNS
* Přehled o zatížení požadavků na serverech DNS
* Monitorování selhání registrace dynamického serveru DNS

![Řídicí panel DNS Analytics](./media/network-monitoring-overview/dns-analytics-overview.png) 

Související odkazy:
* [Příspěvek na blogu](/archive/blogs/msoms/introducing-oms-dns-analytics), [dokumentace](../azure-monitor/insights/dns-analytics.md)

## <a name="miscellaneous"></a>Různé

* [Nové ceny](../azure-monitor/insights/network-performance-monitor-pricing-faq.md)