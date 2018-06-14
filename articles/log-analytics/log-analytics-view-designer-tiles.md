---
title: Referenční příručka k zobrazení návrhu dlaždice v Azure Log Analytics | Microsoft Docs
description: Pomocí zobrazení návrhu v analýzy protokolů můžete vytvořit vlastní zobrazení na portálu Azure, který zobrazí celou řadu vizualizaci dat v pracovním prostoru analýzy protokolů. Tento článek je referenční příručka k nastavení pro dlaždice, které jsou k dispozici do vlastních zobrazení.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: f341cb9430c7750909c1fc1f50c15f0620e74366
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29947312"
---
# <a name="reference-guide-to-view-designer-tiles-in-log-analytics"></a>Referenční příručka k zobrazení návrhu dlaždice v analýzy protokolů
Pomocí zobrazení návrhu v Azure Log Analytics můžete vytvořit vlastní zobrazení na portálu Azure k dispozici různé vizualizace dat v pracovním prostoru analýzy protokolů. Tento článek je referenční příručka k nastavení pro dlaždice, které jsou k dispozici do vlastních zobrazení.

Další informace o návrháři zobrazení najdete v tématu:

* [Zobrazit návrháře](log-analytics-view-designer.md): poskytuje přehled Návrhář zobrazení a postupy pro vytváření a úpravy vlastních zobrazení.
* [Odkaz na část vizualizace](log-analytics-view-designer-parts.md): poskytuje referenční příručka k nastavení pro vizualizaci částí, které jsou k dispozici do vlastních zobrazení.


K dispozici dlaždice Návrhář zobrazení jsou popsané v následující tabulce:  

| Dlaždice | Popis |
|:--- |:--- |
| [Číslo](#number-tile) |Počet záznamů z dotazu. |
| [Dvou čísel.](#two-numbers-tile) |Počet záznamů z dva různé dotazy. |
| [prstenec](#donut-tile) | Graf, který je založen na dotaz, s souhrnnou hodnotu v centru. |
| [Spojnicový graf a popisku](#line-chart-amp-callout-tile) | Spojnicový graf, který je založen na dotaz a popisku s souhrnnou hodnotu. |
| [Spojnicový graf](#line-chart-tile) |Spojnicový graf, který je založen na dotazu. |
| [Dva časové osy](#two-timelines-tile) | Sloupcový graf s dvou řad, každý založené na samostatné dotazu. |

Další části popisují typy dlaždice a jejich vlastnosti podrobně.

## <a name="number-tile"></a>Číslo dlaždice
**Číslo** dlaždice zobrazí počet záznamů z protokolu dotazu a štítek.

![Číslo dlaždice](media/log-analytics-view-designer/tile-number.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části na dlaždici. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| **Dlaždice** | |
| Legenda |Text, který se zobrazí v části hodnota. |
| Dotaz |Dotaz, který se spouští. Zobrazí se počet záznamů vrácených dotazem. |
| **Upřesnit** |**> Ověření toku dat** |
| Povoleno |Vyberte tento odkaz, pokud ověření toku dat by měla být povolená pro dlaždici. Tento přístup poskytuje alternativní zprávu, pokud jsou data nedostupná. Normálně použít přístup k poskytování zprávu dočasné období, když je nainstalován zobrazení a data k dispozici. |
| Dotaz |Dotaz, který se spouští k určení, zda jsou k dispozici pro zobrazení data. Pokud dotaz vrátí žádné výsledky, zobrazí se zpráva místo hodnota hlavní dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud datový tok ověření dotazu nevrátí žádná data. Pokud zadáte žádná zpráva *provádění Assessment* se zobrazí stavová zpráva. |


## <a name="two-numbers-tile"></a>Dlaždice dvou čísel.
Tuto dlaždici zobrazí počet záznamy ze dvou různých protokolových dotazy a popisek pro každý.

![Dlaždice dvou čísel.](media/log-analytics-view-designer/tile-two-numbers.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části na dlaždici. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| **První dlaždice** | |
| Legenda |Text, který se zobrazí v části hodnota. |
| Dotaz |Dotaz, který se spouští. Zobrazí se počet záznamů vrácených dotazem. |
| **Druhý dlaždice** | |
| Legenda |Text, který se zobrazí v části hodnota. |
| Dotaz |Dotaz, který se spouští. Zobrazí se počet záznamů vrácených dotazem. |
| **Upřesnit** |**> Ověření toku dat** |
| Povoleno |Vyberte tento odkaz, pokud ověření toku dat by měla být povolená pro dlaždici. Tento přístup poskytuje alternativní zprávu, pokud jsou data nedostupná. Normálně použít přístup k poskytování zprávu dočasné období, když je nainstalován zobrazení a data k dispozici. |
| Dotaz |Dotaz, který se spouští k určení, zda jsou k dispozici pro zobrazení data. Pokud dotaz vrátí žádné výsledky, zobrazí se zpráva místo hodnota hlavní dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud datový tok ověření dotazu nevrátí žádná data. Pokud zadáte žádná zpráva *provádění Assessment* se zobrazí stavová zpráva. |


## <a name="donut-tile"></a>Dlaždice prstenec
**Prstenec** dlaždice zobrazí jeden číslo, které shrnuje hodnotu sloupce v protokolu dotazu. Prstenec graficky zobrazí výsledky nejvyšší tři záznamy.

![Dlaždice prstenec](media/log-analytics-view-designer/tile-donut.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části na dlaždici. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| **prstenec** | |
| Dotaz |Dotaz, který se spouští prstenec. Textovou hodnotu, která je první vlastností a druhou vlastností je číselná hodnota. Tento dotaz se obvykle používá *měr* – klíčové slovo pro shrnutí výsledků. |
| **prstenec** |**> Center** |
| Text |Text, který se zobrazí v části hodnotu uvnitř prstenec. |
| Operace |Operace, které se provádí na vlastnost value pro shrnutí jako jednu hodnotu.<ul><li>Součet: Přidejte hodnoty všech záznamů se hodnota vlastnosti.</li><li>Procento: Procento sečtené hodnoty ze záznamů se hodnota vlastnosti ve srovnání s sečtené hodnoty všechny záznamy.</li></ul> |
| Výsledek hodnoty použít v operaci center |Volitelně vyberte znaménko plus (+) Chcete-li přidat jednu nebo více hodnot. Výsledky dotazu jsou omezeny na záznamy s hodnotami vlastností, které zadáte. Pokud budou přidávána žádné hodnoty, jsou zahrnuty všechny záznamy v dotazu. |
| **prstenec** |**> Další možnosti** |
| Barvy |Barva, který se zobrazí u každé vlastnosti tři hlavní. Pokud chcete zadat alternativní barvy pro konkrétní hodnoty vlastnosti, použijte *Advanced mapování barva*. |
| Mapování pokročilé barev |Zobrazí barvu, která představuje konkrétní hodnoty vlastnosti. Pokud je hodnota, kterou zadáte v první tři, Alternativní barva se zobrazí místo standardní barvu. Pokud vlastnost není v první tři, barva se nezobrazí. |
| **Upřesnit** |**> Ověření toku dat** |
| Povoleno |Vyberte tento odkaz, pokud ověření toku dat by měla být povolená pro dlaždici. Tento přístup poskytuje alternativní zprávu, pokud jsou data nedostupná. Normálně použít přístup k poskytování zprávu dočasné období, když je nainstalován zobrazení a data k dispozici. |
| Dotaz |Dotaz, který se spouští k určení, zda jsou k dispozici pro zobrazení data. Pokud dotaz vrátí žádné výsledky, zobrazí se zpráva místo hodnota hlavní dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud datový tok ověření dotazu nevrátí žádná data. Pokud zadáte žádná zpráva *provádění Assessment* se zobrazí stavová zpráva. |


## <a name="line-chart-tile"></a>Dlaždice grafu řádku
Tato dlaždice není spojnicový graf, který zobrazuje více řad z protokolu dotazu v čase. 

![Dlaždice graf a popisků čáry](media/log-analytics-view-designer/tile-line-chart.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části na dlaždici. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který se spouští spojnicového grafu. Textovou hodnotu, která je první vlastností a druhou vlastností je číselná hodnota. Tento dotaz se obvykle používá *měr* – klíčové slovo pro shrnutí výsledků. Pokud dotaz používá *interval* – klíčové slovo, osy x používá tento časový interval. Pokud dotaz nepoužívá *interval* – klíčové slovo, hodinových intervalech používá osy x. |
| **Spojnicový graf** |**> Osy y** |
| Použít logaritmickou stupnici |Vyberte tento odkaz se má použít hodnota na logaritmické stupnici pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty vrácených dotazem. Tyto informace slouží k zobrazení popisků v grafu označující typy hodnot a volitelně pro převod hodnoty. **Typ jednotky** Určuje kategorii jednotky a definuje **aktuální typ jednotky** hodnoty, které jsou k dispozici. Pokud vyberete hodnotu v **převést na** pak číselné hodnoty jsou převést z **aktuální jednotku** typ, který má **převést na** typu. |
| Vlastní popisek |Text, který se zobrazí vedle popisek osy y *jednotky* typu. Pokud není zadaný žádný štítek, jenom *jednotky* typu se zobrazí. |
| **Upřesnit** |**> Ověření toku dat** |
| Povoleno |Vyberte tento odkaz, pokud ověření toku dat by měla být povolená pro dlaždici. Tento přístup poskytuje alternativní zprávu, pokud jsou data nedostupná. Normálně použít přístup k poskytování zprávu dočasné období, když je nainstalován zobrazení a data k dispozici. |
| Dotaz |Dotaz, který se spouští k určení, zda jsou k dispozici pro zobrazení data. Pokud dotaz vrátí žádné výsledky, zobrazí se zpráva místo hodnota hlavní dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud datový tok ověření dotazu nevrátí žádná data. Pokud zadáte žádná zpráva *provádění Assessment* se zobrazí stavová zpráva. |


## <a name="line-chart-and-callout-tile"></a>Dlaždice graf a popisků čáry
Tato dlaždice je obou grafu této zobrazí více řad z protokolu dotazu přes čas a popisku s souhrnnou hodnotu. 

![Dlaždice graf a popisků čáry](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části na dlaždici. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který se spouští spojnicového grafu. Textovou hodnotu, která je první vlastností a druhou vlastností je číselná hodnota. Tento dotaz se obvykle používá *měr* – klíčové slovo pro shrnutí výsledků. Pokud dotaz používá *interval* – klíčové slovo, osy x používá tento časový interval. Pokud dotaz nepoužívá *interval* – klíčové slovo, hodinových intervalech používá osy x. |
| **Spojnicový graf** |**> Popisku** |
| Název popisku | Text, který se zobrazí větší než hodnota popisku. |
| Název řady |Hodnota vlastnosti řady má být použit jako hodnotu popisku. Pokud je k dispozici žádné řady, použijí se všechny záznamy z dotazu. |
| Operace |Operace, které se provádí na vlastnost value pro shrnutí jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů vrácených dotazem.</li><li>Poslední ukázkové: hodnota posledního intervalu, který je zahrnut v grafu.</li><li>Maximální počet: Maximální hodnota intervalů, které jsou zahrnuty v grafu.</li><li>Minimální: Minimální hodnota intervalů, které jsou zahrnuty v grafu.</li><li>Součet: Součet hodnot ze všech záznamů.</li></ul> |
| **Spojnicový graf** |**> Osy y** |
| Použít logaritmickou stupnici |Vyberte tento odkaz se má použít hodnota na logaritmické stupnici pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty má být vrácen v dotazu. Tyto informace slouží k zobrazení grafu popisky, které indikují typy hodnot a volitelně k převedení hodnoty. *Jednotky* typ Určuje kategorii jednotky a definuje dostupných *aktuální jednotku* typ hodnoty. Pokud vyberete hodnotu v *převést na*, číselné hodnoty jsou převést z *aktuální jednotku* typ, který má *převést na* typu. |
| Vlastní popisek |Text, který se zobrazí vedle popisek osy y *jednotky* typu. Pokud není zadaný žádný štítek, jenom *jednotky* typu se zobrazí. |
| **Upřesnit** |**> Ověření toku dat** |
| Povoleno |Vyberte tento odkaz, pokud ověření toku dat by měla být povolená pro dlaždici. Tento přístup poskytuje alternativní zprávu, pokud jsou data nedostupná. Normálně použít přístup k poskytování zprávu dočasné období, když je nainstalován zobrazení a data k dispozici. |
| Dotaz |Dotaz, který se spouští k určení, zda jsou k dispozici pro zobrazení data. Pokud dotaz vrátí žádné výsledky, zobrazí se zpráva místo hodnota hlavní dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud datový tok ověření dotazu nevrátí žádná data. Pokud zadáte žádná zpráva *provádění Assessment* se zobrazí stavová zpráva. |


## <a name="two-timelines-tile"></a>Dlaždice obě časové osy
**Obě časové osy** dlaždice zobrazuje výsledky dva dotazy protokolu časem jako sloupcových grafů. U každé série se zobrazí popisek. 

![Dlaždice obě časové osy](media/log-analytics-view-designer/tile-two-timelines.png)

| Nastavení | Popis |
|:--- |:--- |
| Název |Text, který se zobrazí v horní části na dlaždici. |
| Popis |Text, který se zobrazí pod názvem dlaždice. |
| První graf | |
| Legenda |Text, který se zobrazí v části popisku řady, první. |
| Barva |Barvu, která se používá pro sloupce v první řadě. |
| Graf dotazu |Dotaz, který se spouští pro první řady. Počet záznamů v každém časovém intervalu je reprezentována sloupce grafu. |
| Operace |Operace, které se provádí na vlastnost value pro shrnutí jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů vrácených dotazem.</li><li>Poslední ukázkové: hodnota posledního intervalu, který je zahrnut v grafu.</li><li>Maximální počet: Maximální hodnota intervalů, které jsou zahrnuty v grafu.</li></ul> |
| **Druhý graf** | |
| Legenda |Text, který se zobrazí v části popisku řady, druhý. |
| Barva |Barva, který se používá pro sloupce v druhé řady. |
| Graf dotazu |Dotaz, který se spustí druhé řady. Počet záznamů v každém časovém intervalu je reprezentována sloupce grafu. |
| Operace |Operace, které se provádí na vlastnost value pro shrnutí jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů vrácených dotazem.</li><li>Poslední ukázkové: hodnota posledního intervalu, který je zahrnut v grafu.</li><li>Maximální počet: Maximální hodnota intervalů, které jsou zahrnuty v grafu. |
| **Upřesnit** |**> Ověření toku dat** |
| Povoleno |Vyberte tento odkaz, pokud ověření toku dat by měla být povolená pro dlaždici. Tento přístup poskytuje alternativní zprávu, pokud jsou data nedostupná. Normálně použít přístup k poskytování zprávu dočasné období, když je nainstalován zobrazení a data k dispozici. |
| Dotaz |Dotaz, který se spouští k určení, zda jsou k dispozici pro zobrazení data. Pokud dotaz vrátí žádné výsledky, zobrazí se zpráva místo hodnota hlavní dotazu. |
| Zpráva |Zpráva, která se zobrazí, pokud datový tok ověření dotazu nevrátí žádná data. Pokud zadáte žádná zpráva *provádění Assessment* se zobrazí stavová zpráva. |


## <a name="next-steps"></a>Další kroky
* Další informace o [protokolu hledání](log-analytics-log-searches.md) pro podporu dotazů v dlaždice.
* Přidat [vizualizace částí](log-analytics-view-designer-parts.md) do vlastních zobrazení.
