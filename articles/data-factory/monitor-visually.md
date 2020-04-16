---
title: Vizuální monitorování Azure Data Factory
description: Přečtěte si, jak vizuálně monitorovat datové továrny Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 40b1b8d040c4b3ea76372920f88551fba35c5f26
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419438"
---
# <a name="visually-monitor-azure-data-factory"></a>Vizuální monitorování Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Po vytvoření a publikování kanálu v Azure Data Factory ho můžete přidružit k aktivační události nebo ručně zahájit ad hoc spuštění. Všechny vaše kanály běží nativně v uživatelském prostředí Azure Data Factory. Chcete-li otevřít prostředí monitorování, vyberte dlaždici **Monitor & Správa** v okně datové továrny na webu Azure [Portal](https://portal.azure.com/). Pokud jste již v uživatelském panelu ADF, klikněte na ikonu **Monitor** na levém postranním panelu.

Všechny spuštění datové továrny jsou zobrazeny v místním časovém pásmu prohlížeče. Pokud změníte časové pásmo, všechna pole data a času se přichytí k tomu, které jste vybrali.

## <a name="monitor-pipeline-runs"></a>Monitorování spuštění kanálu

Výchozí zobrazení monitorování je seznam spuštění kanálu ve vybraném časovém období. Zobrazí se následující sloupce:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Název potrubí |
| Akce | Ikony, které umožňují zobrazit podrobnosti o aktivitě, zrušit nebo znovu spustit kanál |
| Spustit start | Počáteční datum a čas spuštění kanálu (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Doba trvání | Doba běhu (HH:MM:SS) |
| Spuštěno | Název aktivační události, která spustila kanál |
| Status | **Nezdařilo**se , **proběhlo**úspěšně , **probíhá**, **zrušeno**nebo **zařazeno do fronty.** |
| Poznámky | Filtrovatelné značky přidružené k kanálu  |
| Parametry | Parametry pro spuštění kanálu (dvojice název/hodnota) |
| Chyba | Pokud se potrubí nezdařilo, chyba spuštění |
| ID spuštění | ID spuštění kanálu |

![Zobrazení seznamu pro sledování spuštění kanálu](media/monitor-visually/pipeline-runs.png)

Chcete-li aktualizovat seznam kanálů a spuštění aktivit, musíte ručně vybrat tlačítko **Aktualizovat.** Automatická aktualizace není aktuálně podporována.

![Tlačítko Aktualizovat](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorování spuštění aktivit

Chcete-li zobrazit spuštění aktivity pro každé spuštění kanálu, vyberte ikonu **Zobrazit spuštění aktivity** ve sloupci **Akce.** Zobrazení seznamu zobrazuje spuštění aktivity, které odpovídají každému spuštění kanálu.

| **Název sloupce** | **Popis** |
| --- | --- |
| Název aktivity | Název aktivity uvnitř kanálu |
| Typ aktivity | Typ aktivity, například **Copy**, **ExecuteDataFlow**nebo **AzureMLExecutePipeline** |
| Akce | Ikony, které umožňují zobrazit informace o vstupu JSON, výstupní informace JSON nebo podrobné monitorovací prostředí specifické pro danou aktivitu | 
| Spustit start | Počáteční datum a čas spuštění aktivity (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Doba trvání | Doba běhu (HH:MM:SS) |
| Status | **Nezdařilo**se , **proběhlo úspěšně,** **probíhá**nebo **bylo zrušeno.** |
| Integrační běh | Který integrační runtime aktivita byla spuštěna na |
| Vlastnosti uživatele | Uživatelem definované vlastnosti aktivity |
| Chyba | Pokud se aktivita nezdařila, chyba spuštění |
| ID spuštění | ID spuštění aktivity |

![Zobrazení seznamu pro spuštění aktivity monitorování](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Zvýšení úrovně vlastností uživatelů ke sledování

Propagujte libovolnou vlastnost aktivity kanálu jako vlastnost uživatele tak, aby se stala entitou, kterou sledujete. Můžete například propagovat **vlastnosti zdroja** a **cíle** aktivity kopírování v kanálu jako vlastnosti uživatele. Vyberte **Automatické generování,** chcete-li generovat vlastnosti **uživatele zdrojového** a **cílového** pro aktivitu kopírování.

![Vytvoření uživatelských vlastností](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Jako vlastnosti uživatele můžete propagovat až pět vlastností aktivity kanálu.

Po vytvoření vlastností uživatele je můžete sledovat v zobrazeních seznamu monitorování. Pokud je zdrojem aktivity kopírování název tabulky, můžete sledovat název zdrojové tabulky jako sloupec v zobrazení seznamu pro spuštění aktivity.

![Seznam spuštění aktivit bez vlastností uživatele](media/monitor-visually/monitor-user-properties-image2.png)

![Přidání sloupců pro vlastnosti uživatele do seznamu spuštění aktivity](media/monitor-visually/monitor-user-properties-image3.png)

![Seznam spuštění aktivit se sloupci pro vlastnosti uživatele](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Konfigurace zobrazení seznamu

### <a name="order-and-filter"></a>Objednávka a filtr

Přepněte, zda bude spuštění kanálu sestupně nebo vzestupně podle času spuštění. Kanál filtru běží pomocí následujících sloupců:

| **Název sloupce** | **Popis** |
| --- | --- |
| Název kanálu | Filtrujte podle názvu kanálu. |
| Spustit start |  Určete časový rozsah zobrazených spuštění kanálu. Možnosti zahrnují rychlé filtry pro **posledních 24 hodin**, **Minulý týden**a **Posledních 30 dní** nebo pro výběr vlastního data a času. |
| Stav spuštění | Filtr běží podle stavu: **Proběhlo úspěšné**, **Nezdařilo se**, **Zařazeno do fronty**, **Zrušeno**nebo **Probíhá**. |
| Poznámky | Filtrování podle značek použitých pro každý kanál |
| Běží | Filtrovat, zda chcete zobrazit reran potrubí |

![Možnosti filtrování](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Přidání nebo odebrání sloupců
Klikněte pravým tlačítkem myši na záhlaví zobrazení seznamu a vyberte sloupce, které se mají zobrazit v zobrazení seznamu.

![Možnosti pro sloupce](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Úprava šířky sloupců
Zvětšete a zmenšete šířky sloupců v zobrazení seznamu tak, že najedete na záhlaví sloupce.

## <a name="rerun-activities-inside-a-pipeline"></a>Opětovné spuštění aktivit uvnitř kanálu

Můžete znovu spustit aktivity uvnitř kanálu. Vyberte **Zobrazit spuštění aktivity**a vyberte aktivitu v kanálu, od kterého chcete kanál znovu spustit.

![Zobrazení spuštění aktivit](media/monitor-visually/rerun-activities-image1.png)

![Výběr spuštění aktivity](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Znovu spustit z neúspěšné aktivity

Pokud se aktivita nezdaří, časový čas nebo je zrušena, můžete znovu spustit kanál z této neúspěšné aktivity výběrem **znovu spustit z neúspěšné aktivity**.

![Opětovné spuštění neúspěšné aktivity](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Zobrazit historii opětovného spuštění

Můžete zobrazit historii opětovného spuštění pro všechny spuštění kanálu v zobrazení seznamu.

![Zobrazení historie](media/monitor-visually/rerun-history-image1.png)

Můžete také zobrazit historii opětovného spuštění pro konkrétní spuštění kanálu.

![Zobrazení historie pro spuštění kanálu](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Ganttova zobrazení

Pomocí ganttových zobrazení můžete rychle vizualizovat kanály a spuštění aktivit.

![Příklad Ganttova diagramu](media/monitor-visually/gantt1.png)

Zobrazení Ganttova seznamu podle kanálu nebo skupiny se můžete podívat podle anotací nebo značek, které jste vytvořili v kanálech.

![Poznámky Ganttova diagramu](media/monitor-visually/gantt2.png)

Délka pruhu informuje o délce potrubí. Můžete také vybrat panel, abyste viděli další podrobnosti.

![Doba trvání Ganttova diagramu](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Prohlídky
V levém dolním rohu vyberte ikonu **Informace.** Pak vyberte **prohlídky s průvodcem,** abyste získali podrobné pokyny, jak sledovat spuštění kanálu a aktivity.

![Prohlídky](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Výstrahy

Můžete zvýšit upozornění na podporované metriky v Datové továrně. Chcete-li **začít,** > vyberte možnost**Výstrahy & metriky** sledování na stránce Sledování datové továrny.

![Stránka Sledování datové továrny](media/monitor-visually/alerts01.png)

Sedmiminutový úvod a ukázku této funkce najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Vytváření upozornění

1.  Chcete-li vytvořit novou výstrahu, vyberte **nové pravidlo výstrahy.**

    ![Tlačítko Nové pravidlo výstrahy](media/monitor-visually/alerts02.png)

1.  Zadejte název pravidla a vyberte závažnost výstrahy.

    ![Pole pro název a závažnost pravidla](media/monitor-visually/alerts03.png)

1.  Vyberte kritéria výstrahy.

    ![Kolonka pro cílová kritéria](media/monitor-visually/alerts04.png)

    ![Seznam kritérií](media/monitor-visually/alerts05.png)

1.  Nakonfigurujte logiku výstrah. Můžete vytvořit výstrahu pro vybranou metriku pro všechny kanály a odpovídající aktivity. Můžete také vybrat konkrétní typ aktivity, název aktivity, název kanálu nebo typ selhání.

    ![Možnosti konfigurace logiky výstrah](media/monitor-visually/alerts06.png)

1.  Nakonfigurujte pro výstrahu e-mailová, SMS, nabízená a hlasová oznámení. Vytvořte skupinu akcí nebo zvolte existující skupinu pro upozornění.

    ![Možnosti konfigurace oznámení](media/monitor-visually/alerts07.png)

    ![Možnosti přidání oznámení](media/monitor-visually/alerts08.png)

1.  Vytvořte pravidlo výstrahy.

    ![Možnosti vytvoření pravidla výstrahy](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Další kroky

Další informace o monitorování a správě kanálů najdete v článku [Monitorování a správa kanálů programově.](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)
