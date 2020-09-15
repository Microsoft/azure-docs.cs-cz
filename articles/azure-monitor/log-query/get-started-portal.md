---
title: 'Kurz: Začínáme s Log Analytics dotazy'
description: Přečtěte si tento kurz, jak zapisovat a spravovat Azure Monitor dotazy protokolu pomocí Log Analytics v Azure Portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: b337065f125d12e309dd1f7fcc56c2af72b1c28c
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088353"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Kurz: Začínáme s Log Analytics dotazy

V tomto kurzu se dozvíte, jak používat Log Analytics k zápisu, spouštění a správě dotazů protokolu Azure Monitor v Azure Portal. Pomocí Log Analytics dotazů můžete hledat výrazy, identifikovat trendy, analyzovat vzorce a poskytovat mnoho dalších přehledů z vašich dat. 

V tomto kurzu se naučíte, jak použít Log Analytics k těmto akcím:

> [!div class="checklist"]
> * Pochopení schématu dat protokolu
> * Zápis a spouštění jednoduchých dotazů a změna časového rozsahu pro dotazy
> * Filtrování, řazení a seskupení výsledků dotazu
> * Zobrazení, úprava a sdílení vizuálů výsledků dotazu
> * Uložení, načtení, export a kopírování dotazů a výsledků

Další informace o dotazech protokolu najdete [v tématu Přehled dotazů protokolu v Azure monitor](log-query-overview.md).<br/>
Podrobný kurz týkající se psaní dotazů na protokoly najdete v tématu [Začínáme s dotazy protokolu v Azure monitor](get-started-queries.md).

## <a name="open-log-analytics"></a>Otevřít Log Analytics
Pokud chcete použít Log Analytics, musíte být přihlášení k účtu Azure. Pokud nemáte účet Azure, [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

K dokončení většiny kroků v tomto kurzu můžete použít [toto ukázkové prostředí](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), které zahrnuje spoustu ukázkových dat. V ukázkovém prostředí nebudete moct ukládat dotazy nebo připnout výsledky na řídicí panel.

Pokud používáte Azure Monitor ke shromažďování dat protokolu aspoň na jednom prostředku Azure, můžete použít také vlastní prostředí. Chcete-li otevřít pracovní prostor Log Analytics, vyberte v Azure Monitor levém navigačním panelu možnost **protokoly**. 

## <a name="understand-the-schema"></a>Vysvětlené schématu
 
*Schéma* je kolekce tabulek seskupených pod logickými kategoriemi. Ukázkové schéma má několik kategorií z monitorování řešení. Například kategorie **LogManagement** obsahuje události Windows a syslog, údaje o výkonu a prezenční signály agenta.

Tabulky schématu se zobrazí na kartě **tabulky** v pracovním prostoru Log Analytics. Tabulky obsahují sloupce, z nichž každý má datový typ zobrazený ikonou vedle názvu sloupce. Například tabulka **událostí** obsahuje textové sloupce, jako je například **počítač** a číselné sloupce, například **EventCategory**.

![Snímek obrazovky se stránkou s protokoly Azure Portal s novým dotazem. zvýrazní se podokno tabulky s možností počítač a EventCategory.](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Zápis a spouštění základních dotazů

Log Analytics se otevře s novým prázdným dotazem v **Editoru dotazů**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Napsat dotaz

Azure Monitor dotazy protokolu používají verzi dotazovacího jazyka Kusto. Dotazy mohou být začínat názvem tabulky nebo [vyhledávacím](/azure/kusto/query/searchoperator) příkazem. 

Následující dotaz načte všechny záznamy z tabulky **událostí** :

```Kusto
Event
```

Znak kanálu (|) odděluje příkazy, takže výstup prvního příkazu je vstupem dalšího příkazu. Do jednoho dotazu můžete přidat libovolný počet příkazů. Následující dotaz načte záznamy z tabulky **událostí** a pak je vyhledá v případě **chyby** v libovolné vlastnosti:

```Kusto
Event 
| search "error"
```

Jednoduchá zalomení řádků usnadňuje čtení dotazů. Více než jedno zalomení řádku rozdělí dotaz na samostatné dotazy.

Dalším způsobem, jak napsat stejný dotaz, je:

```Kusto
search in (Event) "error"
```

V druhém příkladu příkaz **Search** vyhledá v případě **chyby**pouze záznamy v tabulce **události** .

Ve výchozím nastavení Log Analytics omezuje dotazy na časový rozsah za posledních 24 hodin. Chcete-li nastavit jiný časový rozsah, můžete do dotazu přidat explicitní filtr **TimeGenerated** nebo použít ovládací prvek **časový rozsah** .

### <a name="use-the-time-range-control"></a>Použití ovládacího prvku časový rozsah
Chcete-li použít řízení **časového rozsahu** , vyberte ho v horním panelu a pak vyberte hodnotu z rozevíracího seznamu nebo vyberte možnost **vlastní** a vytvořte vlastní časový rozsah.

![Výběr času](media/get-started-portal/time-picker.png)

- Hodnoty časového rozsahu jsou ve formátu UTC, což může být jiné než vaše místní časové pásmo.
- Pokud dotaz explicitně nastaví filtr pro **TimeGenerated**, ovládací prvek pro výběr času zobrazuje **hodnotu nastavenou v dotazu**a je zakázán, aby nedocházelo ke konfliktu.

### <a name="run-a-query"></a>Spuštění dotazu
Pokud chcete spustit dotaz, umístěte kurzor někam do dotazu a vyberte **Spustit** na horním panelu nebo stiskněte klávesu **SHIFT** + **ENTER**. Dotaz se spustí, dokud nenajde prázdný řádek.

## <a name="filter-results"></a>Filtrování výsledků
Log Analytics omezuje výsledky na maximálně 10 000 záznamů. Obecný dotaz jako `Event` vrací příliš mnoho výsledků, aby byly užitečné. Můžete filtrovat výsledky dotazu buď pomocí omezení prvků tabulky v dotazu, nebo explicitním přidáním filtru k výsledkům. Filtrování prostřednictvím prvků tabulky vrací novou sadu výsledků, zatímco explicitní filtr platí pro existující sadu výsledků dotazu.

### <a name="filter-by-restricting-table-elements"></a>Filtrovat podle omezení elementů tabulky
Chcete-li filtrovat `Event` výsledky dotazu na **chybové** události omezením prvků tabulky v dotazu:

1. Ve výsledcích dotazu vyberte šipku rozevíracího seznamu vedle libovolného záznamu, který obsahuje **chybu** ve sloupci **EventLevelName** . 
   
1. V rozbalených podrobnostech umístěte ukazatel myši na další a vyberte **...** vedle **EventLevelName**a pak vyberte **Zahrnout chybu**. 
   
   ![Přidat filtr do dotazu](media/get-started-portal/add-filter.png)
   
1. Všimněte si, že dotaz v **Editoru dotazů** se teď změnil na:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Vyberte **Spustit** a spusťte nový dotaz.

### <a name="filter-by-explicitly-filtering-results"></a>Filtrovat podle explicitních výsledků filtrování
Filtrování výsledků dotazu pro filtrování `Event` výsledků dotazu na **chybové** události:

1. V části výsledky dotazu vyberte ikonu **filtru** vedle záhlaví sloupce **EventLevelName**. 
   
1. V prvním poli překryvného okna vyberte **je rovno**a do pole Další zadejte *Error*. 
   
1. Vyberte **Filtr**.
   
   ![Snímek obrazovky zobrazuje tabulku výsledků s místní nabídkou pro filtrování výsledků pomocí EventLevelName.](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Řazení, seskupení a výběr sloupců
Pokud chcete výsledky dotazu seřadit podle konkrétního sloupce, jako je například **TimeGenerated [UTC]**, vyberte záhlaví sloupce. Znovu vyberte záhlaví, které chcete přepínat mezi vzestupném a sestupným pořadím.

![Seřadit sloupec](media/get-started-portal/sort-column.png)

Další možností uspořádání výsledků je seskupení. Chcete-li seskupit výsledky podle konkrétního sloupce, přetáhněte záhlaví sloupce na pruh nad tabulkou výsledků popisek **přetáhněte záhlaví sloupce a přetáhněte ho sem pro seskupení podle daného sloupce**. Pokud chcete vytvořit podskupiny, přetáhněte na horní pruh jiné sloupce. Můžete změnit uspořádání hierarchie a třídění skupin a podskupin na panelu.

![Snímek obrazovky zobrazuje výsledky dotazu s podskupinami pro EventLevelName a počítač.](media/get-started-portal/groups.png)

Chcete-li skrýt nebo zobrazit sloupce ve výsledcích, vyberte **sloupce** nad tabulkou a v rozevíracím seznamu vyberte nebo zrušte výběr sloupců, které chcete.

![Výběr sloupců](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Zobrazení a úprava grafů
Výsledky dotazu můžete zobrazit také ve vizuálních formátech. Jako příklad zadejte následující dotaz:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Ve výchozím nastavení se výsledky zobrazí v tabulce. Výběrem **grafu** nad tabulkou zobrazíte výsledky v grafickém zobrazení.

![Pruhový graf](media/get-started-portal/bar-chart.png)

Výsledky se zobrazí v skládaném pruhovém grafu. Vyberte další možnosti, jako **Skládaný sloupcový** nebo **výsečový** , a zobrazte tak další zobrazení výsledků.

![Výsečový graf](media/get-started-portal/pie-chart.png)

Můžete změnit vlastnosti zobrazení, například osy x a y, nebo seskupení a rozdělení předvoleb ručně z ovládacích panelů.

Můžete také nastavit preferované zobrazení v samotném dotazu pomocí operátoru [vykreslení](/azure/kusto/query/renderoperator) .

## <a name="pin-results-to-a-dashboard"></a>Připnutí výsledků na řídicí panel

Pokud chcete tabulku výsledků nebo graf připnout z Log Analytics na sdílený řídicí panel Azure, vyberte na horním panelu **Připnout na řídicí panel** . 

![Připnout na řídicí panel](media/get-started-portal/pin-dashboard.png)

V podokně **Připnout na jiný řídicí panel** vyberte nebo vytvořte sdílený řídicí panel, na který chcete připnout, a vyberte **použít**. Tabulka nebo graf se zobrazí na vybraném řídicím panelu Azure.

![Graf připnuté na řídicí panel](media/get-started-portal/pin-dashboard2.png)

Tabulka nebo graf, které připnete do sdíleného řídicího panelu, mají následující zjednodušení: 

- Data jsou omezená na posledních 14 dní.
- Tabulka obsahuje jenom čtyři sloupce a prvních sedm řádků.
- Grafy, které mají mnoho diskrétních kategorií, automaticky seskupí méně vyplněné kategorie do jedné přihrádky **ostatní** .

## <a name="save-load-or-export-queries"></a>Uložení, načtení nebo export dotazů

Po vytvoření dotazu můžete dotaz nebo výsledky sdílet s ostatními. 

### <a name="save-queries"></a>Uložení dotazů

Uložení dotazu:

1. Vyberte **Uložit** na horním panelu.
   
1. V dialogovém okně **Uložit** zadejte dotaz na **název**pomocí znaků A – Z, a – z, 0-9, mezera, pomlčka, podtržítko, tečka, závorka nebo kanál. 
   
1. Vyberte, zda má být dotaz uložen jako **dotaz** nebo **funkce**. Funkce jsou dotazy, na které můžou odkazovat jiné dotazy. 
   
   Chcete-li uložit dotaz jako funkci, zadejte **alias funkce**, což je krátký název pro jiné dotazy, které se mají použít pro volání tohoto dotazu.
   
1. Pokud jste v pracovním prostoru Log Analytics, poskytněte **kategorii** pro **Průzkumník dotazů** , která se má použít pro dotaz. (Kategorie nejsou k dispozici pro dotazy Application Insights.)
   
1. Vyberte **Uložit**.
   
   ![Save – funkce](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Načíst dotazy
Pokud chcete načíst uložený dotaz, vyberte v pravém horním rohu možnost **Průzkumník dotazů** . Otevře se podokno **Průzkumník dotazů** s výpisem všech dotazů podle kategorie. Rozbalte kategorie nebo zadejte název dotazu do panelu hledání a pak vyberte dotaz, který se načte do **Editoru dotazů**. Dotaz můžete označit jako **oblíbenou** volbou hvězdičky vedle názvu dotazu.

![Průzkumník dotazů](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Exportovat a sdílet dotazy
Pokud chcete exportovat dotaz, vyberte **exportovat** na horním panelu a pak vyberte **exportovat do sdíleného svazku clusteru – všechny sloupce**, **exportovat do sloupců, které se zobrazí**, nebo **exportovat do Power BI (dotaz M)** z rozevíracího seznamu.

Následující video ukazuje, jak integrovat Log Analytics s Excelem.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Asme]

Pokud chcete sdílet odkaz na dotaz, vyberte **Kopírovat odkaz** na horním panelu a pak vyberte **Kopírovat odkaz na dotaz**, **Kopírovat text dotazu**nebo **zkopírovat výsledky dotazu** , které se zkopírují do schránky. Odkaz na dotaz můžete odeslat jiným uživatelům, kteří mají přístup ke stejnému pracovnímu prostoru.

## <a name="next-steps"></a>Další kroky

V dalším kurzu se dozvíte další informace o psaní Azure Monitorch dotazů protokolu.
> [!div class="nextstepaction"]
> [Zápis Azure Monitorch dotazů protokolu](get-started-queries.md)
