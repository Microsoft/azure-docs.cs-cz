---
title: Monitorování a Správa datových kanálů – Azure
description: Naučte se používat aplikaci pro monitorování a správu k monitorování a správě datových továrn a kanálů Azure.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 3a9eb73aab381f90ae51300bff1250348916c2ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779592"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorování a Správa kanálů Azure Data Factory pomocí aplikace pro monitorování a správu
> [!div class="op_single_selector"]
> * [Použití Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Používání aplikace pro monitorování a správu](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [monitorování a Správa kanálů Data Factory v nástroji](../monitor-visually.md).

Tento článek popisuje, jak pomocí aplikace pro monitorování a správu monitorovat, spravovat a ladit kanály Data Factory. Můžete začít s používáním aplikace, a to sledováním následujícího videa:

> [!NOTE]
> Uživatelské rozhraní zobrazené ve videu se nemusí přesně shodovat s tím, co vidíte na portálu. Je mírně starší, ale koncepty zůstanou stejné. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Spuštění aplikace pro monitorování a správu
Pokud chcete spustit aplikaci pro monitorování a správu, klikněte na dlaždici **monitorování & spravovat** v okně **Data Factory** pro vaši datovou továrnu.

![Dlaždice monitorování na domovské stránce Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Měla by se zobrazit otevřená aplikace pro monitorování a správu v samostatném okně.  

![Monitorování a správa aplikací](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Pokud zjistíte, že webový prohlížeč je zablokovaný při autorizaci..., zrušte zaškrtnutí políčka **Blokovat soubory cookie a data lokality třetích stran** – nebo ho nechte zaškrtnuté, vytvořte výjimku pro **Login.microsoftonline.com** a pak zkuste aplikaci znovu otevřít.


V seznamu okna aktivit v prostředním podokně se zobrazí okno aktivity pro každé spuštění aktivity. Například pokud máte po dobu pěti hodin naplánováno spuštění aktivity naplánované na každou hodinu, zobrazí se pět oken aktivit, která jsou přidružena k pěti datovým řezům. Pokud v seznamu nevidíte okna aktivit, postupujte takto:
 
- Aktualizujte filtry **čas zahájení** a čas **ukončení** v horní části tak, aby odpovídaly času spuštění a ukončení vašeho kanálu, a klikněte na tlačítko **použít** .  
- Seznam oken aktivit se neaktualizuje automaticky. Klikněte na tlačítko **aktualizovat** na panelu nástrojů v seznamu **okna aktivit** .  

Pokud nemáte Data Factory aplikaci k testování těchto kroků, postupujte podle kurzu: [zkopírování dat z BLOB Storage do SQL Database pomocí Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Pochopení aplikace pro monitorování a správu
Vlevo jsou tři karty: **Průzkumník prostředků**, **zobrazení monitorování** a **výstrahy**. Ve výchozím nastavení je vybrána první karta (**Průzkumník prostředků**).

### <a name="resource-explorer"></a>Průzkumník prostředků
Zobrazí se následující:

* **Zobrazení stromové struktury** Průzkumník prostředků v levém podokně.
* **Zobrazení diagramu** v horní části v prostředním podokně.
* Seznam **oken aktivit** v dolní části podokna v prostředním podokně.
* **Vlastnosti**, **Průzkumník okna aktivity** a karty **skriptu** v pravém podokně.

V Průzkumník prostředků se ve stromovém zobrazení zobrazí všechny prostředky (kanály, datové sady, propojené služby) ve službě Data Factory. Když vyberete objekt v Průzkumník prostředků:

* Přidružená Data Factory entita je v zobrazení diagramu zvýrazněna.
* [Okna přidružených aktivit](data-factory-scheduling-and-execution.md) jsou v seznamu okna aktivit v dolní části zvýrazněna.  
* Vlastnosti vybraného objektu jsou zobrazeny v okno Vlastnosti v pravém podokně.
* V případě potřeby se zobrazí definice JSON vybraného objektu. Například: propojená služba, datová sada nebo kanál.

![Průzkumník prostředků](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Podrobné koncepční informace o oknech aktivit najdete v článku [plánování a spouštění](data-factory-scheduling-and-execution.md) .

### <a name="diagram-view"></a>Zobrazení diagramu
Zobrazení diagramu datové továrny poskytuje jediné podokno skla pro monitorování a správu objektu pro vytváření dat a jeho assetů. Když v zobrazení diagramu vyberete entitu Data Factory (datová sada/kanál):

* Entita Data Factory je vybrána ve stromovém zobrazení.
* Okna přidružených aktivit jsou zvýrazněna v seznamu okna aktivit.
* Vlastnosti vybraného objektu jsou zobrazeny v okno Vlastnosti.

Když je kanál povolený (není ve stavu pozastaveno), zobrazí se zelená čára:

![Spuštěný kanál](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Kanál můžete pozastavit, obnovit nebo ukončit jeho výběrem v zobrazení diagramu a pomocí tlačítek na panelu příkazů.

![Pozastavení nebo obnovení na panelu příkazů](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Pro kanál v zobrazení diagramu jsou k dispozici tři tlačítka panelu příkazů. K pozastavení kanálu můžete použít druhé tlačítko. Při pozastavení se neukončí aktuálně spuštěné aktivity a umožní vám to pokračovat v dokončování. Třetí tlačítko pozastaví kanál a ukončí existující spuštěné aktivity. První tlačítko obnoví kanál. Když je kanál pozastaven, změní se barva kanálu. Například pozastavený kanál vypadá jako na následujícím obrázku: 

![Kanál pozastaven](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Můžete vybrat dva nebo více kanálů pomocí klávesy CTRL. Pomocí tlačítek na panelu příkazů můžete pozastavit nebo obnovit více kanálů najednou.

Můžete také kliknout pravým tlačítkem na kanál a vybrat možnosti pro pozastavení, obnovení nebo ukončení kanálu. 

![Místní nabídka pro kanál](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Kliknutím na možnost **Otevřít kanál** zobrazíte všechny aktivity v kanálu. 

![Nabídka Otevřít kanál](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

V otevřeném Zobrazení kanálu uvidíte všechny aktivity v kanálu. V tomto příkladu je k dispozici pouze jedna aktivita: aktivita kopírování. 

![Otevřený kanál](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Chcete-li se vrátit k předchozímu zobrazení, klikněte na název datové továrny v nabídce s popisem cesty v horní části.

Když v Zobrazení kanálu vyberete výstupní datovou sadu nebo přesunete ukazatel myši na výstupní datovou sadu, zobrazí se v místním okně pro tuto datovou sadu okna aktivity.

![Místní okno okna aktivity](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Kliknutím na okno aktivity můžete zobrazit podrobnosti v okně **vlastnosti** v pravém podokně.

![Vlastnosti okna aktivity](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

V pravém podokně se přepněte na kartu **Průzkumník okna aktivity** a zobrazí se další podrobnosti.

![Snímek obrazovky, který ukazuje, jak získat přístup k kartě Průzkumník okna aktivity.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

V části s **pokusy** se zobrazí také **vyřešené proměnné** pro každý pokus o spuštění aktivity.

![Vyřešené proměnné](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Přepněte na kartu **skript** , aby se zobrazila definice skriptu JSON pro vybraný objekt.   

![Karta skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

Okna aktivit si můžete prohlédnout na třech místech:

* Automaticky otevíraná okna aktivity v zobrazení diagramu (prostřední podokno).
* Průzkumník okna aktivity v pravém podokně.
* Seznam oken aktivit v dolním podokně.

V místní nabídce okna aktivity a v Průzkumníkovi oken aktivit můžete přejít na předchozí týden a další týden pomocí šipky vlevo a vpravo.

![Šipky vlevo a vpravo v Průzkumníkovi okna aktivity](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

V dolní části zobrazení diagramu se zobrazí tato tlačítka: přiblížit, oddálit, přiblížit podle potřeby, zvětšit 100%, zamknout rozložení. Tlačítko **Uzamknout rozložení** zabraňuje nechtěnému přesunu tabulek a kanálů v zobrazení diagramu. Ve výchozím nastavení je zapnutý. Můžete ji vypnout a přesunout entity kolem diagramu. Když ho vypnete, můžete k automatickému umístění tabulek a kanálů použít poslední tlačítko. Můžete také přiblížit nebo oddálit pomocí kolečka myši.

![Příkazy lupy zobrazení diagramu](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Seznam Okna aktivit
V seznamu okna aktivity v dolní části podokna se zobrazí všechna okna aktivity pro datovou sadu, kterou jste vybrali v Průzkumník prostředků nebo zobrazení diagramu. Ve výchozím nastavení je seznam v sestupném pořadí, což znamená, že se v horní části zobrazí poslední okno aktivity.

![Seznam Okna aktivit](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Tento seznam se neaktualizuje automaticky, takže ho můžete aktualizovat ručně pomocí tlačítka Aktualizovat na panelu nástrojů.  

Okna aktivit můžou být v jednom z následujících stavů:

<table>
<tr>
    <th align="left">Status</th><th align="left">SubStatus</th><th align="left">Description</th>
</tr>
<tr>
    <td rowspan="8">Čekající</td><td>ScheduleTime</td><td>Čas nepřijde o spuštění okna aktivity.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Nadřazené závislosti nejsou připravené.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Výpočetní prostředky nejsou k dispozici.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Všechny instance aktivity jsou zaneprázdněné spouštěním dalších oken aktivit.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktivita je pozastavena a nemůže spustit okna aktivity, dokud nebude obnovena.</td>
</tr>
<tr>
<td>Zkusit znovu</td><td>Probíhá pokus o opakované spuštění aktivity.</td>
</tr>
<tr>
<td>Ověřování</td><td>Ověřování ještě nebylo zahájeno.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Ověřování čeká na opakování.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Opětovné</td><td>Probíhá ověřování.</td>
</tr>
<td>-</td>
<td>Zpracovává se okno aktivity.</td>
</tr>
<tr>
<td rowspan="4">Neúspěšný</td><td>Vypršel časový limit</td><td>Provádění aktivit trvalo déle, než je povoleno aktivitou.</td>
</tr>
<tr>
<td>Zrušeno</td><td>Činnost okna byla zrušena uživatelem.</td>
</tr>
<tr>
<td>Ověřování</td><td>Ověření se nezdařilo.</td>
</tr>
<tr>
<td>-</td><td>Nepodařilo se vygenerovat nebo ověřit okno aktivity.</td>
</tr>
<td>Připraveno</td><td>-</td><td>Okno aktivity je připravené na spotřebu.</td>
</tr>
<tr>
<td>Přeskočeno</td><td>-</td><td>Okno aktivity nebylo zpracováno.</td>
</tr>
<tr>
<td>Žádné</td><td>-</td><td>Okno aktivity použité pro existenci jiného stavu, ale bylo resetováno.</td>
</tr>
</table>


Když v seznamu kliknete na okno aktivity, zobrazí se vám podrobnosti v **Průzkumníkovi oken aktivity** nebo v pravém okně **vlastnosti** .

![Snímek obrazovky, který ukazuje, jak zobrazit podrobnosti o okně aktivity.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Aktualizovat okna aktivity
Podrobnosti se neaktualizují automaticky, proto pomocí tlačítka Aktualizovat (druhé tlačítko) na panelu příkazů ručně aktualizujte seznam oken aktivit.  

### <a name="properties-window"></a>Vlastnosti – okno
Okno Vlastnosti je v pravém krajním podokně aplikace pro monitorování a správu.

![Vlastnosti – okno](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Zobrazuje vlastnosti položky, kterou jste vybrali v seznamu Průzkumník prostředků (stromové zobrazení), zobrazení diagramu nebo okna aktivit.

### <a name="activity-window-explorer"></a>Průzkumník okna aktivity
Okno **Průzkumník okna aktivity** je v pravém krajním podokně aplikace pro monitorování a správu. Zobrazuje podrobnosti o okně aktivity, které jste vybrali v místním okně okna aktivity nebo v seznamu okna aktivit.

![Průzkumník okna aktivity](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Kliknutím na něj v zobrazení Kalendář v horní části můžete přepnout na jiné okno aktivity. Můžete také použít tlačítka se šipkou doleva a doprava v horním rohu a zobrazit okna aktivity z předchozího týdne nebo příštího týdne.

Pomocí tlačítek na panelu nástrojů v dolním podokně můžete znovu spustit okno aktivity nebo aktualizovat podrobnosti v podokně.

### <a name="script"></a>Skript
Kartu **skript** můžete použít k zobrazení definice JSON vybrané entity data Factory (propojené služby, datové sady nebo kanálu).

![Karta skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Použití systémových zobrazení
Aplikace pro monitorování a správu zahrnuje předem sestavená systémová zobrazení (**Poslední okna aktivity**, **okna se selháním aktivity**, probíhající **okna aktivity**), která umožňují zobrazit pro datovou továrnu okna poslední, neúspěšné/probíhající aktivity.

Kliknutím na kartu **zobrazení monitorování** přejděte na levou stranu.

![Karta zobrazení monitorování](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

V současné době existují tři podporovaná systémová zobrazení. Vyberte možnost pro zobrazení posledních oken aktivity, okna nezdařené aktivity nebo probíhající okna aktivity v seznamu okna aktivit (v dolní části podokna uprostřed).

Když vyberete možnost **Windows nedávné aktivity** , zobrazí se všechna okna nedávné aktivity v sestupném pořadí podle **času posledního pokusu**.

Pomocí zobrazení **Windows nezdařené aktivity** můžete zobrazit všechna okna s neúspěšnou aktivitou v seznamu. Vyberte v seznamu okno neúspěšné aktivity, ve kterém se zobrazí podrobnosti v okně **vlastnosti** nebo v **Průzkumníku okna aktivity**. Můžete si také stáhnout všechny protokoly pro okno neúspěšné aktivity.

## <a name="sort-and-filter-activity-windows"></a>Řazení a filtrování oken aktivit
Na panelu příkazů změňte nastavení **čas spuštění** a čas **ukončení** na možnost filtrovat okna aktivit. Po změně počátečního a koncového času klikněte na tlačítko vedle možnosti koncový čas a aktualizujte seznam oken aktivit.

![Počáteční a koncové časy](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> V současné době jsou všechny časy ve formátu UTC v aplikaci pro monitorování a správu.
>
>

V **seznamu okna aktivit** klikněte na název sloupce (například: stav).

![Nabídka sloupce seznamu oken aktivity](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Můžete provést následující akce:

* Řazení ve vzestupném pořadí.
* Seřadit v sestupném pořadí.
* Filtrovat podle jedné nebo více hodnot (připraveno, čeká a tak dále).

Když pro sloupec zadáte filtr, zobrazí se pro tento sloupec tlačítko filtr, které určuje, že hodnoty ve sloupci jsou filtrované hodnoty.

![Filtrovat podle sloupce seznamu oken aktivit](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Pomocí stejného překryvného okna můžete filtry vymazat. Chcete-li vymazat všechny filtry pro seznam oken aktivit, klikněte na panelu příkazů na tlačítko Vymazat filtr.

![Vymaže všechny filtry pro seznam oken aktivit.](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Provést akce dávky
### <a name="rerun-selected-activity-windows"></a>Znovu spustit vybraná okna aktivity
Vyberte okno aktivity, klikněte na šipku dolů u prvního tlačítka panelu příkazů a vyberte **znovu** spustit  /  **znovu s možností nadřazený v kanálu**. Když vyberete možnost **znovu spustit s nadřazeným kanálem v kanálu, znovu** se spustí také všechna okna s nadřazenými aktivitami.
    ![Znovu spustit okno aktivity](./media/data-factory-monitor-manage-app/ReRunSlice.png)

V seznamu můžete také vybrat několik oken aktivit a znovu je spustit. Můžete chtít filtrovat okna aktivit na základě stavu (například **selhání**) – a pak znovu spustit okna nezdařené aktivity po opravě problému, který způsobí selhání okna aktivity. Podrobnosti o oknech aktivity filtrování v seznamu najdete v následující části.  

### <a name="pauseresume-multiple-pipelines"></a>Pozastavení/obnovení více kanálů
Můžete vybrat dva nebo více kanálů pomocí klávesy CTRL. K pozastavení nebo obnovení můžete použít tlačítka panelu příkazů (zvýrazněná v červeném obdélníku na následujícím obrázku).

![Pozastavení nebo obnovení na panelu příkazů](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
