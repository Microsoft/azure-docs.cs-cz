---
title: Vizuálně monitorujte datové továrny Azure | Microsoft Docs
description: Naučte se vizuálně monitorovat datové továrny Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: 53ead1caed47ae442670f0b6bcd54cd84956a759
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720617"
---
# <a name="visually-monitor-azure-data-factories"></a>Vizuálně monitorujte datové továrny Azure.
Azure Data Factory je cloudová služba pro integraci dat umožňující vytváření pracovních postupů řízených daty v cloudu za účelem orchestrace a automatizace přesunu a transformace dat. Pomocí služby Azure Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (nazývané kanály) se schopností ingestovat data z různorodých úložišť dat, zpracovat a transformovat tato data pomocí výpočetních služeb, jako je Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics a Azure Machine Learning, a publikovat výstupní data do úložišť dat, jako je Azure SQL Data Warehouse, aby je mohly využívat aplikace business intelligence (BI).

V tomto rychlém startu se naučíte vizuálně monitorovat Data Factory kanály, aniž byste museli psát jediný řádek kódu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="monitor-data-factory-pipelines"></a>Monitorování kanálů Data Factory

Monitorování spuštění kanálu a aktivit pomocí jednoduchého rozhraní zobrazení seznamu. Všechna spuštění se zobrazí v místním časovém pásmu prohlížeče. Můžete změnit časové pásmo a všechna pole data a času se mají přitahovat k vybranému časovému pásmu.  

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přihlaste se k [Azure Portal](https://portal.azure.com/).
3. V Azure Portal přejděte do okna vytvořená datová továrna a klikněte na dlaždici monitorovat & spravovat a spusťte Data Factory vizuální monitorování.

## <a name="monitor-pipeline-runs"></a>Monitorování spuštění kanálu
Zobrazení seznamu předvádí jednotlivá spuštění pro kanály Data Factory v2. Zahrnuté sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu. |
| Akce | Jedna akce dostupná pro zobrazení spuštění aktivit. |
| Spustit spuštění | Počáteční datum a čas spuštění kanálu (MM/DD/RRRR, HH: MM: SS dop./odp.) |
| Trvání | Doba trvání běhu (HH: MM: SS) |
| Inicializoval | Manuální aktivační událost, aktivační událost plánovače |
| Stav | Úspěch, úspěšně dokončeno, probíhá |
| Parametry | Parametry běhu kanálu (dvojice název, hodnota) |
| Chyba | Chyba spuštění kanálu (pokud/any) |
| ID běhu | ID spuštění kanálu |

![Monitorování spuštění kanálu](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Monitorování spuštění aktivit
Zobrazení seznamu předvádí spuštění aktivit odpovídající jednotlivým spuštěním kanálu. Kliknutím na ikonu **spuštění aktivit** ve sloupci Actions ( **Akce** ) zobrazíte spuštění aktivit pro každé spuštění kanálu. Zahrnuté sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název aktivity | Název aktivity uvnitř kanálu. |
| Typ aktivity | Typ aktivity, například Copy, HDInsightSpark, HDInsightHive atd. |
| Spustit spuštění | Datum a čas spuštění aktivity (MM/DD/RRRR, HH: MM: SS dop./odp.) |
| Trvání | Doba trvání běhu (HH: MM: SS) |
| Stav | Úspěch, úspěšně dokončeno, probíhá |
| Vstup | Pole JSON popisující vstupy aktivity |
| Výstup | Pole JSON popisující výstupy aktivity |
| Chyba | Chyba spuštění aktivity (pokud/any) |

![Monitorování spuštění aktivit](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Abyste mohli aktualizovat seznam spuštěných kanálů a aktivit, musíte kliknout na ikonu **aktualizovat** nahoře. Automatická aktualizace se v tuto chvíli nepodporuje.

![Aktualizovat](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Vyberte datovou továrnu, kterou chcete monitorovat.
Najeďte myší na ikonu **Data Factory** v levém horním rohu. Kliknutím na ikonu ' šipka ' zobrazíte seznam předplatných a datových továrn Azure, které můžete monitorovat.

![Výběr datové továrny](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Konfigurace zobrazení seznamu

### <a name="apply-rich-ordering-and-filtering"></a>Použití bohatého řazení a filtrování

Pořadí spuštění kanálu v příkazu DESC/ASC spustíte spuštěním příkazu Start a filtrování se spustí pomocí následujících sloupců:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu. Mezi možnosti patří rychlé filtry za "posledních 24 hodin", "minulý týden", "posledních 30 dnů" nebo vyberte vlastní datum a čas. |
| Spustit spuštění | Datum a čas spuštění kanálu |
| Stav spuštění | Spuštění filtru podle stavu – úspěšné, neúspěšné, probíhá |

![Filtr](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Přidat nebo odebrat sloupce
Klikněte pravým tlačítkem na záhlaví zobrazení seznamu a vyberte sloupce, které se mají zobrazit v seznamu.

![Sloupce](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Upravit šířku sloupců
Zvětšení a zmenšení šířky sloupců v zobrazení seznamu přesunutím ukazatele myši na záhlaví sloupce

## <a name="promote-user-properties-to-monitor"></a>Zvýšení úrovně vlastností uživatele na monitorování

Vlastnost aktivity kanálu můžete povýšit jako vlastnost uživatele, aby se stala entitou, kterou můžete monitorovat. Můžete například propagovat vlastnosti **zdroje** a **cíle** aktivity kopírování v kanálu jako vlastnosti uživatele. Můžete také vybrat možnost **automaticky generovat** a vygenerovat vlastnosti **zdrojového** a **cílového** uživatele aktivity kopírování.

![Vytvořit vlastnosti uživatele](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Jako vlastnosti uživatele můžete propagovat až 5 vlastností aktivity kanálu.

Po vytvoření vlastností uživatele je můžete monitorovat v zobrazeních seznamu monitorování. Pokud je zdrojem aktivity kopírování název tabulky, můžete monitorovat název zdrojové tabulky jako sloupec v zobrazení seznamu spuštění aktivit.

![Seznam spuštění aktivit bez vlastností uživatele](media/monitor-visually/monitor-user-properties-image2.png)

![Přidat sloupce pro vlastnosti uživatele do seznamu spuštění aktivit](media/monitor-visually/monitor-user-properties-image3.png)

![Seznam spuštění aktivit se sloupci pro vlastnosti uživatele](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Opětovné spuštění aktivit v rámci kanálu

V rámci kanálu teď můžete znovu spustit aktivity. Klikněte na **Zobrazit spuštění aktivit** a v kanálu vyberte aktivitu, ze které se má kanál znovu spustit.

![Zobrazení spuštění aktivit](media/monitor-visually/rerun-activities-image1.png)

![Vybrat spuštění aktivit](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Zobrazit historii opětovného spuštění

V zobrazení seznamu můžete zobrazit historii opětovného spuštění všech spuštění kanálu.

![Zobrazení historie](media/monitor-visually/rerun-history-image1.png)

Můžete si také zobrazit historii opětovného spuštění určitého běhu kanálu.

![Zobrazení historie spuštění kanálu](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Zobrazení Ganttova diagramu

Použijte zobrazení Ganttova diagramu k rychlé vizualizaci vašich kanálů a spuštění aktivit. Můžete se podívat na zobrazení Ganttova diagramu na kanál nebo skupinu pomocí poznámek nebo značek, které jste vytvořili ve svých kanálech.

![Ganttův diagram](media/monitor-visually/gantt1.png)

![Poznámky k Ganttově diagramu](media/monitor-visually/gantt2.png)

Délka pruhu informuje o délce trvání kanálu. Můžete také kliknout na pruh a zobrazit další podrobnosti.

![Doba trvání Ganttova diagramu](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Řízená prohlídky
Klikněte na ikonu informace dole vlevo a pak klikněte na prohlídky s asistencí. získáte tak podrobné pokyny, jak monitorovat spuštění vašeho kanálu a aktivity.

![Řízená prohlídky](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Zpětná vazba
Klikněte na ikonu zpětné vazby a sdělte nám svůj názor na různé funkce nebo problémy, na které se můžete zajímat.

![Zpětná vazba](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Upozornění

Upozornění na podporované metriky můžete vyvolávat v Data Factory. Začněte tím, že vyberete **monitor – výstrahy > & metriky** na stránce monitorování Data Factory.

![](media/monitor-visually/alerts01.png)

Pokud chcete tuto funkci seznámit a předvedení této funkce, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Vytvořit výstrahy

1.  Kliknutím na **nové pravidlo** výstrahy vytvořte novou výstrahu.

    ![](media/monitor-visually/alerts02.png)

1.  Zadejte název pravidla a vyberte **závažnost**výstrahy.

    ![](media/monitor-visually/alerts03.png)

1.  Vyberte kritéria výstrahy.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Nakonfigurujte logiku výstrah. Pro vybranou metriku můžete vytvořit výstrahu pro všechny kanály a odpovídající aktivity. Můžete také vybrat konkrétní typ aktivity, název aktivity, název kanálu nebo typ selhání.

    ![](media/monitor-visually/alerts06.png)

1.  Konfigurace oznámení o **e-mailu, SMS/nabízení a hlasu** pro výstrahu. Vytvořte nebo vyberte existující **skupinu akcí** pro oznámení výstrah.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Vytvořte pravidlo výstrahy.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Další postup

Další informace o monitorování a správě kanálů najdete v článku [programové monitorování a Správa kanálů](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) .
