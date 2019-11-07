---
title: Vizuální monitorování datových továren Azure
description: Naučte se vizuálně monitorovat datové továrny Azure.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 7b79fd9c87e97e624cce567b57c1c65fefcc151e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684639"
---
# <a name="visually-monitor-azure-data-factories"></a>Vizuální monitorování datových továren Azure
Azure Data Factory je cloudová služba pro integraci dat. Můžete ho použít k vytvoření pracovních postupů řízených daty v cloudu pro orchestraci a automatizaci přesunu dat a transformaci dat. Pomocí Azure Data Factory můžete:

- Vytvářet a plánovat pracovní postupy řízené daty (označované jako kanály), které dokáží ingestovat data z různorodých zdrojů dat.
- Zpracujte nebo Transformujte data pomocí výpočetních služeb, jako jsou Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics a Azure Machine Learning.
- Publikovat výstupní data do úložišť dat, jako je Azure SQL Data Warehouse, a umožnit jejich využití v aplikacích business intelligence (BI).

V tomto rychlém startu se naučíte vizuálně monitorovat Data Factory kanály, aniž byste museli psát jediný řádek kódu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="monitor-data-factory-pipelines"></a>Monitorování kanálů Data Factory

Monitorování spuštění kanálu a aktivit s jednoduchým rozhraním zobrazení seznamu. Všechna spuštění se zobrazí v místním časovém pásmu prohlížeče. Změníte-li časové pásmo, všechna pole data a času se přiřadí k vybranému poli.  

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době je uživatelské rozhraní Data Factory podporováno pouze v těchto dvou webových prohlížečích.
2. Přihlaste se k [Azure Portal](https://portal.azure.com/).
3. V Azure Portal přejdete na okno pro vytvořenou datovou továrnu. Kliknutím na dlaždici **monitorování & spravovat** spustíte Data Factory vizuální prostředí monitorování.

## <a name="monitor-pipeline-runs"></a>Monitorování spuštění kanálu
V zobrazení seznamu se zobrazí všechny spuštěné kanály pro Data Factory kanály. Zahrnuje tyto sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu |
| Akce | Jedna akce dostupná pro zobrazení spuštění aktivit |
| Spustit spuštění | Počáteční datum a čas pro spuštění kanálu (MM/DD/RRRR, HH: MM: SS dop./odp.) |
| Doba trvání | Doba trvání běhu (HH: MM: SS) |
| Aktivoval | Manuální aktivační událost nebo naplánovaná aktivační událost |
| Status | **Došlo k chybě**, **úspěšné**nebo **probíhající** |
| Parametry | Parametry pro spuštění kanálu (páry název/hodnota) |
| Chyba | Chyba spuštění kanálu (pokud existuje) |
| ID běhu | ID spuštění kanálu |

![Zobrazení seznamu pro monitorování spuštění kanálu](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Monitorování spuštění aktivit
V zobrazení seznamu se zobrazí spuštění aktivit, která odpovídají každému spuštění kanálu. Pokud chcete zobrazit spuštění aktivit pro každé spuštění kanálu, vyberte ikonu **spuštění aktivit** ve sloupci **Akce** . Zobrazení seznamu obsahuje tyto sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název aktivity | Název aktivity uvnitř kanálu |
| Typ aktivity | Typ aktivity, například **copy**, **HDInsightSpark**nebo **HDInsightHive** |
| Spustit spuštění | Počáteční datum a čas pro spuštění aktivit (MM/DD/RRRR, HH: MM: SS dop./odp.) |
| Doba trvání | Doba trvání běhu (HH: MM: SS) |
| Status | **Došlo k chybě**, **úspěšné**nebo **probíhající** |
| Vstup | Pole JSON, které popisuje vstupy aktivity |
| Výstup | Pole JSON, které popisuje výstupy aktivity |
| Chyba | Chyba spuštění aktivity (pokud existuje) |

![Zobrazení seznamu pro monitorování spuštění aktivit](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Chcete-li aktualizovat seznam spuštěných kanálů a aktivit, je nutné vybrat tlačítko **aktualizovat** v horní části. Automatická aktualizace se v tuto chvíli nepodporuje.

![Tlačítko Aktualizovat](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Vyberte datovou továrnu, kterou chcete monitorovat.
Najeďte myší na ikonu **Data Factory** v levém horním rohu. Výběrem ikony se šipkou zobrazíte seznam předplatných Azure a datových továrn, které můžete monitorovat.

![Výběr objektu pro vytváření dat](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Konfigurace zobrazení seznamu

### <a name="apply-rich-ordering-and-filtering"></a>Použití bohatého řazení a filtrování

Pořadí kanálu se spouští v příkazu DESC/ASC podle počátečního času spuštění. Filtry se spouštějí pomocí následujících sloupců:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu. Mezi možnosti patří rychlé filtry za **posledních 24 hodin**, **minulý týden**a **posledních 30 dnů**. Nebo vyberte vlastní datum a čas. |
| Spustit spuštění | Počáteční datum a čas pro spuštění kanálu. |
| Stav spuštění | Spuštění filtru podle stavu: **úspěšné**, **neúspěšné**nebo **probíhá**. |

![Možnosti pro filtrování](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Přidat nebo odebrat sloupce
Klikněte pravým tlačítkem na záhlaví zobrazení seznamu a vyberte sloupce, které se mají zobrazit v zobrazení seznamu.

![Možnosti pro sloupce](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Upravit šířku sloupců
Zvětšení a zmenšení šířky sloupců v zobrazení seznamu přesunutím ukazatele myši na záhlaví sloupce.

## <a name="promote-user-properties-to-monitor"></a>Zvýšení úrovně vlastností uživatele na monitorování

Vlastnost aktivity kanálu můžete povýšit jako vlastnost uživatele, aby se stala entitou, kterou můžete monitorovat. Můžete například propagovat vlastnosti **zdroje** a **cíle** aktivity kopírování v kanálu jako vlastnosti uživatele. Můžete také vybrat možnost **automaticky generovat** a vygenerovat vlastnosti **zdrojového** a **cílového** uživatele aktivity kopírování.

![Vytvořit vlastnosti uživatele](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Jako vlastnosti uživatele můžete propagovat až pět vlastností aktivity kanálu.

Po vytvoření vlastností uživatele je můžete monitorovat v zobrazeních seznamu monitorování. Pokud je zdrojem aktivity kopírování název tabulky, můžete monitorovat název zdrojové tabulky jako sloupec v zobrazení seznamu pro spuštění aktivit.

![Seznam spuštění aktivit bez vlastností uživatele](media/monitor-visually/monitor-user-properties-image2.png)

![Přidat sloupce pro vlastnosti uživatele do seznamu spuštění aktivit](media/monitor-visually/monitor-user-properties-image3.png)

![Seznam spuštění aktivit se sloupci pro vlastnosti uživatele](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Opětovné spuštění aktivit v rámci kanálu

V rámci kanálu teď můžete znovu spustit aktivity. Vyberte **Zobrazit spuštění aktivit**a potom v kanálu vyberte aktivitu, ze které chcete svůj kanál znovu spustit.

![Zobrazení spuštění aktivit](media/monitor-visually/rerun-activities-image1.png)

![Vybrat spuštění aktivit](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Zobrazit historii opětovného spuštění

V zobrazení seznamu můžete zobrazit historii opětovného spuštění všech spuštění kanálu.

![Zobrazení historie](media/monitor-visually/rerun-history-image1.png)

Můžete si také zobrazit historii opětovného spuštění určitého běhu kanálu.

![Zobrazení historie spuštění kanálu](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Zobrazení Ganttova diagramu

Použijte zobrazení Ganttova diagramu k rychlé vizualizaci vašich kanálů a spuštění aktivit. Můžete se podívat na zobrazení Ganttova diagramu na kanál nebo skupinu pomocí poznámek nebo značek, které jste vytvořili ve svých kanálech.

![Příklad Ganttova diagramu](media/monitor-visually/gantt1.png)

![Poznámky k Ganttově diagramu](media/monitor-visually/gantt2.png)

Délka pruhu informuje o délce trvání kanálu. Můžete také vybrat pruh a zobrazit další podrobnosti.

![Doba trvání Ganttova diagramu](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Řízená prohlídky
Vyberte ikonu **informace** v levém dolním rohu. Pak vyberte **prohlídky s asistencí** a Získejte podrobné pokyny, jak monitorovat spuštění kanálu a aktivity.

![Řízená prohlídky](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Váš názor
Vyberte ikonu **zpětné vazby** a poskytněte nám zpětnou vazbu k různým funkcím nebo jakýmkoli problémům, se kterými se můžete setkat.

![Váš názor](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Výstrahy

Upozornění na podporované metriky můžete vyvolávat v Data Factory. Začněte tím, že vyberete **Monitor** > **výstrahy & metrik** na stránce monitorování Data Factory.

![Stránka monitorování objektu pro vytváření dat](media/monitor-visually/alerts01.png)

Pokud chcete tuto funkci seznámit a předvedení této funkce, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Vytváření upozornění

1.  Vyberte **nové pravidlo výstrahy** a vytvořte novou výstrahu.

    ![Tlačítko nové pravidlo upozornění](media/monitor-visually/alerts02.png)

1.  Zadejte název pravidla a vyberte Závažnost výstrahy.

    ![Pole pro název pravidla a závažnost](media/monitor-visually/alerts03.png)

1.  Vyberte kritéria výstrahy.

    ![Box pro kritéria cíle](media/monitor-visually/alerts04.png)

    ![Seznam kritérií](media/monitor-visually/alerts05.png)

1.  Nakonfigurujte logiku výstrah. Pro vybranou metriku můžete vytvořit výstrahu pro všechny kanály a odpovídající aktivity. Můžete také vybrat konkrétní typ aktivity, název aktivity, název kanálu nebo typ selhání.

    ![Možnosti konfigurace logiky výstrah](media/monitor-visually/alerts06.png)

1.  Konfigurace e-mailu, SMS, nabízených oznámení a hlasových oznámení pro výstrahu. Vytvořte skupinu akcí nebo pro oznámení výstrah vyberte některou z existujících.

    ![Možnosti konfigurace oznámení](media/monitor-visually/alerts07.png)

    ![Možnosti pro přidání oznámení](media/monitor-visually/alerts08.png)

1.  Vytvořte pravidlo výstrahy.

    ![Možnosti pro vytvoření pravidla výstrahy](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Další kroky

Další informace o monitorování a správě kanálů najdete v článku o sledování [a správě kanálů programově](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) .
