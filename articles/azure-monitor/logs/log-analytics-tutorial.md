---
title: Kurz Log Analytics
description: V tomto kurzu se dozvíte, jak používat funkce Log Analytics v Azure Monitor k vytvoření dotazu na spuštění protokolu a k analýze jeho výsledků v Azure Portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: a7a6ba395769677fe46ddfff675640f6e15060a0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723176"
---
# <a name="log-analytics-tutorial"></a>Kurz Log Analytics
Log Analytics je nástroj v Azure Portal, který umožňuje upravovat a spouštět dotazy protokolu z dat shromažďovaných službou Azure Monitor protokoly a interaktivně analyzovat jejich výsledky. Pomocí Log Analytics dotazů můžete načíst záznamy, které odpovídají konkrétním kritériím, identifikovat trendy, analyzovat vzorce a poskytovat nejrůznější přehledy o vašich datech. 

Tento kurz vás provede rozhraním Log Analytics, který vám pomůže začít s některými základními dotazy a ukazuje, jak můžete pracovat s výsledky. Naučíte se:

> [!div class="checklist"]
> * Pochopení schématu dat protokolu
> * Zápis a spouštění jednoduchých dotazů a změna časového rozsahu pro dotazy
> * Filtrování, řazení a seskupení výsledků dotazu
> * Zobrazení, úprava a sdílení vizuálů výsledků dotazu
> * Načtení, export a kopírování dotazů a výsledků

> [!IMPORTANT]
> V tomto kurzu se používá funkce Log Analytics k sestavení a spuštění dotazu namísto práce s samotným dotazem. Budete využívat Log Analytics funkce pro vytvoření jednoho dotazu a použití dalšího ukázkového dotazu. Až budete připraveni zjistit syntaxi dotazů a začít přímo upravovat samotný dotaz, Projděte si [kurz dotazovacího jazyka Kusto](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor). V tomto kurzu se seznámíte s několika příklady dotazů, které můžete upravovat a spouštět v Log Analytics s využitím několika funkcí, které se v tomto kurzu naučíte.


## <a name="prerequisites"></a>Požadavky
V tomto kurzu se používá ukázkové [prostředí Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), které zahrnuje spoustu ukázkových dat podporujících Ukázkové dotazy. Můžete také použít vlastní předplatné Azure, ale možná nebudete mít data ve stejných tabulkách.

## <a name="open-log-analytics"></a>Otevřít Log Analytics
Otevřete [Log Analytics ukázkové prostředí](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) nebo v nabídce Azure monitor v předplatném vyberte **protokoly** . Tím se nastaví počáteční obor na Log Analytics pracovní prostor, což znamená, že váš dotaz vybere ze všech dat v daném pracovním prostoru. Pokud vyberete **protokoly** z nabídky prostředku Azure, obor se nastaví jenom na záznamy z tohoto prostředku. Podrobnosti o oboru najdete v tématu [rozsah dotazu protokolu](./scope.md) .

Rozsah můžete zobrazit v levém horním rohu obrazovky. Pokud používáte vlastní prostředí, zobrazí se možnost vybrat jiný obor, ale tato možnost není k dispozici v ukázkovém prostředí.

[![Rozsah dotazu](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>Schéma tabulky
Levá strana obrazovky obsahuje kartu **tabulky** , která vám umožní zkontrolovat tabulky, které jsou dostupné v aktuálním oboru. Ve výchozím nastavení jsou seskupené podle **řešení** , ale měníte jejich seskupení nebo filtrování. 

Rozbalte řešení **Správa protokolů** a vyhledejte tabulku **AzureActivity** . Můžete rozbalit tabulku pro zobrazení jejího schématu nebo najeďte myší na její název a zobrazit tak další informace o ní. 

[![Zobrazení tabulek](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

Kliknutím na **Další informace** můžete přejít na odkaz na tabulku, který bude dokumentovat každou tabulku a její sloupce. Klikněte na **Náhled dat** , abyste se mohli rychle podívat na několik nedávných záznamů v tabulce. To může být užitečné, aby se zajistilo, že se jedná o data, která očekáváte před samotným spuštěním dotazu.

[![Ukázková data](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>Napsat dotaz
Pojďme pokračovat a napsat dotaz pomocí tabulky **AzureActivity** . Poklikejte na jeho název a přidejte ho do okna dotazu. Můžete také zadat přímo v okně a dokonce získat IntelliSense, který vám pomůže dokončit názvy tabulek v aktuálním oboru a příkazech KQL.

Toto je nejjednodušší dotaz, který můžeme zapsat. Pouze vrátí všechny záznamy v tabulce. Spusťte ji kliknutím na tlačítko **Spustit** nebo stisknutím klávesy SHIFT + ENTER s kurzorem umístěným kdekoli v textu dotazu.

[![Výsledky dotazu](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

Uvidíte, že máme výsledky. Počet záznamů vrácených dotazem se zobrazí v pravém dolním rohu. 

## <a name="filter"></a>Filtrovat

Pojďme do dotazu přidat filtr, aby se snížil počet vrácených záznamů. V levém podokně vyberte kartu **Filtr** . Tato možnost zobrazí různé sloupce ve výsledcích dotazu, které lze použít k filtrování výsledků. Nejvyšší hodnoty v těchto sloupcích se zobrazí s počtem záznamů s touto hodnotou. Klikněte na **správce** v části **CategoryValue** a pak **použijte & spustit**. 

[![Podokno dotazu](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

Příkaz **WHERE** se přidá do dotazu s hodnotou, kterou jste vybrali. Výsledky nyní obsahují pouze záznamy s touto hodnotou, abyste viděli, že počet záznamů je snížen.

[![Filtrované výsledky dotazu](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>Časové rozmezí
Všechny tabulky v pracovním prostoru Log Analytics mají sloupec s názvem **TimeGenerated** , což je čas vytvoření záznamu. Všechny dotazy mají časový rozsah, který omezí výsledky na záznamy s hodnotou **TimeGenerated** v daném rozsahu. Časový rozsah lze nastavit buď v dotazu, nebo pomocí selektoru v horní části obrazovky.

Ve výchozím nastavení bude dotaz vracet záznamy za posledních 24 hodin. Vyberte rozevírací seznam **časový rozsah** a změňte jej na **7 dní**. Kliknutím na tlačítko **Spustit** znovu vrátíte výsledky. Můžete vidět, že se vrátí výsledky, ale tady se zobrazí zpráva, že nevidíme všechny výsledky. Důvodem je to, že Log Analytics může vracet maximálně 10 000 záznamů a náš dotaz vrátil více záznamů. 

[![Časový rozsah](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>Více podmínek dotazu
Pojďme výsledky dál snížit přidáním další podmínky filtru. Dotaz může obsahovat libovolný počet filtrů pro cílení přesně na sadu záznamů, které chcete. V části **ActivityStatusValue** vyberte **úspěch** a klikněte na **použít & spustit**. 

[![Více filtrů výsledků dotazu](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>Analýza výsledků
Kromě pomoci psaní a spouštění dotazů Log Analytics poskytuje funkce pro práci s výsledky. Začněte tím, že rozbalíte záznam a zobrazíte hodnoty všech jeho sloupců.

[![Rozbalit záznam](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

Kliknutím na název libovolného sloupce seřadíte výsledky podle daného sloupce. Kliknutím na ikonu filtru vedle něho zadejte podmínku filtru. To je podobné jako přidání podmínky filtru do samotného dotazu s tím rozdílem, že tento filtr je vymazán, pokud je dotaz znovu spuštěn. Tuto metodu použijte, chcete-li rychle analyzovat sadu záznamů v rámci interaktivní analýzy.

Nastavte například filtr na sloupec **CallerIpAddress** , který omezí záznamy na jednoho volajícího. 

[![Filtr výsledků dotazu](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

Místo filtrování výsledků můžete seskupit záznamy podle konkrétního sloupce. Vymažte právě vytvořený filtr a potom zapněte posuvník **sloupce skupiny** . 

[![Seskupení sloupců](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

Nyní přetáhněte sloupec **CallerIpAddress** do řádku seskupení. Výsledky jsou nyní uspořádány podle tohoto sloupce a můžete sbalit jednotlivé skupiny, které vám pomohou s analýzou.

[![Seskupené výsledky dotazu](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>Práce s grafy
Pojďme se podívat na dotaz, který používá číselná data, která je možné zobrazit v grafu. Namísto sestavování dotazu vybereme ukázkový dotaz.

V levém podokně klikněte na **dotazy** . Toto podokno obsahuje příklady dotazů, které můžete přidat do okna dotazu. Pokud používáte vlastní pracovní prostor, měli byste mít nejrůznější dotazy ve více kategoriích, ale pokud používáte ukázkové prostředí, může se zobrazit jenom jedna kategorie **Log Analytics pracovní prostory** . Chcete-li zobrazit dotazy v kategorii, rozbalte ji.

Klikněte na dotaz s názvem **Počet požadavků podle ResponseCode**. Tím se dotaz přidá do okna dotazu. Všimněte si, že nový dotaz je od sebe oddělen prázdným řádkem. Dotaz v KQL končí, když nalezne prázdný řádek, takže se tyto dotazy zobrazují jako samostatné dotazy. 

[![Nový dotaz](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

Aktuální dotaz je ten, na kterém je umístěn kurzor. Vidíte, že je první dotaz zvýrazněný, což značí, že se jedná o aktuální dotaz. Kliknutím na libovolné místo v novém dotazu ho vyberte a potom ho spusťte kliknutím na tlačítko **Spustit** .

[![Graf výsledků dotazu](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

Všimněte si, že tento výstup je graf místo tabulky, jako je například poslední dotaz. To je proto, že vzorový dotaz na konci používá příkaz [vykreslení](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) . Všimněte si, že existují různé možnosti pro práci s grafem, jako je například změna na jiný typ.

Zkuste vybrat **výsledky** a zobrazit výstup dotazu jako tabulku. 

[![Tabulka výsledků dotazu](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>Další kroky

Když teď víte, jak používat Log Analytics, dokončete kurz použití dotazů protokolu.
> [!div class="nextstepaction"]
> [Zápis Azure Monitorch dotazů protokolu](get-started-queries.md)