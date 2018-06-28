---
title: Vizuální sledování Azure data Factory | Microsoft Docs
description: Naučte se monitorovat vizuálně Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: shlo
ms.openlocfilehash: e654cc23d6a558469ea238fc5ade82b44562f9a2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050365"
---
# <a name="visually-monitor-azure-data-factories"></a>Vizuální sledování Azure data Factory
Azure Data Factory je cloudová služba pro integraci dat umožňující vytváření pracovních postupů řízených daty v cloudu za účelem orchestrace a automatizace přesunu a transformace dat. Pomocí služby Azure Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (nazývané kanály) se schopností ingestovat data z různorodých úložišť dat, zpracovat a transformovat tato data pomocí výpočetních služeb, jako je Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics a Azure Machine Learning, a publikovat výstupní data do úložišť dat, jako je Azure SQL Data Warehouse, aby je mohly využívat aplikace business intelligence (BI).
V této úvodní se dozvíte, jak vizuálně monitorování kanálů v2 objekt pro vytváření dat bez nutnosti napsat jediný řádek kódu.
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="monitor-data-factory-v2-pipelines"></a>Monitorování kanálů v2 objekt pro vytváření dat

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přihlaste se k [portál Azure](https://portal.azure.com/).
3. Přejděte do okna objekt pro vytváření dat vytvořený na portálu Azure a klikněte na dlaždici, monitorování a správa'. Tím se spustí ADF v2 visual monitorováním.

## <a name="list-view-monitoring"></a>Zobrazení seznamu monitorování

Monitorování kanálu a aktivity spouští s rozhraním zobrazení jednoduchých seznamů. Všechna spuštění se zobrazují v místním časovém pásmu prohlížeče. Časové pásmo je možné změnit. Všechna pole data a času se potom přizpůsobí vybranému časovému pásmu.  

#### <a name="monitoring-pipeline-runs"></a>Spustí monitorování kanálu
Zobrazení seznamu předvádí jednotlivá spuštění pro kanály Data Factory v2. Zahrnuté sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu. |
| Akce | Chcete-li zobrazit běh aktivit je jedné akce. |
| Spustit počáteční | Kanál spustit počáteční datum a čas (MM/DD/RRRR HH: mm: SS dop. / odp.) |
| Doba trvání | Doba běhu (hh: mm:) |
| Inicializoval | Ruční aktivaci plánu aktivační události |
| Status | Chyba, byly úspěšné, v průběhu |
| Parametry | Kanál spustit parametry (název, hodnotu páry) |
| Chyba | Kanál spustit chyby (Pokud nebo any) |
| ID běhu | ID spuštění kanálu |

![Monitorování spuštění kanálu](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Spustí monitorování aktivity
Zobrazení seznamu předvádí spuštění aktivit odpovídající jednotlivým spuštěním kanálu. Klikněte na tlačítko **aktivita spuštěna** ikonu pod **"Akce"** sloupce k zobrazení aktivity spouští pro každý kanál spustit. Zahrnuté sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název aktivity | Název aktivity v kanálu. |
| Typ aktivity | Typ aktivity tj kopírování, HDInsightSpark, HDInsightHive atd. |
| Spustit počáteční | Aktivity při spuštění počáteční datum a čas (MM/DD/RRRR HH: mm: SS dop. / odp.) |
| Doba trvání | Doba běhu (hh: mm:) |
| Status | Chyba, byly úspěšné, v průběhu |
| Vstup | Pole JSON s popisem vstupy aktivity |
| Výstup | Pole JSON s popisem výstupů aktivity |
| Chyba | Aktivity při spuštění chyby (Pokud nebo any) |

![Monitorování spuštění aktivit](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Je třeba kliknout na **'Aktualizujte'** ikonu na horní k aktualizaci seznamu kanálu a aktivita běží. Automatická aktualizace se v tuto chvíli nepodporuje.
>

![Obnovení](media/monitor-visually/refresh.png)

## <a name="features"></a>Funkce

#### <a name="rich-ordering-and-filtering"></a>Bohaté možnosti uspořádání a filtrování

Pořadí kanálu se spustí v desc/asc spustit Start a filtr kanálu se spustí následující sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu. Možnosti zahrnují rychlé filtry pro posledních 24 hodin 'Poslední týden', 'posledních 30 dní, nebo vyberte vlastní datum čas. |
| Spustit počáteční | Kanál spustit počáteční datum a čas |
| Stav spuštění | Filtr se spouští podle stavu znamená úspěšné, neúspěšné, probíhá |

![Filtr](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>Přidání nebo odebrání sloupců v zobrazení seznamu
Klikněte pravým tlačítkem na hlavičku zobrazení seznamu a vyberte sloupce, které se má zobrazit v zobrazení seznamu

![Sloupce](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Změna pořadí šířku sloupců v zobrazení seznamu
Zvýšení a snižte šířku sloupců v zobrazení seznamu jednoduše ukazatele myši na záhlaví sloupce

#### <a name="select-data-factory"></a>Výběr datové továrny
Najeďte na ikonu služby Data Factory v levé horní části. Klikněte na ikonu 'Šipku' Chcete-li zobrazit seznam azure odběry a data Factory, můžete monitorovat.

![Výběr datové továrny](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>Cesty s průvodcem
Klikněte na informační ikona ve spodní levé a klikněte na tlačítko 'Na základě prohlídka' Chcete-li získat podrobné pokyny o tom, jak sledovat vaše kanálu a aktivita běží.

![Cesty s průvodcem](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Váš názor
Klikněte na ikonu 'zpětnou vazbu, sdělte nám svůj názor na různých funkcí nebo potíže, které se setkávají.

![Váš názor](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Další postup

V tématu [monitorování a Správa kanálů prostřednictvím kódu programu](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) článku se dozvíte o monitorování a Správa kanálů
