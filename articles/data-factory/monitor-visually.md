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
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: 721e904fa5e1ec839d5d236ba4bcda60201f5b26
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555383"
---
# <a name="visually-monitor-azure-data-factories"></a>Vizuálně monitorovat objekty pro vytváření dat Azure
Azure Data Factory je cloudová služba pro integraci dat umožňující vytváření pracovních postupů řízených daty v cloudu za účelem orchestrace a automatizace přesunu a transformace dat. Pomocí služby Azure Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (nazývané kanály) se schopností ingestovat data z různorodých úložišť dat, zpracovat a transformovat tato data pomocí výpočetních služeb, jako je Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics a Azure Machine Learning, a publikovat výstupní data do úložišť dat, jako je Azure SQL Data Warehouse, aby je mohly využívat aplikace business intelligence (BI).
V tomto rychlém startu se dozvíte, jak vizuálně sledovat kanály data factory v2, aniž byste museli napsat jediný řádek kódu.
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="monitor-data-factory-pipelines"></a>Monitorování kanálů Data Factory

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přihlaste se k [webu Azure portal](https://portal.azure.com/).
3. Přejděte do okna objekt pro vytváření dat vytvořené na webu Azure portal a klikněte na dlaždici "Monitorování a správa" ke spuštění prostředí pro vizuální monitorování služby Data Factory.

## <a name="list-view-monitoring"></a>Monitorování pomocí zobrazení seznamu

Monitorování spuštění aktivit a kanálu pomocí jednoduché rozhraní zobrazení seznamu. Všechna spuštění se zobrazují v místním časovém pásmu prohlížeče. Můžete změnit časové pásmo a všechna pole Datum čas Přichytit k vybranému časovému pásmu.  

### <a name="monitoring-pipeline-runs"></a>Monitorování spuštění kanálu
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

### <a name="monitoring-activity-runs"></a>Monitorování spuštění aktivit
Zobrazení seznamu předvádí spuštění aktivit odpovídající jednotlivým spuštěním kanálu. Klikněte na tlačítko **spuštění aktivit** ikonu v části **"Akce"** sloupec a zobrazit aktivitu spuštění pro každé spuštění kanálu. Zahrnuté sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název aktivity | Název aktivity v kanálu. |
| Typ aktivity | Typ aktivity, jako je například kopírování, HDInsightSpark, HDInsightHive atd. |
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

## <a name="monitoring-features"></a>Funkce sledování

### <a name="select-a-data-factory-to-monitor"></a>Vyberte objekt pro vytváření dat monitorování
Najeďte myší na **služby Data Factory** ikony v levém horním rohu. Klikněte na ikonu "Šipka" k zobrazení seznamu azure předplatná a datových továren, které můžete monitorovat.

![Výběr datové továrny](media/monitor-visually/select-datafactory.png)

### <a name="rich-ordering-and-filtering"></a>Bohaté možnosti uspořádání a filtrování

Spuštění kanálu pořadí ve vzestupném nebo sestupném podle začátku spuštění a spuštění kanálu filtr podle následujících sloupců:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu. Možnosti zahrnují rychlé filtry pro "posledních 24 hodin", "Minulý týden", "posledních 30 dní" nebo vyberte vlastní datum čas. |
| Spuštění | Počáteční datum a čas spuštění kanálu |
| Stav spuštění | Filtr se spouští podle stavu – úspěšné, se nezdařil, probíhá |

![Filtr](media/monitor-visually/filter.png)

### <a name="addremove-columns-in-list-view"></a>Přidání nebo odebrání sloupců v zobrazení seznamu
Klikněte pravým tlačítkem na záhlaví zobrazení seznamu a vybrat sloupce, které chcete zobrazit v zobrazení seznamu

![Sloupce](media/monitor-visually/columns.png)

### <a name="reorder-column-widths-in-list-view"></a>Změna pořadí sloupců v zobrazení seznamu
Zvýšení a snížení šířky sloupců v seznamu zobrazit ukázáním myší na záhlaví sloupce

### <a name="user-properties"></a>Uživatelské vlastnosti

Můžete zvýšit úroveň jakékoli vlastnosti aktivity kanálu jako vlastnost uživatele tak, aby je entita, která můžete monitorovat. Například můžete zvýšit úroveň **zdroj** a **cílové** vlastnosti aktivity kopírování v kanálu jako vlastnosti uživatele. Můžete také vybrat **automaticky generovat** ke generování **zdroj** a **cílové** vlastnosti uživatele pro aktivitu kopírování.

![Vytvoření vlastnosti uživatele.](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Až 5 vlastnosti aktivity kanálu můžete zvýšit úroveň pouze jako vlastnosti uživatele.

Po vytvoření vlastnosti uživatele, potom je monitorují kontrolní seznamy. Pokud název tabulky je zdrojem pro aktivitu kopírování, můžete monitorovat název tabulky zdrojového sloupce v aktivitě spuštění zobrazení seznamu.

![Aktivita se spustí bez vlastnosti uživatele.](media/monitor-visually/monitor-user-properties-image2.png)

![Přidání sloupce pro vlastnosti uživatele do seznamu spuštění aktivit](media/monitor-visually/monitor-user-properties-image3.png)

![Seznam spuštění aktivit se sloupci pro vlastnosti uživatele.](media/monitor-visually/monitor-user-properties-image4.png)

### <a name="guided-tours"></a>Kurzy s asistencí
Klepněte na informační ikonu v levé dolní části a klikněte na tlačítko 'S asistencí prohlídky' Chcete-li získat podrobné pokyny o tom, jak monitorovat spuštění aktivit a kanálu.

![Kurzy s asistencí](media/monitor-visually/guided-tours.png)

### <a name="feedback"></a>Váš názor
Klikněte na ikonu "Názory" a sdělte nám svůj názor na různé funkce nebo uvést případné potíže, které můžete se setkat.

![Váš názor](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Výstrahy

Může vyvolat upozornění na podporované metriky ve službě Data Factory. Vyberte monitorování -> Výstrahy a metriky pro monitorování objekt pro vytváření dat stránky, abyste mohli začít.

![](media/monitor-visually/alerts01.png)

### <a name="create-alerts"></a>Vytvořit výstrahy

1.  Klikněte na tlačítko **nová výstraha pravidlo** vytvořit nové oznámení.

    ![](media/monitor-visually/alerts02.png)

1.  Zadejte název pravidla a vyberte příslušnou výstrahu **závažnost**.

    ![](media/monitor-visually/alerts03.png)

1.  Vyberte kritéria výstrahy.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Nakonfigurujte logika upozornění. Můžete vytvořit výstrahy pro vybranou metriku pro všechny kanály a odpovídající aktivity. Můžete také vybrat typ konkrétní aktivity, název aktivity, názvu kanálu nebo typ selhání.

    ![](media/monitor-visually/alerts06.png)

1.  Konfigurace **e-mailu/SMS nebo nabízená/hlasové** oznámení pro výstrahy. Vytvořit nebo vybrat existující **skupiny akcí** oznámení výstrah.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Vytvořte pravidlo upozornění.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Další postup

Zobrazit [monitorování a Správa kanálů prostřednictvím kódu programu](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) článku najdete informace o monitorování a Správa kanálů.
