---
title: Monitorování sítě v protokolech Azure Monitor | Dokumenty společnosti Microsoft
description: Přehled řešení monitorování sítě, včetně NPM, pro správu sítí v cloudových, místních a hybridních prostředích.
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
ms.openlocfilehash: 2912488286745bf8d2e567d09e445b0a44dc7c39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67672182"
---
# <a name="network-monitoring-solutions"></a>Řešení pro monitorování sítě 

Azure nabízí celou řadu řešení pro monitorování vašich síťových prostředků. Azure má řešení a nástroje pro monitorování připojení k síti, stav okruhů ExpressRoute a analýzu síťového provozu v cloudu.

## <a name="network-performance-monitor-npm"></a>Sledování výkonu sítě (NPM)

Sledování výkonu sítě (NPM) je sada funkcí, z nichž každá je zaměřena na sledování stavu vaší sítě, připojení k síti k aplikacím a poskytuje přehled o výkonu vaší sítě. Služba NPM je založená na cloudu a poskytuje řešení pro monitorování hybridní sítě, které monitoruje připojení mezi:
 
* Cloudová nasazení a místní umístění
* Více datových center a poboček
* Kritické víceúrovňové aplikace/mikroslužby
* Umístění uživatelů a webové aplikace (HTTP/HTTP) 

Sledování výkonu, monitor ExpressRoute a sledování připojení služby jsou funkce monitorování v rámci služby NPM a jsou popsány níže.

## <a name="performance-monitor"></a>Monitorování výkonu

Sledování výkonu je součástí npm a je monitorování sítě pro cloudová, hybridní a místní prostředí. Připojení k síti můžete monitorovat napříč vzdálenými pobočkami a pobočkami, umístěními úložišť, datovými centry a cloudy. Problémy se sítí můžete zjistit dříve, než si uživatelé stěžují. Hlavní výhody jsou:

* Sledování ztrát a latence v různých podsítích a nastavení výstrah
* Sledování všech cest (včetně redundantních cest) v síti
* Řešení potíží s přechodnou sítí a problémy se sítí v okamžiku, které je obtížné replikovat
* Určení konkrétního segmentu v síti, který je zodpovědný za snížený výkon
* Sledovat stav sítě, bez nutnosti SNMP

![Toplogická mapa NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Další informace naleznete v následujících článcích:

* [Konfigurace řešení sledování výkonu sítě v protokolech Azure Monitor](../azure-monitor/insights/network-performance-monitor.md) 
* [Případy použití](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
* Aktualizace produktu:
  * [Únor 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/)
  * [Srpen 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute Monitor

NPM pro ExpressRoute nabízí komplexní monitorování ExpressRoute pro privátní partnerský vztah Azure a připojení partnerského vztahu Microsoftu. Můžete sledovat připojení a výkon E2E mezi pobočkami a Azure přes ExpressRoute. Klíčové možnosti jsou:

* Automatická detekce obvodů ER spojených s vaším předplatným
* Detekce topologie sítě z místních do cloudových aplikací
* Plánování kapacity, analýza využití šířky pásma
* Monitorování a upozorňování na primární i sekundární cesty
* Monitorování připojení ke službám Azure, jako je Office 365, Dynamics 365, ... přes ExpressRoute
* Zjišťování degradace připojení k virtuálním sítím

![Geomapa zobrazující návštěvnost napříč regiony](./media/network-monitoring-overview/expressroute-topology-map.png) 

Další informace najdete v těchto článcích:

* [Konfigurace Network Performance Monitor pro ExpressRoute](../expressroute/how-to-npm.md)
* [Blogu](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitorování připojení služby

Díky monitorování připojení služeb můžete nyní testovat dostupnost aplikací a zjišťovat kritická místa výkonu v místním prostředí, sítích operátorů a cloudových/privátních datových centrech.

* Sledování síťového připojení k aplikacím od konce roku
* Korelovat doručování aplikací s výkonem sítě, zjistit přesné umístění degradace podél cesty mezi uživatelem a aplikací
* Testování dosažitelnosti aplikací z více uživatelských míst po celém světě
* Určení latence sítě a ztráty paketů pro vaše obchodní aplikace a aplikace SaaS
* Určení horkých míst v síti, která mohou způsobovat nízký výkon aplikace
* Sledování dostupnosti aplikací Office 365 pomocí integrovaných testů pro Microsoft Office 365, Dynamics 365, Skype pro firmy a další služby Microsoftu

Další informace najdete v těchto článcích:

* [Konfigurace sledování výkonu sítě pro sledování koncových bodů služby](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blogu](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Analýza provozu
Traffic Analytics je cloudové řešení, které poskytuje přehled o aktivitách uživatelů a aplikací ve vašich cloudových sítích. Protokoly toku nsg jsou analyzovány poskytnout přehled o:

* Přenosy mezi vašimi sítěmi mezi Azure a Internetem, oblastmi veřejného cloudu, virtuálními sítěmi a podsítěmi
* Aplikace a protokoly ve vaší síti bez nutnosti sledování nebo vyhrazených zařízení pro sběr průtoku
* Nejlepší talkers, upovídaný aplikace, Virtuální konverzace v cloudu, dopravní hotspoty
* Zdroje a cíle provozu napříč virtuálními sítěmi, vzájemné vztahy mezi kritickými obchodními službami a aplikacemi
* Zabezpečení – škodlivý provoz, porty otevřené pro Internet, aplikace nebo virtuální aplikace, které se pokoušejí o přístup k Internetu...
* Využití kapacity - pomáhá eliminovat problémy nadměrného zřizování nebo nedostatečného využití sledováním trendů využití vpn bran a dalších služeb

Služba Traffic Analytics vás vybaví informacemi, které vám pomohou auditovat síťovou aktivitu vaší organizace, zabezpečit aplikace a data, optimalizovat výkon pracovního vytížení a zůstat v souladu s předpisy.

![Geomapa zobrazující návštěvnost napříč regiony](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Související odkazy:
* [Příspěvek na blogu](https://aka.ms/trafficanalytics), [Dokumentace](https://aka.ms/trafficanalyticsdocs), [Nejčastější dotazy](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS Analytics
Toto řešení, vytvořené pro správce DNS, shromažďuje, analyzuje a koreluje protokoly DNS, aby poskytovalo přehledy týkající se zabezpečení, operací a výkonu.  Některé z možností jsou:

* Identifikace klientů, kteří se pokoušejí vyřešit škodlivé domény
* Identifikace zastaralých záznamů o prostředcích
* Přehled o často dotazovaných doménových jménech a hovorných klientech DNS
* Přehled o zatížení požadavků na serverech DNS
* Sledování dynamických selhání registrace DNS

![Řídicí panel služby DNS Analytics](./media/network-monitoring-overview/dns-analytics-overview.png) 

Související odkazy:
* [Příspěvek na blogu](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [Dokumentace](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Různé

* [Nové ceny](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
