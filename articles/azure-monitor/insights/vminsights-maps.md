---
title: Jak zobrazit závislosti aplikace pomocí Azure monitoru pro virtuální počítače (preview) | Dokumentace Microsoftu
description: Mapování je funkce služby Azure Monitor pro virtuální počítače. Automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi službami. Tento článek obsahuje podrobnosti o tom, jak používat funkci Mapa v různých scénářích.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: f6273e9b6c7ed0c4685479976343497f01201b0b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206763"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>Seznámení s komponentami aplikací pomocí funkce mapy služby Azure Monitor pro virtuální počítače (preview)
Ve službě Azure Monitor pro virtuální počítače můžete zobrazit zjištěnou aplikaci součásti Windows a Linuxu na virtuálních počítačích (VM), které běží v Azure nebo ve vašem prostředí. Podívejte se na virtuální počítače dvěma způsoby. Zobrazení mapy přímo z virtuálního počítače nebo zobrazení mapování ze služby Azure Monitor zobrazíte komponenty napříč skupinami virtuálních počítačů. Tento článek vám pomůže pochopit těchto dvou metod prohlížení a o tom, jak používat funkci Mapa. 

Informace o konfiguraci monitorování Azure pro virtuální počítače najdete v tématu [povolit monitorování Azure pro virtuální počítače](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure
Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Úvod do prostředí mapy
Před všemi zúčastněnými stranami mapování prostředí, by měl pochopit, jak uvede a vizualizuje informace. Určuje, zda můžete vybrat funkci Mapa přímo z virtuálního počítače nebo ze služby Azure Monitor, představuje funkci Mapa konzistentní prostředí. Jediným rozdílem je, že ze služby Azure Monitor, jedna mapa zobrazuje všechny členy více vrstvami aplikace nebo clusteru.

Mapování funkce vizualizuje zjištěním spuštěné procesy, které mají závislosti virtuálních počítačů: 

- Aktivní síťová připojení mezi servery.
- Latence příchozí a odchozí připojení.
- Porty v jakékoli architektuře propojené TCP za zadané časové období.  
 
Rozbalte virtuální počítač, který zobrazí podrobnosti o procesu a pouze procesy, které komunikují s virtuálním Počítačem. Skupina klientů zobrazuje počet front-endu klientů, které se připojují k virtuálnímu počítači. Port serveru skupiny se zobrazí počet back-end serverů, které se připojí k virtuálnímu počítači. Rozbalte skupinu port serveru zobrazíte podrobné seznam serverů, které se připojují přes tento port.  

Při výběru virtuálního počítače, **vlastnosti** podokně na pravé straně se zobrazí vlastnosti Virtuálního počítače. Vlastnosti zahrnují informace o systému hlášených operačního systému, vlastnosti virtuálního počítače Azure a prstencový graf, který shrnuje zjištěných připojení. 

![Podokno vlastností](./media/vminsights-maps/properties-pane-01.png)

Na pravé straně podokna, vyberte **události protokolu** zobrazí seznam dat, které virtuální počítač odeslal do Azure monitoru. Tato data jsou k dispozici pro dotazování.  Vyberte libovolný typ záznamu a otevřete **protokoly** stránku, kde uvidíte výsledky pro daný typ záznamu. Zobrazí také předem dotaz s filtrem na virtuální počítač.  

![V podokně události protokolu](./media/vminsights-maps/properties-pane-logs-01.png)

Zavřít **protokoly** stránky a vrátit se **vlastnosti** podokně. Vyberte **výstrahy** zobrazovat výstrahy kritéria stavu virtuálního počítače. Funkce Mapa se integruje s Azure Alerts, chcete-li zobrazit výstrahy pro vybraný server ve vybraném časovém rozsahu. Serveru se zobrazí ikona pro aktuální výstrahy a **počítač výstrahy** podokně je seznam výstrah. 

![V podokně výstrahy](./media/vminsights-maps/properties-pane-alerts-01.png)

Chcete-li funkci mapy zobrazení příslušné výstrahy, vytvořte pravidlo upozornění, která se vztahuje k určitému počítači:

- Obsahovat klauzuli výstrah skupiny podle počítače (například **počítače interval 1 minuta**).
- Základní upozornění na metriku.

Další informace o Azure výstrahy a vytváření pravidel upozornění, najdete v části [Unified výstrah ve službě Azure Monitor](../../azure-monitor/platform/alerts-overview.md).

V pravém horním rohu **legendy** možnost popisuje symboly a role na mapě. Pro bližší pohled na mapě a přesuňte ho kolem použijte ovládací prvky zvětšení v pravém dolním rohu. Můžete nastavit úroveň přiblížení a přizpůsobit mapu, aby velikost stránky.  

## <a name="connection-metrics"></a>Metrik připojení
**Připojení** podokně se zobrazí standardní metriky pro vybrané připojení z virtuálního počítače přes TCP port. Metriky zahrnují doby odezvy, počet požadavků za minutu, propustnost přenosů a odkazy.  

![Grafy připojení k síti v podokně připojení](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Neúspěšná připojení
Na mapě zobrazí neúspěšné připojení pro procesy a počítače. Přerušované červená čára označuje, že klientský systém nemůže kontaktovat procesu nebo portu. Pro systémy, které používají agenta závislostí bude agent podřízen na neúspěšné pokusy o připojení. Funkce Mapa sleduje proces pozorováním sockety TCP, které se nepodařilo navázat připojení. Tato chyba může být výsledkem bránu firewall, chybnou konfigurací v klientovi nebo serveru nebo není k dispozici vzdálené služby.

![Selhání připojení na mapě](./media/vminsights-maps/map-group-failed-connection-01.png)

Principy vám může pomoct neúspěšná připojení řešení potíží s ověřit migraci, analýzu zabezpečení a vysvětlení architektury služby. Neúspěšná připojení jsou někdy neškodné, ale často ukazují na problém. Připojení může například selhat při převzetí služeb při selhání prostředí náhle nestane nedostupným nebo dvou aplikačních vrstev nemůže komunikovat mezi sebou po migrace do cloudu.

### <a name="client-groups"></a>Skupin klientů
Na mapě představují skupin klientů klientské počítače, které se připojují k namapované počítači. Jedné skupiny klientů reprezentuje klienty pro jednotlivé procesy nebo počítač.

![Skupina klientů na mapě](./media/vminsights-maps/map-group-client-groups-01.png)

Pokud chcete zobrazit monitorovaných klientů a IP adres v systémech ve skupině klientů, vyberte skupinu. Se níže zobrazí obsah skupiny.  

![Skupina klientů seznam IP adres na mapě](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Pokud skupina obsahuje monitorované a nemonitorované klienty, můžete vybrat odpovídající části skupiny prstencový graf pro filtrování klienty.

### <a name="server-port-groups"></a>Port serveru skupiny
Port serveru skupiny představují portů na serverech, které mají příchozí připojení z namapované počítače. Tato skupina obsahuje port serveru a počet serverů, které jste připojení k tomuto portu. Vyberte skupinu, kterou chcete zobrazit jednotlivé servery a připojení. 

![Port serveru skupiny na mapě](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Pokud skupina obsahuje servery monitorované a nemonitorované, můžete vybrat odpovídající části skupiny prstencový graf pro filtrování servery.

## <a name="view-a-map-from-a-vm"></a>Zobrazit mapování z virtuálního počítače 

Pro přístup k Azure Monitor pro virtuální počítače přímo z virtuálního počítače:

1. Na webu Azure Portal, vyberte **virtuálních počítačů**. 
2. V seznamu vyberte virtuální počítač. V **monitorování** zvolte **Insights (preview)** .  
3. Vyberte **mapy** kartu.

Na mapě vizualizuje závislosti Virtuálního počítače pomocí zjišťování skupin procesů a procesy, které mají aktivními síťovými připojeními za zadané časové období.  

Ve výchozím nastavení zobrazí na mapě posledních 30 minut. Pokud chcete zobrazit, jak závislosti hledá v minulosti, můžete zadat dotaz na historické časových rozsahů až jednu hodinu. Chcete-li spustit dotaz, použijte **TimeRange** selektoru v levém horním rohu. Můžete spustit dotaz, například během incident nebo pokud chcete zobrazit stav před změnu.  

![Přímé mapování Přehled virtuálních počítačů](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Zobrazení mapování ze škálovací sady virtuálních počítačů

Pro přístup k Azure Monitor pro virtuální počítače přímo ze škálovací sady virtuálních počítačů:

1. Na webu Azure Portal, vyberte **škálovací sady virtuálních počítačů**.
2. V seznamu vyberte virtuální počítač. Pak v **monitorování** zvolte **Insights (preview)** .  
3. Vyberte **mapy** kartu.

Na mapě vizualizuje všechny instance ve škálovací sadě jako uzel skupiny spolu s skupiny závislosti. Rozbalený uzel obsahuje seznam instancí ve škálovací sadě. Tyto instance 10 můžete procházet v čase. 

Chcete-li načíst mapu pro konkrétní instanci, nejprve vyberte této instance na mapě. Vyberte **tlačítko se třemi tečkami** tlačítko (...) napravo a zvolte **načíst serverovou mapu**. V objektu map, který se zobrazí uvidíte skupin procesů a procesy, které mají aktivními síťovými připojeními za zadané časové období. 

Ve výchozím nastavení zobrazí na mapě posledních 30 minut. Pokud chcete zobrazit, jak závislosti hledá v minulosti, můžete zadat dotaz na historické časových rozsahů až jednu hodinu. Chcete-li spustit dotaz, použijte **TimeRange** selektor. Můžete spustit dotaz, například během incident nebo pokud chcete zobrazit stav před změnu.

![Přímé mapování Přehled virtuálních počítačů](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Mapování pro konkrétní instanci z se dá dostat taky **instance** zobrazení pro škálovací sadu virtuálních počítačů. V **nastavení** oddílu, přejděte na **instance** > **Insights (preview)** .

## <a name="view-a-map-from-azure-monitor"></a>Zobrazení mapování ze služby Azure Monitor
Ve službě Azure Monitor funkce mapy poskytuje globální přehled o vašich virtuálních počítačů a jejich závislosti. Pro přístup k funkci mapy ve službě Azure Monitor:

1. Na webu Azure Portal, vyberte **monitorování**. 
2. V **Insights** zvolte **virtuálních počítačů (preview)** .
3. Vyberte **mapy** kartu.

   ![Azure Monitor přehledovou mapu více virtuálních počítačů](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Vyberte pracovní prostor, s použitím **pracovní prostor** selektoru v horní části stránky. Pokud máte více než jednomu pracovnímu prostoru Log Analytics vyberte pracovní prostor, který je povolená s řešením a má virtuální počítače, které mu podává zprávy. 

**Skupiny** selektor vrátí předplatná, skupiny prostředků, [skupiny počítačů](../../azure-monitor/platform/computer-groups.md)a škálovací sady virtuálních počítačů z počítačů, které se vztahují k vybranému pracovnímu prostoru. Váš výběr se vztahuje pouze na funkci mapy a nepřenáší na výkon nebo stav.

Ve výchozím nastavení zobrazí na mapě posledních 30 minut. Pokud chcete zobrazit, jak závislosti hledá v minulosti, můžete zadat dotaz na historické časových rozsahů až jednu hodinu. Chcete-li spustit dotaz, použijte **TimeRange** selektor. Můžete spustit dotaz, například během incident nebo pokud chcete zobrazit stav před změnu.  

## <a name="next-steps"></a>Další postup
- Další informace o použití funkce stavu, najdete v článku [stav virtuálního počítače Azure zobrazení](vminsights-health.md). 
- Identifikujte kritické body, zkontrolujte výkon a pochopit celkové využití vašich virtuálních počítačů, přečtěte si [zobrazení stavu výkonu pro monitorování Azure pro virtuální počítače](vminsights-performance.md). 
