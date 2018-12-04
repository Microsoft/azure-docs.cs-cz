---
title: Shromažďujte a analyzujte čítače výkonu ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Čítače výkonu jsou shromážděná službou Log Analytics k analýze výkonu agentů Windows a Linux.  Tento článek popisuje, jak konfigurovat shromažďování čítačů výkonu pro obě Windows a agenti systému Linux, podrobnosti o jejich jsou uložené v pracovním prostoru a analyzovat je na webu Azure Portal.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 1069916efc1932b0a1a3139d015f448d4f8153a4
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843117"
---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Windows a Linuxem zdroje dat výkonu do Log Analytics
Čítače výkonu ve Windows a Linuxu poskytují přehled o výkonu hardwarové součásti, operačních systémů a aplikací.  Log Analytics může shromažďovat čítače výkonu v pravidelných intervalech pro analýzu v téměř reálném čase (NRT) kromě agregaci údajů o výkonu pro delší dlouhodobější analýzu a generování sestav.

![Čítače výkonu](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurace čítačů výkonu
Nakonfigurujte čítače výkonu z [dat nabídky v nastavení analýzy protokolů](agent-data-sources.md#configuring-data-sources).

Při první konfiguraci Windows nebo Linux výkon čítače pro nový pracovní prostor Log Analytics, budete mít možnost rychle vytvořit několik běžných čítačů.  Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.  Ujistěte se, že jsou kontrolovány všechny čítače mají zpočátku vytvořit a potom klikněte na **přidat vybrané čítače výkonu**.

Pro čítače výkonu Windows můžete zvolit konkrétní instance pro každý čítač výkonu. Pro čítače výkonu systému Linux instance jednotlivých čítačů, kterou zvolíte se vztahuje na všechny podřízené čítače nadřazené čítače. Následující tabulka uvádí běžné instance k dispozici pro čítače výkonu systému Linux a Windows.

| Název instance | Popis |
| --- | --- |
| \_Celkem |Celkový počet všech instancí |
| \* |Všechny instance |
| (/&#124;/var) |Odpovídá instance s názvem: / nebo /var |

### <a name="windows-performance-counters"></a>Čítače výkonu Windows

![Nakonfigurujte čítače výkonu Windows](media/data-sources-performance-counters/configure-windows.png)

Pomocí následujícího postupu přidejte nový čítač výkonu Windows pro shromažďování.

1. Zadejte název čítače v textovém poli ve formátu *objektu (instance) \counter*.  Když začnete psát, zobrazí se odpovídající seznam běžných čítačů.  Čítače můžete vybrat ze seznamu nebo zadejte svůj vlastní.  Můžete se taky vrátit na všechny instance konkrétní čítač zadáním *object\counter*.  

    Při shromažďování čítače výkonu pro SQL Server z pojmenované instance, všechny pojmenované instance čítače začínají u *MSSQL$* a za nímž následuje název instance.  Například pokud chcete shromáždit čítače poměr přístupů do mezipaměti protokolu pro všechny databáze z objektu výkonu databáze SQL s názvem instance INST2, zadejte `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Klikněte na tlačítko **+** nebo stiskněte klávesu **Enter** přidat čítače do seznamu.
3. Když přidáte čítače, použije výchozí hodnotu 10 sekund, jeho **Interval vzorkování**.  Můžete určit to k vyšší hodnotě až 1800 sekund (30 minut) Pokud budete chtít snížit požadavky na úložiště dat shromážděných výkonu.
4. Po dokončení přidávání čítačů, klikněte na tlačítko **Uložit** tlačítko v horní části obrazovky, čímž konfiguraci uložíte.

### <a name="linux-performance-counters"></a>Čítače výkonu Linuxu

![Konfiguraci Linuxových čítačů výkonu](media/data-sources-performance-counters/configure-linux.png)

Pomocí následujícího postupu přidejte nový čítač výkonu systému Linux ke shromažďování.

1. Standardně jsou všechny změny konfigurace automaticky nahrány do všech agentů.  U agentů systému Linux je konfigurační soubor odeslané do kolektoru dat Fluentd.  Pokud chcete upravit tento soubor ručně na každý agent systému Linux, poté zrušte zaškrtnutí políčka *použít uvedenou konfiguraci u mých Linuxových počítačů* a postupujte podle pokynů níže.
2. Zadejte název čítače v textovém poli ve formátu *objektu (instance) \counter*.  Když začnete psát, zobrazí se odpovídající seznam běžných čítačů.  Čítače můžete vybrat ze seznamu nebo zadejte svůj vlastní.  
3. Klikněte na tlačítko **+** nebo stiskněte klávesu **Enter** přidat čítač seznam dalších čítačů pro objekt.
4. Všechny čítače pro objekt použít stejné **Interval vzorkování**.  Výchozí hodnota je 10 sekund.  Můžete změnit na vyšší hodnotu až 1 800 sekund (30 minut) Pokud budete chtít snížit požadavky na úložiště dat shromážděných výkonu.
5. Po dokončení přidávání čítačů, klikněte na tlačítko **Uložit** tlačítko v horní části obrazovky, čímž konfiguraci uložíte.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfiguraci čítačů výkonu systému Linux v konfiguračním souboru
Namísto konfigurace Linuxovými čítači výkonu pomocí webu Azure portal, máte možnost upravit konfigurační soubory do agenta pro Linux.  Metriky výkonu pro shromažďování se řídí konfigurací v **/etc/opt/microsoft/omsagent/\<id pracovního prostoru\>/conf/omsagent.conf**.

Každý objekt nebo kategorii, metriky výkonu ke shromažďování musí být definován v konfiguračním souboru jako jediný `<source>` elementu. Syntaxe má následující formát, níže.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Parametry v tomto elementu jsou popsány v následující tabulce.

| Parametry | Popis |
|:--|:--|
| objekt\_název | Název objektu pro kolekci. |
| instance\_regulární výraz |  A *regulárního výrazu* definování které instance, které chcete shromažďovat. Hodnota: `.*` Určuje všechny instance. Shromažďovat metriky procesoru pro pouze \_celkový počet instancí, můžete zadat `_Total`. Chcete-li shromažďovat metriky procesů pro pouze crond nebo sshd instancí, můžete zadat: `(crond\|sshd)`. |
| Čítač\_název\_regulární výraz | A *regulárního výrazu* definující, které čítačů (pro objekt), které pokud chcete shromažďovat. Chcete-li shromažďovat všechny čítače pro objekt, zadejte: `.*`. Chcete-li shromažďovat pouze čítače místa odkládacího souboru paměti objektu, například můžete zadat: `.+Swap.+` |
| interval | Frekvence, s jakou se shromažďují čítače objektu. |


V následující tabulce jsou uvedeny objekty a čítače, které můžete zadat v konfiguračním souboru.  K dispozici další čítače pro určité aplikace, jak je popsáno v [shromáždit čítače výkonu pro Linuxové aplikace ve službě Log Analytics](data-sources-linux-applications.md).

| Název objektu | Název počítadla |
|:--|:--|
| Logický Disk | % Volných uzlů Inode |
| Logický Disk | % Volného místa |
| Logický Disk | % Použitých uzlů Inode |
| Logický Disk | % Využitého místa |
| Logický Disk | Bajty čtení z disku/s |
| Logický Disk | Čtení disku/s |
| Logický Disk | Přenosy disku/s |
| Logický Disk | Bajty zapisování na disk/s |
| Logický Disk | Zápis disku/s |
| Logický Disk | Volné megabajty |
| Logický Disk | Bajtů logického disku/s |
| Memory (Paměť) | Dostupná paměť v % |
| Memory (Paměť) | % Dostupného odkládacího prostoru |
| Memory (Paměť) | % Využité paměti |
| Memory (Paměť) | Využitý prostor záměny v % |
| Memory (Paměť) | Dostupná paměť v MB |
| Memory (Paměť) | Dostupný prostor záměny v MB |
| Memory (Paměť) | Čtení stránek/s |
| Memory (Paměť) | Zápisy stránek/s |
| Memory (Paměť) | Stránky/s |
| Memory (Paměť) | Použitý odkládací prostor v MB místa |
| Memory (Paměť) | Použitá paměť v MB |
| Síť | Celkový počet bajtů přenesených |
| Síť | Celkový počet přijatých bajtů |
| Síť | Bajty celkem |
| Síť | Celkový počet paketů odesílané informace |
| Síť | Celkový počet přijatých paketů |
| Síť | Chyby celkem příjmu |
| Síť | Celkový počet odesílání chyb |
| Síť | Celkový počet kolizí |
| Fyzický Disk | Střední Doba disku/čtení |
| Fyzický Disk | Střední Doba disku/přenos |
| Fyzický Disk | Střední Doby disku/zápis |
| Fyzický Disk | Bajtů fyzického disku/s |
| Proces | Procento privilegovaného času |
| Proces | Procento uživatelského času |
| Proces | Využité paměti kB |
| Proces | Sdílené virtuální paměti |
| Procesor | Čas DPC v % |
| Procesor | Čas nečinnosti v % |
| Procesor | Čas přerušení v % |
| Procesor | Doba čekání % vstupně-výstupních operací |
| Procesor | Dobrý čas v % |
| Procesor | % Privilegovaného času |
| Procesor | % Času procesoru |
| Procesor | Uživatelský čas v % |
| Systémový | Volná fyzická paměť |
| Systémový | Volné místo ve stránkovacích souborech |
| Systémový | Volná virtuální paměť |
| Systémový | Procesy |
| Systémový | Velikost uložená ve stránkovacích souborech |
| Systémový | Doba provozu |
| Systémový | Uživatelé |


Toto je výchozí konfigurace pro metriky výkonu.

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
Log Analytics shromažďuje všechny čítače výkonu zadaný v jejich zadané vzorkovací interval na všechny agenty, kteří mají nainstalované čítač.  Data nejsou agregovány a nezpracovaných dat je k dispozici ve všech zobrazeních log search po dobu zadanou vaším předplatným.

## <a name="performance-record-properties"></a>Vlastnosti výkonnostní záznamu
Výkon záznamy mají typ **výkonu** a mít vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Počítač, který událost byla shromážděna z. |
| Hodnota counterName |Název čítače výkonu |
| Cesta_k_čítači |Úplná cesta k čítači ve formě \\ \\ \<počítače >\\objekt(instance)\\čítače. |
| CounterValue |Číselná hodnota čítače. |
| InstanceName |Název instance události.  Prázdný, pokud žádná instance. |
| Název objektu |Název objektu výkonu |
| SourceSystem |Typ agenta data se shromažďovala ze. <br><br>OpsManager – Windows agent, buď přímé připojení nebo SCOM <br> Linux – všichni agenti systému Linux  <br> AzureStorage – diagnostiky Azure |
| TimeGenerated |Datum a čas, který je Vzorkovaná data. |

## <a name="sizing-estimates"></a>Odhady velikosti
 Je hrubý odhad pro kolekci jednotlivých čítačů v intervalech po 10 sekundách je přibližně 1 MB za den na jednu instanci.  Chcete-li odhadnout požadavky na úložiště jednotlivých čítačů s následující vzorec.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Hledání v protokolu s záznamů o výkonu
Následující tabulka obsahuje příklady různých prohledávání protokolů, které načítají záznamů o výkonu.

| Dotaz | Popis |
|:--- |:--- |
| Perf |Všechny údaje o výkonu |
| Perf &#124; kde počítač == "Počítač" |Všechny údaje o výkonu z určitého počítače |
| Perf &#124; tam, kde CounterName == "Aktuální délka fronty disku" |Všechny údaje o výkonu pro konkrétní čítač |
| Perf &#124; kde ObjectName == "Procesor pro" a hodnota CounterName == "% Processor Time" a InstanceName == "_Total" &#124; shrnout AVGCPU = avg(Average) podle počítače |Průměrné využití procesoru ve všech počítačích |
| Perf &#124; tam, kde CounterName == "% Processor Time" &#124; summarize AggregatedValue = max(Max) podle počítače |Maximální využití CPU ve všech počítačích |
| Perf &#124; kde ObjectName == "LogicalDisk" a hodnota CounterName == "Aktuální délka fronty disku" a počítač == "MyComputerName" &#124; summarize AggregatedValue = avg(Average) podle InstanceName |Průměrná napříč všemi instancemi daného počítače. aktuální délka fronty disku |
| Perf &#124; tam, kde CounterName == "DiskTransfers za sekundu" &#124; summarize AggregatedValue = percentil (průměr, 95) podle počítače |95. percentil z přenosy disku/s pro všechny počítače |
| Perf &#124; tam, kde CounterName == "% Processor Time" a InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hodina), počítač |Hodinové průměrné zatížení CPU ve všech počítačích |
| Perf &#124; kde počítač == "Počítač" a hodnota CounterName startswith_cs "%" a InstanceName == "_Total" &#124; summarize AggregatedValue = podle bin (TimeGenerated, 1 hodina), hodnota CounterName. percentilu (CounterValue, 70) | Hodinové 70 percentilu každý % procenta čítače pro určitý počítač |
| Perf &#124; tam, kde CounterName == "% Processor Time" a InstanceName == "_Total" a počítač == "Počítač" &#124; shrnout ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] percentil (CounterValue, 75) = ["max(CounterValue)"] = max(CounterValue) podle bin (TimeGenerated, 1 hodina), počítač |Hodinové průměrné, minimální, maximální a 75 percentilu využití procesoru pro určitý počítač |
| Perf &#124; kde ObjectName == "MSSQL$ INST2: databáze" a InstanceName == "hlavní" | Všechny údaje o výkonu z objektu výkonu databáze pro hlavní databázi z pojmenované instanci systému SQL Server INST2.  




## <a name="next-steps"></a>Další postup
* [Získat čítače výkonu z aplikace pro Linux](data-sources-linux-applications.md) včetně MySQL serveru Apache HTTP Server.
* Další informace o [prohledávání protokolů](../../azure-monitor/log-query/log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.  
* Export shromážděná data [Power BI](../../log-analytics/log-analytics-powerbi.md) další vizualizace a analýzy.
