---
title: Zobrazení závislostí aplikací pomocí Azure Monitoru pro virtuální počítače
description: Mapa je funkce Azure Monitor u virtuálních počítačů. Automaticky zjišťuje součásti aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Tento článek obsahuje podrobnosti o použití funkce Mapa v různých scénářích.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: acb96984a49e4ad8535f87a41da11b3b63ae207b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283850"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>Použití funkce Map azure monitoru pro virtuální počítače k pochopení součástí aplikací
V Azure Monitor pro virtuální počítače můžete zobrazit zjištěné součásti aplikací na virtuálních počítačích (VM) Windows a Linuxu, které běží v Azure nebo ve vašem prostředí. Virtuální chod můžete sledovat dvěma způsoby. Zobrazení mapy přímo z virtuálního počítače nebo zobrazení mapy z Azure Monitor uvidíte komponenty napříč skupinami virtuálních počítačích. Tento článek vám pomůže pochopit tyto dvě metody zobrazení a jak používat funkci Mapa. 

Informace o konfiguraci Azure Monitoru pro virtuální počítače najdete [v tématu Povolení Azure Monitoru pro virtuální počítače](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Úvod do prostředí mapy
Než se ponoříte do prostředí mapy, měli byste pochopit, jak prezentuje a vizualizuje informace. Ať už vyberete funkci Map přímo z virtuálního počítače nebo z Azure Monitor, funkce Map představuje konzistentní prostředí. Jediným rozdílem je, že z Azure Monitoru, jedna mapa zobrazuje všechny členy vícevrstvé aplikace nebo clusteru.

Funkce Map vizualizuje závislosti virtuálních zařízení zjišťováním spuštěných procesů, které mají: 

- Aktivní síťová připojení mezi servery.
- Latence příchozího a odchozího připojení.
- Porty napříč libovolnou architekturou připojenou k protokolu TCP v zadaném časovém rozsahu.  
 
Rozbalte virtuální hosti a zobrazte podrobnosti procesu a jenom ty procesy, které komunikují s virtuálním ms. Skupina klientů zobrazuje počet front-endových klientů, kteří se připojují k virtuálnímu virtuálnímu virtuálnímu mísu. Skupiny portů serveru zobrazují počet serverů back-end, ke serverům, ke kterých se virtuální server připojuje. Rozbalte skupinu portů serveru a zobcete podrobný seznam serverů, které se přes tento port připojují.  

Když vyberete virtuální počítač, podokno **Vlastnosti** vpravo zobrazuje vlastnosti virtuálního počítače. Vlastnosti zahrnují systémové informace hlášené operačním systémem, vlastnosti virtuálního počítače Azure a prstencový graf, který shrnuje zjištěná připojení. 

![Podokno Vlastnosti](./media/vminsights-maps/properties-pane-01.png)

Na pravé straně podokna vyberte **Události protokolu,** chcete-li zobrazit seznam dat, která virtuální počítač odeslal do Azure Monitoru. Tato data jsou k dispozici pro dotazování.  Vyberte libovolný typ záznamu, chcete-li otevřít stránku **Protokoly,** kde se zobrazí výsledky pro tento typ záznamu. Zobrazí se také předkonfigurovaný dotaz, který je filtrovaný proti virtuálnímu počítače.  

![Podokno Události protokolu](./media/vminsights-maps/properties-pane-logs-01.png)

Zavřete stránku **Protokoly** a vraťte se do **podokna Vlastnosti.** Tady vyberte **Výstrahy,** chcete-li zobrazit výstrahy kritérií stavu virtuálního počítače. Funkce Map se integruje s výstrahami Azure a zobrazuje výstrahy pro vybraný server ve vybraném časovém rozsahu. Na serveru se zobrazí ikona aktuálních výstrah a podokno **Výstrahy počítače** zobrazí seznam výstrah. 

![Podokno Upozornění](./media/vminsights-maps/properties-pane-alerts-01.png)

Chcete-li, aby funkce Map zobrazovala relevantní výstrahy, vytvořte pravidlo výstrahy, které se vztahuje na konkrétní počítač:

- Zahrňte klauzuli pro seskupení výstrah podle počítače (například **podle intervalu počítače 1 minuta).**
- Založte výstrahu na metrice.

Další informace o azure výstrahy a vytváření pravidel výstrah, najdete [v tématu jednotné výstrahy v Azure Monitor](../../azure-monitor/platform/alerts-overview.md).

V pravém horním rohu **legenda** volba popisuje symboly a role na mapě. Chcete-li se blíže podívat na mapu a přesunout ji, použijte ovládací prvky lupy v pravém dolním rohu. Můžete nastavit úroveň zvětšení a přizpůsobit mapu velikosti stránky.  

## <a name="connection-metrics"></a>Metriky připojení
Podokno **Připojení** zobrazuje standardní metriky pro vybrané připojení z virtuálního virtuálního virtuálního serveru přes port TCP. Metriky zahrnují dobu odezvy, požadavky za minutu, propustnost provozu a odkazy.  

![Grafy připojení k síti v podokně Připojení](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Neúspěšná připojení
Mapa zobrazuje neúspěšná připojení procesů a počítačů. Přerušovaná červená čára označuje, že klientský systém nedosahuje procesu nebo portu. Pro systémy, které používají agentzávislost, agent hlásí neúspěšné pokusy o připojení. Funkce Map monitoruje proces sledováním soketů TCP, které nenavazují navázat připojení. Tato chyba může být důsledkem brány firewall, chybné konfigurace klienta nebo serveru nebo nedostupné vzdálené služby.

![Neúspěšné připojení na mapě](./media/vminsights-maps/map-group-failed-connection-01.png)

Principy neúspěšných připojení vám mohou pomoci řešit potíže s migrací, ověřit migraci, analyzovat zabezpečení a porozumět celkové architektuře služby. Neúspěšná připojení jsou někdy neškodná, ale často poukazují na problém. Připojení může selhat, například když prostředí s podporou převzetí služeb při selhání náhle stane nedostupnýnebo když dvě vrstvy aplikace nemůže komunikovat mezi sebou po migraci do cloudu.

### <a name="client-groups"></a>Skupiny klientů
Na mapě představují skupiny klientů klientské počítače, které se připojují k mapovanému počítači. Jedna skupina klientů představuje klienty pro jednotlivé procesy nebo počítače.

![Skupina klientů na mapě](./media/vminsights-maps/map-group-client-groups-01.png)

Chcete-li zobrazit monitorované klienty a adresy IP systémů ve skupině klientů, vyberte skupinu. Obsah skupiny se zobrazí níže.  

![Seznam IP adres skupiny klientů na mapě](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Pokud skupina obsahuje monitorované a nemonitorované klienty, můžete vybrat příslušnou část prstencového grafu skupiny a filtrovat je.

### <a name="server-port-groups"></a>Skupiny portů serveru
Skupiny portů serveru představují porty na serverech, které mají příchozí připojení z mapovaného počítače. Skupina obsahuje port serveru a počet serverů, které mají připojení k tomuto portu. Výběrem skupiny zobrazíte jednotlivé servery a připojení. 

![Skupina portů serveru na mapě](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Pokud skupina obsahuje monitorované a nemonitorované servery, můžete vybrat příslušnou část prstencového grafu skupiny a filtrovat servery.

## <a name="view-a-map-from-a-vm"></a>Zobrazení mapy z virtuálního virtuálního mě 

Přístup k Azure Monitor pro virtuální počítače přímo z virtuálního počítače:

1. Na webu Azure Portal vyberte **Virtuální počítače**. 
2. V seznamu zvolte virtuální hod. V části **Monitorování** zvolte **Přehledy**.  
3. Vyberte kartu **Mapa.**

Mapa vizualizuje závislosti virtuálního uživatele zjišťováním spuštěných skupin procesů a procesů, které mají aktivní síťová připojení v zadaném časovém rozsahu.  

Ve výchozím nastavení se na mapě zobrazuje posledních 30 minut. Pokud chcete vidět, jak vypadaly závislosti v minulosti, můžete dotazovat na historické časové rozsahy až jednu hodinu. Chcete-li spustit dotaz, použijte volič **TimeRange** v levém horním rohu. Můžete spustit dotaz, například během incidentu nebo zobrazit stav před změnou.  

![Přehled mapy přímého virtuálního zařízení](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Zobrazení mapy ze škálovací sady virtuálních strojů

Přístup k Azure Monitoru pro virtuální počítače přímo ze škálovací sady virtuálních strojů:

1. Na webu Azure Portal vyberte **škálovací sady virtuálních strojů**.
2. V seznamu zvolte virtuální hod. Potom v části **Monitorování** zvolte **Přehledy**.  
3. Vyberte kartu **Mapa.**

Mapa vizualizuje všechny instance v škálovací sadě jako uzel skupiny spolu se závislostmi skupiny. V rozšířeném uzlu jsou uvedeny instance v škálovací sadě. Tyto instance můžete posouvat současně po 10. 

Chcete-li načíst mapu pro určitou instanci, nejprve vyberte tuto instanci na mapě. Pak vyberte tlačítko **se třemi tečkami** (...) vpravo a zvolte **Načíst mapu serveru**. Na mapě, která se zobrazí, se zobrazí skupiny procesů a procesy, které mají aktivní síťová připojení v zadaném časovém rozsahu. 

Ve výchozím nastavení se na mapě zobrazuje posledních 30 minut. Pokud chcete vidět, jak vypadaly závislosti v minulosti, můžete dotazovat na historické časové rozsahy až jednu hodinu. Chcete-li spustit dotaz, použijte volič **TimeRange.** Můžete spustit dotaz, například během incidentu nebo zobrazit stav před změnou.

![Přehled mapy přímého virtuálního zařízení](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Můžete také přistupovat k mapě pro konkrétní **instanci** ze zobrazení instance pro škálovací sadu virtuálních strojů. V části **Nastavení** přejděte na **Přehledy instancí** > **Insights**.

## <a name="view-a-map-from-azure-monitor"></a>Zobrazení mapy z Azure Monitoru

Ve službě Azure Monitor poskytuje funkce Map globální zobrazení virtuálních počítačích a jejich závislostí. Přístup k funkci Map ve službě Azure Monitor:

1. Na webu Azure Portal vyberte **Monitor**. 
2. V části **Přehledy** zvolte **Virtuální počítače**.
3. Vyberte kartu **Mapa.**

   ![Mapa přehledu Azure Monitoru pro více virtuálních počítačů](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Zvolte pracovní prostor pomocí voliče **pracovního prostoru** v horní části stránky. Pokud máte více než jeden pracovní prostor Log Analytics, zvolte pracovní prostor, který je povolený s řešením a který má virtuální chod, které se do něj hlásí. 

Volič **skupiny** vrátí odběry, skupiny prostředků, [skupiny počítačů](../../azure-monitor/platform/computer-groups.md)a škálovací sady virtuálních počítačů, které souvisejí s vybraným pracovním prostorem. Váš výběr se vztahuje pouze na funkci Mapa a nepřenáší se na výkon nebo stav.

Ve výchozím nastavení se na mapě zobrazuje posledních 30 minut. Pokud chcete vidět, jak vypadaly závislosti v minulosti, můžete dotazovat na historické časové rozsahy až jednu hodinu. Chcete-li spustit dotaz, použijte volič **TimeRange.** Můžete spustit dotaz, například během incidentu nebo zobrazit stav před změnou.  

## <a name="next-steps"></a>Další kroky

Pokud chcete identifikovat kritická místa, zkontrolovat výkon a pochopit celkové využití virtuálních počítačů, přečtěte si část [Zobrazení stavu výkonu pro Azure Monitor pro virtuální počítače](vminsights-performance.md). 
