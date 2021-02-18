---
title: Shromáždění zdrojů dat výkonu pro Windows a Linux pomocí agenta Log Analytics v Azure Monitor
description: Čítače výkonu jsou shromažďovány nástrojem Azure Monitor k analýze výkonu v agentech systému Windows a Linux.  Tento článek popisuje, jak nakonfigurovat shromažďování čítačů výkonu pro agenty Windows i Linux, podrobnosti o nich jsou uložené v pracovním prostoru a jak je analyzovat v Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: f885945dfb6910df919038106487db912d87caee
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610306"
---
# <a name="collect-windows-and-linux-performance-data-sources-with-log-analytics-agent"></a>Shromažďování zdrojů dat výkonu pro Windows a Linux pomocí agenta Log Analytics
Čítače výkonu ve Windows a Linux poskytují přehled o výkonu hardwarových komponent, operačních systémů a aplikací.  Azure Monitor může shromažďovat čítače výkonu od agentů Log Analytics v častých intervalech pro analýzu téměř v reálném čase (NRT) společně s agregací údajů o výkonu pro dlouhodobé analýzy a generování sestav.

> [!IMPORTANT]
> Tento článek popisuje shromažďování údajů o výkonu pomocí [agenta Log Analytics](../platform/log-analytics-agent.md) , který je jedním z agentů používaných Azure monitor. Jiní agenti shromažďují různá data a nakonfigurují se jinak. Seznam dostupných agentů a data, která mohou shromažďovat, najdete v tématu [Přehled agentů Azure monitor](../agents/agents-overview.md) .

![Čítače výkonu](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurace čítačů výkonu
Nakonfigurujte čítače výkonu z [nabídky data v části Upřesnit nastavení](../agents/agent-data-sources.md#configuring-data-sources) pro pracovní prostor Log Analytics.

Při první konfiguraci čítačů výkonu systému Windows nebo Linux pro nový pracovní prostor budete mít možnost rychle vytvořit několik běžných čítačů.  Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.  Ujistěte se, že jsou zaškrtnuté všechny čítače, které chcete zpočátku vytvořit, a pak klikněte na **Přidat vybrané čítače výkonu**.

V případě čítačů výkonu systému Windows můžete zvolit konkrétní instanci pro každý čítač výkonu. Pro čítače výkonu Linux se instance každého čítače, kterou zvolíte, vztahuje na všechny podřízené čítače nadřazeného čítače. V následující tabulce jsou uvedeny běžné instance dostupné pro čítače výkonu pro systémy Linux a Windows.

| Název instance | Description |
| --- | --- |
| \_Celkem |Celkem všech instancí |
| \* |Všechny instance |
| (/&#124;/var) |Odpovídá instancím s názvem:/nebo/var |

### <a name="windows-performance-counters"></a>Čítače výkonu Windows

![Konfigurace čítačů výkonu systému Windows](media/data-sources-performance-counters/configure-windows.png)

Pomocí tohoto postupu můžete přidat nový čítač výkonu systému Windows, který se má shromáždit. Upozorňujeme, že čítače výkonu v2 pro Windows nejsou podporované.

1. Do textového pole ve formátu *objekt (instance) \counter* zadejte název čítače.  Když začnete psát, zobrazí se seznam s vyhovujícími společnými čítači.  Můžete buď vybrat čítač ze seznamu, nebo zadat jednu z nich.  Můžete také vrátit všechny instance pro konkrétní čítač zadáním *object\counter*.  

    Když shromažďujete čítače výkonu SQL Server z pojmenovaných instancí, všechny pojmenované čítače instancí začínají na *MSSQL $* a za názvem instance.  Například pro shromáždění čítače poměru přístupů do mezipaměti protokolu pro všechny databáze z objektu výkonu databáze pro pojmenovanou instanci SQL INST2 zadejte `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio` .

2. Chcete **+** -li přidat čítač do seznamu, klikněte nebo stiskněte klávesu **ENTER** .
3. Když přidáte čítač, použije se výchozí hodnota 10 sekund pro svůj **interval vzorkování**.  Pokud chcete snížit požadavky na úložiště shromážděných dat o výkonu, můžete to změnit na vyšší hodnotu až na 1800 sekund (30 minut).
4. Až budete s přidáváním čítačů hotovi, kliknutím na tlačítko **Uložit** v horní části obrazovky uložte konfiguraci.

### <a name="linux-performance-counters"></a>Čítače výkonu pro Linux

![Konfigurace čítačů výkonu systému Linux](media/data-sources-performance-counters/configure-linux-1.png)

Pomocí tohoto postupu můžete přidat nový čítač výkonu pro Linux, který se má shromáždit.

1. Do textového pole ve formátu *objekt (instance) \counter* zadejte název čítače.  Když začnete psát, zobrazí se seznam s vyhovujícími společnými čítači.  Můžete buď vybrat čítač ze seznamu, nebo zadat jednu z nich.  
1. Klikněte **+** nebo stiskněte klávesu **ENTER** , chcete-li přidat čítač do seznamu dalších čítačů pro daný objekt.
1. Všechny čítače pro objekt používají stejný **interval vzorkování**.  Výchozí hodnota je 10 sekund.  Pokud chcete snížit požadavky na úložiště shromážděných dat výkonu, změňte tuto hodnotu na vyšší hodnotu až na 1800 sekund (30 minut).
1. Až budete s přidáváním čítačů hotovi, kliknutím na tlačítko **Uložit** v horní části obrazovky uložte konfiguraci.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurace čítačů výkonu systému Linux v konfiguračním souboru
Místo konfigurace čítačů výkonu systému Linux pomocí Azure Portal máte možnost upravovat konfigurační soubory v agentovi systému Linux.  Metriky výkonu ke shromáždění se řídí konfigurací v **/etc/opt/Microsoft/omsagent/ \<workspace id\> /conf/omsagent.conf**.

Každý objekt nebo kategorie metriky výkonu ke shromáždění by měly být definovány v konfiguračním souboru jako jeden `<source>` prvek. Syntaxe následuje níže uvedený vzor.

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


Parametry v tomto elementu jsou popsány v následující tabulce.

| Parametry | Description |
|:--|:--|
| \_název objektu | Název objektu pro kolekci |
| \_regulární výraz instance |  *Regulární výraz* definující, které instance se mají shromažďovat Hodnota: `.*` Určuje všechny instance. Pokud chcete shromáždit metriky procesoru jenom pro \_ celkovou instanci, můžete zadat `_Total` . Pokud chcete shromáždit metriky procesu jenom pro instance crond nebo sshd, můžete zadat: `(crond\|sshd)` . |
| \_název čítače \_ regulárního výrazu | *Regulární výraz* definující, které čítače (pro objekt) se mají shromáždit. Chcete-li shromáždit všechny čítače pro objekt, zadejte: `.*` . Chcete-li shromažďovat pouze čítače odkládacích míst pro objekt paměti, například můžete zadat: `.+Swap.+` |
| interval | Frekvence, s níž jsou shromažďovány čítače objektu. |


V následující tabulce jsou uvedeny objekty a čítače, které lze zadat v konfiguračním souboru.  Pro některé aplikace jsou k dispozici další čítače, jak je popsáno v tématu [shromažďování čítačů výkonu pro aplikace pro Linux v Azure monitor](data-sources-linux-applications.md).

| Název objektu | Název čítače |
|:--|:--|
| Logický disk | % Bezplatného uzlů inode |
| Logický disk | % Volného místa |
| Logický disk | % Použitého uzlů inode |
| Logický disk | % Využitého místa |
| Logický disk | Bajty čtení z disku/s |
| Logický disk | Čtení z disku/s |
| Logický disk | Přenosy disku/s |
| Logický disk | Bajty zápisu na disk/s |
| Logický disk | Zápisy na disk/s |
| Logický disk | Volné megabajty |
| Logický disk | Bajty logického disku/s |
| Memory (Paměť) | % Dostupné paměti |
| Memory (Paměť) | % Dostupného odkládacího prostoru |
| Memory (Paměť) | % Využité paměti |
| Memory (Paměť) | % Využitého místa odkládacího souboru |
| Memory (Paměť) | Dostupná paměť v MB |
| Memory (Paměť) | Dostupný počet MB swap |
| Memory (Paměť) | Čtení stránek/s |
| Memory (Paměť) | Zápisy stránek/s |
| Memory (Paměť) | Stránky/s |
| Memory (Paměť) | Využité místo odkládacího souboru v MB |
| Memory (Paměť) | Využitá paměť v MB |
| Síť | Celkový počet odeslaných bajtů |
| Síť | Celkový počet přijatých bajtů |
| Síť | Bajty celkem |
| Síť | Celkový počet odeslaných paketů |
| Síť | Celkový počet přijatých paketů |
| Síť | Celkový počet chyb příjmu |
| Síť | Chyby odesílání celkem |
| Síť | Celkový počet kolizí |
| Fyzický disk | Střední doba disku/čtení |
| Fyzický disk | Střední doba disku/přenos |
| Fyzický disk | Střední doba disku/zápis |
| Fyzický disk | Bajty fyzického disku/s |
| Proces | Privilegovaný čas protokolu PCT |
| Proces | Doba uživatele v protokolu PCT |
| Proces | Využitá paměť v kilobajtech |
| Proces | Virtuální sdílená paměť |
| Procesor | % Času DPC |
| Procesor | % Času nečinnosti |
| Procesor | % Času přerušení |
| Procesor | % Času čekání na v/v |
| Procesor | % Dobrý čas |
| Procesor | % Privilegovaného času |
| Procesor | Procesorový čas v % |
| Procesor | % Uživatelského času |
| Systém | Volná fyzická paměť |
| Systém | Volné místo ve stránkovacích souborech |
| Systém | Volná virtuální paměť |
| Systém | Procesy |
| Systém | Velikost uložená ve stránkovacích souborech |
| Systém | Doba provozu |
| Systém | Uživatelé |


Následuje výchozí konfigurace pro metriky výkonu.

```xml
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
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*"
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
```

## <a name="data-collection"></a>Shromažďování dat
Azure Monitor shromažďuje všechny zadané čítače výkonu v zadaném vzorkovacím intervalu u všech agentů, u kterých je nainstalovaný tento čítač.  Data nejsou agregovaná a nezpracovaná data jsou k dispozici ve všech zobrazeních dotazu protokolu po dobu určenou v pracovním prostoru Log Analytics.

## <a name="performance-record-properties"></a>Vlastnosti záznamu výkonu
Záznamy o výkonu mají typ **výkonu** a mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Počítač, ze kterého byla událost shromážděna. |
| CounterName |Název čítače výkonu |
| CounterPath |Úplná cesta čítače v \\ \\ \<Computer> \\ čítači objektu formuláře (instance) \\ . |
| CounterValue |Číselná hodnota čítače. |
| InstanceName |Název instance události  Prázdné, pokud není instance. |
| ObjectName |Název objektu výkonu |
| SourceSystem |Typ agenta, ze kterého se data shromáždila. <br><br>OpsManager – Agent pro Windows, buď přímé připojení, nebo SCOM <br> Linux – všichni agenti se systémem Linux  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Datum a čas vzorkování dat |

## <a name="sizing-estimates"></a>Odhady velikosti
 Hrubý odhad pro shromažďování určitého čítače v intervalu 10 sekund je přibližně 1 MB za den na instanci.  Požadavky na úložiště určitého čítače můžete odhadnout pomocí následujícího vzorce.

> 1 MB x (počet čítačů) x (počet agentů) x (počet instancí)

## <a name="log-queries-with-performance-records"></a>Dotazy protokolu se záznamy o výkonu
Následující tabulka uvádí různé příklady dotazů protokolu, které načítají záznamy o výkonu.

| Dotaz | Description |
|:--- |:--- |
| Výkon |Všechna data o výkonu |
| &#124; výkonu, kde Computer = = "MyComputer" |Všechna data o výkonu z konkrétního počítače |
| &#124; výkonu, kde CounterName = = "aktuální délka fronty disku" |Veškerá data o výkonu pro určitý čítač |
| Výkon &#124;, kde ObjectName = = "procesor" a CounterName = = "% času procesoru" a InstanceName = = "_Total" &#124; sumarizace AVGCPU = AVG (CounterValue) podle počítače |Průměrné využití procesoru napříč všemi počítači |
| &#124; výkonu, kde CounterName = = "% času procesoru" &#124; sumarizace AggregatedValue = Max (CounterValue) podle počítače |Maximální využití procesoru napříč všemi počítači |
| &#124; výkonu, kde ObjectName = = "logický disk" a CounterName = = "aktuální délka fronty disku" a počítač = = "MyComputerName" &#124; sumarizace AggregatedValue = prům (CounterValue) by InstanceName |Průměrná aktuální délka fronty disku napříč všemi instancemi daného počítače |
| &#124; výkonu, kde CounterName = = "přenosů disku/s" &#124; sumarizace AggregatedValue = percentil (CounterValue, 95) podle počítače |95. percentil přenosů disku za sekundu ve všech počítačích |
| &#124; výkonu, kde CounterName = = "% času procesoru" a InstanceName = = "_Total" &#124; sumarizace AggregatedValue = prům (CounterValue) by bin (TimeGenerated, 1H), počítač |Hodinový průměr využití procesoru napříč všemi počítači |
| &#124; výkonu, kde Computer = = "MyComputer" a CounterName startswith_cs "%" a InstanceName = = "_Total" &#124; sumarizace AggregatedValue = percentil (CounterValue, 70) podle bin (TimeGenerated, 1H), CounterName | Hodinově 70. percentil každého čítače%% pro určitý počítač |
| &#124; výkonu, kde CounterName = = "% času procesoru" a InstanceName = = "_Total" a počítač = = "MyComputer" &#124; shrnout ["min (CounterValue)"] = min (CounterValue), ["AVG (CounterValue)"] = AVG (CounterValue); ["percentile75 (CounterValue)"] = percentil (CounterValue, 75), ["Max (CounterValue)"] = Max (CounterValue) po přihrádce (TimeGenerated, 1H), počítač |Hodinový průměr, minimum, maximum a 75-percentil využití CPU pro určitý počítač |
| &#124; výkonu, kde ObjectName = = "MSSQL $ INST2: databases" and InstanceName = = "Master" | Veškerá data o výkonu z objektu výkonu databáze pro hlavní databázi z pojmenované SQL Server instance INST2.  




## <a name="next-steps"></a>Další kroky
* [Shromážděte čítače výkonu z aplikací pro Linux](data-sources-linux-applications.md) , včetně MySQL a serveru http Apache.
* Přečtěte si o [dotazech protokolů](../log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení.  
* Exportujte shromážděná data do [Power BI](../platform/powerbi.md) pro další vizualizace a analýzu.
