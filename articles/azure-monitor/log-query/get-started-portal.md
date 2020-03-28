---
title: 'Kurz: Začínáme s dotazy analýzy protokolů'
description: Naučte se z tohoto kurzu, jak psát a spravovat dotazy protokolu Azure Monitor pomocí Log Analytics na webu Azure Portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 29e24166218a6757cded9d1b002321800ab0c073
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80055433"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Kurz: Začínáme s dotazy analýzy protokolů

Tento kurz ukazuje, jak pomocí Analýzy protokolů psát, spouštět a spravovat dotazy protokolu Azure Monitor na webu Azure Portal. Pomocí dotazů Log Analytics můžete vyhledávat termíny, identifikovat trendy, analyzovat vzorce a poskytovat mnoho dalších přehledů z vašich dat. 

V tomto kurzu se dozvíte, jak pomocí analýzy protokolů:

> [!div class="checklist"]
> * Principy schématu dat protokolu
> * Psaní a spouštění jednoduchých dotazů a úprava časového rozsahu dotazů
> * Filtrování, řazení a seskupování výsledků dotazu
> * Zobrazení, úprava a sdílení vizuálů výsledků dotazu
> * Ukládání, načítání, export a kopírování dotazů a výsledků

Další informace o dotazech protokolu najdete [v tématu Přehled dotazů protokolu v Azure Monitoru](log-query-overview.md).<br/>
Podrobný kurz o psaní dotazů protokolu najdete [v tématu Začínáme s dotazy protokolu v Azure Monitoru](get-started-queries.md).

## <a name="open-log-analytics"></a>Otevřít analýzu protokolů
Pokud chcete používat Log Analytics, musíte být přihlášení k účtu Azure. Pokud nemáte účet Azure, [vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Chcete-li dokončit většinu kroků v tomto kurzu, můžete použít [toto ukázkové prostředí](https://portal.loganalytics.io/demo), které obsahuje velké množství ukázkových dat. Díky ukázkovému prostředí nebudete moct ukládat dotazy ani připnout výsledky na řídicí panel.

Můžete také použít vlastní prostředí, pokud používáte Azure Monitor ke shromažďování dat protokolu alespoň na jednom prostředku Azure. Pokud chcete otevřít pracovní prostor Log Analytics, v levém navigačním panelu Azure Monitoru vyberte **Protokoly**. 

## <a name="understand-the-schema"></a>Vysvětlené schématu
*Schéma* je kolekce tabulek seskupených do logických kategorií. Ukázkové schéma má několik kategorií z monitorování řešení. Například **LogManagement** kategorie obsahuje windows a Syslog události, data o výkonu a prezenční chod agenta.

Tabulky schématu se zobrazí na **kartě Tabulky** v pracovním prostoru Analýzy protokolů. Tabulky obsahují sloupce, z nichž každý má datový typ zobrazený ikonou vedle názvu sloupce. Tabulka **Událostí** například obsahuje textové sloupce, jako **je Počítač,** a číselné sloupce, jako **je EventCategory**.

![Schéma](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Psaní a spouštění základních dotazů

Log Analytics se otevře s novým prázdným dotazem v **editoru dotazů**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Napsat dotaz
Dotazy protokolu Azure Monitor použít verzi dotazovacího jazyka Kusto. Dotazy mohou začínat názvem tabulky nebo příkazem [hledání.](/azure/kusto/query/searchoperator) 

Následující dotaz načte všechny záznamy z tabulky **Událost:**

```Kusto
Event
```

Znak kanálu (|) odděluje příkazy, takže výstup prvního příkazu je vstupem dalšího příkazu. Do jednoho dotazu můžete přidat libovolný počet příkazů. Následující dotaz načte záznamy z tabulky **Událost** a poté je vyhledá v libovolné vlastnosti **termínu:**

```Kusto
Event 
| search "error"
```

Jeden řádek zalomení usnadňuje dotazy číst. Více než jeden řádek konec rozdělí dotaz na samostatné dotazy.

Dalším způsobem, jak napsat stejný dotaz, je:

```Kusto
search in (Event) "error"
```

V druhém příkladu hledá příkaz **hledat** pouze záznamy v tabulce **Události** pro termín **chyba**.

Ve výchozím nastavení služba Log Analytics omezuje dotazy na časový rozsah za posledních 24 hodin. Chcete-li nastavit jiný časový rozsah, můžete do dotazu přidat explicitní filtr **TimeGenerated** nebo použít ovládací prvek **Časový rozsah.**

### <a name="use-the-time-range-control"></a>Použití ovládacího prvku Časový rozsah
Chcete-li použít ovládací prvek **Časový rozsah,** vyberte ho v horním pruhu a pak vyberte hodnotu z rozevíracího seznamu nebo vyberte **Vlastní,** chcete-li vytvořit vlastní časový rozsah.

![Výběr času](media/get-started-portal/time-picker.png)

- Hodnoty časového rozsahu jsou v utc, které se mohou lišit od místního časového pásma.
- Pokud dotaz explicitně nastaví filtr pro **TimeGenerated**, ovládací prvek výběru času zobrazí **Nastavit v dotazu**a je zakázán, aby se zabránilo konfliktu.

### <a name="run-a-query"></a>Spuštění dotazu
Chcete-li spustit dotaz, umístěte kurzor někde uvnitř dotazu a vhorní liště vyberte **Spustit** nebo stiskněte **Shift**+**Enter**. Dotaz se spustí, dokud nenajde prázdný řádek.

## <a name="filter-results"></a>Filtrování výsledků
Log Analytics omezuje výsledky na maximálně 10 000 záznamů. Obecný dotaz `Event` jako vrátí příliš mnoho výsledků, aby byly užitečné. Výsledky dotazu můžete filtrovat buď omezením prvků tabulky v dotazu, nebo explicitním přidáním filtru k výsledkům. Filtrování prvků tabulky vrátí novou sadu výsledků, zatímco explicitní filtr se vztahuje na existující sadu výsledků.

### <a name="filter-by-restricting-table-elements"></a>Filtrování omezením prvků tabulky
Filtrování `Event` výsledků dotazu na **chybové** události omezením prvků tabulky v dotazu:

1. Ve výsledcích dotazu vyberte šipku rozevíracího seznamu vedle libovolného záznamu, který má **chybu** ve sloupci **EventLevelName.** 
   
1. V rozšířených podrobnostech najeďte dál a vyberte **...** vedle **Položky EventLevelName**a pak vyberte **Zahrnout "Chyba"**. 
   
   ![Přidání filtru do dotazu](media/get-started-portal/add-filter.png)
   
1. Všimněte si, že dotaz v **editoru dotazů** se nyní změnil na:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Chcete-li spustit nový dotaz, vyberte **spustit.**

### <a name="filter-by-explicitly-filtering-results"></a>Filtrování podle explicitního filtrování výsledků
Chcete-li `Event` filtrovat výsledky dotazu na **chybové** události filtrováním výsledků dotazu:

1. Ve výsledcích dotazu vyberte ikonu **Filtr** vedle záhlaví sloupce **EventLevelName**. 
   
1. V prvním poli rozbalovacího okna vyberte **Možnost Je rovna**a v dalším poli zadejte *chybu*. 
   
1. Vyberte **filtr**.
   
   ![Filtr](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Řazení, seskupit a vybírat sloupce
Chcete-li seřadit výsledky dotazu podle určitého sloupce, například **TimeGenerated [UTC],** vyberte záhlaví sloupce. Znovu vyberte nadpis, chcete-li přepínat mezi vzestupným a sestupným pořadím.

![Sloupec řazení](media/get-started-portal/sort-column.png)

Dalším způsobem, jak uspořádat výsledky, je podle skupin. Chcete-li seskupit výsledky podle určitého sloupce, přetáhněte záhlaví sloupce na pruh nad výslednou tabulku s popiskem **Přetáhněte záhlaví sloupce a přetáhněte ho sem, abyste je seskupili podle tohoto sloupce**. Chcete-li vytvořit podskupiny, přetáhněte ostatní sloupce do horního pruhu. Můžete změnit uspořádání hierarchie a řazení skupin a podskupin v pruhu.

![Skupiny](media/get-started-portal/groups.png)

Pokud chcete skrýt nebo zobrazit sloupce ve výsledcích, vyberte **Sloupce** nad tabulkou a pak v rozevíracím seznamu vyberte nebo zrušte výběr požadovaných sloupců.

![Výběr sloupců](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Zobrazení a úpravy grafů
Můžete také zobrazit výsledky dotazu ve vizuálních formátech. Jako příklad zadejte následující dotaz:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Ve výchozím nastavení se výsledky zobrazují v tabulce. Výběrem **možnosti Graf** nad tabulkou zobrazíte výsledky v grafickém zobrazení.

![Pruhový graf](media/get-started-portal/bar-chart.png)

Výsledky se zobrazí ve skládaném pruhovém grafu. Vyberte další možnosti, jako **je Skládaný sloupec** nebo **Výsečový graf,** chcete-li zobrazit další zobrazení výsledků.

![Výsečový graf](media/get-started-portal/pie-chart.png)

Vlastnosti pohledu, například osy x a y, nebo předvolby seskupení a rozdělení můžete změnit ručně z ovládacího panelu.

Můžete také nastavit upřednostňované zobrazení v samotném dotazu pomocí operátoru [vykreslení.](/azure/kusto/query/renderoperator)

## <a name="pin-results-to-a-dashboard"></a>Připnutí výsledků na řídicí panel
Pokud chcete připnout tabulku výsledků nebo graf z Analýzy protokolů na sdílený řídicí panel Azure, vyberte **Připnout na řídicí panel** na horním panelu. 

![Připnout na řídicí panel](media/get-started-portal/pin-dashboard.png)

V **podokně Připnout k jinému řídicímu panelu** vyberte nebo vytvořte sdílený řídicí panel, na který se chcete připnout, a vyberte **Použít**. Tabulka nebo graf se zobrazí na vybraném řídicím panelu Azure.

![Graf připnutý na řídicí panel](media/get-started-portal/pin-dashboard2.png)

Tabulka nebo graf, který připnete ke sdílenému řídicímu panelu, má následující zjednodušení: 

- Data jsou omezena na posledních 14 dní.
- Tabulka zobrazuje pouze čtyři sloupce a prvních sedm řádků.
- Grafy s mnoha diskrétními kategoriemi automaticky seskupují méně vyplněné kategorie do jedné **další** přihrádky.

## <a name="save-load-or-export-queries"></a>Uložení, načtení nebo export dotazů
Po vytvoření dotazu můžete dotaz nebo výsledky uložit nebo sdílet s ostatními. 

### <a name="save-queries"></a>Uložení dotazů
Uložení dotazu:

1. V horní liště vyberte **Uložit.**
   
1. V dialogovém okně **Uložit** přiřazuje dotazu **název**pomocí znaků a–z, A–Z, 0-9, mezery, pomlčky, podtržítka, tečky, závorek nebo kanálu. 
   
1. Vyberte, zda chcete dotaz uložit jako **dotaz** nebo **funkci**. Funkce jsou dotazy, na které mohou odkazovat jiné dotazy. 
   
   Chcete-li uložit dotaz jako funkci, zadejte **alias funkce**, což je krátký název pro jiné dotazy, které mají být volány tento dotaz.
   
1. Zadejte **kategorii** **průzkumníka dotazu,** který se má použít pro dotaz.
   
1. Vyberte **Uložit**.
   
   ![Uložit funkci](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Načtení dotazů
Chcete-li načíst uložený dotaz, vyberte **Průzkumník dotazů** v pravém horním bodě. Otevře se podokno **Průzkumník dotazů** se seznamem všech dotazů podle kategorií. Rozbalte kategorie nebo zadejte název dotazu do vyhledávacího panelu a vyberte dotaz, který chcete načíst do **editoru dotazů**. Dotaz můžete označit jako **oblíbenou** výběrem hvězdičky vedle názvu dotazu.

![Průzkumník dotazů](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Export a sdílení dotazů
Pokud chcete exportovat dotaz, vyberte **Exportovat** na horním panelu a pak v rozevíracím seznamu vyberte **Exportovat do CSV – všechny sloupce**, **Exportovat do CSV – zobrazené sloupce**nebo **Exportovat do Power BI (dotaz M).**

Chcete-li sdílet odkaz na dotaz, vyberte **Kopírovat odkaz** na horním panelu a pak vyberte Kopírovat odkaz **na dotaz**, **Zkopírujte text dotazu**nebo **Zkopírujte výsledky dotazu,** které chcete zkopírovat do schránky. Odkaz na dotaz můžete odeslat ostatním uživatelům, kteří mají přístup ke stejnému pracovnímu prostoru.

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu kurzu, kde se dozvíte další informace o psaní dotazů protokolu Azure Monitor.
> [!div class="nextstepaction"]
> [Zápis dotazů protokolu Azure Monitor](get-started-queries.md)
