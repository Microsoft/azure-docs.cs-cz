---
title: Referenční příručka pro dlaždice návrháře zobrazení v Azure Monitor | Microsoft Docs
description: Pomocí návrháře zobrazení v Azure Monitor můžete vytvořit vlastní zobrazení, která jsou zobrazena v Azure Portal a obsahují celou řadu vizualizací dat v pracovním prostoru Log Analytics. Tento článek představuje referenční příručku k nastavením pro dlaždice, které jsou k dispozici ve vlastních zobrazeních.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: abcd95a0c433b59df6e7a564e5db948ea2a9eaf5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717209"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Referenční příručka pro zobrazení dlaždic návrháře v Azure Monitor
Pomocí návrháře zobrazení v Azure Monitor můžete v Azure Portal vytvořit nejrůznější vlastní zobrazení, která vám pomůžou vizualizovat data v pracovním prostoru Log Analytics. Tento článek představuje referenční příručku k nastavením pro dlaždice, které jsou k dispozici ve vlastních zobrazeních.

Další informace o návrháři zobrazení najdete v těchto tématech:

* [Návrhář zobrazení](view-designer.md): poskytuje přehled návrháře zobrazení a postupů pro vytváření a úpravy vlastních zobrazení.
* [Odkaz na součást vizualizace](view-designer-parts.md): poskytuje referenční příručku k nastavením pro části vizualizace, které jsou k dispozici ve vlastních zobrazeních.


K dispozici jsou dlaždice návrháře zobrazení, které jsou popsány v následující tabulce:  

| dlaždici | Popis |
|:--- |:--- |
| [Číselná](#number-tile) |Počet záznamů z dotazu. |
| [Dvě čísla](#two-numbers-tile) |Počty záznamů ze dvou různých dotazů. |
| [Prstencový graf](#donut-tile) | Graf založený na dotazu se souhrnnou hodnotou uprostřed. |
| Spojnicový graf a popisek | Spojnicový graf, který je založen na dotazu, a popisek se souhrnnou hodnotou. |
| [Spojnicový graf](#line-chart-tile) |Spojnicový graf, který je založen na dotazu. |
| [Dvě časové osy](#two-timelines-tile) | Sloupcový graf se dvěma řadami, každý založený na samostatném dotazu. |

V dalších oddílech jsou podrobně popsány typy dlaždic a jejich vlastnosti.

> [!NOTE]
> Dlaždice v zobrazeních jsou založené na [dotazech protokolu](../logs/log-query-overview.md) v pracovním prostoru Log Analytics. V současné době nepodporují [dotazy na více zdrojů](../logs/cross-workspace-query.md) k načtení dat z Application Insights.

## <a name="number-tile"></a>Dlaždice číslo
Dlaždice **číslo** zobrazuje počet záznamů z dotazu protokolu a popisku.

![Dlaždice číslo](media/view-designer-tiles/tile-number.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text zobrazený v horní části dlaždice |
| Popis |Text zobrazený pod názvem dlaždice |
| **dlaždici** | |
| Legenda |Text zobrazený pod hodnotou |
| Dotaz |Dotaz, který je spuštěn. Zobrazí se počet záznamů vrácených dotazem. |
| **Pokročilý** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověřování toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup se obvykle používá k poskytnutí zprávy během dočasného období, kdy se zobrazení nainstaluje a data budou k dispozici. |
| Dotaz |Dotaz, který má být spuštěn, aby bylo možné určit, zda jsou data pro zobrazení k dispozici. Pokud dotaz nevrátí žádné výsledky, zobrazí se místo hodnoty pro hlavní dotaz zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz na ověření toku dat nevrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *posouzení* . |


## <a name="two-numbers-tile"></a>Dlaždice dvě čísla
Tato dlaždice zobrazuje počet záznamů ze dvou různých dotazů protokolu a popisek pro každou z nich.

![Dlaždice dvě čísla](media/view-designer-tiles/tile-two-numbers.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text zobrazený v horní části dlaždice |
| Popis |Text zobrazený pod názvem dlaždice |
| **První dlaždice** | |
| Legenda |Text zobrazený pod hodnotou |
| Dotaz |Dotaz, který je spuštěn. Zobrazí se počet záznamů vrácených dotazem. |
| **Druhá dlaždice** | |
| Legenda |Text zobrazený pod hodnotou |
| Dotaz |Dotaz, který je spuštěn. Zobrazí se počet záznamů vrácených dotazem. |
| **Pokročilý** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověřování toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup se obvykle používá k poskytnutí zprávy během dočasného období, kdy se zobrazení nainstaluje a data budou k dispozici. |
| Dotaz |Dotaz, který má být spuštěn, aby bylo možné určit, zda jsou data pro zobrazení k dispozici. Pokud dotaz nevrátí žádné výsledky, zobrazí se místo hodnoty pro hlavní dotaz zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz na ověření toku dat nevrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *posouzení* . |


## <a name="donut-tile"></a>Prstenec – dlaždice
Na dlaždici **prstenec** se zobrazí jedno číslo, které shrnuje sloupec hodnoty v dotazu protokolu. Prstenec graficky zobrazuje výsledky prvních tří záznamů.

![Prstenec – dlaždice](media/view-designer-tiles/tile-donut.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text zobrazený v horní části dlaždice |
| Popis |Text zobrazený pod názvem dlaždice |
| **Prstencový graf** | |
| Dotaz |Dotaz, který je spuštěn pro prstenec. První vlastností je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *Measure* k sumarizaci výsledků. |
| **Prstencový graf** |**Centrum>** |
| Text |Text zobrazený pod hodnotou uvnitř prstence prstence |
| Operace |Operace, která se provádí na vlastnosti Value pro Shrnutí jako jedné hodnoty.<ul><li>Sum: přidejte hodnoty všech záznamů s hodnotou vlastnosti.</li><li>Procento: procento sečtených hodnot z záznamů s hodnotou vlastnosti v porovnání se sečtenými hodnotami všech záznamů.</li></ul> |
| Hodnoty výsledku použité v operaci na středu |Volitelně můžete vybrat znaménko plus (+) a přidat jednu nebo více hodnot. Výsledky dotazu jsou omezeny na záznamy s hodnotami vlastností, které zadáte. Pokud nejsou přidány žádné hodnoty, jsou do dotazu zahrnuty všechny záznamy. |
| **Prstencový graf** |**> další možnosti** |
| Barvy |Barva zobrazená pro každou ze tří horních vlastností Chcete-li zadat alternativní barvy pro konkrétní hodnoty vlastností, použijte *rozšířené mapování barev*. |
| Rozšířené mapování barev |Zobrazí barvu, která představuje konkrétní hodnoty vlastností. Pokud je zadaná hodnota v horní části tři, zobrazí se místo standardní barvy alternativní barva. Pokud vlastnost není v horní části tři, barva se nezobrazí. |
| **Pokročilý** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověřování toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup se obvykle používá k poskytnutí zprávy během dočasného období, kdy se zobrazení nainstaluje a data budou k dispozici. |
| Dotaz |Dotaz, který má být spuštěn, aby bylo možné určit, zda jsou data pro zobrazení k dispozici. Pokud dotaz nevrátí žádné výsledky, zobrazí se místo hodnoty pro hlavní dotaz zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz na ověření toku dat nevrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *posouzení* . |


## <a name="line-chart-tile"></a>Dlaždice spojnicového grafu
Tato dlaždice je spojnicový graf, který v průběhu času zobrazuje více řad z dotazu protokolu. 

![Snímek obrazovky dlaždice spojnicového grafu v Návrháři zobrazení Azure Monitor.](media/view-designer-tiles/tile-line-chart.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text zobrazený v horní části dlaždice |
| Popis |Text zobrazený pod názvem dlaždice |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který se spouští pro spojnicový graf. První vlastností je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *Measure* k sumarizaci výsledků. Pokud dotaz používá klíčové slovo *interval* , osa x použije tento časový interval. Pokud dotaz nepoužije klíčové slovo *interval* , osa x použije hodinové intervaly. |
| **Spojnicový graf** |**> osa Y** |
| Použití logaritmické stupnice |Vyberte tento odkaz pro použití logaritmické stupnice pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty vrácené dotazem. Tyto informace slouží k zobrazení popisků v grafu, které označují typy hodnot a volitelně pro převod hodnot. **Typ jednotky** určuje kategorii jednotky a definuje **aktuální hodnoty typu jednotky** , které jsou k dispozici. Pokud vyberete hodnotu v poli **převést na** , budou číselné hodnoty převedeny z **aktuální jednotky** typu na typ **převést na** typ. |
| Vlastní popisek |Text zobrazený pro osu y vedle popisku pro typ *jednotky* Pokud není zadán žádný popisek, zobrazí se pouze typ *jednotky* . |
| **Pokročilý** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověřování toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup se obvykle používá k poskytnutí zprávy během dočasného období, kdy se zobrazení nainstaluje a data budou k dispozici. |
| Dotaz |Dotaz, který má být spuštěn, aby bylo možné určit, zda jsou data pro zobrazení k dispozici. Pokud dotaz nevrátí žádné výsledky, zobrazí se místo hodnoty pro hlavní dotaz zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz na ověření toku dat nevrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *posouzení* . |


## <a name="line-chart-and-callout-tile"></a>Dlaždice spojnicový graf a popisek
Tato dlaždice má spojnicový graf, který zobrazuje více řad z dotazu protokolu v průběhu času a popisek s souhrnnou hodnotou. 

![Snímek obrazovky dlaždice spojnicového grafu a popisku v Návrháři zobrazení Azure Monitor. popisek rozšiřuje spojnicový graf zobrazením shrnuté hodnoty.](media/view-designer-tiles/tile-line-chart-callout.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text zobrazený v horní části dlaždice |
| Popis |Text zobrazený pod názvem dlaždice |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který se spouští pro spojnicový graf. První vlastností je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *Measure* k sumarizaci výsledků. Pokud dotaz používá klíčové slovo *interval* , osa x použije tento časový interval. Pokud dotaz nepoužije klíčové slovo *interval* , osa x použije hodinové intervaly. |
| **Spojnicový graf** |**Popisek>** |
| Nadpis popisku | Text zobrazený nad hodnotou popisku |
| Název řady |Hodnota vlastnosti řady, která se má použít jako hodnota popisku Pokud není zadána žádná řada, budou použity všechny záznamy z dotazu. |
| Operace |Operace, která se provádí na vlastnosti Value pro sumarizaci jako jedinou hodnotu pro popisek.<ul><li>Average: průměr hodnot ze všech záznamů.</li><li>Count (počet): počet všech záznamů vrácených dotazem.</li><li>Poslední Ukázka: hodnota posledního intervalu, který je zahrnutý v grafu.</li><li>Max: maximální hodnota intervalů, které jsou zahrnuty v grafu.</li><li>Min: minimální hodnota intervalů, které jsou zahrnuty v grafu.</li><li>Sum: součet hodnot ze všech záznamů.</li></ul> |
| **Spojnicový graf** |**> osa Y** |
| Použití logaritmické stupnice |Vyberte tento odkaz pro použití logaritmické stupnice pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty, které má dotaz vrátit. Tyto informace slouží k zobrazení popisků grafů, které určují typy hodnot a volitelně k převodu hodnot. Typ *jednotky* určuje kategorii jednotky a definuje dostupné hodnoty *aktuálního typu jednotky* . Pokud vyberete hodnotu v poli *převést na*, číselné hodnoty jsou převedeny z *aktuální jednotky* typu na typ *převést na* typ. |
| Vlastní popisek |Text zobrazený pro osu y vedle popisku pro typ *jednotky* Pokud není zadán žádný popisek, zobrazí se pouze typ *jednotky* . |
| **Pokročilý** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověřování toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup se obvykle používá k poskytnutí zprávy během dočasného období, kdy se zobrazení nainstaluje a data budou k dispozici. |
| Dotaz |Dotaz, který má být spuštěn, aby bylo možné určit, zda jsou data pro zobrazení k dispozici. Pokud dotaz nevrátí žádné výsledky, zobrazí se místo hodnoty pro hlavní dotaz zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz na ověření toku dat nevrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *posouzení* . |


## <a name="two-timelines-tile"></a>Dlaždice dvou časových os
Dlaždice **dvě časové osy** zobrazuje výsledky dvou dotazů protokolu v průběhu času jako sloupcové grafy. Pro každou řadu se zobrazí popisek. 

![Dlaždice dvou časových os](media/view-designer-tiles/tile-two-timelines.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text zobrazený v horní části dlaždice |
| Popis |Text zobrazený pod názvem dlaždice |
| První graf | |
| Legenda |Text zobrazený pod popiskem prvního seriálu |
| Barva |Barva, která se používá pro sloupce v první řadě |
| Dotaz na graf |Dotaz, který se spustí pro první řadu. Počet záznamů v průběhu každého časového intervalu je reprezentován sloupci grafu. |
| Operace |Operace, která se provádí na vlastnosti Value pro sumarizaci jako jedinou hodnotu pro popisek.<ul><li>Average: průměr hodnot ze všech záznamů.</li><li>Count (počet): počet všech záznamů vrácených dotazem.</li><li>Poslední Ukázka: hodnota posledního intervalu, který je zahrnutý v grafu.</li><li>Max: maximální hodnota intervalů, které jsou zahrnuty v grafu.</li></ul> |
| **Druhý graf** | |
| Legenda |Text zobrazený pod popiskem pro druhou řadu |
| Barva |Barva, která se používá pro sloupce ve druhé řadě. |
| Dotaz na graf |Dotaz, který je spuštěn pro druhou řadu. Počet záznamů v průběhu každého časového intervalu je reprezentován sloupci grafu. |
| Operace |Operace, která se provádí na vlastnosti Value pro sumarizaci jako jedinou hodnotu pro popisek.<ul><li>Average: průměr hodnot ze všech záznamů.</li><li>Count (počet): počet všech záznamů vrácených dotazem.</li><li>Poslední Ukázka: hodnota posledního intervalu, který je zahrnutý v grafu.</li><li>Max: maximální hodnota intervalů, které jsou zahrnuty v grafu. |
| **Pokročilý** |**> ověření toku dat** |
| Povoleno |Tento odkaz vyberte, pokud má být pro dlaždici povoleno ověřování toku dat. Tento přístup poskytuje alternativní zprávu, pokud data nejsou k dispozici. Tento přístup se obvykle používá k poskytnutí zprávy během dočasného období, kdy se zobrazení nainstaluje a data budou k dispozici. |
| Dotaz |Dotaz, který má být spuštěn, aby bylo možné určit, zda jsou data pro zobrazení k dispozici. Pokud dotaz nevrátí žádné výsledky, zobrazí se místo hodnoty pro hlavní dotaz zpráva. |
| Zpráva |Zpráva, která se zobrazí, pokud dotaz na ověření toku dat nevrátí žádná data. Pokud nezadáte žádnou zprávu, zobrazí se zpráva o stavu *posouzení* . |


## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolu](../logs/log-query-overview.md) pro podporu dotazů v dlaždicích.
* Přidejte do vlastního zobrazení [části vizualizace](view-designer-parts.md) .