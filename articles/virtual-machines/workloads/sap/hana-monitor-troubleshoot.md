---
title: Monitorování a řešení potíží z HANA na straně na systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Monitorování a řešení potíží s ze strany HANA na systému SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 15b3fb4ae483e5b0e4f930d0dc08de6d198d0e5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61128843"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Sledování a řešení potíží na straně HANA

Abyste mohli efektivně analyzovat problémy související se SAP HANA v Azure (velké instance), je vhodné zúžit hlavní příčinu problému. SAP publikoval velké množství dokumentací a pomohou vám.

Následující poznámky SAP najdete příslušné nejčastějších dotazech týkajících se výkon platformy SAP HANA:

- [Poznámka SAP #2222200 – nejčastější dotazy: SAP HANA sítě](https://launchpad.support.sap.com/#/notes/2222200)
- [Poznámka SAP #2100040 – nejčastější dotazy: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [Poznámka SAP #199997 – nejčastější dotazy: SAP HANA paměti](https://launchpad.support.sap.com/#/notes/2177064)
- [Poznámka SAP #200000 – nejčastější dotazy: Optimalizace výkonu SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [Poznámka SAP #199930 – nejčastější dotazy: SAP HANA vstupně-výstupní operace analýzy](https://launchpad.support.sap.com/#/notes/1999930)
- [Poznámka SAP #2177064 – nejčastější dotazy: Restartujte službu SAP HANA a dojde k chybě](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA výstrahy

Jako první krok zkontrolujte aktuální výstrahy protokoly SAP HANA. V SAP HANA Studio, přejděte na **konzole pro správu: Upozornění: Zobrazit: všechny výstrahy**. Na této kartě se zobrazí všechny výstrahy SAP HANA pro konkrétní hodnoty (Volná fyzická paměť, procesor, atd.), které spadají mimo sadu minimální a maximální mezní hodnoty. Ve výchozím nastavení kontroly se automaticky aktualizují každých 15 minut.

![V SAP HANA Studio přejděte do konzoly pro správu: Upozornění: Zobrazit: všechny výstrahy](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>Procesor

Řešení upozornění aktivuje z důvodu nesprávné prahová hodnota nastavení, je resetovat na výchozí hodnotu nebo přijatelnějšímu prahovou hodnotu.

![Resetovat na výchozí hodnotu nebo přijatelnějšímu prahová hodnota](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Tyto výstrahy mohou označovat potíže s procesoru prostředků:

- Využití procesoru hostitele (upozornění 5)
- Poslední operace úložného bodu (upozornění 28)
- Doba trvání úložný bod (upozornění 54)

Můžete si všimnout vysoké využití procesoru na databázi SAP HANA z jednoho z následujících akcí:

- Upozornění 5 (využití procesoru hostitele) se vyvolá za posledních nebo aktuální využití procesoru
- Využití procesoru zobrazených na obrazovce Přehled

![Zobrazuje využití procesoru v obrazovce Přehled](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Graf zatížení může zobrazovat vysoké využití procesoru nebo vysoké využití v minulosti:

![Graf zatížení může zobrazovat vysoké využití procesoru nebo vysoké využití v minulosti](./media/troubleshooting-monitoring/image4-load-graph.png)

Upozornění aktivuje z důvodu vysoké využití procesoru může být způsobeno několika důvodů, včetně, ale nikoli výhradně: provádění určitých transakce, načítání dat, úlohy, které nereagují dlouho běžící příkazy SQL a výkonu neplatný dotaz (například) s BW on HANA datové krychle).

Odkazovat [SAP, řešení potíží s HANA: Související způsobí, že využití procesoru a řešení](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) lokality podrobné kroky pro řešení potíží.

## <a name="operating-system"></a>Operační systém

Jednou z vašich nejdůležitějších kontroly pro SAP HANA v Linuxu je Ujistěte se, že velké transparentní stránky je zakázáno, přečtěte si téma [SAP Poznámka #2131662 – transparentní velké stránky (THP) na servery SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

- Můžete zkontrolovat, zda jsou povoleny velké transparentní stránky pomocí následujícího příkazu Linux: **cat /sys/kernel/mm/transparent\_hugepage/povoleno**
- Pokud _vždy_ není uzavřen v závorkách, jak je uvedeno níže, znamená to, že jsou povolené transparentní velké stránky: [vždy] madvise nikdy; Pokud _nikdy_ není uzavřen v závorkách, jak je uvedeno níže, znamená to, že velké transparentní Stránky zakázány: vždy madvise [nikdy]

Následující příkaz Linux by měl vrátit hodnotu nothing: **ot. / min - kontrola kvality | grep ulimit.** Pokud se zobrazí _ulimit_ je nainstalovaný, odinstalovat okamžitě.

## <a name="memory"></a>Memory (Paměť)

Podívat se, že množství paměti přidělené v databázi SAP HANA větší než se čekalo. Tyto výstrahy indikují problémy s vysoké využití paměti:

- Využití fyzické paměti hostitele (upozornění 1)
- Využití paměti názvového serveru (Alert 12)
- Celkové využití paměti Store sloupec tabulek (upozornění 40)
- Využití paměti služby (upozornění 43)
- Využití paměti hlavní úložiště tabulek sloupec Store (45 výstrah)
- Soubory s výpisem paměti modulu CLR (upozornění 46)

Odkazovat [SAP, řešení potíží s HANA: Problémy s pamětí](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) lokality podrobné kroky pro řešení potíží.

## <a name="network"></a>Síť

Odkazovat na [SAP Poznámka #2081065 – řešení potíží s SAP HANA sítě](https://launchpad.support.sap.com/#/notes/2081065) a provedení kroků v této poznámky SAP pro řešení potíží síť.

1. Analýza dobu odezvy mezi serverem a klientem.
  A. Spusťte skript SQL [ _HANA\_sítě\_klienti_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analýza komunikace mezi uzly.
  A. Spusťte skript SQL [ _HANA\_sítě\_služby_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Spusťte příkaz Linux **ifconfig** (výstup zobrazuje, pokud se vyskytnou ztráty paketů).
4. Spusťte příkaz Linux **tcpdump**.

Navíc pomocí open source [IPERF](https://iperf.fr/) nástroje (nebo podobnou) k měření aplikace skutečný výkon sítě.

Odkazovat [SAP, řešení potíží s HANA: Výkon sítě a potíže s připojením k](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) lokality podrobné kroky pro řešení potíží.

## <a name="storage"></a>Úložiště

Z pohledu koncového uživatele aplikace (systém jako celek) běží pomalu, přestane reagovat nebo může dokonce zdát přestane reagovat, pokud dojde k problémům s výkonem vstupně-výstupních operací. V **svazky** kartu v sadě Studio SAP HANA, můžete zobrazit připojené svazky a svazky, které jsou používány jednotlivých služeb.

![Na kartě svazky v SAP HANA Studio uvidíte připojených svazků a svazky, které jsou používány každá služba](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Připojené svazky v dolní části obrazovky uvidíte podrobnosti svazků, jako jsou soubory a vstupně-výstupních operací statistiky.

![Připojené svazky v dolní části obrazovky uvidíte podrobnosti svazků, jako jsou soubory a Statistika](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Odkazovat [SAP, řešení potíží s HANA: Vstupně-výstupní operace související s hlavní příčiny a řešení](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) a [SAP, řešení potíží s HANA: Související hlavní příčiny potíží a řešení disku](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) lokality podrobné kroky pro řešení potíží.

## <a name="diagnostic-tools"></a>Diagnostické nástroje

Provést kontrolu stavu SAP HANA pomocí HANA\_konfigurace\_Minichecks. Tento nástroj vrátí potenciálně kritickým technických problémů, které by měl mít bylo vyvoláno již jako upozornění v SAP HANA Studio.

Odkazovat na [SAP Poznámka #1969700 – kolekce příkaz SQL pro SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) a stáhněte si soubor SQL Statements.zip připojené k této poznámce. Store tento soubor ZIP na místní pevný disk.

V SAP HANA Studio na **systémové informace** kartu, klikněte pravým tlačítkem **název** sloupci a vyberte **SQL příkazy pro Import**.

![V SAP HANA Studio, na kartě informace o systému klikněte pravým tlačítkem na sloupec název a vyberte SQL příkazy pro Import](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Vyberte soubor SQL Statements.zip ukládají místně, a naimportuje do složky s odpovídajících příkazů SQL. V tuto chvíli mnoho různých diagnostické kontroly spuštěním pomocí těchto příkazů SQL.

Například pokud chcete vyzkoušet SAP HANA System Replication požadavky na šířku pásma, klikněte pravým tlačítkem **šířky pásma** příkaz za **replikace: Šířka pásma** a vyberte **otevřít** v konzole SQL.

Příkaz SQL se otevře, povolení vstupní parametry (úpravy oddíl), změnit a potom se spustil.

![Příkaz SQL se otevře, povolení vstupní parametry (úpravy oddíl), změnit a potom se spustil](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Dalším příkladem je pravým tlačítkem myši na příkazy v rámci **replikace: Přehled**. Vyberte **Execute** z místní nabídky:

![Dalším příkladem je pravým tlačítkem myši na příkazy v rámci replikace: Přehled. Vyberte provést v místní nabídce](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Výsledkem je informace, které pomáhá při řešení potíží:

![Výsledkem bude informace, které vám pomohou při odstraňování potíží](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Totéž proveďte pro HANA\_konfigurace\_Minichecks a vyhledat všechny _X_ označí ve _C_ sloupec (kritická).

Ukázkový výstup:

**HANA\_konfigurace\_MiniChecks\_Rev102.01 + 1** pro kontroluje obecné SAP HANA.

![HANA\_konfigurace\_MiniChecks\_Rev102.01 + 1 pro obecné kontroly SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_služby\_přehled** přehled toho, jaké jsou služby SAP HANA aktuálně spuštěné.

![HANA\_služby\_přehled přehled toho, jaké jsou služby SAP HANA aktuálně spuštěné](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_služby\_statistiky** pro SAP HANA (procesor, paměť atd.) informace o službě.

![HANA\_služby\_informace o službě statistiky pro SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_konfigurace\_přehled\_Rev110 +** obecné informace o instanci SAP HANA.

![HANA\_konfigurace\_přehled\_Rev110 + obecné informace o instanci SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_konfigurace\_parametry\_Rev70 +** Zkontrolujte parametry SAP HANA.

![HANA\_konfigurace\_parametry\_Rev70 + Zkontrolujte parametry SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Další kroky**

- Přečtěte si [vysokou dostupnost v SUSE pomocí využitím techniky STONITH](ha-setup-with-stonith.md).