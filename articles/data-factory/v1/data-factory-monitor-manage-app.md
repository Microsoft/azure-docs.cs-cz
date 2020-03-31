---
title: Monitorování a správa datových kanálů – Azure
description: Přečtěte si, jak pomocí aplikace Monitorování a správa monitorovat a spravovat datové továrny a kanály Azure.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: de56ba1281d0f20c8be838fa1bc9ebc24905b26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260368"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Sledování a správa kanálů Azure Data Factory pomocí aplikace Monitorování a správa
> [!div class="op_single_selector"]
> * [Používání Azure portal/Azure PowerShellu](data-factory-monitor-manage-pipelines.md)
> * [Používání aplikace Monitorování a správa](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma monitorování a správa kanálů Data Factory v .](../monitor-visually.md)

Tento článek popisuje, jak pomocí aplikace Monitorování a správa sledovat, spravovat a ladit kanály datové továrny. S používáním aplikace můžete začít sledováním následujícího videa:

> [!NOTE]
> Uživatelské rozhraní zobrazené ve videu nemusí přesně odpovídat tomu, co vidíte na portálu. Je o něco starší, ale koncepty zůstávají stejné. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Spuštění aplikace Monitorování a správa
Chcete-li spustit aplikaci Sledování a správa, klikněte na dlaždici **Monitor & Správa** v okně Data **Factory** pro vaši datovou továrnu.

![Dlaždice sledování na domovské stránce datové továrny](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Aplikace Monitorování a správa by se měla zobrazit v samostatném okně.  

![Monitorování a správa aplikací](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Pokud zjistíte, že webový prohlížeč je přilepená na "Autorizace...", zrušte **zaškrtnutí políčka Blokovat soubory cookie třetích stran a data webu** - nebo ji ponechat vybranou, vytvořte výjimku pro **login.microsoftonline.com**a zkuste aplikaci znovu otevřít.


V seznamu Windows aktivity v prostředním podokně se zobrazí okno aktivity pro každé spuštění aktivity. Pokud máte například aktivitu naplánovanou na hodinové spuštění po dobu pěti hodin, zobrazí se pět oken aktivit přidružených k pěti řezům dat. Pokud okna aktivit v seznamu dole nevidíte, postupujte takto:
 
- Aktualizujte filtry **počátečního** a **koncového času** v horní části tak, aby odpovídaly počátečnímu a koncovému času kanálu, a klikněte na tlačítko **Použít.**  
- Seznam Aktivita systému Windows není automaticky aktualizován. Klepněte na tlačítko **Aktualizovat** na panelu nástrojů v seznamu **Windows aktivity.**  

Pokud nemáte aplikaci Data Factory, pomocí které chcete tyto kroky otestovat, proveďte kurz: [kopírování dat z úložiště objektů blob do databáze SQL pomocí data factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Principy aplikace Monitorování a správa
Vlevo jsou tři karty: **Průzkumník prostředků**, **Zobrazení monitorování**a **Výstrahy**. Ve výchozím nastavení je vybrána první karta (**Průzkumník prostředků**).

### <a name="resource-explorer"></a>Průzkumník zdrojů
Zobrazí se následující:

* **Stromové zobrazení** Průzkumníka prostředků v levém podokně.
* **Zobrazení diagramu** v horní části prostředního podokna.
* Seznam **Windows aktivity v** dolní části prostředního podokna.
* Karty **Vlastnosti**, **Průzkumník oken aktivit**y a **Skript** v pravém podokně.

V Průzkumníku prostředků se ve stromovém zobrazení zobrazí všechny prostředky (kanály, datové sady, propojené služby) v datové továrně. Když vyberete objekt v Průzkumníku zdrojů:

* Přidružená entita Data Factory je zvýrazněna v zobrazení diagramu.
* [Okna přidružených aktivit](data-factory-scheduling-and-execution.md) jsou zvýrazněna v seznamu Windows aktivity v dolní části.  
* Vlastnosti vybraného objektu jsou zobrazeny v okně Vlastnosti v pravém podokně.
* JSON definice vybraného objektu je zobrazen, pokud je k dispozici. Například: propojená služba, datová sada nebo kanál.

![Průzkumník zdrojů](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Podrobné koncepční informace o oknech aktivit y naleznete v článku [Plánování a spuštění.](data-factory-scheduling-and-execution.md)

### <a name="diagram-view"></a>Zobrazení diagramu
Zobrazení diagramu datové továrny poskytuje jediné podokno skla pro sledování a správu datové továrny a jejích prostředků. Když vyberete entitu Data Factory (datová sada/kanál) v zobrazení diagramu:

* Entita datové továrny je vybrána ve stromovém zobrazení.
* Okna souvisejících aktivit jsou zvýrazněna v seznamu Windows aktivity.
* Vlastnosti vybraného objektu se zobrazí v okně Vlastnosti.

Pokud je kanál povolen (není v pozastaveném stavu), zobrazí se se zelenou čárou:

![Kanál běží](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Kanál můžete pozastavit, obnovit nebo ukončit tak, že ho vyberete v zobrazení diagramu a použijete tlačítka na panelu příkazů.

![Pozastavení/pokračování na panelu příkazů](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
V zobrazení diagramu jsou tři tlačítka panelu příkazů pro kanál. Druhé tlačítko můžete pozastavit. Pozastavení neukončí aktuálně spuštěné aktivity a umožní jim pokračovat v dokončení. Třetí tlačítko pozastaví kanál a ukončí jeho stávající provádění aktivit. První tlačítko obnoví potrubí. Když je kanál pozastaven, změní se barva kanálu. Například pozastavený kanál vypadá na následujícím obrázku: 

![Kanál pozastaven.](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Pomocí klávesy Ctrl můžete vícenásobně vybrat dva nebo více kanálů. Pomocí tlačítek panelu příkazů můžete pozastavit nebo obnovit více kanálů najednou.

Můžete také klepnout pravým tlačítkem myši na kanál a vybrat možnosti pozastavení, obnovení nebo ukončení kanálu. 

![Místní nabídka pro kanál](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Kliknutím na možnost **Otevřít kanál** zobrazíte všechny aktivity v kanálu. 

![Nabídka Otevřít kanál](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

V otevřeném zobrazení kanálu se zobrazí všechny aktivity v kanálu. V tomto příkladu je pouze jedna aktivita: Aktivita kopírování. 

![Otevřený potrubí](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Chcete-li se vrátit k předchozímu zobrazení, klepněte na název datové továrny v nabídce s popisem cesty v horní části.

V zobrazení kanálu se při výběru výstupní datové sady nebo při přesunutí myši nad výstupní datovou sadou zobrazí rozbalovací okno Aktivita systému Windows pro tuto datovou sadu.

![Automaticky otevírané okno Aktivita windows](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Kliknutím na okno aktivity můžete zobrazit podrobnosti v okně **Vlastnosti** v pravém podokně.

![Vlastnosti okna aktivity](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

V pravém podokně přejděte na kartu **Průzkumník okna aktivity,** abyste viděli další podrobnosti.

![Průzkumník oken aktivit](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Zobrazí se také **vyřešené proměnné** pro každý pokus o spuštění aktivity v části **Pokusy.**

![Vyřešené proměnné](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Přepnutím na kartu **Skript** zobrazíte definici skriptu JSON pro vybraný objekt.   

![Karta Skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

Okna aktivit můžete zobrazit na třech místech:

* Automaticky otevírané okno Windows aktivity v zobrazení diagramu (prostřední podokno).
* Průzkumník oken aktivit v pravém podokně.
* Seznam Windows aktivity v dolním podokně.

V průzkumníku automaticky otevíraných oken Windows a okna aktivity můžete přejít na předchozí týden a další týden pomocí levé a pravé šipky.

![Šipky okna aktivity vlevo/vpravo](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

V dolní části zobrazení diagramu se zobrazí tato tlačítka: Přiblížit, Oddálit, Přiblížit, Přizpůsobit, Zvětšit 100 %, Zamknout. Tlačítko **Zamknout rozložení** zabraňuje nechtěnému přesunutí tabulek a kanálů v zobrazení diagramu. Ve výchozím nastavení je zapnutý. Můžete jej vypnout a přesunout entity v diagramu. Když ji vypnete, můžete pomocí posledního tlačítka automaticky umístit tabulky a kanály. Zobrazení můžete také přiblížit nebo oddálit pomocí kolečka myši.

![Příkazy lupy zobrazení diagramu](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Seznam Okna aktivit
Seznam Windows aktivity v dolní části prostředního podokna zobrazuje všechna okna aktivit pro datovou sadu, kterou jste vybrali v Průzkumníku zdrojů nebo v zobrazení diagramu. Ve výchozím nastavení je seznam v sestupném pořadí, což znamená, že se nahoře zobrazí poslední okno aktivity.

![Seznam Okna aktivit](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Tento seznam se neaktualizuje automaticky, proto jej pomocí tlačítka aktualizovat na panelu nástrojů ručně aktualizujte.  

Okna aktivit mohou být v jednom z následujících stavů:

<table>
<tr>
    <th align="left">Status</th><th align="left">Dílčí stav</th><th align="left">Popis</th>
</tr>
<tr>
    <td rowspan="8">Čekání</td><td>ScheduleTime</td><td>Nenadešel čas spuštění okna aktivity.</td>
</tr>
<tr>
<td>Závislostí datové sady</td><td>Upstream závislosti nejsou připraveny.</td>
</tr>
<tr>
<td>Výpočetní prostředky</td><td>Výpočetní prostředky nejsou k dispozici.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Všechny instance aktivity jsou zaneprázdněny spuštěním jiných oken aktivit.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktivita je pozastavena a nemůže spustit okna aktivity, dokud nebude obnovena.</td>
</tr>
<tr>
<td>Retry</td><td>Provádění aktivity je opakováno.</td>
</tr>
<tr>
<td>Ověřování</td><td>Ověření ještě nezačalo.</td>
</tr>
<tr>
<td>Ověření Opakování</td><td>Ověření čeká na opakování.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Probíhá</td><td>Ověřování</td><td>Probíhá ověření.</td>
</tr>
<td>-</td>
<td>Okno aktivity je zpracováváno.</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>Timedout</td><td>Provádění aktivity trvalo déle, než je povoleno aktivitou.</td>
</tr>
<tr>
<td>Zrušeno</td><td>Okno aktivity bylo zrušeno akcí uživatele.</td>
</tr>
<tr>
<td>Ověřování</td><td>Ověření se nezdařilo.</td>
</tr>
<tr>
<td>-</td><td>Okno aktivity se nezdařilo a nebylo vygenerováno nebo ověřeno.</td>
</tr>
<td>Připraveno</td><td>-</td><td>Okno aktivity je připraveno ke spotřebě.</td>
</tr>
<tr>
<td>Přeskočen</td><td>-</td><td>Okno aktivity nebylo zpracováno.</td>
</tr>
<tr>
<td>Žádný</td><td>-</td><td>Okno aktivity dříve existovalo s jiným stavem, ale bylo resetováno.</td>
</tr>
</table>


Když kliknete na okno aktivity v seznamu, zobrazí se podrobnosti o něm v **Průzkumníkovi aktivity** nebo v okně **Vlastnosti** vpravo.

![Průzkumník oken aktivit](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Aktualizovat okna aktivity
Podrobnosti se neaktualizují automaticky, proto pomocí tlačítka aktualizace (druhé tlačítko) na panelu příkazů ručně aktualizujte seznam oken aktivit.  

### <a name="properties-window"></a>Vlastnosti – okno
Okno Vlastnosti se nachází v pravém podokně aplikace Monitorování a správa.

![Vlastnosti – okno](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Zobrazí vlastnosti položky, kterou jste vybrali v Průzkumníku zdrojů (stromové zobrazení), zobrazení diagramu nebo v seznamu Windows aktivity.

### <a name="activity-window-explorer"></a>Průzkumník oken aktivit
Okno **Průzkumník aktivit** se nachází v pravém podokně aplikace Monitorování a správa. Zobrazí podrobnosti o okně aktivity, které jste vybrali v rozbalovacím okně Okna aktivity systému Windows nebo v seznamu Windows aktivity.

![Průzkumník oken aktivit](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Do jiného okna aktivity můžete přepnout kliknutím v zobrazení kalendáře v horní části. Můžete také použít šipku vlevo / šipka vpravo tlačítka v horní části zobrazíte okna aktivity z předchozího týdne nebo příštího týdne.

Pomocí tlačítek panelu nástrojů v dolním podokně můžete znovu spustit okno aktivity nebo aktualizovat podrobnosti v podokně.

### <a name="script"></a>Skript
Kartu **Skript** můžete použít k zobrazení definice JSON vybrané entity Data Factory (propojená služba, datová sada nebo kanál).

![Karta Skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Použití systémových zobrazení
Aplikace Monitorování a správa obsahuje předem vyestavěná systémová zobrazení (**okna poslední aktivity**, **okna s neúspěšnou aktivitou**, **okna probíhající aktivity**), která umožňují zobrazit okna posledních/neúspěšných/probíhajících aktivit pro vaši datovou továrnu.

Kliknutím na něj přepněte na kartu **Zobrazení monitorování** vlevo.

![Karta Sledování zobrazení](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

V současné době existují tři zobrazení systému, které jsou podporovány. Vyberte možnost, chcete-li zobrazit okna posledních aktivit, okna s neúspěšnou aktivitou nebo okna probíhajícíaktivity v seznamu Windows aktivit (v dolní části prostředního podokna).

Když vyberete možnost **Okna poslední aktivity,** zobrazí se všechna okna nedávné aktivity v sestupném pořadí **podle posledního času pokusu**.

Pomocí zobrazení **okna Se nezdařilou aktivity** můžete zobrazit všechna okna neúspěšných aktivit v seznamu. Vyberte okno neúspěšné aktivity v seznamu, chcete-li zobrazit podrobnosti o něm v okně **Vlastnosti** nebo v **Průzkumníku oken aktivit**. Můžete také stáhnout všechny protokoly pro okno neúspěšné aktivity.

## <a name="sort-and-filter-activity-windows"></a>Řazení a filtrování oken aktivit
Změňte nastavení **počátečního a** **koncového času** v panelu příkazů tak, aby filtrovala okna aktivit. Po změně počátečního a koncového času klikněte na tlačítko vedle koncového času a aktualizujte seznam Windows aktivity.

![Počáteční a koncový čas](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> V současné době jsou všechny časy ve formátu UTC v aplikaci Monitorování a správa.
>
>

V **seznamu Windows aktivity**klepněte na název sloupce (například Stav).

![Nabídka sloupců seznamu Aktivita systému Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Můžete provést následující akce:

* Seřadit ve vzestupném pořadí.
* Seřaďte se v sestupném pořadí.
* Filtrujte podle jedné nebo více hodnot (Připraveno, Čekání a tak dále).

Když zadáte filtr ve sloupci, zobrazí se pro tento sloupec povolené tlačítko filtru, které označuje, že hodnoty ve sloupci jsou filtrované hodnoty.

![Filtrování ve sloupci seznamu Windows pro aktivity](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Ke vymazání filtrů můžete použít stejné vyskakovací okno. Chcete-li vymazat všechny filtry pro seznam Windows aktivity, klepněte na tlačítko vymazat filtr na panelu příkazů.

![Vymazání všech filtrů pro seznam Windows aktivit](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Provádět dávkové akce
### <a name="rerun-selected-activity-windows"></a>Opětovné spuštění vybraných oken aktivit
Vyberte okno aktivity, klepněte na šipku dolů pro první tlačítko panelu příkazů a vyberte **znovu spustit** / **s potrubím**proti proudu . Když vyberete **možnost Znovu spustit s možností upstream v kanálu,** znovu se spustí také všechna okna upstream aktivity.
    ![Opětovné spuštění okna aktivity](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Můžete také vybrat více oken aktivit v seznamu a znovu je spustit současně. Můžete chtít filtrovat okna aktivity na základě stavu (například: **Nezdařilo se**)-- a potom znovu spustit okna s neúspěšnou aktivitou po opravě problému, který způsobuje selhání oken aktivity. Podrobnosti o filtrování oken aktivit v seznamu naleznete v následující části.  

### <a name="pauseresume-multiple-pipelines"></a>Pozastavení/obnovení více kanálů
Pomocí klávesy Ctrl můžete vícenásobně vybrat dva nebo více kanálů. Pomocí tlačítek panelu příkazů (která jsou zvýrazněna červeným obdélníkem na následujícím obrázku) je můžete pozastavit/obnovit.

![Pozastavení/pokračování na panelu příkazů](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
