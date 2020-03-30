---
title: Shromažďování a analýza protokolů událostí systému Windows v Azure Monitoru | Dokumenty společnosti Microsoft
description: Popisuje, jak nakonfigurovat kolekci protokolů událostí systému Windows pomocí Azure Monitor a podrobnosti o záznamech, které vytvářejí.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa34196233ce4037ef6fa49b782b9aa958f7632d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274681"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Zdroje dat protokolu událostí Windows v Azure Monitoru
Protokoly událostí systému Windows jsou jedním z nejběžnějších [zdrojů dat](agent-data-sources.md) pro shromažďování dat pomocí agentů systému Windows, protože mnoho aplikací zapisuje do protokolu událostí systému Windows.  Můžete shromažďovat události ze standardních protokolů, jako je systém a aplikace kromě určení všechny vlastní protokoly vytvořené aplikace, které je třeba sledovat.

![Události systému Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Konfigurace protokolů událostí systému Windows
Nakonfigurujte protokoly událostí systému Windows z [nabídky Data v rozšířeném nastavení](agent-data-sources.md#configuring-data-sources).

Azure Monitor shromažďuje pouze události z protokolů událostí systému Windows, které jsou zadané v nastavení.  Protokol událostí můžete přidat zadáním názvu protokolu a **+** klepnutím na tlačítko .  Pro každý protokol jsou shromažďovány pouze události s vybranou závažností.  Zkontrolujte závažnost i konkrétní protokol, který chcete shromáždit.  Pro filtrování událostí nelze zadat žádná další kritéria.

Při psaní názvu protokolu událostí Azure Monitor poskytuje návrhy běžných názvů protokolů událostí. Pokud se protokol, který chcete přidat, v seznamu nezobrazí, můžete jej přesto přidat zadáním úplného názvu protokolu. Úplný název protokolu můžete najít pomocí prohlížeče událostí. V prohlížeči událostí otevřete stránku *Vlastnosti* protokolu a zkopírujte řetězec z pole *Celé jméno.*

![Konfigurace událostí systému Windows](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Kritické události z protokolu událostí systému Windows bude mít závažnost "Chyba" v protokolech monitorování Azure.

## <a name="data-collection"></a>Shromažďování dat
Azure Monitor shromažďuje každou událost, která odpovídá vybrané závažnosti z protokolu monitorovaných událostí při vytváření události.  Agent zaznamenává své místo v každém protokolu událostí, ze kterého shromažďuje.  Pokud agent přejde do offline po určitou dobu, pak shromažďuje události z místa, kde naposledy skončil, i v případě, že tyto události byly vytvořeny v době, kdy byl agent offline.  Existuje potenciál pro tyto události, které nelze shromažďovat, pokud protokol událostí obtéká nesebrané události přepsány v době, kdy je agent offline.

>[!NOTE]
>Azure Monitor neshromažďuje události auditu vytvořené SQL Server ze zdroje *MSSQLSERVER* s ID události 18453, která obsahuje klíčová slova - *klasický* nebo *audit úspěch* a klíčové slovo *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Vlastnosti záznamů událostí systému Windows
Záznamy událostí systému Windows mají typ **události** a mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Název počítače, ze kterého byla událost shromážděna. |
| Kategorie události |Kategorie události. |
| Eventdata |Všechna data událostí v nezpracovaném formátu. |
| ID události |Číslo události. |
| Úroveň události |Závažnost události v číselné podobě. |
| Název_události_události |Závažnost události v textové podobě. |
| Eventlog |Název protokolu událostí, ze kterého byla událost shromážděna. |
| ParametrXml |Hodnoty parametrů události ve formátu XML. |
| ManagementGroupName |Název skupiny pro správu pro agenty nástroje Operations Manager systémového centra.  Pro ostatní agenty je tato hodnota`AOI-<workspace ID>` |
| Vykreslený popis |Popis události s hodnotami parametrů |
| Zdroj |Zdroj události. |
| SourceSystem |Typ agenta, od kterého byla událost shromážděna. <br> OpsManager – agent windows, přímé připojení nebo správce operací <br> Linux – Všichni linuxoví agenti  <br> AzureStorage – diagnostika Azure |
| TimeGenerated |Datum a čas, kdy byla událost vytvořena v systému Windows. |
| UserName |Uživatelské jméno účtu, který událost zaznamenal. |

## <a name="log-queries-with-windows-events"></a>Protokolovat dotazy pomocí událostí systému Windows
Následující tabulka obsahuje různé příklady dotazů protokolu, které načítají záznamy událostí systému Windows.

| Dotaz | Popis |
|:---|:---|
| Událost |Všechny události systému Windows. |
| &#124; událostí, kde EventLevelName == "chyba" |Všechny události systému Windows se závažností chyby. |
| Počet &#124; událostí sumarizuje podle zdroje |Počet událostí systému Windows podle zdroje. |
| &#124; událostí, kde EventLevelName == "chyba" &#124; sumarizovat count() podle zdroje |Počet chybových událostí systému Windows podle zdroje. |


## <a name="next-steps"></a>Další kroky
* Nakonfigurujte analýzu protokolů tak, aby shromažďovala další [zdroje dat](agent-data-sources.md) pro analýzu.
* Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení.  
* Nakonfigurujte [kolekci čítačů výkonu](data-sources-performance-counters.md) od agentů systému Windows.
