---
title: Shromažďovat zdroje dat protokolu událostí systému Windows pomocí agenta Log Analytics v Azure Monitor
description: V této části najdete popis postupu konfigurace shromažďování protokolů událostí systému Windows Azure Monitor a podrobností záznamů, které vytvoří.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 0eaa73fa7e0a9896a875af7e3a3aab22db2a37d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657055"
---
# <a name="collect-windows-event-log-data-sources-with-log-analytics-agent"></a>Shromažďovat zdroje dat protokolu událostí systému Windows pomocí agenta Log Analytics
Protokoly událostí systému Windows jsou jedním z nejběžnějších [zdrojů dat](../agents/agent-data-sources.md) pro agenty Log Analytics na virtuálních počítačích s Windows, protože mnoho aplikací zapisuje do protokolu událostí systému Windows.  Kromě určení libovolných vlastních protokolů vytvořených aplikacemi, které je třeba monitorovat, můžete shromažďovat události ze standardních protokolů, jako je například systém a aplikace.

> [!IMPORTANT]
> Tento článek popisuje shromažďování událostí systému Windows pomocí [agenta Log Analytics](./log-analytics-agent.md) , který je jedním z agentů používaných Azure monitor. Jiní agenti shromažďují různá data a nakonfigurují se jinak. Seznam dostupných agentů a data, která mohou shromažďovat, najdete v tématu [Přehled agentů Azure monitor](../agents/agents-overview.md) .

![Události systému Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurace protokolů událostí systému Windows
Nakonfigurujte protokoly událostí systému Windows z [nabídky konfigurace agenti](../agents/agent-data-sources.md#configuring-data-sources) pro pracovní prostor Log Analytics.

Azure Monitor shromažďuje pouze události z protokolů událostí systému Windows, které jsou zadány v nastavení.  Protokol událostí můžete přidat zadáním názvu protokolu a kliknutím **+** .  U každého protokolu se shromažďují jenom události s vybranými závažnostmi.  Ověřte závažnost konkrétního protokolu, který chcete shromáždit.  Nemůžete zadat žádná další kritéria pro filtrování událostí.

Při zadávání názvu protokolu událostí Azure Monitor poskytuje návrhy běžných názvů protokolů událostí. Pokud se v seznamu nezobrazí protokol, který chcete přidat, můžete ho přidat tak, že zadáte úplný název protokolu. Úplný název protokolu můžete najít pomocí prohlížeče událostí. V prohlížeči událostí otevřete stránku *vlastností* protokolu a zkopírujte řetězec z pole *celé jméno* .

[![Konfigurace událostí systému Windows](media/data-sources-windows-events/configure.png)](media/data-sources-windows-events/configure.png#lightbox)

> [!IMPORTANT]
> Nemůžete konfigurovat shromažďování událostí zabezpečení z pracovního prostoru. K shromažďování událostí zabezpečení je nutné použít [Azure Security Center](../../security-center/security-center-enable-data-collection.md) nebo [Azure Sentinel](../../sentinel/connect-windows-security-events.md) .


> [!NOTE]
> Kritické události z protokolu událostí systému Windows budou mít u protokolů Azure Monitor závažnost "Error".

## <a name="data-collection"></a>Shromažďování dat
Azure Monitor shromažďuje každou událost, která odpovídá vybrané závažnosti z monitorovaného protokolu událostí při vytvoření události.  Agent zaznamenává své místo do každého protokolu událostí, ze kterého shromažďuje.  Pokud agent přejde do režimu offline po určitou dobu, bude shromažďovat události, ze kterých byl naposled ponechán, a to i v případě, že byly tyto události vytvořeny v době, kdy byl agent offline.  Je možné, že tyto události nebudou shromažďovány, pokud se protokol událostí zalomí s neshromážděnými událostmi, zatímco je agent v režimu offline.

>[!NOTE]
>Azure Monitor neshromažďuje události auditu vytvořené SQL Server ze zdrojové *MSSQLSERVER* s ID události 18453, které obsahuje klíčová slova – *klasický* nebo *0xa0000000000000*, a klíčová slova s výsledky *auditu* .
>

## <a name="windows-event-records-properties"></a>Vlastnosti záznamů událostí systému Windows
Záznamy událostí systému Windows mají typ **události** a mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Název počítače, ze kterého byla událost shromážděna. |
| EventCategory |Kategorie události |
| EventData |Všechna data události v nezpracovaném formátu. |
| ID události |Číslo události |
| EventLevel |Závažnost události v číselném tvaru. |
| EventLevelName |Závažnost události v textovém formátu. |
| EventLog |Název protokolu událostí, ze kterého byla událost shromážděna. |
| ParameterXml |Hodnoty parametrů událostí ve formátu XML. |
| ManagementGroupName |Název skupiny pro správu pro agenty System Center Operations Manager.  Pro jiné agenty je tato hodnota `AOI-<workspace ID>` |
| RenderedDescription |Popis události s hodnotami parametrů |
| Zdroj |Zdroj události |
| SourceSystem |Typ agenta, ze kterého byla událost shromážděna <br> OpsManager – Agent pro Windows, buď přímá připojení, nebo Operations Manager spravovaná <br> Linux – všichni agenti se systémem Linux  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Datum a čas vytvoření události v systému Windows. |
| Uživatelské jméno |Uživatelské jméno účtu, který událost zaznamenal. |

## <a name="log-queries-with-windows-events"></a>Dotazy protokolu s událostmi systému Windows
Následující tabulka uvádí různé příklady dotazů protokolu, které načítají záznamy událostí systému Windows.

| Dotaz | Description |
|:---|:---|
| Událost |Všechny události systému Windows. |
| Událost &#124;, kde EventLevelName = = "Error" |Všechny události systému Windows se závažností chyby. |
| Event &#124; sumarizace – počet () podle zdroje |Počet událostí systému Windows podle zdroje |
| Událost &#124;, kde EventLevelName = = "Error" &#124; sumarizace Count () podle zdroje |Počet událostí chyb systému Windows podle zdroje. |


## <a name="next-steps"></a>Další kroky
* Nakonfigurujte Log Analytics pro shromažďování dalších [zdrojů dat](../agents/agent-data-sources.md) pro účely analýzy.
* Přečtěte si o [dotazech protokolů](../logs/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení.  
* Nakonfigurujte [shromažďování čítačů výkonu](data-sources-performance-counters.md) z agentů Windows.
