---
title: Shromažďovat a analyzovat protokoly událostí Windows v Azure Log Analytics | Dokumentace Microsoftu
description: Protokoly událostí Windows jsou jedním z nejběžnější zdroje dat používané Log Analytics.  Tento článek popisuje, jak konfigurovat shromažďování protokolů událostí Windows a podrobnosti o záznamy, které vytvářejí v pracovním prostoru Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 4275b734a30310c896c397a5ef9cc1b218d89476
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842539"
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Zdroje dat protokolu událostí Windows ve službě Log Analytics
Protokoly událostí Windows jsou jedním z nejčastěji používaných [zdroje dat](agent-data-sources.md) pro shromažďování dat pomocí agentů Windows, protože mnoho aplikací se zapisují do protokolu událostí Windows.  Kromě zadání jakékoli vlastní protokoly vytvořené pomocí aplikací, které potřebujete k monitorování může shromažďovat události z standardní protokoly jako je například systém a aplikace.

![Události Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Protokoly událostí konfigurace Windows
Konfigurovat protokoly událostí Windows z [dat nabídky v nastavení analýzy protokolů](agent-data-sources.md#configuring-data-sources).

Log Analytics shromažďuje jenom události z protokolů událostí Windows, které jsou určené v nastavení.  Protokol událostí přidáte zadáním názvu protokolu a kliknutím na **+**.  Pro všechny protokoly se shromažďují jen události s vybranou závažnosti.  Zaškrtněte závažnosti pro konkrétní protokol, který chcete shromáždit.  Nelze zadat žádná další kritéria filtrování událostí.

Jak budete zadávat název protokolu událostí, Log Analytics poskytuje návrhy běžnému názvu certifikátu protokolu událostí. Pokud na protokol, který chcete přidat, se nezobrazí v seznamu, můžete ho přidat stále tak, že zadáte úplný název protokolu. Úplný název protokolu můžete najít pomocí prohlížeče událostí. V prohlížeči událostí, otevřete *vlastnosti* stránce protokolu a zkopírujte řetězec z *jméno a příjmení* pole.

![Konfigurace událostí Windows](media/data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Shromažďování dat
Log Analytics shromažďuje každou událost, která odpovídá vybrané závažnosti z monitorovaných protokolu událostí při vytváření události.  Agenta zaznamenává každý protokol událostí, který shromažďuje z jeho umístění.  Pokud agenta přejde do režimu offline pro určitou dobu, pak Log Analytics shromažďuje události z tam, kde poslední skončila, i v případě, že tyto události byly vytvořeny v době, kdy agent offline.  Je v provozu tyto události nebudou shromažďují, pokud se zabalí do protokolu událostí s nesebraný události přepsání agenta je offline.

>[!NOTE]
>Log Analytics vytvořená serverem SQL ze zdroje událostí auditu neshromažďuje *MSSQLSERVER* s ID události 18453, který obsahuje klíčová slova - *Classic* nebo *auditu úspěch* a klíčové slovo *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Vlastnosti záznamů událostí Windows
Záznamy událostí Windows mají typ **události** a mít vlastnosti v následující tabulce:

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Název počítače, který událost byla shromážděna z. |
| EventCategory |Kategorie události. |
| EventData |Všechna data události v nezpracovaném formátu. |
| ID události |Počet události. |
| eventLevel |Závažnost události v číselné podobě. |
| EventLevelName |Závažnost události v textové podobě. |
| Protokol událostí |Název shromážděné události z protokolu událostí. |
| ParameterXml |Hodnoty parametru událostí ve formátu XML. |
| ManagementGroupName |Název skupiny pro správu pro agenty System Center Operations Manageru.  Pro ostatní agenty tato hodnota je AOI-<workspace ID> |
| RenderedDescription |Popis události s hodnotami parametrů |
| Zdroj |Zdroj události. |
| SourceSystem |Typ agenta událost byla shromážděna z. <br> OpsManager – Windows agent, buď přímé připojení nebo spravované nástroje Operations Manager <br> Linux – všichni agenti systému Linux  <br> AzureStorage – diagnostiky Azure |
| TimeGenerated |Datum a čas vytvoření události ve Windows. |
| Uživatelské jméno |Uživatelské jméno účtu, který protokoluje událost. |

## <a name="log-searches-with-windows-events"></a>Hledání v protokolu událostí Windows
Následující tabulka obsahuje příklady různých prohledávání protokolů, které načítají záznamy událostí Windows.

| Dotaz | Popis |
|:---|:---|
| Událost |Všechny události Windows. |
| Událost &#124; kde EventLevelName == "Chyba" |Všechny události Windows závažnost chyby. |
| Událost &#124; shrnout count() podle zdroje |Počet Windows události podle zdroje. |
| Událost &#124; kde EventLevelName == "Chyba" &#124; shrnout count() podle zdroje |Počet Windows chybové události podle zdroje. |


## <a name="next-steps"></a>Další postup
* Konfigurace Log Analytics ke shromažďování dalších [zdroje dat](agent-data-sources.md) pro analýzu.
* Další informace o [prohledávání protokolů](../../azure-monitor/log-query/log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.  
* Použití [vlastní pole](../../log-analytics/log-analytics-custom-fields.md) k analýze záznamů událostí do jednotlivých polí.
* Konfigurace [shromažďování čítačů výkonu](data-sources-performance-counters.md) z agentů Windows.
