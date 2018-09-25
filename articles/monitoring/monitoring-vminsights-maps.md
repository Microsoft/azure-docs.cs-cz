---
title: Jak zobrazit závislosti aplikace pomocí Azure monitoru pro virtuální počítače | Dokumentace Microsoftu
description: Mapování je funkce služby Azure Monitor pro virtuální počítače, který automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi službami. Tento článek obsahuje podrobnosti o tom, jak použít v nejrůznějších scénářích.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: magoedte
ms.openlocfilehash: 30f9a874853f4cb492d7a562d0d2439b73cf62dc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989970"
---
# <a name="using-azure-monitor-for-vms-map-to-understand-application-components"></a>Pomocí Azure monitoru pro virtuální počítače mapu pochopit součásti aplikace
Zobrazení komponenty zjištěnou aplikaci ve Windows a Linuxu, virtuální počítače provozované v Azure máte prostředí můžete pozorovat dvě možnosti, jak pomocí Azure monitoru pro virtuální počítače z virtuálního počítače přímo nebo přes skupiny virtuálních počítačů ze služby Azure Monitor. 

Tento článek vám pomůže pochopit prostředí mezi dvěma perspektivy a jak používat funkci Mapa. Informace o konfiguraci monitorování Azure pro virtuální počítače najdete v tématu [povolit monitorování Azure pro virtuální počítače](monitoring-vminsights-onboard.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="introduction-to-map-experience"></a>Úvod do prostředí mapy
Než se podíváme do zobrazení mapy pro jeden virtuální počítač nebo skupinu virtuálních počítačů, je důležité, že zajišťuje stručný úvod k funkci tak, že chápete, jak je uvedené informace a co představují vizualizace.  

Určuje, zda můžete vybrat funkci Mapa přímo z virtuálního počítače nebo ze služby Azure Monitor, uvede konzistentní prostředí.  Jediným rozdílem je ze služby Azure Monitor že můžete zobrazit všechny členy vícevrstvou aplikaci nebo cluster v jednu mapu.

Mapování závislostí virtuálních počítačů vizualizuje zjištěním spuštěné procesy s aktivními síťovými připojeními mezi servery, příchozí a odchozí připojení latence a porty v jakékoli architektuře propojené TCP za zadané časové období.  Rozšíření virtuálního počítače se zobrazí podrobnosti o procesu a jsou zobrazeny pouze procesy, které komunikují s virtuálním Počítačem. Počet front-endu klientů, které se připojují k virtuálnímu počítači je vyznačené skupiny klientů. Počet back-end serverů, které se virtuální počítač připojí k jsou uvedeny na skupiny portů serveru. Rozbalte skupinu portů serveru zobrazíte podrobné seznam serverů, které jsou připojené přes tento port.  

Když kliknete na virtuálním počítači, **vlastnosti** rozbalený na pravé straně zobrazíte vlastnosti položky vybrané, jako je například systémové informace hlášené operačního systému, vlastnosti virtuálního počítače Azure a prstencový Sumarizace zjištěných připojení. 

![Vlastnosti systému počítače](./media/monitoring-vminsights-maps/properties-pane-01.png)

Na pravé straně podokna, klikněte na **události protokolu** ikonu pro přepnutí podokna se zobrazí seznam tabulek, které shromažďují data z virtuálního počítače se odesílají do Log Analytics a je k dispozici pro dotazování.  Kliknutím na některou z uvedených typů záznamů se otevře **protokoly** stránka pro zobrazení výsledků pro daný typ pomocí předem nakonfigurovaných dotaz vyfiltruje proti konkrétní virtuální počítač.  

![Seznam protokolu hledání v podokně vlastností](./media/monitoring-vminsights-maps/properties-pane-logs-01.png)

Zavřít *protokoly** a vrátit se **vlastnosti** podokně a vyberte **výstrahy** zobrazovat výstrahy, které výstrahy generují pro virtuální počítač z kritérií stavu. Mapa se integruje s Azure Alerts zobrazíte aktivovaná upozornění pro vybraný server ve vybraném časovém rozsahu. Na serveru zobrazuje ikonu, pokud je aktuální výstrahy a podokno počítače výstrah obsahuje výstrahy. 

![Výstrahy počítače v podokně vlastností](./media/monitoring-vminsights-maps/properties-pane-alerts-01.png)

Pokud chcete povolit funkci Mapa k zobrazení příslušné výstrahy, vytvořte pravidlo upozornění, který se aktivuje pro určitý počítač. Chcete-li vytvořit správné výstrahy:
- Obsahovat klauzuli do skupiny podle počítače (například **počítače interval 1 minuta**).
- Vyberte výstrahy založené na základě měření metriky.

Další informace o Azure výstrahy a vytváření pravidel upozornění, najdete v části [Unified výstrah ve službě Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)

**Legendy** v pravém horním rohu možnost popisuje symboly a role na mapě.  Přiblížit pro bližší pohled na mapě a přesuňte it kolem ovládací prvky zvětšení v dolní pravé straně stránky nastaví úroveň přiblížení a přizpůsobit stránky a velikost aktuální stránky.  

## <a name="connection-metrics"></a>Metrik připojení
**Připojení** podokně se zobrazí standardní připojení metriky pro vybrané připojení z virtuálního počítače přes TCP port. Metriky zahrnují doby odezvy, počet požadavků za minutu, propustnost přenosů a odkazy.  

![Příklad podokně grafy připojení sítě](./media/monitoring-vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Neúspěšná připojení
Neúspěšná připojení jsou uvedeny v objektu map pro procesy a počítačů s přerušované červená čára indikující, že klientský systém nemůže kontaktovat procesu nebo port. Neúspěšná připojení jsou hlášeny z jakéhokoli systému pomocí agenta závislostí, pokud daný systém je pokusu o připojení se nezdařilo. Mapa měří tento proces pozorováním sockety TCP, které se nepodařilo navázat připojení. Tato chyba může být výsledkem bránu firewall, chybnou konfigurací v klientovi nebo serveru nebo vzdálené služby není k dispozici.

![Příklad připojení se nezdařilo na mapě](./media/monitoring-vminsights-maps/map-group-failed-connection-01.png)

Principy selhání připojení můžou pomoct se řešení potíží, ověření migrace, analýzu zabezpečení a vysvětlení architektury služby. Neúspěšná připojení jsou někdy neškodné, ale často ukazují přímo na problém, jako je převzetí služeb při selhání prostředí náhle stane nedostupný nebo dvou aplikačních vrstev nemožnosti komunikovat mezi sebou po migrace do cloudu.

### <a name="client-groups"></a>Skupiny klientů
Skupiny klientů na mapě představují klientské počítače, které mají připojení k počítači, který namapované. Jedné skupiny klientů reprezentuje klienty pro jednotlivé procesy nebo počítač.

![Příklad skupin klientů na mapě](./media/monitoring-vminsights-maps/map-group-client-groups-01.png)

Pokud chcete zobrazit monitorovaných klientů a IP adres v systémech ve skupině klientů, vyberte skupinu. Obsah skupiny patří pod skupinu.  

![Příklad seznamu klienta skupiny IP adres na mapě](./media/monitoring-vminsights-maps/map-group-client-group-iplist-01.png)

Pokud skupina obsahuje monitorované a nemonitorovaná klienty, můžete vybrat odpovídající část na prstencový graf skupinu, kterou chcete filtrovat klienty.

### <a name="server-port-groups"></a>Port serveru skupiny
Port serveru skupiny představují porty serveru na servery, které obsahují příchozí připojení z namapované počítače. Tato skupina obsahuje port serveru a počet serverů s připojením k tomuto portu. Vyberte skupinu, kterou chcete zobrazit jednotlivé servery a připojení, které jsou uvedeny. 

![Příklad skupina portů serveru na mapě](./media/monitoring-vminsights-maps/map-group-server-port-groups-01.png)  

Pokud skupina obsahuje servery monitorované a nemonitorovaná, můžete vybrat odpovídající část na prstencový graf skupinu, kterou chcete filtrovat servery.

## <a name="view-map-directly-from-a-virtual-machine"></a>Zobrazit mapu přímo z virtuálního počítače 

Pokud chcete získat přístup k Azure Monitor pro virtuální počítače přímo z virtuálního počítače, postupujte takto.

1. Na webu Azure Portal, vyberte **virtuálních počítačů**. 
2. V seznamu vyberte virtuální počítač a **monitorování** zvolte **Insights (preview)**.  
3. Vyberte **mapy** kartu.

Mapa vizualizuje závislosti virtuálních počítačů, ve kterých běží skupin procesů a procesy s aktivními síťovými připojeními za zadané časové období.  Ve výchozím nastavení zobrazí na mapě posledních 30 minut.  Použití **TimeRange** selektoru v levém horním rohu, můžete zadat dotaz na historické časových rozsahů až jednu hodinu, než ukazují, jak závislosti hledá v minulosti (například během incident nebo předtím, než došlo ke změně).  

![Přímé mapování Přehled virtuálních počítačů](./media/monitoring-vminsights-maps/map-direct-vm-01.png)

## <a name="view-map-from-azure-monitor"></a>Zobrazení mapování ze služby Azure Monitor
Funkci Mapa ze služby Azure Monitor poskytuje globální přehled o vašich virtuálních počítačů a jejich závislosti.  Pokud chcete získat přístup k funkci Mapa ze služby Azure Monitor, postupujte takto. 

1. Na webu Azure Portal, vyberte **monitorování**. 
2. Zvolte **virtuálních počítačů (preview)** v **Insights** oddílu.
3. Vyberte **mapy** kartu.

![Mapa Přehled služby Azure Monitor více virtuálních počítačů](./media/monitoring-vminsights-maps/map-multivm-azure-monitor-01.png)

Z **pracovní prostor** selektoru v horní části stránky, pokud máte více než jednomu pracovnímu prostoru Log Analytics, vyberte ten, který se integruje s řešením a má virtuální počítače, které jí reportují.  Vyberte z **skupiny** selektor, předplatné nebo skupinu prostředků Chcete-li zobrazit sadu virtuálních počítačů a jejich závislostí odpovídající skupině v zadaném časovém období.  Ve výchozím nastavení zobrazí na mapě posledních 30 minut.  Použití **TimeRange** selektor, můžete zadat dotaz na historické časových rozsahů až jednu hodinu, než ukazují, jak závislosti hledá v minulosti (například během incident nebo předtím, než došlo ke změně).   

## <a name="next-steps"></a>Další postup
Další informace o použití funkce stavu, najdete v článku [zobrazení stavu virtuálních počítačů Azure](monitoring-vminsights-health.md), nebo pokud chcete identifikovat problémová místa a celkové využití výkonu vašich virtuálních počítačů, přečtěte si téma [zobrazení Azure monitoru výkonu virtuálních počítačů](monitoring-vminsights-performance.md). 
