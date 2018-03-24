---
title: Monitorování a Správa kanálů data - Azure | Microsoft Docs
description: Naučte se používat monitorování a správu aplikace ke sledování a správě Azure data Factory a kanály.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5aae5057cccdf8dfbe2a42a39da5b9e70b9166f9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorování a Správa kanálů služby Azure Data Factory pomocí monitorování a správy aplikace
> [!div class="op_single_selector"]
> * [Použití Azure portal nebo Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Pomocí monitorování a správu aplikací](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [sledování a Správa kanálů služby Data Factory v verze 2](../monitor-visually.md).

Tento článek popisuje, jak používat monitorování a správu aplikace monitorovat, spravovat a ladit kanály Data Factory. Nabízí taky informace o tom, jak vytvářet výstrahy, které dostanete upozornění na selhání. Můžete začít s pomocí následujícím videem aplikace:

> [!NOTE]
> Uživatelské rozhraní zobrazené na videu nemusí přesně odpovídat vidět na portálu. Je mírně starší, ale koncepty zůstávají stejné. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Spustit monitorování a správu aplikací
Chcete-li spustit aplikaci monitorování a správa, klikněte na tlačítko **monitorování a správa** na dlaždici **Data Factory** okno objektu pro vytváření dat.

![Monitorování dlaždice na domovské stránce objektu pro vytváření dat](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Měli byste vidět monitorování a správu aplikací, otevřete v samostatném okně.  

![Monitorování a správa aplikací](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Pokud se zobrazí, že webový prohlížeč zasekl ve fázi "autorizace …", zrušte **blokovat soubory cookie třetích stran a data lokality** políčko--nebo udržování je vybrána, vytvořte výjimku pro **login.microsoftonline.com**, a Zkuste se znovu otevřete aplikaci.


V seznamu okna aktivity v prostředním podokně zobrazí okno s aktivity pro každé spuštění aktivity. Například pokud máte aktivity naplánované spuštění každou hodinu pět hodin, uvidíte pět okna aktivity spojené s pěti datové řezy. Pokud nevidíte okna aktivity v seznamu dole, postupujte takto:
 
- Aktualizace **počáteční čas** a **čas ukončení** filtrů v horní části odpovídající počáteční a koncový čas svůj kanál, a klikněte **použít** tlačítko.  
- V seznamu aktivity Windows automaticky neobnoví. Klikněte na tlačítko **aktualizovat** tlačítka na panelu nástrojů v **aktivity Windows** seznamu.  

Pokud nemáte aplikaci služby Data Factory tyto kroky otestovat, proveďte tento kurz: [kopírování dat z úložiště objektů Blob do SQL Database pomocí služby Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Porozumění sledování a správu aplikací
Na levé straně jsou tři karty: **Průzkumníka prostředků**, **monitorování zobrazení**, a **výstrahy**. Na první kartě (**Průzkumníka prostředků**) je standardně vybraná.

### <a name="resource-explorer"></a>Průzkumník prostředků
Zobrazí následující:

* Průzkumník prostředků **stromovém zobrazení** v levém podokně.
* **Zobrazení diagramu** nahoře v prostředním podokně.
* **Aktivity Windows** seznamu dole v prostředním podokně.
* **Vlastnosti**, **aktivity okno Průzkumníka**, a **skriptu** karty v pravém podokně.

V Průzkumníku prostředků zobrazí všechny prostředky (kanály, datové sady, propojených služeb) v objektu pro vytváření dat ve stromovém zobrazení. Když vyberete objekt v Průzkumníku prostředků:

* Související entity služby Data Factory je zvýrazněn v zobrazení diagramu.
* [Související aktivity windows](data-factory-scheduling-and-execution.md) jsou vyznačené na seznamu okna aktivity v dolní části.  
* V okně vlastností v pravém podokně se zobrazí vlastnosti vybraného objektu.
* Definici JSON vybraného objektu se zobrazí, pokud je k dispozici. Příklad: propojené služby, datové sady nebo kanálu.

![Průzkumník prostředků](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Najdete v článku [plánování a provádění](data-factory-scheduling-and-execution.md) článku podrobné koncepční informace o windows aktivity.

### <a name="diagram-view"></a>Zobrazení diagramu
Zobrazení diagramu objektu pro vytváření dat poskytuje skla ke sledování a správě objekt pro vytváření dat a její prostředky. Když vyberete entity služby Data Factory (datové sady nebo kanál) v zobrazení diagramu:

* Entity objektu pro vytváření dat je vybraný ve stromovém zobrazení.
* Okna přidružené aktivity jsou vyznačené na seznamu okna aktivity.
* V okně Vlastnosti se zobrazí vlastnosti vybraného objektu.

Když kanál je povolené (ne v pozastaveném stavu), zobrazí se zelený řádku:

![Spuštění kanálu](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Můžete pozastavit, obnovit nebo ukončit kanál tak, že ho vyberete v zobrazení diagramu a pomocí tlačítek na panelu příkazů.

![Pozastavení nebo obnovení na panelu příkazů](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Existují tři panelu příkazů pro kanál v zobrazení diagramu. Na druhé tlačítko můžete pozastavit kanálu. Pozastavení není ukončit probíhající aktivity a umožňuje jim přejít k dokončení. Tlačítko třetí pozastavuje kanálu a ukončí její existující provádění aktivity. Na první tlačítko obnoví kanálu. Pokud vaše kanálu je pozastavena, změní barvu kanálu. Například pozastavený kanálu vypadá jako na následujícím obrázku: 

![Pozastavená kanálu](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Pomocí klávesy Ctrl můžete vybrat víc dva nebo víc kanálů. Panelu příkazů můžete pozastavit nebo obnovit více kanálů najednou.

Můžete také kanál klikněte pravým tlačítkem a vyberte možnosti pro pozastavení, obnovení nebo ukončit kanál. 

![Kontextové nabídky pro kanál](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Klikněte **otevřít kanál** možnost zobrazíte všechny aktivity v kanálu. 

![Nabídka Otevřít kanál](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

V zobrazení otevřenou kanálu zobrazí všechny aktivity v kanálu. V tomto příkladu je jenom jedna aktivita: aktivity kopírování. 

![Otevřenou kanálu](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Chcete-li vrátit do předchozího zobrazení, klikněte na název objektu pro vytváření dat v nabídce s popisem cesty v horní části.

V zobrazení kanálu po výběru datovou sadu výstupů nebo když přesunutím ukazatele myši nad výstupní datovou sadu, se zobrazí automaticky otevírané okno okno aktivity Windows pro tuto datovou sadu.

![Místní okno Windows aktivity](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Můžete kliknout na okno s aktivity zobrazíte podrobnosti ho **vlastnosti** okno v pravém podokně.

![Vlastnosti – okno](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

V pravém podokně přepnout **aktivity okno Průzkumníka** karty zobrazíte další podrobnosti.

![Okno Průzkumníka aktivity](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Zobrazí také **přeložit proměnné** pro každý pokus o spuštění pro aktivitu v **pokusy o** části.

![Vyřešený proměnné](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Přepnout **skriptu** karty zobrazíte definici JSON skript pro vybraný objekt.   

![Karta skriptu](./media/data-factory-monitor-manage-app/ScriptTab.png)

Zobrazí okna aktivity na třech místech:

* Místní nabídce okna aktivity v zobrazení diagramu (střední podokno).
* Průzkumník okno aktivity v pravém podokně.
* Seznam okna aktivity v dolním podokně.

V automaticky otevírané okno aktivity Windows a aktivity okno Průzkumníka můžete přejít do předchozího týdne a další týden pomocí šipky vlevo a vpravo.

![Aktivita okno Průzkumníka levé nebo pravé šipky](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

V dolní části zobrazení diagramu, zobrazí tato tlačítka: přiblížení přiblížit, oddálit, přizpůsobit, zvětšení 100 %, rozložení zámku. **Zámku rozložení** tlačítko zabraňuje nechtěnému přesunutí tabulek a kanálů v zobrazení diagramu. Ve výchozím nastavení je. Můžete ho vypnout a pohyb entity v diagramu. Když vypnete ho, můžete na poslední tlačítko umístit automaticky tabulky a kanály. Můžete také přiblížení nebo oddálení pomocí kolečka myši.

![Diagram příkazy přiblížení zobrazení](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Seznam Okna aktivit
V seznamu okna aktivity v dolní části v prostředním podokně se zobrazí všechny aktivity windows pro datovou sadu, který jste vybrali v Průzkumníku prostředků nebo zobrazení diagramu. Ve výchozím nastavení je v seznamu v sestupném pořadí, což znamená, že vidíte nejnovější okna aktivita v horní části.

![Seznam Okna aktivit](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Tento seznam není aktualizuje automaticky, takže použijte tlačítko Aktualizovat na panelu nástrojů jej ručně aktualizovat.  

Okna aktivity může být v jednom z následujících stavů:

<table>
<tr>
    <th align="left">Status</th><th align="left">Podřízený stav</th><th align="left">Popis</th>
</tr>
<tr>
    <td rowspan="8">Čekání</td><td>ScheduleTime</td><td>Čas ještě nenastal pro okna aktivity ke spuštění.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Upstreamové závislosti nejsou připravené.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Výpočetní prostředky nejsou k dispozici.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Jsou všechny instance aktivit právě zpracovávají jiné aktivity windows.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktivita je pozastavená a aktivity windows nelze spustit, dokud nebude obnovená.</td>
</tr>
<tr>
<td>Retry</td><td>Probíhá pokus o spuštění aktivity je zopakován.</td>
</tr>
<tr>
<td>Ověření</td><td>Ověření se ještě nespustilo.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Ověření čeká na opakovat.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Probíhá zpracování.</td><td>Ověřování platnosti</td><td>Probíhá ověřování.</td>
</tr>
<td>-</td>
<td>Okna aktivity je zpracovávána.</td>
</tr>
<tr>
<td rowspan="4">Selhalo</td><td>TimedOut</td><td>Provedení aktivity trvalo déle, než je povolené aktivitou.</td>
</tr>
<tr>
<td>Zrušeno</td><td>Okno aktivity zrušil akce uživatele.</td>
</tr>
<tr>
<td>Ověření</td><td>Ověření selhalo.</td>
</tr>
<tr>
<td>-</td><td>Okno aktivity se nepodařilo vygenerovat nebo ověřit.</td>
</tr>
<td>Připraveno</td><td>-</td><td>Okna aktivity je připraven ke spotřebování.</td>
</tr>
<tr>
<td>Vynecháno</td><td>-</td><td>Okno aktivity nebyla zpracována.</td>
</tr>
<tr>
<td>Žádný</td><td>-</td><td>Okno s aktivity měl dříve jiný stav, ale byl obnoven.</td>
</tr>
</table>


Po kliknutí na tlačítko okno s aktivity v seznamu, můžete zobrazit podrobnosti o ho **aktivity Windows Explorer** nebo **vlastnosti** okno na pravé straně.

![Okno Průzkumníka aktivity](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Aktualizujte windows aktivity
Podrobnosti nejsou automaticky aktualizují, proto použijte tlačítko Aktualizovat (druhé tlačítko) na panelu příkazů ručně aktualizovat seznam windows aktivit.  

### <a name="properties-window"></a>Vlastnosti – okno
Okno Vlastnosti je v podokně nejvíce vpravo monitorování a správu aplikace.

![Vlastnosti – okno](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Zobrazí vlastnosti pro položku, kterou jste vybrali v Průzkumníku prostředků (stromové zobrazení), zobrazení diagramu nebo seznamu aktivity Windows.

### <a name="activity-window-explorer"></a>Okno Průzkumníka aktivity
**Aktivity okno Průzkumníka** okno se v podokně nejvíce vpravo monitorování a správu aplikace. Zobrazuje podrobnosti o okně aktivity, které jste vybrali v místním okně aktivity Windows nebo seznamu okna aktivity.

![Okno Průzkumníka aktivity](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Kliknutím na zobrazení kalendáře v horní části můžete přepnout do jiného okna aktivity. Také můžete šipku vlevo nebo vpravo tlačítek v horní části zobrazíte okna aktivity z předchozího týdne nebo do následujícího týdne.

Tlačítka panelu nástrojů v dolním podokně slouží k spustit znovu okna aktivity nebo aktualizovat podrobnosti v podokně.

### <a name="script"></a>Skript
Můžete použít **skriptu** zobrazíte definici JSON vybrané entity služby Data Factory (propojené služby, datové sady nebo kanál).

![Karta skriptu](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Pomocí zobrazení systému
Monitorování a správu aplikace obsahuje předem připravené systémová zobrazení (**poslední aktivity windows**, **se nezdařilo aktivity windows**, **probíhající aktivity windows**), povolit můžete zobrazit posledních nebo se nezdařilo nebo probíhající aktivity windows pro datovou továrnu.

Přepnout **monitorování zobrazení** karty na levé straně kliknutím.

![Zobrazení Karta sledování](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

V současné době jsou tři zobrazení systému, které jsou podporovány. Vyberte možnost Zobrazit nedávné aktivity windows, windows neúspěšné aktivity nebo okna probíhající aktivity v seznamu okna aktivity (v dolní části v prostředním podokně).

Když vyberete **poslední aktivity windows** možnost zobrazí všechny poslední aktivity windows v sestupném pořadí podle **čas posledního pokusu**.

Můžete použít **se nezdařilo aktivity windows** zobrazíte všechny neúspěšné aktivity windows v seznamu. Vyberte v seznamu zobrazíte podrobnosti o něm v období neúspěšné aktivity **vlastnosti** okno nebo **aktivity okno Průzkumníka**. Můžete také stáhnout všechny protokoly pro okno neúspěšné aktivity.

## <a name="sort-and-filter-activity-windows"></a>Třídění a filtrování aktivity windows
Změna **počáteční čas** a **čas ukončení** nastavení na panelu příkazů do filtru aktivity windows. Po změně počáteční čas a koncový čas, klikněte na tlačítko vedle koncový čas k aktualizaci seznamu okna aktivity.

![Počáteční a koncový čas](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> V současné době všechny časy jsou ve formátu UTC v aplikaci sledování a správu.
>
>

V **seznamu aktivity Windows**, klikněte na název sloupce (například: stav).

![Aktivity Windows seznamu sloupec nabídky](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Můžete provést následující:

* Seřadit ve vzestupném pořadí.
* Řazení v sestupném pořadí.
* Filtrovat podle jednu nebo více hodnot (připravené, čekání a tak dále).

Když zadáte filtr na sloupci, zobrazí tlačítko filtru pro sloupce, který označuje, jestli jsou hodnoty ve sloupci filtrované hodnoty povoleno.

![Filtrování podle sloupce seznamu okna aktivity](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Zrušení filtrů můžete stejné překryvné okno. Zrušení všech filtrů pro okna aktivity seznamu, klikněte na tlačítko Vymazat filtr na panelu příkazů.

![Zruší všechny filtry pro okna aktivity seznamu](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Udělejte batch
### <a name="rerun-selected-activity-windows"></a>Znovu spustit vybranou aktivitou windows
Vyberte okno s aktivity, klikněte na šipku dolů pro první příkazového tlačítka panelu a vyberte **spusťte znovu** / **znovu spustit s proti proudu v kanálu**. Když vyberete **znovu spustit s proti proudu v kanálu** možnost, se znovu spustí všechna okna nadřízené činnosti také.
    ![Spusťte okno s aktivity](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Můžete také v seznamu vyberte více aktivity windows a znovu spustit, je ve stejnou dobu. Můžete filtrovat na základě stavu okna aktivity (například: **se nezdařilo**) – a poté znovu spusťte windows neúspěšné aktivity po vyřešení potíží, které způsobí, že aktivita windows selhání. Následující části Podrobnosti o filtrování okna aktivity v seznamu.  

### <a name="pauseresume-multiple-pipelines"></a>Pozastavení nebo obnovení více kanálů
Vícenásobný výběr dva nebo víc kanálů můžete pomocí klávesu Ctrl. Pozastavení nebo obnovení je můžete panelu příkazů (které jsou vyznačené na červeným rámečkem na následujícím obrázku).

![Pozastavení nebo obnovení na panelu příkazů](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## <a name="create-alerts"></a>Vytváření upozornění
**Výstrahy** stránky umožňuje vytvářet výstrahy a zobrazení, úpravy nebo odstranění existující výstrahy. Vám může také zapnout/vypnout výstrahu. Chcete-li zobrazit stránku výstrahy, klikněte na tlačítko **výstrahy** kartě.

![Karta výstrahy](./media/data-factory-monitor-manage-app/AlertsTab.png)

### <a name="to-create-an-alert"></a>Vytvoření výstrahy
1. Klikněte na tlačítko **přidat výstraha** přidat výstrahu. Zobrazí **podrobnosti** stránky.

    ![Vytvářet výstrahy - stránce s podrobnostmi o](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
2. Zadejte **název** a **popis** pro výstrahy a klikněte na tlačítko **Další**. Měli byste vidět **filtry** stránky.

    ![Vytvořte upozornění – filtry stránky](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)
3. Vyberte **událostí**, **stav**, a **substatus** (volitelné) Chcete-li vytvořit výstrahu pro službu Data Factory, a klikněte na **Další**. Měli byste vidět **příjemce** stránky.

    ![Vytvořte upozornění – stránka příjemce](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png)
4. Vyberte **e-mailem správci předplatného** možnost a zadejte **další správce e-mailu**a klikněte na tlačítko **Dokončit**. Měli byste vidět výstrahu v seznamu.

    ![Seznam výstrah](./media/data-factory-monitor-manage-app/AlertsList.png)

V seznamu výstrah pomocí tlačítek, které jsou přidruženy výstrahu, kterou chcete upravit nebo odstranit nebo zapnout/vypnout výstrahu.

### <a name="eventstatussubstatus"></a>Události, stav/substatus
Následující tabulka obsahuje seznam dostupných událostí a stavy (a dílčí stavy).

| Název události | Status | Podřízený stav |
| --- | --- | --- |
| Aktivity při spuštění Začínáme |Spuštěno |Spouštění |
| Aktivity při spuštění bylo dokončeno |Úspěch |Úspěch |
| Aktivity při spuštění bylo dokončeno |Selhalo |Přidělení prostředků se nezdařilo<br/><br/>Spuštění se nezdařilo<br/><br/>Vypršení časového limitu<br/><br/>Ověření se nezdařilo<br/><br/>Abandoned |
| Vytvoření clusteru HDI na vyžádání Začínáme |Spuštěno |-|
| Clusteru HDI na vyžádání úspěšně vytvořena. |Úspěch |-|
| Odstranit clusteru HDI na vyžádání |Úspěch |-|

### <a name="to-edit-delete-or-disable-an-alert"></a>Chcete-li upravit, odstranit nebo zakázat výstrahy

Pomocí následujících tlačítek (zvýrazněné červeně) upravit, odstranit nebo zakázat výstrahu.

![Výstrahy tlačítka](./media/data-factory-monitor-manage-app/AlertButtons.png)
