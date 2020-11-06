---
title: Vizuální monitorování služby Azure Data Factory
description: Naučte se vizuálně monitorovat datové továrny Azure.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ecb066c7269217af3f8cc84e0f59ab29b4b39a9e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331467"
---
# <a name="visually-monitor-azure-data-factory"></a>Vizuální monitorování služby Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Po vytvoření a publikování kanálu v Azure Data Factory ho můžete přidružit k triggeru nebo ručně spustit ad hoc spuštění. Můžete monitorovat všechny spuštěné kanály nativně v prostředí Azure Data Factory koncového uživatele. Chcete-li otevřít monitorování, vyberte dlaždici **monitorování & spravovat** v okně objektu pro vytváření dat [Azure Portal](https://portal.azure.com/). Pokud už jste v uživatelském prostředí ADF, klikněte na ikonu **monitorování** na levém bočním panelu.

Ve výchozím nastavení se všechna spuštění objektu pro vytváření dat zobrazují v místním časovém pásmu prohlížeče. Změníte-li časové pásmo, všechna pole data a času se přiřadí k vybranému poli.

## <a name="monitor-pipeline-runs"></a>Monitorování spuštění kanálu

Výchozím zobrazením monitorování je seznam aktivovaných spuštění kanálu ve vybraném časovém období. Můžete změnit časový rozsah a filtrovat podle stavu, názvu kanálu nebo poznámky. Najeďte myší na konkrétní běh kanálu, abyste získali akce specifické pro spuštění, jako je třeba opětovné spuštění a sestava o spotřebě.

![Zobrazení seznamu pro monitorování spuštění kanálu](media/monitor-visually/pipeline-runs.png)

Mřížka spuštění kanálu obsahuje následující sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu |
| Spustit spuštění | Počáteční datum a čas pro spuštění kanálu (MM/DD/RRRR, HH: MM: SS dop./odp.) |
| Konec běhu | Koncové datum a čas spuštění kanálu (MM/DD/RRRR, HH: MM: SS AM/PM) |
| Doba trvání | Doba trvání běhu (HH: MM: SS) |
| Aktivoval | Název triggeru, který spustil kanál |
| Status | **Selhání** , **úspěšné** , **probíhající** , **zrušeno** nebo zařazení do **fronty** |
| Poznámky | Filtrovatelné značky přidružené k kanálu  |
| Parametry | Parametry pro spuštění kanálu (páry název/hodnota) |
| Chyba | Pokud se kanál nezdařil, Chyba spuštění |
| ID spuštění | ID spuštění kanálu |

Chcete-li aktualizovat seznam spuštěných kanálů a aktivit, je nutné ručně vybrat tlačítko **aktualizovat** . Služba AutoRefresh není aktuálně podporována.

![Tlačítko Aktualizovat](media/monitor-visually/refresh.png)

Chcete-li zobrazit výsledky běhu ladění, vyberte kartu **ladění** .

![Vyberte ikonu Zobrazit aktivní ladicí běhy.](media/iterative-development-debugging/view-debug-runs.png)

## <a name="monitor-activity-runs"></a>Monitorování spuštění aktivit

Pokud chcete získat podrobné zobrazení jednotlivých spuštění určitého kanálu, klikněte na název kanálu.

![Zobrazení spuštění aktivit](media/monitor-visually/view-activity-runs.png)

V zobrazení seznamu se zobrazí spuštění aktivit, která odpovídají každému spuštění kanálu. Najeďte myší na konkrétní spuštění aktivity a získejte informace specifické pro spuštění, jako je vstup JSON, výstup JSON a podrobné prostředí pro monitorování konkrétní aktivity.

![Zobrazení seznamu pro monitorování spuštění aktivit](media/monitor-visually/activity-runs.png)

| **Název sloupce** | **Popis** |
| --- | --- |
| Název aktivity | Název aktivity uvnitř kanálu |
| Typ aktivity | Typ aktivity, například **copy** , **ExecuteDataFlow** nebo **AzureMLExecutePipeline** |
| Akce | Ikony, které vám umožní zobrazit vstupní informace JSON, informace o výstupu JSON nebo podrobné prostředí pro monitorování konkrétní aktivity | 
| Spustit spuštění | Počáteční datum a čas pro spuštění aktivit (MM/DD/RRRR, HH: MM: SS dop./odp.) |
| Doba trvání | Doba trvání běhu (HH: MM: SS) |
| Status | **Došlo** **k** chybě, **úspěšné** , probíhající nebo **zrušené** |
| Integration Runtime | Který Integration Runtime aktivity běžely |
| Vlastnosti uživatele | Uživatelem definované vlastnosti aktivity |
| Chyba | Pokud se aktivita nezdařila, Chyba spuštění |
| ID spuštění | ID spuštění aktivity |

Pokud se aktivita nezdařila, můžete zobrazit podrobnou chybovou zprávu kliknutím na ikonu ve sloupci chyba. 

![Zobrazení seznamu pro monitorování spuštění aktivit](media/monitor-visually/activity-run-error.png)

### <a name="promote-user-properties-to-monitor"></a>Zvýšení úrovně vlastností uživatele na monitorování

Zvyšte úroveň vlastnosti aktivity kanálu jako vlastnost uživatele tak, aby se stala entitou, kterou sledujete. Můžete například propagovat vlastnosti **zdroje** a **cíle** aktivity kopírování v kanálu jako vlastnosti uživatele.

> [!NOTE]
> Jako vlastnosti uživatele můžete propagovat až pět vlastností aktivity kanálu.

![Vytvořit vlastnosti uživatele](media/monitor-visually/promote-user-properties.png)

Po vytvoření vlastností uživatele je můžete monitorovat v zobrazeních seznamu monitorování.

![Přidat sloupce pro vlastnosti uživatele do seznamu spuštění aktivit](media/monitor-visually/choose-user-properties.png)

 Pokud je zdrojem aktivity kopírování název tabulky, můžete monitorovat název zdrojové tabulky jako sloupec v zobrazení seznamu pro spuštění aktivit.

![Seznam spuštění aktivit se sloupci pro vlastnosti uživatele](media/monitor-visually/view-user-properties.png)

## <a name="rerun-pipelines-and-activities"></a>Znovu spustit kanály a aktivity

Chcete-li znovu spustit kanál, který byl dříve spuštěn od spuštění, najeďte myší na konkrétní spuštění kanálu a vyberte možnost **znovu spustit**. Pokud vyberete více kanálů, můžete k jejich spuštění použít tlačítko **Spustit znovu** .

![Opětovné spuštění kanálu](media/monitor-visually/rerun-pipeline.png)

Pokud chcete spustit spuštění znovu v určitém okamžiku, můžete to provést ze zobrazení spuštění aktivit. Vyberte aktivitu, ze které chcete začít, a vyberte možnost **znovu spustit z aktivity**. 

![Znovu spustit běh aktivity](media/monitor-visually/rerun-activity.png)

### <a name="rerun-from-failed-activity"></a>Znovu spustit z neúspěšné aktivity

Pokud dojde k selhání aktivity, vypršení časového limitu nebo zrušení, můžete znovu spustit kanál z této neúspěšné aktivity výběrem možnosti **znovu spustit z neúspěšné aktivity**.

![Znovu spustit neúspěšnou aktivitu](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Zobrazit historii opětovného spuštění

V zobrazení seznamu můžete zobrazit historii opětovného spuštění všech spuštění kanálu.

![Zobrazení historie](media/monitor-visually/rerun-history-1.png)

Můžete si také zobrazit historii opětovného spuštění určitého běhu kanálu.

![Zobrazení historie spuštění kanálu](media/monitor-visually/view-rerun-history.png)

## <a name="monitor-consumption"></a>Monitorovat spotřebu

Prostředky spotřebované spuštěním kanálu můžete zobrazit kliknutím na ikonu spotřebovat vedle spuštění. 

![Snímek obrazovky, který ukazuje, kde vidíte prostředky spotřebované kanálem.](media/monitor-visually/monitor-consumption-1.png)

Kliknutím na ikonu otevřete sestavu využití prostředků používaných tímto spuštěním kanálu. 

![Monitorovat spotřebu](media/monitor-visually/monitor-consumption-2.png)

Tyto hodnoty můžete připojit do [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/details/data-factory/) a odhadnout náklady na spuštění kanálu. Další informace o cenách Azure Data Factory najdete v tématu [Principy cen](pricing-concepts.md).

> [!NOTE]
> Tyto hodnoty vracené cenové kalkulačkou jsou odhad. Neodráží přesnou částku, kterou se vám bude účtovat Azure Data Factory 

## <a name="gantt-views"></a>Zobrazení Ganttova diagramu

Ganttův diagram je zobrazení, které umožňuje zobrazit historii spuštění v časovém intervalu. Přepnutím do zobrazení Ganttova diagramu se zobrazí všechna spuštění kanálu seskupená podle názvu zobrazená jako pruhy vzhledem k době trvání běhu. Můžete také seskupit podle poznámek nebo značek, které jste vytvořili v kanálu. Zobrazení Ganttův diagram je také k dispozici na úrovni běhu aktivit.

![Příklad Ganttova diagramu](media/monitor-visually/select-gantt.png)

Délka pruhu informuje o délce trvání kanálu. Můžete také vybrat pruh a zobrazit další podrobnosti.

![Doba trvání Ganttova diagramu](media/monitor-visually/view-gantt-run.png)

## <a name="alerts"></a>Výstrahy

Upozornění na podporované metriky můžete vyvolávat v Data Factory. Začněte tím, že vyberete **monitorovat**  >  **výstrahy & metriky** na stránce monitorování Data Factory.

![Stránka monitorování objektu pro vytváření dat](media/monitor-visually/start-page.png)

Pokud chcete tuto funkci seznámit a předvedení této funkce, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Vytváření upozornění

1.  Vyberte **nové pravidlo výstrahy** a vytvořte novou výstrahu.

    ![Tlačítko nové pravidlo upozornění](media/monitor-visually/new-alerts.png)

1.  Zadejte název pravidla a vyberte Závažnost výstrahy.

    ![Pole pro název pravidla a závažnost](media/monitor-visually/name-and-severity.png)

1.  Vyberte kritéria výstrahy.

    ![Box pro kritéria cíle](media/monitor-visually/add-criteria-1.png)

    ![Snímek obrazovky, který ukazuje, kde vyberete jednu metriku pro nastavení podmínky upozornění.](media/monitor-visually/add-criteria-2.png)

    ![Seznam kritérií](media/monitor-visually/add-criteria-3.png)

    Můžete vytvářet upozornění na různé metriky, včetně těch, které se týkají počtu a velikosti entit ADF, spuštění aktivit, kanálu a triggeru, Integration Runtime (IR) využití procesoru/paměti, počtu uzlů/fronty, a také pro spouštění balíčků SSIS a operace SSIS IR pro spuštění/zastavení.

1.  Nakonfigurujte logiku výstrah. Pro vybranou metriku můžete vytvořit výstrahu pro všechny kanály a odpovídající aktivity. Můžete také vybrat konkrétní typ aktivity, název aktivity, název kanálu nebo typ selhání.

    ![Možnosti konfigurace logiky výstrah](media/monitor-visually/alert-logic.png)

1.  Konfigurace e-mailu, SMS, nabízených oznámení a hlasových oznámení pro výstrahu. Vytvořte skupinu akcí nebo pro oznámení výstrah vyberte některou z existujících.

    ![Možnosti konfigurace oznámení](media/monitor-visually/configure-notification-1.png)

    ![Možnosti pro přidání oznámení](media/monitor-visually/configure-notification-2.png)

1.  Vytvořte pravidlo výstrahy.

    ![Možnosti pro vytvoření pravidla výstrahy](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Další kroky

Další informace o monitorování a správě kanálů najdete v článku o sledování [a správě kanálů programově](./monitor-programmatically.md) .