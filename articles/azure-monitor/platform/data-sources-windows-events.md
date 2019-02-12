---
title: Shromažďovat a analyzovat protokoly událostí Windows ve službě Azure Monitor | Dokumentace Microsoftu
description: Popisuje postup konfigurace shromažďování protokolů událostí Windows službou Azure Monitor a podrobnosti záznamy, které vytvářejí.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: 1f55e03d9a925bf939d627f376d29edf27461e74
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001115"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Zdroje dat protokolu událostí Windows ve službě Azure Monitor
Protokoly událostí Windows jsou jedním z nejčastěji používaných [zdroje dat](agent-data-sources.md) pro shromažďování dat pomocí agentů Windows, protože mnoho aplikací se zapisují do protokolu událostí Windows.  Kromě zadání jakékoli vlastní protokoly vytvořené pomocí aplikací, které potřebujete k monitorování může shromažďovat události z standardní protokoly jako je například systém a aplikace.

![Události Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Protokoly událostí konfigurace Windows
Konfigurovat protokoly událostí Windows z [dat nabídky v upřesňujícím nastavení](agent-data-sources.md#configuring-data-sources).

Azure Monitor shromažďuje jenom události z protokolů událostí Windows, které jsou určené v nastavení.  Protokol událostí přidáte zadáním názvu protokolu a kliknutím na **+**.  Pro všechny protokoly se shromažďují jen události s vybranou závažnosti.  Zaškrtněte závažnosti pro konkrétní protokol, který chcete shromáždit.  Nelze zadat žádná další kritéria filtrování událostí.

Jak budete zadávat název protokolu událostí, Azure Monitor nabízí návrhy běžnému názvu certifikátu protokolu událostí. Pokud na protokol, který chcete přidat, se nezobrazí v seznamu, můžete ho přidat stále tak, že zadáte úplný název protokolu. Úplný název protokolu můžete najít pomocí prohlížeče událostí. V prohlížeči událostí, otevřete *vlastnosti* stránce protokolu a zkopírujte řetězec z *jméno a příjmení* pole.

![Konfigurace událostí Windows](media/data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Shromažďování dat
Azure Monitor provádí sběr každou událost, která odpovídá vybrané závažnosti z monitorovaných protokolu událostí při vytváření události.  Agenta zaznamenává každý protokol událostí, který shromažďuje z jeho umístění.  Pokud agenta přejde do režimu offline pro určitou dobu, potom shromáždí události z tam, kde poslední skončila, i v případě, že tyto události byly vytvořeny v době, kdy agent offline.  Je v provozu tyto události nebudou shromažďují, pokud se zabalí do protokolu událostí s nesebraný události přepsání agenta je offline.

>[!NOTE]
>Azure Monitor neshromažďuje vytvořená serverem SQL ze zdroje událostí auditu *MSSQLSERVER* s ID události 18453, který obsahuje klíčová slova - *Classic* nebo *auditu úspěch* a klíčové slovo *0xa0000000000000*.
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
| EventLog |Název shromážděné události z protokolu událostí. |
| ParameterXml |Hodnoty parametru událostí ve formátu XML. |
| ManagementGroupName |Název skupiny pro správu pro agenty System Center Operations Manageru.  Pro ostatní agenty tato hodnota je AOI-<workspace ID> |
| RenderedDescription |Popis události s hodnotami parametrů |
| Zdroj |Zdroj události. |
| SourceSystem |Typ agenta událost byla shromážděna z. <br> OpsManager – Windows agent, buď přímé připojení nebo spravované nástroje Operations Manager <br> Linux – všichni agenti systému Linux  <br> AzureStorage – diagnostiky Azure |
| TimeGenerated |Datum a čas vytvoření události ve Windows. |
| Uživatelské jméno |Uživatelské jméno účtu, který protokoluje událost. |

## <a name="log-queries-with-windows-events"></a>Protokol dotazů pomocí událostí Windows
Následující tabulka obsahuje příklady různých dotazů na protokoly, které načítají záznamy událostí Windows.

| Dotaz | Popis |
|:---|:---|
| Událost |Všechny události Windows. |
| Událost &#124; kde EventLevelName == "Chyba" |Všechny události Windows závažnost chyby. |
| Event &#124; summarize count() by Source |Počet Windows události podle zdroje. |
| Event &#124; where EventLevelName == "error" &#124; summarize count() by Source |Počet Windows chybové události podle zdroje. |


## <a name="next-steps"></a>Další postup
* Konfigurace Log Analytics ke shromažďování dalších [zdroje dat](agent-data-sources.md) pro analýzu.
* Další informace o [protokolu dotazy](../log-query/log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.  
* Konfigurace [shromažďování čítačů výkonu](data-sources-performance-counters.md) z agentů Windows.