---
title: Monitorování a řešení potíží ze strany HANA na SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Monitorování a řešení potíží ze strany HANA na SAP HANA na Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 047ea4d07f2b497ac8c7deb90c056d63976094f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617080"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Sledování a řešení potíží na straně HANA

Chcete-li efektivně analyzovat problémy související s SAP HANA v Azure (velké instance), je užitečné zúžit hlavní příčinu problému. SPOLEČNOST SAP zveřejnila velké množství dokumentace, která vám pomůže.

Příslušné časté otázky týkající se výkonu SAP HANA naleznete v následujících poznámkách SAP:

- [SAP Note #2222200 – nejčastější dotazy: SÍŤ SAP HANA](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Poznámka #2100040 – nejčastější dotazy: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – nejčastější dotazy: Paměť SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – nejčastější dotazy: Optimalizace výkonu SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Note #199930 – faq: SAP HANA I/O Analýza](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – nejčastější dotazy: Restartování a selhání služby SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Výstrahy SAP HANA

Jako první krok zkontrolujte aktuální protokoly výstrah SAP HANA. V aplikaci SAP HANA Studio přejděte na **Konzolu pro správu: Výstrahy: Zobrazit: všechna upozornění**. Tato karta zobrazí všechny výstrahy SAP HANA pro konkrétní hodnoty (volná fyzická paměť, využití procesoru atd.), které spadají mimo nastavené minimální a maximální prahové hodnoty. Ve výchozím nastavení jsou kontroly automaticky aktualizovány každých 15 minut.

![V SAP HANA Studio přejděte na Konzolu pro správu: Upozornění: Zobrazit: všechna upozornění](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>Procesor

Pro výstrahu aktivovanou z důvodu nesprávného nastavení prahové hodnoty je řešením obnovení výchozí hodnoty nebo přiměřenější prahové hodnoty.

![Obnovení výchozí hodnoty nebo přiměřenější prahové hodnoty](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Následující výstrahy mohou znamenat problémy s prostředky procesoru:

- Využití procesoru hostitele (výstraha 5)
- Poslední operace uloženého bodu (výstraha 28)
- Doba uložení bodu (výstraha 54)

Můžete si všimnout vysoké spotřeby procesoru v databázi SAP HANA z jedné z následujících možností:

- Výstraha 5 (využití hostitelského procesoru) je aktivována pro aktuální nebo minulé využití procesoru
- Zobrazené využití procesoru na obrazovce přehledu

![Zobrazené využití procesoru na obrazovce přehledu](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Graf zatížení může v minulosti vykazovat vysokou spotřebu procesoru nebo vysokou spotřebu:

![Graf zatížení může vykazovat vysokou spotřebu procesoru nebo vysokou spotřebu v minulosti](./media/troubleshooting-monitoring/image4-load-graph.png)

Výstraha aktivovaná z důvodu vysokého využití procesoru může být způsobena několika důvody, mimo jiné: provádění určitých transakcí, načítání dat, úlohy, které nereagují, dlouho běžící příkazy SQL a špatný výkon dotazu (například s BW na kostek HANA).

Podrobné kroky řešení potíží naleznete na webu [SAP HANA Troubleshooting: CPU Related Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) site.

## <a name="operating-system"></a>Operační systém

Jedním z nejdůležitějších kontrol pro SAP HANA na Linuxu je ujistit se, že transparentní obrovské stránky jsou zakázány, viz [SAP Note #2131662 - Transparent Huge Pages (THP) na sap hana serverech](https://launchpad.support.sap.com/#/notes/2131662).

- Můžete zkontrolovat, zda jsou transparentní obrovské stránky povoleny pomocí následujícího příkazu Linux: **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- Pokud je _vždy_ uzavřena v závorkách, jak je uvedeno níže, znamená to, že transparentní obrovské stránky jsou povoleny: [vždy] madvise nikdy; pokud _nikdy_ není uzavřen v závorkách, jak je uvedeno níže, to znamená, že Transparentní Obrovské stránky jsou zakázány: vždy madvise [nikdy]

Následující příkaz Linuxu by neměl vracet nic: **rpm -qa | grep ulimit.** Pokud se zobrazí _ulimit_ je nainstalován, okamžitě jej odinstalovat.

## <a name="memory"></a>Memory (Paměť)

Můžete pozorovat, že velikost paměti přidělené databáze SAP HANA je vyšší, než bylo očekáváno. Následující výstrahy označují problémy s vysokým využitím paměti:

- Využití fyzické paměti hostitele (výstraha 1)
- Využití paměti názvového serveru (Výstraha 12)
- Celkové využití paměti tabulek úložiště sloupců (výstraha 40)
- Využití paměti služeb (výstraha 43)
- Využití paměti hlavního úložiště tabulek úložiště sloupců (výstraha 45)
- Soubory výpisu runtime (Výstraha 46)

Podrobné kroky řešení potíží naleznete na webu [SAP HANA Troubleshooting: Memory Problems.](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false)

## <a name="network"></a>Network (Síť)

Podívejte se na [SAP Note #2081065 – Řešení potíží SAP HANA Network](https://launchpad.support.sap.com/#/notes/2081065) a proveďte kroky řešení potíží se sítí v této poznámce SAP.

1. Analýza doby odezvy mezi serverem a klientem.
  A. Spusťte skript SQL [_HANA\_\_Network Clients_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analyzujte komunikaci internodů.
  A. Spusťte skript SQL [_HANA\_\_Network Services_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Spusťte linuxový příkaz **ifconfig** (výstup ukazuje, zda dochází ke ztrátám paketů).
4. Spusťte linuxový příkaz **tcpdump**.

Použijte také nástroj [IPERF](https://iperf.fr/) s otevřeným zdrojovým kódem (nebo podobný) k měření skutečného výkonu aplikační sítě.

Podrobné kroky řešení potíží naleznete na webu [SAP HANA Troubleshooting: Networking Performance and Connectivity Problems](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) .

## <a name="storage"></a>Úložiště

Z hlediska koncového uživatele aplikace (nebo systém jako celek) běží pomalu, nereaguje, nebo se dokonce může zdát, že přestane reagovat, pokud se jedná o problémy s výkonem vstupně-va. Na kartě **Svazky** v aplikaci SAP HANA Studio vidíte připojené svazky a jaké svazky používají jednotlivé služby.

![Na kartě Svazky v aplikaci SAP HANA Studio vidíte připojené svazky a jaké svazky používají jednotlivé služby.](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Připojené svazky v dolní části obrazovky můžete vidět podrobnosti o svazcích, jako jsou soubory a vstupně-v statistiky.

![Připojené svazky v dolní části obrazovky můžete vidět podrobnosti o svazcích, jako jsou soubory a vstupně-v., statistiky](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Podrobné kroky řešení potíží naleznete na webu [SAP HANA Troubleshooting: I/O Related Root Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) a SAP [HANA Troubleshooting: Disk Related Root Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) site.

## <a name="diagnostic-tools"></a>Diagnostické nástroje

Proveďte kontrolu stavu SAP HANA\_prostřednictvím minikontrol konfigurace HANA.\_ Tento nástroj vrátí potenciálně kritické technické problémy, které by již byly vyvolány jako výstrahy v SAP HANA Studio.

Podívejte se na [SAP Note #1969700 – kolekce příkazů SQL pro SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) a stáhněte soubor SQL Statements.zip připojený k této poznámce. Uložte tento soubor ZIP na místní pevný disk.

V aplikaci SAP HANA Studio klikněte na kartě **Systémové informace** pravým tlačítkem myši na sloupec **Název** a vyberte import **ovat příkazy SQL**.

![V aplikaci SAP HANA Studio klikněte na kartě Systémové informace pravým tlačítkem myši do sloupce Název a vyberte Import příkazů SQL](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Vyberte soubor SQL Statements.zip uložený místně a bude importována složka s odpovídajícími příkazy SQL. V tomto okamžiku mnoho různých diagnostických kontrol lze spustit s těmito příkazy SQL.

Chcete-li například otestovat požadavky na šířku pásma služby SAP HANA System Replication, klepněte pravým tlačítkem myši na příkaz **Šířka pásma** v části **Replikace: Šířka pásma** a vyberte **příkaz Otevřít** v konzole SQL Console.

Otevře se úplný příkaz SQL, který umožňuje změnu a provedení vstupních parametrů (sekce úprav).

![Otevře se příkaz COMPLETE SQL, který umožňuje změnu vstupních parametrů (část modifikace) a následné spuštění](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Dalším příkladem je kliknutí pravým tlačítkem myši na příkazy v části **Replikace: Přehled**. Z kontextové nabídky vyberte **Spustit:**

![Dalším příkladem je kliknutí pravým tlačítkem myši na příkazy v části Replikace: Přehled. Vybrat spustit z kontextové nabídky](./media/troubleshooting-monitoring/image9-import-statements-c.png)

To má za následek informace, které pomáhají při řešení potíží:

![To bude mít za následek informace, které pomohou při řešení potíží](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Proveďte totéž\_pro\_minikontroly konfigurace HANA a zkontrolujte všechny značky _X_ ve sloupci _C_ (Kritický).

Ukázkové výstupy:

**Hana\_\_Konfigurace\_MiniChecks Rev102.01+1** pro obecné kontroly SAP HANA.

![Hana\_\_Konfigurace\_MiniChecks Rev102.01+1 pro obecné kontroly SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_\_Služby Přehled** přehled u toho, co SAP HANA služby jsou aktuálně spuštěny.

![Hana\_\_Services Přehled přehledu aktuálně spuštěných služeb SAP HANA](./media/troubleshooting-monitoring/image12-services-overview.png)

**Statistika\_\_služeb HANA** pro informace o službě SAP HANA (procesor, paměť atd.).

![Statistika\_\_služeb HANA pro informace o službě SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

**Hana\_\_Configuration\_Overview Rev110+** pro obecné informace o instanci SAP HANA.

![Hana\_\_\_Přehled konfigurace Rev110+ pro obecné informace o instanci SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**Hana\_\_Konfigurační parametry\_Rev70+** pro kontrolu parametrů SAP HANA.

![Hana\_\_Konfigurační parametry\_Rev70+ pro kontrolu parametrů SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Další kroky**

- Odkazovat [vysoká dostupnost nastavit v SUSE pomocí STONITH](ha-setup-with-stonith.md).