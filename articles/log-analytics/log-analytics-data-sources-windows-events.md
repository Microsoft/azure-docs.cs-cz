---
title: Shromažďovat a analyzovat protokoly událostí systému Windows v Azure Log Analytics | Microsoft Docs
description: Protokoly událostí systému Windows jsou jedním z nejčastějších zdroje dat používané analýzy protokolů.  Tento článek popisuje postup konfigurace shromažďování protokolů událostí systému Windows a podrobnosti záznamů, které vytvoří v pracovním prostoru analýzy protokolů.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: bwren
ms.openlocfilehash: 7a7deb4d7a287b2e9613e6035a7ffd7bb6f14f9c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782026"
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Zdroje dat protokolu událostí systému Windows v analýzy protokolů
Protokoly událostí systému Windows jsou jedním z většiny běžných [zdroje dat](log-analytics-data-sources.md) pro shromažďování dat pomocí agentů v systému Windows, vzhledem k tomu, že řada aplikací pro zápis do protokolu událostí systému Windows.  Kromě určení žádné vlastní protokoly, které jsou vytvořené pomocí aplikací, které potřebujete k monitorování může shromažďovat události z standardní protokoly, jako je například systém a aplikace.

![Události systému Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Protokoly událostí konfigurace systému Windows
Protokoly událostí systému Windows z konfigurace [nabídce Data v nastavení analýzy protokolů](log-analytics-data-sources.md#configuring-data-sources).

Analýzy protokolů pouze shromažďuje události z protokolů událostí systému Windows, které jsou určené v nastavení.  Protokol událostí můžete přidat zadáním názvu do protokolu a kliknutím na tlačítko  **+** .  Pro každý protokol se shromažďují jenom události s vybranou závažnosti.  Zkontrolujte závažnosti pro určitý protokol, který chcete shromáždit.  Nelze poskytnout žádná další kritéria filtrování událostí.

Jak budete zadávat název protokolu událostí, analýzy protokolů poskytuje návrhy běžné názvy protokolu událostí. Pokud na protokol, který chcete přidat v seznamu nezobrazí, můžete ho přidat stále zadáním do úplného názvu protokolu. Úplný název protokolu můžete najít pomocí prohlížeče událostí. V prohlížeči událostí, otevřete *vlastnosti* stránky pro protokol a zkopírujte řetězec z *úplný název* pole.

![Konfigurace události systému Windows](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Shromažďování dat
Analýzy protokolů shromažďuje všechny události, která odpovídá vybrané závažnost z monitorovaných protokolu událostí, při vytváření události.  Agent zaznamenává jeho místo v každém protokolu událostí, shromažďující z.  Pokud agenta přejde do režimu offline dobu, pak analýzy protokolů shromažďuje události od poslední místa vypnout, i v případě, že tyto události byly vytvořeny v době, kdy agent offline.  Existuje možnost pro tyto události, které nelze shromažďovat, pokud v protokolu událostí zabalí s nesebraný události přepsáním při agenta je offline.

>[!NOTE]
>Analýzy protokolů nejsou shromažďovány události auditu vytvořené systémem SQL Server ze zdroje *MSSQLSERVER* s ID události 18453, který obsahuje klíčová slova - *Classic* nebo *auditovat úspěšné* a – klíčové slovo *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Vlastnosti záznamů událostí systému Windows
Záznamy událostí Windows mít typ **událostí** a mít vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Název počítače, který událost nebyla shromážděna z. |
| EventCategory |Kategorie události. |
| EventData |Data všech událostí v nezpracovaném formátu. |
| ID události |Číslo události. |
| eventLevel |Závažnost události v číselný tvar. |
| EventLevelName |Závažnost události v textové podobě. |
| Protokol událostí |Název protokolu událostí, který událost nebyla shromážděna z. |
| ParameterXml |Hodnoty parametru události ve formátu XML. |
| ManagementGroupName |Název skupiny pro správu pro agenty nástroje System Center Operations Manager.  Pro jiné agenty tato hodnota je AOI-<workspace ID> |
| RenderedDescription |Popis události s hodnotami parametrů |
| Zdroj |Zdroj události. |
| SourceSystem |Typ agenta událost nebyla shromážděna z. <br> Připojit OpsManager – agent systému Windows, buď přímo nebo spravovaná nástroje Operations Manager <br> Linux – všechny agenty Linux  <br> Azurestorage – Azure Diagnostics. |
| TimeGenerated |Datum a čas vytvoření události v systému Windows. |
| Uživatelské jméno |Uživatelské jméno účtu, která událost zaznamenala. |

## <a name="log-searches-with-windows-events"></a>Protokol hledání se události systému Windows
Následující tabulka obsahuje různé příklady vyhledávání protokolu, které načtení záznamů událostí systému Windows.

| Dotaz | Popis |
|:---|:---|
| Událost |Všechny události systému Windows. |
| Událost &#124; kde EventLevelName == "error. |Všechny události systému Windows s závažnosti chyby. |
| Událost &#124; shrnout count() zdrojem. |Události počet Windows zdrojem. |
| Událost &#124; kde EventLevelName == "Chyba" &#124; shrnout count() zdrojem. |Události chyb počet Windows zdrojem. |


## <a name="next-steps"></a>Další kroky
* Konfigurace analýzy protokolů ke shromažďování dalších [zdroje dat](log-analytics-data-sources.md) pro analýzu.
* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení.  
* Použití [vlastní pole](log-analytics-custom-fields.md) k analýze záznamů událostí do jednotlivých polí.
* Konfigurace [kolekci čítačů výkonu](log-analytics-data-sources-performance-counters.md) z agentů v systému Windows.
