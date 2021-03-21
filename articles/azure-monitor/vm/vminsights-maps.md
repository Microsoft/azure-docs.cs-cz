---
title: Zobrazení závislostí aplikace s využitím virtuálních počítačů Insights
description: Mapa je funkce pro službu VM Insights. Automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Tento článek poskytuje podrobné informace o tom, jak používat funkci map v různých scénářích.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: 267ec0ed01fb85e9e29b0b8c9294d20d610db8fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046563"
---
# <a name="use-the-map-feature-of-vm-insights-to-understand-application-components"></a>Použití funkce map pro službu VM Insights k porozumění součástem aplikace
V části VM Insights můžete zobrazit zjištěné součásti aplikace na virtuálních počítačích s Windows a Linux, které běží v Azure nebo ve vašem prostředí. Virtuální počítače můžete sledovat dvěma způsoby. Zobrazit mapu přímo z virtuálního počítače nebo zobrazit mapu z Azure Monitor, abyste viděli komponenty napříč skupinami virtuálních počítačů. Tento článek vám pomůže pochopit tyto dvě metody zobrazení a způsob použití funkce map. 

Informace o konfiguraci služby VM Insights najdete v tématu [Povolení služby VM Insights](./vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Seznámení s mapovým prostředím
Předtím, než se začnete na mapové prostředí, byste měli pochopit, jak prezentuje a vizualizují informace. Bez ohledu na to, jestli vyberete funkci mapa přímo z virtuálního počítače nebo z Azure Monitor, funkce mapa prezentuje konzistentní prostředí. Jediným rozdílem je, že od Azure Monitor jedna mapa zobrazuje všechny členy více aplikací nebo clusterů s více vrstvami.

Funkce map vizualizuje závislosti virtuálních počítačů pomocí zjišťování spuštěných procesů, které mají: 

- Aktivní síťová připojení mezi servery.
- Latence příchozího a odchozího připojení.
- Porty napříč libovolnou architekturou připojenou k protokolu TCP v zadaném časovém rozsahu.  
 
Rozbalte virtuální počítač pro zobrazení podrobností o procesu a jenom těch procesů, které komunikují s virtuálním počítačem. Skupina klientů zobrazuje počet front-end klientů, kteří se připojují k virtuálnímu počítači. Skupiny portů serveru zobrazují počet back-endové serverů, ke kterým se virtuální počítač připojuje. Rozbalte skupinu server-port, abyste viděli podrobný seznam serverů, které se připojují přes tento port.  

Po výběru virtuálního počítače se v podokně **vlastností** vpravo zobrazí vlastnosti virtuálního počítače. Mezi vlastnosti patří systémové informace hlášené operačním systémem, vlastnosti virtuálního počítače Azure a prstencový graf, který shrnuje zjištěná připojení. 

![Podokno Vlastnosti](./media/vminsights-maps/properties-pane-01.png)

Na pravé straně podokna vyberte **události protokolu** , abyste zobrazili seznam dat, která se do virtuálního počítače poslala Azure monitor. Tato data jsou k dispozici pro dotazování.  Výběrem libovolného typu záznamu otevřete stránku **protokoly** , kde vidíte výsledky pro daný typ záznamu. Zobrazí se také předkonfigurovaný dotaz, který je filtrovaný proti virtuálnímu počítači.  

![Podokno události protokolu](./media/vminsights-maps/properties-pane-logs-01.png)

Zavřete stránku **protokoly** a vraťte se do podokna **vlastnosti** . V takovém případě vyberte **výstrahy** pro zobrazení stavu virtuálního počítače výstrahy. Funkce map se integruje s výstrahami Azure, aby zobrazovala výstrahy pro vybraný server ve vybraném časovém rozsahu. Server zobrazuje ikonu pro aktuální výstrahy a v podokně **výstrahy počítače** se zobrazují výstrahy. 

![Podokno výstrahy](./media/vminsights-maps/properties-pane-alerts-01.png)

Chcete-li, aby funkce map zobrazovala relevantní výstrahy, vytvořte pravidlo upozornění, které bude platit pro konkrétní počítač:

- Přidejte klauzuli pro seskupení výstrah podle počítače (například **podle intervalu počítače 1 minute**).
- Založte upozornění na metriku.

Další informace o výstrahách Azure a vytváření pravidel výstrah najdete v tématu [sjednocené výstrahy v Azure monitor](../alerts/alerts-overview.md).

V pravém horním rohu možnost **Legenda** popisuje symboly a role na mapě. Pro bližší zobrazení mapy a jejich přesunu použijte ovládací prvky přiblížení v pravém dolním rohu. Můžete nastavit úroveň přiblížení a přizpůsobit mapu velikosti stránky.  

## <a name="connection-metrics"></a>Metriky připojení
V podokně **připojení** se zobrazí standardní metriky pro vybrané připojení z virtuálního počítače přes port TCP. Metriky zahrnují dobu odezvy, požadavky za minutu, propustnost provozu a odkazy.  

![Grafy připojení k síti v podokně připojení](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Neúspěšná připojení
Mapa zobrazuje neúspěšná připojení pro procesy a počítače. Přerušovaná červená čára indikuje, že se klientský systém nedaří připojit k procesu nebo portu. Pro systémy, které používají agenta závislostí, agent hlásí neúspěšné pokusy o připojení. Funkce map monitoruje proces tím, že sleduje sokety TCP, které selhaly při navázání spojení. K této chybě mohlo dojít z brány firewall, chybné konfigurace v klientovi nebo serveru nebo v nedostupné vzdálené službě.

![Neúspěšné připojení na mapě](./media/vminsights-maps/map-group-failed-connection-01.png)

Porozumění neúspěšným připojením vám pomůže při odstraňování potíží, ověřování migrace, analýze zabezpečení a pochopení celkové architektury služby. Neúspěšná připojení jsou někdy neškodná, ale často ukazují na problém. Připojení se nemusí zdařit, například když dojde k náhlému výpadku prostředí převzetí služeb při selhání nebo když dvě aplikační vrstvy nemůžou vzájemně komunikovat po migraci do cloudu.

### <a name="client-groups"></a>Skupiny klientů
Na mapě budou skupiny klientů představovat klientské počítače, které se připojují k mapovanému počítači. Jedna skupina klientů představuje klienty pro jednotlivý proces nebo počítač.

![Skupina klientů na mapě](./media/vminsights-maps/map-group-client-groups-01.png)

Chcete-li zobrazit monitorované klienty a IP adresy systémů ve skupině klientů, vyberte skupinu. Níže se zobrazí obsah skupiny.  

![Seznam IP adres na mapě pro skupinu klientů](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Pokud skupina obsahuje monitorované a nemonitorované klienty, můžete vybrat příslušnou část prstencového grafu skupiny a filtrovat tak klienty.

### <a name="server-port-groups"></a>Servery – skupiny portů
Skupiny portů serveru reprezentují porty na serverech, které mají příchozí připojení z namapovaného počítače. Tato skupina obsahuje port serveru a počet serverů, které mají připojení k tomuto portu. Výběrem skupiny zobrazíte jednotlivé servery a připojení. 

![Server – Skupina portů na mapě](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Pokud skupina obsahuje monitorované a nemonitorované servery, můžete vybrat příslušnou část prstencového grafu skupiny a filtrovat servery.

## <a name="view-a-map-from-a-vm"></a>Zobrazení mapy z virtuálního počítače 

Přístup ke CLOUDu Insights přímo z virtuálního počítače:

1. V Azure Portal vyberte **Virtual Machines**. 
2. V seznamu vyberte virtuální počítač. V části **monitorování** vyberte **přehledy**.  
3. Vyberte kartu **Mapa** .

Mapa vizualizuje závislosti virtuálních počítačů pomocí zjišťování spuštěných skupin procesů a procesů, které mají aktivní síťová připojení v zadaném časovém rozsahu.  

Ve výchozím nastavení Mapa zobrazuje posledních 30 minut. Pokud chcete vidět, jak se závislosti prohlédly v minulosti, můžete se dotázat na historické časové rozsahy až na jednu hodinu. Chcete-li spustit dotaz, použijte selektor **TimeRange** v levém horním rohu. Můžete spustit dotaz, například během incidentu nebo zobrazit stav před změnou.  

![Snímek obrazovky karty mapa v části Přehled monitorování Azure Portal zobrazuje Diagram závislostí mezi virtuálními počítači.](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Zobrazení mapy ze sady škálování virtuálních počítačů

Přístup k VIRTUÁLNÍmu počítači můžete získat přímo ze sady škálování virtuálních počítačů:

1. V Azure Portal vyberte **Virtual Machine Scale Sets**.
2. V seznamu vyberte virtuální počítač. Pak v části **monitorování** zvolte **přehledy**.  
3. Vyberte kartu **Mapa** .

Mapa vizualizuje všechny instance v sadě škálování jako uzel skupiny spolu se závislostmi skupiny. Rozbalený uzel obsahuje seznam instancí v sadě škálování. V tuto chvíli můžete procházet pomocí těchto instancí: 10. 

Pokud chcete načíst mapu pro určitou instanci, nejdřív vyberte tuto instanci na mapě. Pak vyberte tlačítko se **třemi tečkami** (...) napravo a zvolte **načíst Server mapa**. Na zobrazené mapě vidíte skupiny procesů a procesy, které mají aktivní síťová připojení v zadaném časovém rozsahu. 

Ve výchozím nastavení Mapa zobrazuje posledních 30 minut. Pokud chcete vidět, jak se závislosti prohlédly v minulosti, můžete se dotázat na historické časové rozsahy až na jednu hodinu. Chcete-li spustit dotaz, použijte selektor **TimeRange** . Můžete spustit dotaz, například během incidentu nebo zobrazit stav před změnou.

![Snímek obrazovky s kartou map v části Přehled monitorování Azure Portal zobrazuje Diagram závislostí mezi sadami škálování virtuálních počítačů.](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Přístup k mapě konkrétní instance můžete získat také ze zobrazení **instance** pro sadu škálování virtuálního počítače. V části **Nastavení** přejdete na **instance**  >  **přehledy**.

## <a name="view-a-map-from-azure-monitor"></a>Zobrazit mapu z Azure Monitor

V Azure Monitor funkce mapa poskytuje globální pohled na vaše virtuální počítače a jejich závislosti. Přístup k funkci mapa v Azure Monitor:

1. V Azure Portal vyberte **monitorovat**. 
2. V části **přehledy** vyberte **Virtual Machines**.
3. Vyberte kartu **Mapa** .

   ![Azure Monitor – přehled mapy více virtuálních počítačů](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Pomocí selektoru **pracovního prostoru** v horní části stránky vyberte pracovní prostor. Pokud máte více než jeden Log Analytics pracovní prostor, vyberte pracovní prostor, který je povolený pro řešení a který obsahuje virtuální počítače, které se k němu nahlásí. 

Selektor **skupin** vrátí předplatná, skupiny prostředků, [skupiny počítačů](../logs/computer-groups.md)a sady škálování virtuálních počítačů pro počítače, které se vztahují k vybranému pracovnímu prostoru. Váš výběr se vztahuje pouze na funkci mapy a neprovádí se nad výkonem nebo stavem.

Ve výchozím nastavení Mapa zobrazuje posledních 30 minut. Pokud chcete vidět, jak se závislosti prohlédly v minulosti, můžete se dotázat na historické časové rozsahy až na jednu hodinu. Chcete-li spustit dotaz, použijte selektor **TimeRange** . Můžete spustit dotaz, například během incidentu nebo zobrazit stav před změnou.  

## <a name="next-steps"></a>Další kroky

K identifikaci kritických bodů, kontrole výkonu a pochopení celkového využití virtuálních počítačů, najdete informace v tématu [zobrazení stavu výkonu pro službu VM Insights](vminsights-performance.md).
