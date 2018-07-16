---
title: Vizuálně monitorovat objekty pro vytváření dat Azure | Dokumentace Microsoftu
description: Zjistěte, jak vizuálně monitorovat objekty pro vytváření dat Azure
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
ms.date: 07/12/2018
ms.author: shlo
ms.openlocfilehash: 4b3828e1857d17a128de346449d5cf2041709e50
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041071"
---
# <a name="visually-monitor-azure-data-factories"></a>Vizuálně monitorovat objekty pro vytváření dat Azure
Azure Data Factory je cloudová služba pro integraci dat umožňující vytváření pracovních postupů řízených daty v cloudu za účelem orchestrace a automatizace přesunu a transformace dat. Pomocí služby Azure Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (nazývané kanály) se schopností ingestovat data z různorodých úložišť dat, zpracovat a transformovat tato data pomocí výpočetních služeb, jako je Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics a Azure Machine Learning, a publikovat výstupní data do úložišť dat, jako je Azure SQL Data Warehouse, aby je mohly využívat aplikace business intelligence (BI).
V tomto rychlém startu se dozvíte, jak vizuálně sledovat kanály data factory v2, aniž byste museli napsat jediný řádek kódu.
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="monitor-data-factory-v2-pipelines"></a>Monitorování kanálů data factory v2

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přihlaste se k [webu Azure portal](https://portal.azure.com/).
3. Přejděte do okna objekt pro vytváření dat vytvořené na webu Azure portal a klikněte na dlaždici "Monitorování a správa". Tím se spustí ADF v2 visual monitorováním.

## <a name="list-view-monitoring"></a>Monitorování pomocí zobrazení seznamu

Monitorování spuštění aktivit a kanálu pomocí jednoduché rozhraní zobrazení seznamu. Všechna spuštění se zobrazují v místním časovém pásmu prohlížeče. Časové pásmo je možné změnit. Všechna pole data a času se potom přizpůsobí vybranému časovému pásmu.  

#### <a name="monitoring-pipeline-runs"></a>Monitorování spuštění kanálu
Zobrazení seznamu předvádí jednotlivá spuštění pro kanály Data Factory v2. Zahrnuté sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu. |
| Akce | Chcete-li zobrazit spuštění aktivit jedné akce. |
| Spuštění | Kanál datum a čas spuštění (MM/DD/RRRR HH: mm: SS AM/PM) |
| Doba trvání | Doba trvání (hh: mm:) |
| Inicializoval | Ruční spuštění aktivační události plánovače |
| Status | Se nezdařilo, probíhá úspěšně |
| Parametry | Parametry (název, hodnotu páry) spuštění kanálu |
| Chyba | Spuštění kanálu chyb (if/any) |
| ID běhu | ID spuštění kanálu |

![Monitorování spuštění kanálu](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Monitorování spuštění aktivit
Zobrazení seznamu předvádí spuštění aktivit odpovídající jednotlivým spuštěním kanálu. Klikněte na tlačítko **spuštění aktivit** ikonu v části **"Akce"** sloupec a zobrazit aktivitu spuštění pro každé spuštění kanálu. Zahrnuté sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název aktivity | Název aktivity v kanálu. |
| Typ aktivity | Typ aktivity například kopírování, HDInsightSpark, HDInsightHive atd. |
| Spuštění | Počáteční datum a čas spuštění aktivit (MM/DD/RRRR HH: mm: SS AM/PM) |
| Doba trvání | Doba trvání (hh: mm:) |
| Status | Se nezdařilo, probíhá úspěšně |
| Vstup | Pole JSON popisující vstupech do aktivity |
| Výstup | Pole JSON popisující výstupů aktivity |
| Chyba | (Pokud/existuje) Chyba spuštění aktivit |

![Monitorování spuštění aktivit](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Budete muset kliknout na **'Aktualizovat'** ikonu v horní části můžete aktualizovat seznam spuštění aktivit a kanálu. Automatická aktualizace se v tuto chvíli nepodporuje.
>

![Obnovení](media/monitor-visually/refresh.png)

## <a name="features"></a>Funkce

#### <a name="select-a-data-factory-to-monitor"></a>Vyberte objekt pro vytváření dat monitorování
Najeďte myší na **služby Data Factory** ikony v levém horním rohu. Klikněte na ikonu "Šipka" k zobrazení seznamu azure předplatná a datových továren, které můžete monitorovat.

![Výběr datové továrny](media/monitor-visually/select-datafactory.png)

#### <a name="rich-ordering-and-filtering"></a>Bohaté možnosti uspořádání a filtrování

Spuštění kanálu pořadí ve vzestupném nebo sestupném podle začátku spuštění a spuštění kanálu filtr podle následujících sloupců:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu. Možnosti zahrnují rychlé filtry pro "posledních 24 hodin", "Minulý týden", "posledních 30 dní" nebo vyberte vlastní datum čas. |
| Spuštění | Počáteční datum a čas spuštění kanálu |
| Stav spuštění | Filtr se spouští podle stavu to znamená úspěch, se nezdařil, probíhá |

![Filtr](media/monitor-visually/filter.png)

#### <a name="addremove-columns-in-list-view"></a>Přidání nebo odebrání sloupců v zobrazení seznamu
Klikněte pravým tlačítkem na záhlaví zobrazení seznamu a vybrat sloupce, které chcete zobrazit v zobrazení seznamu

![Sloupce](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Změna pořadí sloupců v zobrazení seznamu
Zvýšit a snížit šířku sloupců v zobrazení seznamu jednoduše najede myší na záhlaví sloupce

#### <a name="user-properties"></a>Uživatelské vlastnosti

Můžete zvýšit úroveň jakékoli vlastnosti aktivity kanálu jako vlastnost uživatele tak, aby je entita, která můžete monitorovat. Například můžete zvýšit úroveň **zdroj** a **cílové** vlastnosti aktivity kopírování v kanálu jako vlastnosti uživatele. Můžete také vybrat **automaticky generovat** ke generování **zdroj** a **cílové** vlastnosti uživatele pro aktivitu kopírování.

![Vytvoření vlastnosti uživatele.](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Až 5 vlastnosti aktivity kanálu můžete zvýšit úroveň pouze jako vlastnosti uživatele.

Po vytvoření vlastnosti uživatele, potom je monitorují kontrolní seznamy. Pokud název tabulky je zdrojem pro aktivitu kopírování, můžete monitorovat název tabulky zdrojového sloupce v aktivitě spuštění zobrazení seznamu.

![Aktivita se spustí bez vlastnosti uživatele.](media/monitor-visually/monitor-user-properties-image2.png)

![Přidání sloupce pro vlastnosti uživatele do seznamu spuštění aktivit](media/monitor-visually/monitor-user-properties-image3.png)

![Seznam spuštění aktivit se sloupci pro vlastnosti uživatele.](media/monitor-visually/monitor-user-properties-image4.png)

#### <a name="guided-tours"></a>Kurzy s asistencí
Klepněte na informační ikonu v levé dolní části a klikněte na tlačítko 'S asistencí prohlídky' Chcete-li získat podrobné pokyny o tom, jak monitorovat spuštění aktivit a kanálu.

![Kurzy s asistencí](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Váš názor
Klikněte na ikonu "Názory" a sdělte nám svůj názor na různé funkce nebo uvést případné potíže, které můžete se setkat.

![Váš názor](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Další postup

Zobrazit [monitorování a Správa kanálů prostřednictvím kódu programu](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) článku najdete informace o monitorování a Správa kanálů
