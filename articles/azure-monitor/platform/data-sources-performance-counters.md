---
title: Shromažďování a analýza čítačů výkonu ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Čítače výkonu jsou shromažďovány azure monitorem k analýze výkonu na agenty Windows a Linux.  Tento článek popisuje, jak nakonfigurovat kolekci čítačů výkonu pro agenty Windows i Linux, podrobnosti o nich jsou uloženy v pracovním prostoru a jak je analyzovat na webu Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: d1a972a1d89066b961f2dcc28fba830e3a04ebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274759"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Zdroje dat o výkonu Windows a Linuxu ve Službě Azure Monitor
Čítače výkonu v systému Windows a Linuxposkytují přehled o výkonu hardwarových komponent, operačních systémů a aplikací.  Azure Monitor můžete shromažďovat čítače výkonu v častých intervalech pro analýzu téměř v reálném čase (NRT) kromě agregace dat o výkonu pro dlouhodobější analýzy a vykazování.

![Čítače výkonu](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurace čítačů výkonu
Konfigurovat čítače výkonu z [nabídky Data v rozšířeném nastavení](agent-data-sources.md#configuring-data-sources).

Při první konfiguraci čítačů výkonu systému Windows nebo Linux pro nový pracovní prostor máte možnost rychle vytvořit několik běžných čítačů.  Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.  Ujistěte se, že jsou zaškrtnuty všechny čítače, které chcete původně vytvořit, a klepněte na tlačítko **Přidat vybrané čítače výkonu**.

Pro čítače výkonu systému Windows můžete zvolit konkrétní instanci pro každý čítač výkonu. Pro čítače výkonu Linuxu instance každého čítače, které zvolíte, platí pro všechny podřízené čítače nadřazeného čítače. V následující tabulce jsou uvedeny běžné instance, které jsou k dispozici pro čítače výkonu Linuxu i Windows.

| Název instance | Popis |
| --- | --- |
| \_Celkem |Součet všech instancí |
| \* |Všechny instance |
| (/&#124;/var) |Odpovídá instancí s názvem: / nebo /var |

### <a name="windows-performance-counters"></a>Čítače výkonu Windows

![Konfigurace čítačů výkonu systému Windows](media/data-sources-performance-counters/configure-windows.png)

Chcete-li přidat nový čítač výkonu systému Windows, který chcete shromáždit, postupujte podle tohoto postupu.

1. Do textového pole do objektu *(instance)\counter*zadejte název čítače .  Když začnete psát, zobrazí se odpovídající seznam běžných čítačů.  Můžete buď vybrat čítač ze seznamu, nebo zadat jeden z vašich vlastních.  Můžete také vrátit všechny instance pro konkrétní čítač zadáním *objektu\čítače*.  

    Při shromažďování čítačů výkonu serveru SQL Server z pojmenovaných instancí začínají všechny čítače pojmenovaných instancí *mssql$* a následuje název instance.  Chcete-li například shromažďovat čítač Výskyt přístupů mezipaměti protokolu pro všechny databáze `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`z objektu výkonu databáze pro pojmenovanou instanci SQL INST2, zadejte .

2. Kliknutím **+** nebo stisknutím **klávesy Enter** přidejte čítač do seznamu.
3. Když přidáte čítač, použije výchozí hodnotu 10 sekund pro **interval vzorkování**.  Můžete změnit na vyšší hodnotu až 1800 sekund (30 minut), pokud chcete snížit požadavky na úložiště shromážděných dat výkonu.
4. Po přidání čítačů kliknutím na tlačítko **Uložit** v horní části obrazovky uložte konfiguraci.

### <a name="linux-performance-counters"></a>Čítače výkonu Linuxu

![Konfigurace čítačů výkonu Linuxu](media/data-sources-performance-counters/configure-linux.png)

Podle tohoto postupu přidejte nový čítač výkonu Linuxu pro shromažďování.

1. Ve výchozím nastavení jsou všechny změny konfigurace automaticky odesílány všem agentům.  Pro agenty Linuxu je konfigurační soubor odeslán do sběrače dat Fluentd.  Pokud chcete tento soubor upravit ručně na každém agentovi Linuxu, pak odškrtávací *políčko Použít níže na mých počítačích s Linuxem* a postupujte podle níže uvedených pokynů.
2. Do textového pole do objektu *(instance)\counter*zadejte název čítače .  Když začnete psát, zobrazí se odpovídající seznam běžných čítačů.  Můžete buď vybrat čítač ze seznamu, nebo zadat jeden z vašich vlastních.  
3. Kliknutím **+** nebo stisknutím **klávesy Enter** přidejte čítač do seznamu dalších čítačů objektu.
4. Všechny čítače objektu používají stejný **interval vzorkování**.  Výchozí hodnota je 10 sekund.  Pokud chcete snížit požadavky na úložiště shromážděných dat o výkonu, můžete to změnit na vyšší hodnotu až 1800 sekund (30 minut).
5. Po přidání čítačů kliknutím na tlačítko **Uložit** v horní části obrazovky uložte konfiguraci.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurace čítačů výkonu Linuxu v konfiguračním souboru
Namísto konfigurace čítačů výkonu Linuxu pomocí portálu Azure máte možnost upravovat konfigurační soubory na agenta Linuxu.  Metriky výkonu, které mají být shromažďovány, jsou řízeny konfigurací v **souboru /etc/opt/microsoft/omsagent/\<id\>pracovního prostoru /conf/omsagent.conf**.

Každý objekt nebo kategorie metriky výkonu, které mají být shromažďovány, by měly být definovány v konfiguračním souboru jako jeden `<source>` prvek. Syntaxe následuje podle níže uvedeného vzoru.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Parametry v tomto prvku jsou popsány v následující tabulce.

| Parametry | Popis |
|:--|:--|
| název\_objektu | Název objektu pro kolekci. |
| reex instance\_ |  *Regulární výraz* definující instance, které mají být shromažďovány. Hodnota: `.*` určuje všechny instance. Chcete-li shromažďovat metriky \_procesoru pouze `_Total`pro instanci Celkem, můžete zadat . Chcete-li shromažďovat metriky procesů pouze pro instance crond nebo `(crond\|sshd)`sshd, můžete zadat: . |
| regex\_názvu čítače\_ | *Regulární výraz* definující čítače (pro objekt) shromažďovat. Chcete-li shromáždit všechny čítače pro objekt, zadejte: `.*`. Chcete-li shromažďovat pouze čítače zaměnit prostor pro objekt paměti, můžete například zadat:`.+Swap.+` |
| interval | Frekvence, s jakou jsou shromažďovány čítače objektu. |


V následující tabulce jsou uvedeny objekty a čítače, které můžete zadat v konfiguračním souboru.  Pro určité aplikace jsou k dispozici další čítače, jak je popsáno v [čítačích výkonu Collect pro linuxové aplikace v Azure Monitoru](data-sources-linux-applications.md).

| Název objektu | Název čítače |
|:--|:--|
| Logický disk | % volných inodů |
| Logický disk | % volného místa |
| Logický disk | % použitých inodů |
| Logický disk | % využitého místa |
| Logický disk | Čtení bajtů disku/s |
| Logický disk | Čtení disku/s |
| Logický disk | Přenosy disků/s |
| Logický disk | Bajty zápisu disku/s |
| Logický disk | Zápisy na disk/s |
| Logický disk | Megabajty zdarma |
| Logický disk | Bajty logického disku/s |
| Memory (Paměť) | % dostupné paměti |
| Memory (Paměť) | % dostupného odkládacího prostoru |
| Memory (Paměť) | % použité paměti |
| Memory (Paměť) | % použitého odkládacího prostoru |
| Memory (Paměť) | Dostupná paměť MBytes |
| Memory (Paměť) | K dispozici mbytes swap |
| Memory (Paměť) | Čtení stránky/s |
| Memory (Paměť) | Zápisy stránky/s |
| Memory (Paměť) | Stránky/s |
| Memory (Paměť) | Použité mbytes swap ové místo |
| Memory (Paměť) | Mbytes použité paměti |
| Network (Síť) | Celkový počet přenesených bajtů |
| Network (Síť) | Celkový počet přijatých bajtů |
| Network (Síť) | Celkový počet bajtů |
| Network (Síť) | Celkový počet přenesených paketů |
| Network (Síť) | Celkový počet přijatých paketů |
| Network (Síť) | Celkový počet chyb Rx |
| Network (Síť) | Celkový počet chyb Tx |
| Network (Síť) | Celkový počet kolizí |
| Fyzický disk | Vně disku sec/čtení |
| Fyzický disk | Vznětový disk ový s/přenos |
| Fyzický disk | Vg. Disk sec/Zápis |
| Fyzický disk | Bajty fyzického disku/s |
| Proces | Pct privilegovaný čas |
| Proces | Pct uživatelský čas |
| Proces | Kby využité paměti |
| Proces | Virtuální sdílená paměť |
| Procesor | % času DPC |
| Procesor | % doby nečinnosti |
| Procesor | % času přerušení |
| Procesor | % čekací doby vi |
| Procesor | % Pěkný čas |
| Procesor | % privilegovaného času |
| Procesor | Procesorový čas v % |
| Procesor | % času uživatele |
| Systém | Volná fyzická paměť |
| Systém | Volné místo v stránkovacích souborech |
| Systém | Virtuální paměť zdarma |
| Systém | Procesy |
| Systém | Velikost uložená v stránkovacích souborech |
| Systém | Uptime |
| Systém | Uživatelé |


Následuje výchozí konfigurace pro metriky výkonu.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Shromažďování dat
Azure Monitor shromažďuje všechny zadané čítače výkonu v jejich zadaný interval vzorkování na všechny agenty, kteří mají tento čítač nainstalován.  Data nejsou agregována a nezpracovaná data jsou k dispozici ve všech zobrazeních dotazu protokolu po dobu zadanou vaším předplatným.

## <a name="performance-record-properties"></a>Vlastnosti záznamu výkonu
Záznamy o výkonu mají typ **Perf** a mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Počítač, ze kterého byla událost shromážděna. |
| Countername |Název čítače výkonu |
| Proticesta |Úplná cesta čítače ve \\ \\ \<formuláři Čítač Objekt>\\počítač(instance).\\ |
| Hodnota čítače |Číselná hodnota čítače. |
| Název_instance |Název instance události.  Prázdné, pokud žádná instance. |
| ObjectName |Název objektu výkonu |
| SourceSystem |Typ agenta, od kterého byla data shromážděna. <br><br>OpsManager – agent Windows, buď přímé připojení nebo SCOM <br> Linux – Všichni linuxoví agenti  <br> AzureStorage – diagnostika Azure |
| TimeGenerated |Datum a čas, kdy byla data vzorkována. |

## <a name="sizing-estimates"></a>Odhady velikosti
 Hrubý odhad pro shromažďování konkrétníčí čítač v intervalech 10 sekund je asi 1 MB za den na instanci.  Požadavky na úložiště konkrétního čítače můžete odhadnout pomocí následujícího vzorce.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Protokolovat dotazy pomocí záznamů výkonu
Následující tabulka obsahuje různé příklady dotazů protokolu, které načítají záznamy výkonu.

| Dotaz | Popis |
|:--- |:--- |
| Výkon |Všechna data o výkonu |
| Perf &#124; kde počítač == "MyComputer" |Všechna data o výkonu z konkrétního počítače |
| Perf &#124; kde CounterName == "Aktuální délka fronty disku" |Všechna data o výkonu pro konkrétní čítač |
| Perf &#124; kde ObjectName == "Procesor" a CounterName == "% času procesoru" a InstanceName == "_Total" &#124; shrnout AVGCPU = avg(CounterValue) podle počítače |Průměrné využití procesoru ve všech počítačích |
| Perf &#124; kde CounterName == "% času procesoru" &#124; sumarizovat AggregatedValue = max(CounterValue) podle počítače |Maximální využití procesoru ve všech počítačích |
| Perf &#124; kde ObjectName == "LogicalDisk" a CounterName == "Aktuální délka fronty disku" a počítač == "MyComputerName" &#124; sumarizovat AggregatedValue = avg(CounterValue) podle InstanceName |Průměrná délka aktuální fronty disku ve všech instancích daného počítače |
| Perf &#124; kde CounterName == "Disk transfers/sec" &#124; sumarizovat AggregatedValue = percentil(CounterValue, 95) podle počítače |95. percentil diskových přenosů/s ve všech počítačích |
| Perf &#124; kde CounterName == "% času procesoru" a InstanceName == "_Total" &#124; shrnout AggregatedValue = avg(CounterValue) podle bin(TimeGenerated, 1h), Počítač |Hodinový průměr využití procesoru ve všech počítačích |
| Perf &#124; kde počítač == "MyComputer" a CounterName startswith_cs "%" a InstanceName == "_Total" &#124; shrnout AggregatedValue = percentil(CounterValue, 70) podle bin(TimeGenerated, 1h), CounterName | Hodinový 70 percentil každého % % čítače pro konkrétní počítač |
| Perf &#124; kde CounterName == "% času procesoru" a InstanceName == "_Total" a Počítač == "MyComputer" &#124; shrnout ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentil(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) podle bin(TimeGenerated, 1h), Počítač |Hodinové průměrné, minimální, maximální a 75percentilové využití procesoru pro konkrétní počítač |
| Perf &#124; kde ObjectName == "MSSQL$INST2:Databases" a InstanceName == "master" | Všechna data výkonu z objektu výkonu databáze pro hlavní databázi z pojmenované instance serveru SQL Server INST2.  




## <a name="next-steps"></a>Další kroky
* [Shromažďujte čítače výkonu z linuxových aplikací](data-sources-linux-applications.md) včetně MySQL a Apache HTTP Server.
* Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení.  
* Exportujte shromážděná data do [Power BI](powerbi.md) pro další vizualizace a analýzy.
