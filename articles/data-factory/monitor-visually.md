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
ms.openlocfilehash: 48373c9ffc9146b6e62b62fb7d7fe10d571ce27f
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638104"
---
# <a name="visually-monitor-azure-data-factory"></a>Vizuální monitorování služby Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Po vytvoření a publikování kanálu v Azure Data Factory ho můžete přidružit k triggeru nebo ručně spustit ad hoc spuštění. Můžete monitorovat všechny spuštěné kanály nativně v prostředí Azure Data Factory koncového uživatele. Chcete-li otevřít monitorování, vyberte dlaždici **monitorování & spravovat** v okně objektu pro vytváření dat [Azure Portal](https://portal.azure.com/). Pokud už jste v uživatelském prostředí ADF, klikněte na ikonu **monitorování** na levém bočním panelu.

Všechna spuštění objektu pro vytváření dat se zobrazí v místním časovém pásmu prohlížeče. Změníte-li časové pásmo, všechna pole data a času se přiřadí k vybranému poli.

## <a name="monitor-pipeline-runs"></a>Monitorování spuštění kanálu

Výchozí zobrazení monitorování je seznam spuštění kanálu ve vybraném časovém období. Zobrazí se následující sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název kanálu |
| Akce | Ikony, které umožňují zobrazit podrobnosti o aktivitě, zrušit nebo spustit kanál znovu |
| Spustit spuštění | Počáteční datum a čas pro spuštění kanálu (MM/DD/RRRR, HH: MM: SS dop./odp.) |
| Doba trvání | Doba trvání běhu (HH: MM: SS) |
| Aktivoval | Název triggeru, který spustil kanál |
| Status | **Selhání** , **úspěšné** , **probíhající** , **zrušeno** nebo zařazení do **fronty** |
| Poznámky | Filtrovatelné značky přidružené k kanálu  |
| Parametry | Parametry pro spuštění kanálu (páry název/hodnota) |
| Chybová | Pokud se kanál nezdařil, Chyba spuštění |
| ID spuštění | ID spuštění kanálu |

![Zobrazení seznamu pro monitorování spuštění kanálu](media/monitor-visually/pipeline-runs.png)

Chcete-li aktualizovat seznam spuštěných kanálů a aktivit, je nutné ručně vybrat tlačítko **aktualizovat** . Služba AutoRefresh není aktuálně podporována.

![Tlačítko Aktualizovat](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorování spuštění aktivit

Pokud chcete zobrazit spuštění aktivit pro každé spuštění kanálu, vyberte ve sloupci **Akce** ikonu **Zobrazit spuštění aktivit** . V zobrazení seznamu se zobrazí spuštění aktivit, která odpovídají každému spuštění kanálu.

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
| Chybová | Pokud se aktivita nezdařila, Chyba spuštění |
| ID spuštění | ID spuštění aktivity |

![Zobrazení seznamu pro monitorování spuštění aktivit](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Zvýšení úrovně vlastností uživatele na monitorování

Zvyšte úroveň vlastnosti aktivity kanálu jako vlastnost uživatele tak, aby se stala entitou, kterou sledujete. Můžete například propagovat vlastnosti **zdroje** a **cíle** aktivity kopírování v kanálu jako vlastnosti uživatele. Vyberte možnost **automaticky generovat** a vygenerujte vlastnosti **zdrojového** a **cílového** uživatele pro aktivitu kopírování.

![Vytvořit vlastnosti uživatele](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Jako vlastnosti uživatele můžete propagovat až pět vlastností aktivity kanálu.

Po vytvoření vlastností uživatele je můžete monitorovat v zobrazeních seznamu monitorování. Pokud je zdrojem aktivity kopírování název tabulky, můžete monitorovat název zdrojové tabulky jako sloupec v zobrazení seznamu pro spuštění aktivit.

![Seznam spuštění aktivit bez vlastností uživatele](media/monitor-visually/monitor-user-properties-image2.png)

![Přidat sloupce pro vlastnosti uživatele do seznamu spuštění aktivit](media/monitor-visually/monitor-user-properties-image3.png)

![Seznam spuštění aktivit se sloupci pro vlastnosti uživatele](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Konfigurace zobrazení seznamu

### <a name="order-and-filter"></a>Pořadí a filtr

Přepněte na to, jestli se spuštění kanálu sestaví sestupně nebo vzestupně podle času spuštění. Filtry se spouštějí pomocí následujících sloupců:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Filtrovat podle názvu kanálu. |
| Spustit spuštění |  Určete časový rozsah zobrazených spuštění kanálu. Mezi možnosti patří rychlé filtry za **posledních 24 hodin** , **minulý týden** a **posledních 30 dnů** nebo pro výběr vlastního data a času. |
| Stav spuštění | Filtry se spouští podle stavu: **úspěšné** , **neúspěšné** , **zařazené do fronty** , **zrušené** nebo **probíhající** . |
| Poznámky | Filtrovat podle značek použitých u jednotlivých kanálů |
| Běží | Filtrovat, jestli chcete zobrazit kanály Reran |

![Možnosti pro filtrování](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Přidání nebo odebrání sloupců
Klikněte pravým tlačítkem na záhlaví zobrazení seznamu a vyberte sloupce, které se mají zobrazit v zobrazení seznamu.

![Možnosti pro sloupce](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Upravit šířku sloupců
Zvětšení a zmenšení šířky sloupců v zobrazení seznamu přesunutím ukazatele myši na záhlaví sloupce.

## <a name="rerun-activities-inside-a-pipeline"></a>Opětovné spuštění aktivit v rámci kanálu

V rámci kanálu můžete znovu spustit aktivity. Vyberte **Zobrazit spuštění aktivit** a potom v kanálu vyberte aktivitu, ze které chcete svůj kanál znovu spustit.

![Zobrazení spuštění aktivit](media/monitor-visually/rerun-activities-image1.png)

![Vybrat spuštění aktivit](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Znovu spustit z neúspěšné aktivity

Pokud dojde k selhání aktivity, vypršení časového limitu nebo zrušení, můžete znovu spustit kanál z této neúspěšné aktivity výběrem možnosti **znovu spustit z neúspěšné aktivity** .

![Znovu spustit neúspěšnou aktivitu](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Zobrazit historii opětovného spuštění

V zobrazení seznamu můžete zobrazit historii opětovného spuštění všech spuštění kanálu.

![Zobrazení historie](media/monitor-visually/rerun-history-image1.png)

Můžete si také zobrazit historii opětovného spuštění určitého běhu kanálu.

![Zobrazení historie spuštění kanálu](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>Monitorovat spotřebu

Prostředky spotřebované spuštěním kanálu můžete zobrazit kliknutím na ikonu spotřebovat vedle spuštění. 

![Snímek obrazovky, který ukazuje, kde vidíte prostředky spotřebované kanálem.](media/monitor-visually/monitor-consumption-1.png)

Kliknutím na ikonu otevřete sestavu využití prostředků používaných tímto spuštěním kanálu. 

![Monitorovat spotřebu](media/monitor-visually/monitor-consumption-2.png)

Tyto hodnoty můžete připojit do [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/details/data-factory/) a odhadnout náklady na spuštění kanálu. Další informace o cenách Azure Data Factory najdete v tématu [Principy cen](pricing-concepts.md).

> [!NOTE]
> Tyto hodnoty vracené cenové kalkulačkou jsou odhad. Neodráží přesnou částku, kterou se vám bude účtovat Azure Data Factory 

## <a name="gantt-views"></a>Zobrazení Ganttova diagramu

Použijte zobrazení Ganttova diagramu k rychlé vizualizaci vašich kanálů a spuštění aktivit.

![Příklad Ganttova diagramu](media/monitor-visually/gantt1.png)

Můžete se podívat na zobrazení Ganttova diagramu na kanál nebo skupinu pomocí poznámek nebo značek, které jste vytvořili ve svých kanálech.

![Poznámky k Ganttově diagramu](media/monitor-visually/gantt2.png)

Délka pruhu informuje o délce trvání kanálu. Můžete také vybrat pruh a zobrazit další podrobnosti.

![Doba trvání Ganttova diagramu](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Řízená prohlídky
Vyberte ikonu **informace** v levém dolním rohu. Pak vyberte **prohlídky s asistencí** a Získejte podrobné pokyny, jak monitorovat spuštění kanálu a aktivity.

![Řízená prohlídky](media/monitor-visually/guided-tours.png)

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