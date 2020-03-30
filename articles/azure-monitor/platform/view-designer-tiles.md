---
title: Referenční příručka k dlaždicím Návrháře zobrazení ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Pomocí Návrháře zobrazení v Azure Monitoru můžete vytvořit vlastní zobrazení, která se zobrazují na webu Azure Portal a obsahují různé vizualizace dat v pracovním prostoru Log Analytics. Tento článek je referenční příručkou k nastavení dlaždic, které jsou k dispozici ve vlastních zobrazeních.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: 0320be3a2cfbb96367799577a6e56bcf5da87dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658502"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Referenční příručka k dlaždicím Návrháře zobrazení v Azure Monitoru
Pomocí Návrháře zobrazení v Azure Monitoru můžete na webu Azure Portal vytvořit celou řadu vlastních zobrazení, která vám pomůžou vizualizovat data v pracovním prostoru Log Analytics. Tento článek je referenční příručkou k nastavení dlaždic, které jsou k dispozici ve vlastních zobrazeních.

Další informace o Návrháři zobrazení naleznete v tématu:

* [Návrhář zobrazení](view-designer.md): Poskytuje přehled návrháře zobrazení a postupy pro vytváření a úpravy vlastních zobrazení.
* [Odkaz na vizualizační část](view-designer-parts.md): Poskytuje referenční příručku k nastavení vizualizačních částí, které jsou k dispozici ve vlastních pohledech.


Dostupné dlaždice Návrháře zobrazení jsou popsány v následující tabulce:  

| dlaždici | Popis |
|:--- |:--- |
| [Číslo](#number-tile) |Počet záznamů z dotazu. |
| [Dvě čísla](#two-numbers-tile) |Počty záznamů ze dvou různých dotazů. |
| [Kobliha](#donut-tile) | Graf založený na dotazu se souhrnnou hodnotou uprostřed. |
| Spojnicový graf a popisek | Spojnicový graf založený na dotazu a popisek se souhrnnou hodnotou. |
| [Spojnicový graf](#line-chart-tile) |Spojnicový graf založený na dotazu. |
| [Dvě časové osy](#two-timelines-tile) | Sloupcový graf se dvěma řadami, každá založená na samostatném dotazu. |

V dalších částech podrobně popisují typy dlaždic a jejich vlastnosti.

> [!NOTE]
> Dlaždice v zobrazeních jsou založeny na [dotazech protokolu](../log-query/log-query-overview.md) v pracovním prostoru Log Analytics. V současné době nepodporují [dotazy na příčné prostředky](../log-query/cross-workspace-query.md) k načtení dat z Application Insights.

## <a name="number-tile"></a>Dlaždice číslo
Dlaždice **Číslo** zobrazuje počet záznamů z dotazu protokolu i popisek.

![Dlaždice číslo](media/view-designer-tiles/tile-number.png)

| Nastavení | Popis |
|:--- |:--- |
| Name (Název) |Text, který je zobrazen v horní části dlaždice. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| **Dlaždice** | |
| Legenda |Text, který je zobrazen pod hodnotou. |
| Dotaz |Dotaz, který je spuštěn. Zobrazí se počet záznamů vrácených dotazem. |
| **Upřesnit** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověření toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup obvykle používáte k poskytnutí zprávy během dočasného období, kdy je zobrazení nainstalováno a data budou k dispozici. |
| Dotaz |Dotaz, který je spuštěn k určení, zda jsou k dispozici data pro zobrazení. Pokud dotaz vrátí žádné výsledky, zobrazí se místo hodnoty hlavního dotazu zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud ověřovací dotaz toku dat vrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *provádění hodnocení.* |


## <a name="two-numbers-tile"></a>Dlaždice Dvě čísla
Tato dlaždice zobrazuje počet záznamů ze dvou různých dotazů protokolu a popisek pro každý.

![Dlaždice Dvě čísla](media/view-designer-tiles/tile-two-numbers.png)

| Nastavení | Popis |
|:--- |:--- |
| Name (Název) |Text, který je zobrazen v horní části dlaždice. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| **První dlaždice** | |
| Legenda |Text, který je zobrazen pod hodnotou. |
| Dotaz |Dotaz, který je spuštěn. Zobrazí se počet záznamů vrácených dotazem. |
| **Druhá dlaždice** | |
| Legenda |Text, který je zobrazen pod hodnotou. |
| Dotaz |Dotaz, který je spuštěn. Zobrazí se počet záznamů vrácených dotazem. |
| **Upřesnit** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověření toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup obvykle používáte k poskytnutí zprávy během dočasného období, kdy je zobrazení nainstalováno a data budou k dispozici. |
| Dotaz |Dotaz, který je spuštěn k určení, zda jsou k dispozici data pro zobrazení. Pokud dotaz vrátí žádné výsledky, zobrazí se místo hodnoty hlavního dotazu zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud ověřovací dotaz toku dat vrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *provádění hodnocení.* |


## <a name="donut-tile"></a>Koblihová dlaždice
Dlaždice **Donut** zobrazí jedno číslo, které shrnuje sloupec hodnoty v dotazu protokolu. Kobliha graficky zobrazuje výsledky prvních tří záznamů.

![Koblihová dlaždice](media/view-designer-tiles/tile-donut.png)

| Nastavení | Popis |
|:--- |:--- |
| Name (Název) |Text, který je zobrazen v horní části dlaždice. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| **Kobliha** | |
| Dotaz |Dotaz, který je spuštěn pro koblihu. První vlastnost je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *míra* sumarizovat výsledky. |
| **Kobliha** |**> centrum** |
| Text |Text, který je zobrazen pod hodnotou uvnitř koblihy. |
| Operace |Operace, která se provádí na vlastnost value shrnout jako jednu hodnotu.<ul><li>Součet: Přidejte hodnoty všech záznamů s hodnotou vlastnosti.</li><li>Procento: Procento sečtených hodnot ze záznamů s hodnotou vlastnosti v porovnání se sečtenými hodnotami všech záznamů.</li></ul> |
| Výsledné hodnoty použité v provozu středu |Volitelně můžete vybrat znaménko plus (+) a přidat jednu nebo více hodnot. Výsledky dotazu jsou omezeny na záznamy s zadanými hodnotami vlastností. Pokud nejsou přidány žádné hodnoty, jsou do dotazu zahrnuty všechny záznamy. |
| **Kobliha** |**> Další možnosti** |
| Barvy |Barva, která je zobrazena pro každou ze tří nejvyšších vlastností. Chcete-li určit alternativní barvy pro určité hodnoty vlastností, použijte *rozšířené mapování barev*. |
| Pokročilé mapování barev |Zobrazí barvu, která představuje určité hodnoty vlastností. Pokud je zadaná hodnota v první trojce, zobrazí se místo standardní barvy alternativní barva. Pokud vlastnost není v prvních třech, barva se nezobrazí. |
| **Upřesnit** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověření toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup obvykle používáte k poskytnutí zprávy během dočasného období, kdy je zobrazení nainstalováno a data budou k dispozici. |
| Dotaz |Dotaz, který je spuštěn k určení, zda jsou k dispozici data pro zobrazení. Pokud dotaz vrátí žádné výsledky, zobrazí se místo hodnoty hlavního dotazu zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud ověřovací dotaz toku dat vrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *provádění hodnocení.* |


## <a name="line-chart-tile"></a>Dlaždice spojnicového grafu
Tato dlaždice je spojnicový graf, který zobrazuje více řad z dotazu protokolu v průběhu času. 

![Spojnicový graf a dlaždice popisku](media/view-designer-tiles/tile-line-chart.png)

| Nastavení | Popis |
|:--- |:--- |
| Name (Název) |Text, který je zobrazen v horní části dlaždice. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který je spuštěn pro spojnicový graf. První vlastnost je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *míra* sumarizovat výsledky. Pokud dotaz používá klíčové slovo *interval,* používá tento časový interval osa x. Pokud dotaz nepoužívá klíčové slovo *interval,* používá osa x hodinové intervaly. |
| **Spojnicový graf** |**> osy Y** |
| Použít logaritmické měřítko |Vyberte tento odkaz, chcete-li použít logaritmické měřítko pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty vrácené dotazem. Tyto informace slouží k zobrazení popisků v grafu označujících typy hodnot a volitelně k převodu hodnot. **Typ jednotky** určuje kategorii jednotky a definuje aktuální hodnoty **typu jednotky,** které jsou k dispozici. Pokud vyberete hodnotu v **převést na** pak číselné hodnoty jsou převedeny z typu **Aktuální jednotka** na **Convert to** type. |
| Vlastní popisek |Text, který se zobrazí pro osu y vedle popisku pro typ *jednotky.* Pokud není zadán žádný popisek, zobrazí se pouze typ *jednotky.* |
| **Upřesnit** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověření toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup obvykle používáte k poskytnutí zprávy během dočasného období, kdy je zobrazení nainstalováno a data budou k dispozici. |
| Dotaz |Dotaz, který je spuštěn k určení, zda jsou k dispozici data pro zobrazení. Pokud dotaz vrátí žádné výsledky, zobrazí se místo hodnoty hlavního dotazu zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud ověřovací dotaz toku dat vrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *provádění hodnocení.* |


## <a name="line-chart-and-callout-tile"></a>Spojnicový graf a dlaždice popisku
Tato dlaždice obsahuje jak spojnicový graf, který zobrazuje více řad z dotazu protokolu v průběhu času, tak popisek se souhrnnou hodnotou. 

![Spojnicový graf a dlaždice popisku](media/view-designer-tiles/tile-line-chart-callout.png)

| Nastavení | Popis |
|:--- |:--- |
| Name (Název) |Text, který je zobrazen v horní části dlaždice. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který je spuštěn pro spojnicový graf. První vlastnost je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *míra* sumarizovat výsledky. Pokud dotaz používá klíčové slovo *interval,* používá tento časový interval osa x. Pokud dotaz nepoužívá klíčové slovo *interval,* používá osa x hodinové intervaly. |
| **Spojnicový graf** |**> popisek** |
| Název popisku | Text, který je zobrazen nad hodnotou popisku. |
| Název řady |Hodnota vlastnosti řady, která má být použita jako hodnota popisku. Pokud není k dispozici žádná řada, budou použity všechny záznamy z dotazu. |
| Operace |Operace, která se provádí na vlastnost value shrnout jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů, které jsou vráceny dotazem.</li><li>Poslední ukázka: Hodnota posledního intervalu, který je součástí grafu.</li><li>Max: Maximální hodnota intervalů, které jsou zahrnuty v grafu.</li><li>Min: Minimální hodnota intervalů, které jsou zahrnuty v grafu.</li><li>Součet: Součet hodnot ze všech záznamů.</li></ul> |
| **Spojnicový graf** |**> osy Y** |
| Použít logaritmické měřítko |Vyberte tento odkaz, chcete-li použít logaritmické měřítko pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty, které mají být vráceny dotazem. Tyto informace slouží k zobrazení popisků grafu, které označují typy hodnot, a volitelně k převodu hodnot. Typ *jednotky* určuje kategorii jednotky a definuje dostupné hodnoty typu *Aktuální jednotka.* Pokud vyberete hodnotu v *příkazu Převést na*, číselné hodnoty se převedou z typu *Aktuální jednotka* na *typ Převést na.* |
| Vlastní popisek |Text, který se zobrazí pro osu y vedle popisku pro typ *jednotky.* Pokud není zadán žádný popisek, zobrazí se pouze typ *jednotky.* |
| **Upřesnit** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověření toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup obvykle používáte k poskytnutí zprávy během dočasného období, kdy je zobrazení nainstalováno a data budou k dispozici. |
| Dotaz |Dotaz, který je spuštěn k určení, zda jsou k dispozici data pro zobrazení. Pokud dotaz vrátí žádné výsledky, zobrazí se místo hodnoty hlavního dotazu zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud ověřovací dotaz toku dat vrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *provádění hodnocení.* |


## <a name="two-timelines-tile"></a>Dlaždice se dvěma časovými osami
Dlaždice **Dvě časové osy** zobrazuje výsledky dvou dotazů protokolu v průběhu času jako sloupcové grafy. Pro každou řadu se zobrazí popisek. 

![Dlaždice se dvěma časovými osami](media/view-designer-tiles/tile-two-timelines.png)

| Nastavení | Popis |
|:--- |:--- |
| Name (Název) |Text, který je zobrazen v horní části dlaždice. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| První graf | |
| Legenda |Text, který se zobrazí pod popiskem pro první řadu. |
| Barvy |Barva, která se používá pro sloupce v první řadě. |
| Dotaz na graf |Dotaz, který je spuštěn pro první řadu. Počet záznamů v každém časovém intervalu je reprezentován sloupci grafu. |
| Operace |Operace, která se provádí na vlastnost value shrnout jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů, které jsou vráceny dotazem.</li><li>Poslední ukázka: Hodnota posledního intervalu, který je součástí grafu.</li><li>Max: Maximální hodnota intervalů, které jsou zahrnuty v grafu.</li></ul> |
| **Druhý graf** | |
| Legenda |Text, který je zobrazen pod popiskem pro druhou řadu. |
| Barvy |Barva, která se používá pro sloupce v druhé řadě. |
| Dotaz na graf |Dotaz, který je spuštěn pro druhou řadu. Počet záznamů v každém časovém intervalu je reprezentován sloupci grafu. |
| Operace |Operace, která se provádí na vlastnost value shrnout jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů, které jsou vráceny dotazem.</li><li>Poslední ukázka: Hodnota posledního intervalu, který je součástí grafu.</li><li>Max: Maximální hodnota intervalů, které jsou zahrnuty v grafu. |
| **Upřesnit** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověření toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup obvykle používáte k poskytnutí zprávy během dočasného období, kdy je zobrazení nainstalováno a data budou k dispozici. |
| Dotaz |Dotaz, který je spuštěn k určení, zda jsou k dispozici data pro zobrazení. Pokud dotaz vrátí žádné výsledky, zobrazí se místo hodnoty hlavního dotazu zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud ověřovací dotaz toku dat vrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *provádění hodnocení.* |


## <a name="next-steps"></a>Další kroky
* Další informace o [dotazech protokolu](../log-query/log-query-overview.md) pro podporu dotazů v dlaždicích.
* Přidejte do vlastního zobrazení [vizualizační části.](view-designer-parts.md)
