---
title: Monitorovat a spravovat datové kanály – Azure | Dokumentace Microsoftu
description: Zjistěte, jak monitorovat a spravovat kanály a objekty pro vytváření dat Azure pomocí monitorování a Správa aplikací.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5b70edd4f65538b52c70881258bc500a34b04d80
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025415"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorování a Správa kanálů Azure Data Factory pomocí aplikace pro monitorování a Správa
> [!div class="op_single_selector"]
> * [Pomocí Azure portal nebo Azure Powershellu](data-factory-monitor-manage-pipelines.md)
> * [Pomocí monitorování a správu aplikací](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [monitorování a Správa kanálů Data Factory v](../monitor-visually.md).

Tento článek popisuje, jak použít monitorování a Správa aplikací pro monitorování, spravovat a ladit kanály Data Factory. Můžete začít s používáním aplikace Zhlédnutím následujícího videa:

> [!NOTE]
> Uživatelské rozhraní zobrazené ve videu nemusí přesně odpovídat uvidí na portálu. Je mírně starší, ale koncepty zůstávají stejné. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Spuštění monitorování a správu aplikací
Spustit monitorování a Správa aplikací, klikněte na tlačítko **monitorování a správa** na dlaždici **služby Data Factory** okno datové továrny.

![Monitorování dlaždici na domovské stránce objektu pro vytváření dat](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Měli byste vidět monitorování a Správa aplikace otevře v samostatném okně.  

![Monitorování a správa aplikací](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Pokud se zobrazí, že se webový prohlížeč zasekl ve "Autorizace …", zrušte zaškrtnutí políčka **zablokuje soubory cookie třetích stran a data lokality** zaškrtávací políčko--nebo zachovat je vybrané, vytvořte výjimku pro **login.microsoftonline.com**, a Zkuste aplikaci znovu otevřít.


V seznamu aktivit Windows v prostředním podokně se zobrazí okna aktivity pro každé spuštění aktivity. Například pokud máte aktivitu naplánované na každou hodinu běžel pět hodin, uvidíte pět oken aktivity spojené s pěti datové řezy. Pokud nevidíte okna aktivity v seznamu dole, postupujte takto:
 
- Aktualizace **počáteční čas** a **čas ukončení** filtrů v horní části odpovídají počátečním a koncovým časem kanálu a potom klikněte na tlačítko **použít** tlačítko.  
- Seznam aktivit Windows zobrazení se neobnovuje automaticky. Klikněte na tlačítko **aktualizovat** tlačítko na panelu nástrojů **aktivity Windows** seznamu.  

Pokud nemáte k dispozici aplikace služby Data Factory, která tyto kroky otestovat, udělat kurz: [kopírování dat z Blob Storage do SQL Database s použitím služby Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Porozumění sledování a správu aplikací
Existují tři karty na levé straně: **Průzkumník prostředků**, **zobrazení monitorování**, a **výstrahy**. Na první kartě (**Průzkumníka prostředků**) je standardně vybraná.

### <a name="resource-explorer"></a>Průzkumník prostředků
Zobrazí se následující:

* Průzkumník prostředků **zobrazení stromové struktury** v levém podokně.
* **Zobrazení diagramu** v horní části stránky v prostředním podokně.
* **Aktivity Windows** seznam v dolní části v prostředním podokně.
* **Vlastnosti**, **Průzkumníku okna aktivity**, a **skript** karty v pravém podokně.

V Průzkumníku prostředků se zobrazí všechny prostředky (kanály, datové sady, propojené služby) ve službě data factory ve stromovém zobrazení. Když vyberete objektu v Průzkumníku prostředků:

* Související entity služby Data Factory je zvýrazněn v zobrazení diagramu.
* [Související aktivita windows](data-factory-scheduling-and-execution.md) jsou zvýrazněné v seznamu aktivit Windows v dolní části.  
* V okně Vlastnosti v pravém podokně jsou zobrazeny vlastnosti vybraného objektu.
* Definice JSON vybraného objektu se zobrazí, pokud je k dispozici. Příklad: propojené služby, datové sady a kanál.

![Průzkumník prostředků](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Zobrazit [plánování a provádění](data-factory-scheduling-and-execution.md) najdete podrobné informace o okna aktivit.

### <a name="diagram-view"></a>Zobrazení diagramu
Zobrazení diagramu datové továrny poskytuje podokně ze skla ke sledování a správě data factory a její prostředky. Když vyberete datovou továrnu entity (datové sady a kanál) v zobrazení diagramu:

* Entita objektu pro vytváření dat je vybraný ve stromovém zobrazení.
* Přidružená aktivita windows jsou zvýrazněné v seznamu aktivit Windows.
* V okně Vlastnosti jsou zobrazeny vlastnosti vybraného objektu.

Pokud kanál je povolené (ne v pozastaveném stavu), zobrazí se zelená čára:

![Spuštění kanálu](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Můžete pozastavit, obnovit nebo ukončit kanálu tak, že ji vyberete v zobrazení diagramu a pomocí tlačítek na panelu příkazů.

![Pozastavit/pokračovat na panelu příkazů](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Existují tři tlačítka na panelu příkazů pro kanál v zobrazení diagramu. Na druhé tlačítko slouží k pozastavení kanálu. Přerušení není ukončí aktuálně spuštěnou aktivity a umožní jim přejít k dokončení. Na třetí tlačítko pozastaví kanál a skončí jeho stávající prováděných aktivit. Na první tlačítko obnoví kanálu. Pokud váš kanál je pozastavený, změní se barva kanálu. Například pozastaveného kanál vypadal podobně jako na následujícím obrázku: 

![Pozastaví kanál](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Pomocí klávesy Ctrl můžete vybrat víc dva nebo víc kanálů. Panelu příkazů můžete pozastavit/obnovit více kanálů současně.

Můžete také klikněte pravým tlačítkem na kanálu a vybrat možnost pozastavit, obnovit nebo ukončit kanálu. 

![Místní nabídka pro kanál](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Klikněte na tlačítko **otevřít kanál** můžete zobrazit všechny aktivity v kanálu. 

![Nabídka Otevřít kanál](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

V zobrazení kanálu otevřený uvidíte všechny aktivity v kanálu. V tomto příkladu je jenom jedna aktivita: Aktivita kopírování. 

![Otevřené kanálu](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Chcete-li přejít zpět na předchozí zobrazení, klikněte na název datové továrny v nabídce navigace s popisem cesty v horní části.

V zobrazení kanálu při výběru výstupní datovou sadu nebo při přesunutí myši nad výstupní datová sada zobrazí automaticky otevírané okno aktivity Windows pro tuto datovou sadu.

![Automaticky otevíraná okna aktivity Windows](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Kliknutím zobrazíte podrobnosti pro něj v okně aktivity **vlastnosti** okno v pravém podokně.

![Okno Vlastnosti aktivit](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

V pravém podokně, přepněte **Průzkumníku okna aktivity** kartu zobrazíte další podrobnosti.

![Průzkumníku okna aktivity](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Zobrazí také **přeložit proměnné** pro každého pokusu o spuštění aktivity v **pokusy** části.

![Vyřešené proměnné](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Přepněte **skript** kartu pro zobrazení definice skript JSON pro vybraný objekt.   

![Karta skriptu](./media/data-factory-monitor-manage-app/ScriptTab.png)

Můžete zobrazit okna aktivit na třech místech:

* Aktivity Windows automaticky otevírané okno v zobrazení diagramu (prostřední podokno).
* Průzkumníku okna aktivity v pravém podokně.
* Seznam aktivit Windows v dolním podokně.

V místní nabídce aktivity Windows a Průzkumníku okna aktivity můžete přejít do předchozího týdne a příští týden pomocí šipky vlevo a vpravo.

![Šipky vlevo/vpravo Průzkumníku okna aktivity](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

V dolní části zobrazení diagramu zobrazí se tato tlačítka: Přiblížení, oddálení, zvětšení, přizpůsobit, přiblížení 100 %, zámek rozložení. **Zámek rozložení** tlačítko zabraňuje nechtěnému přesunutí tabulek a kanálů v zobrazení diagramu. Ve výchozím nastavení je. Můžete ji vypnout a přesouvat entit v diagramu. Když vy ji vypnete, vám pomůže poslední tlačítko umožňuje automatické umísťování tabulky a kanály. Můžete také přiblížení nebo oddálení použít kolečko myši.

![Příkazy přiblížení zobrazení diagramu](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Seznam Okna aktivit
Zobrazí se seznam aktivit Windows v dolní části podokna uprostřed všechna okna aktivity pro datovou sadu, kterou jste vybrali v Průzkumníku prostředků nebo v zobrazení diagramu. Ve výchozím nastavení je seznam v sestupném pořadí, což znamená, že vidíte nejnovější v horní části okna aktivity.

![Seznam Okna aktivit](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Tento seznam nebude automaticky aktualizovat, tak pomocí tlačítka Aktualizovat na panelu nástrojů můžete ručně aktualizovat.  

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
<td>ComputeResources</td><td>Výpočetní prostředky, které nejsou k dispozici.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Všechny instance aktivit právě zpracovávají zaneprázdněn spouštěním ostatní okna aktivit.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktivita je pozastavená a okna aktivity nelze spustit, dokud nebude obnovená.</td>
</tr>
<tr>
<td>Retry</td><td>Je o provádění aktivity.</td>
</tr>
<tr>
<td>Ověření</td><td>Ověření se ještě nespustilo.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Ověření čeká se na opakování pokusu.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Probíhá</td><td>Ověřování platnosti</td><td>Probíhá ověřování.</td>
</tr>
<td>-</td>
<td>Okno aktivity se zpracovává.</td>
</tr>
<tr>
<td rowspan="4">Selhalo</td><td>Vypršel časový limit</td><td>Provádění aktivity trvalo déle, než povoluje aktivitou.</td>
</tr>
<tr>
<td>Zrušeno</td><td>V okně aktivita bylo zrušeno uživatelem.</td>
</tr>
<tr>
<td>Ověření</td><td>Ověření selhalo.</td>
</tr>
<tr>
<td>-</td><td>Okno aktivity se nepodařilo vygenerovat nebo ověřit.</td>
</tr>
<td>Připraveno</td><td>-</td><td>Okna aktivity je připravený k použití.</td>
</tr>
<tr>
<td>Přeskočeno</td><td>-</td><td>V okně Aktivita nebyla zpracována.</td>
</tr>
<tr>
<td>Žádný</td><td>-</td><td>Okna aktivity se jiný stav, ale bylo resetováno.</td>
</tr>
</table>


Po kliknutí na okna aktivity v seznamu se zobrazí podrobnosti o řezu **aktivity Windows Explorer** nebo **vlastnosti** okno na pravé straně.

![Průzkumníku okna aktivity](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Aktualizace okna aktivit
Podrobnosti nejsou automaticky aktualizují, proto na tlačítko Aktualizovat (druhé tlačítko) na panelu příkazů můžete ručně aktualizovat seznam okna aktivit.  

### <a name="properties-window"></a>Vlastnosti – okno
V okně Vlastnosti je v podokně úplně vpravo monitorování a Správa aplikací.

![Vlastnosti – okno](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Zobrazí vlastnosti pro položku, kterou jste vybrali v Průzkumníku prostředků (stromové zobrazení), zobrazení diagramu nebo seznamu aktivity Windows.

### <a name="activity-window-explorer"></a>Průzkumníku okna aktivity
**Průzkumníku okna aktivity** je okno v podokně úplně vpravo monitorování a Správa aplikací. Zobrazí podrobnosti o okně aktivity, kterou jste vybrali v automaticky otevíraném okně aktivity Windows nebo v seznamu aktivit Windows.

![Průzkumníku okna aktivity](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Můžete přepnout do jiného okna aktivit kliknutím na zobrazení kalendáře v horní části. Také vám pomůže tlačítek se šipkami šipka vlevo nebo vpravo v horní části okna aktivity z předchozího týdne nebo příští týden naleznete v tématu.

Tlačítka panelu nástrojů v dolním podokně můžete znovu spustit okno aktivity nebo aktualizovat podrobnosti v podokně.

### <a name="script"></a>Skript
Můžete použít **skript** kartu k zobrazení definice JSON vybrané entity služby Data Factory (propojené služby, datové sady nebo kanál).

![Karta skriptu](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Použití systémová zobrazení
Monitorování a Správa aplikací obsahuje zobrazení, předem připravené systému (**nedávné aktivity windows**, **nepovedlo okna aktivit**, **okna aktivity v průběhu**), která umožňují můžete zobrazit nedávné/nepovedlo/probíhající aktivity windows pro službu data factory.

Přepněte **zobrazení monitorování** karty na levé straně, že na něho kliknete.

![Karta zobrazení sledování](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

V současné době jsou tři zobrazení systému, které jsou podporovány. Vyberte možnost pro zobrazení nedávné aktivity windows, windows neúspěšné aktivity nebo okna aktivity v průběhu v seznamu aktivit Windows (v dolní části podokna uprostřed).

Když vyberete **nedávné aktivity windows** možnost zobrazit všechny nedávné okna aktivity v sestupném pořadí podle **poslední pokus čas**.

Můžete použít **nepovedlo okna aktivit** zobrazení, abyste viděli všechna okna neúspěšné aktivity v seznamu. Vyberte okno neúspěšné aktivity v seznamu zobrazíte podrobnosti o řezu **vlastnosti** okno nebo **Průzkumníku okna aktivity**. Můžete také stáhnout všechny protokoly pro okno neúspěšné aktivity.

## <a name="sort-and-filter-activity-windows"></a>Seřadit a filtrovat aktivity windows
Změnit **počáteční čas** a **čas ukončení** nastavení na panelu příkazů do okna aktivit filtru. Po změně počáteční čas a koncový čas, klikněte na tlačítko vedle koncový čas aktualizovat seznam aktivit Windows.

![Počáteční a koncový čas](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> V současné době všechny časy jsou ve formátu UTC v monitorování a Správa aplikací.
>
>

V **aktivity Windows seznamu**, klikněte na název sloupce (například: Stav).

![Nabídka sloupce seznamu aktivit Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Můžete provádět následující akce:

* Řazení ve vzestupném pořadí.
* Seřadí v sestupném pořadí.
* Filtrovat podle jednoho nebo více hodnot (připravené, čekání a tak dále).

Při zadat filtr pro sloupec se zobrazí tlačítko Filtr pro tento sloupec, který označuje, zda jsou hodnoty ve sloupci filtrované hodnoty povoleno.

![Vyfiltrujte sloupec seznamu aktivity Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Vymazat filtry můžete použít stejné automaticky otevírané okno. Pokud chcete vymazat všechny filtry pro seznam Windows aktivity, klikněte na tlačítko Vymazat filtr na panelu příkazů.

![Vymazat všechny filtry pro seznam Windows aktivity](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Provádění akcí služby batch
### <a name="rerun-selected-activity-windows"></a>Znovu spustit vybranou aktivitou windows
Výběrem okna aktivity, klikněte na šipku dolů u první řádek příkazového tlačítka a vyberte **znovu spustit** / **znovu spustit s upstream v kanálu**. Když vyberete **znovu spustit s upstream v kanálu** možnost, zhodnotíte také všechna okna upstreamovou aktivitou.
    ![Opětovné spuštění okna aktivity](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Můžete také vybrat několik okna aktivity v seznamu a znovu ve stejnou dobu. Můžete chtít filtrovat na základě stavu okna aktivit (například: **Nepovedlo**) – a pak znovu spusťte windows neúspěšné aktivity po odstranění problému, který způsobí, že windows aktivita selže. Následující části Podrobnosti o filtrování okna aktivity v seznamu.  

### <a name="pauseresume-multiple-pipelines"></a>Pozastavit/obnovit více kanálů
Pomocí klávesy Ctrl můžete vícenásobného výběru dva nebo víc kanálů. Tlačítka panelu příkazů (která red obdélníku na následujícím obrázku jsou zvýrazněné) můžete pozastavit/obnovit je.

![Pozastavit/pokračovat na panelu příkazů](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
