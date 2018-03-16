---
title: "Referenční příručka Návrhář zobrazení částí v Azure Log Analytics | Microsoft Docs"
description: "Pomocí zobrazení návrhu v analýzy protokolů můžete vytvořit vlastní zobrazení na portálu Azure, který zobrazí celou řadu vizualizaci dat v pracovním prostoru analýzy protokolů. Tento článek je referenční příručka k nastavení pro vizualizaci částí, které jsou k dispozici do vlastních zobrazení."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: bwren
ms.openlocfilehash: a2573eef3c90c1840c0d53b2f8aa2cfe2d3a7242
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-log-analytics"></a>Referenční příručka Návrhář zobrazení vizualizace částí v analýzy protokolů
Pomocí zobrazení návrhu v Azure Log Analytics můžete vytvořit vlastní zobrazení na portálu Azure k dispozici různé vizualizace dat z pracovního prostoru analýzy protokolů. Tento článek je referenční příručka k nastavení pro vizualizaci částí, které jsou k dispozici do vlastních zobrazení.

Další informace o návrháři zobrazení najdete v tématu:

* [Zobrazit návrháře](log-analytics-view-designer.md): poskytuje přehled Návrhář zobrazení a postupy pro vytváření a úpravy vlastních zobrazení.
* [Odkaz na dlaždici](log-analytics-view-designer-tiles.md): poskytuje odkaz na nastavení pro každou dlaždici k dispozici do vlastních zobrazení.


Dostupné typy dlaždice Návrhář zobrazení jsou popsané v následující tabulce:

| Typ zobrazení | Popis |
|:--- |:--- |
| [Seznam dotazů](#list-of-queries-part) |Zobrazí seznam protokolů vyhledávací dotazy. Můžete vybrat každý dotaz zobrazíte jeho výsledky. |
| [Počet a seznamu](#number-and-list-part) |Záhlaví zobrazí jedno číslo, který ukazuje počet záznamů z vyhledávacího dotazu protokolu. Zobrazí se seznam top deset výsledky z dotazu, s graf, který určuje relativní hodnota je číselný sloupec nebo jeho změn v průběhu času. |
| [Dvě čísla a seznamu](#two-numbers-and-list-part) |Záhlaví zobrazí dvou čísel, které se zobrazí počty záznamy ze samostatné protokolu vyhledávací dotazy. Zobrazí se seznam top deset výsledky z dotazu, s graf, který určuje relativní hodnota je číselný sloupec nebo jeho změn v průběhu času. |
| [Prstenec a seznamu](#donut-and-list-part) |Záhlaví zobrazí jeden číslo, které shrnuje hodnotu sloupce v protokolu dotazu. Prstenec graficky zobrazí výsledky nejvyšší tři záznamy. |
| [Dva časové osy a seznamu](#two-timelines-and-list-part) |Záhlaví zobrazí výsledky dva dotazy protokolu časem jako sloupcové grafy s popisku, který zobrazuje jeden číslo, které shrnuje hodnotu sloupce v dotazu protokolu. Zobrazí se seznam top deset výsledky z dotazu, s graf, který určuje relativní hodnota je číselný sloupec nebo jeho změn v průběhu času. |
| [Informace o](#information-part) |Záhlaví zobrazí statický text a nepovinný odkaz. Zobrazí se seznam jedné nebo více položek s statické nadpis a text. |
| [Spojnicový graf, popisků a seznamu](#line-chart-callout-and-list-part) |Záhlaví zobrazí spojnicový graf, s více řad z protokolu dotazu přes čas a popisku s souhrnnou hodnotu. Zobrazí se seznam top deset výsledky z dotazu, s graf, který určuje relativní hodnota je číselný sloupec nebo jeho změn v průběhu času. |
| [Spojnicový graf a seznamu](#line-chart-and-list-part) |Záhlaví zobrazí spojnicový graf, s více řad z protokolu dotazu v čase. Zobrazí se seznam top deset výsledky z dotazu, s graf, který určuje relativní hodnota je číselný sloupec nebo jeho změn v průběhu času. |
| [Zásobník části grafy řádku](#stack-of-line-charts-part) |Zobrazuje tři samostatné spojnicových grafů, s více řad z protokolu dotazu v čase. |

Další části popisují typy dlaždice a jejich vlastnosti podrobně.

## <a name="list-of-queries-part"></a>Seznam dotazů část
Seznam dotazů část zobrazí seznam protokolů vyhledávací dotazy. Můžete vybrat každý dotaz zobrazíte jeho výsledky. Zobrazení obsahuje jeden dotaz ve výchozím nastavení, a můžete vybrat **+ dotazu** přidat další dotazy.

![Seznam zobrazení dotazů](media/log-analytics-view-designer/view-list-queries.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Nadpis |Text, který se zobrazí v horní části zobrazení. |
| Nové skupiny |Vyberte tento odkaz na vytvoření nové skupiny v zobrazení začínající na aktuální zobrazení. |
| Předvybrané filtry |Čárkami oddělený seznam vlastnosti, které chcete zahrnout do filtru v levém podokně, když vyberete dotazu. |
| V režimu |Počáteční zobrazení, které se zobrazí, když je vybraný dotaz. Můžete vybrat všechny dostupné zobrazení po otevření dotazu. |
| **Dotazy** | |
| Vyhledávací dotaz |Dotaz spustit. |
| Popisný název | Popisný název, který se zobrazí. |

## <a name="number-and-list-part"></a>Počet a seznam součástí
Záhlaví zobrazí jedno číslo, který ukazuje počet záznamů z vyhledávacího dotazu protokolu. Zobrazí se seznam top deset výsledky z dotazu, s graf, který určuje relativní hodnota je číselný sloupec nebo jeho změn v průběhu času.

![Seznam zobrazení dotazů](media/log-analytics-view-designer/view-number-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části zobrazení. |
| Nové skupiny |Vyberte tento odkaz na vytvoření nové skupiny v zobrazení začínající na aktuální zobrazení. |
| Ikona |Zobrazí se vedle výsledek v hlavičce souboru bitové kopie. |
| Použití ikony |Vyberte tento odkaz zobrazíte ikonu. |
| **Název** | |
| Legenda |Text, který se zobrazí v horní části záhlaví. |
| Dotaz |Dotaz pro spuštění pro hlavičku. Zobrazí se počet záznamů vrácených dotazem. |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na záhlaví.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **seznam** | |
| Dotaz |Dotaz, aby běžel v seznamu. První dvě vlastnosti pro prvních deset záznamy ve výsledcích se zobrazí. Textovou hodnotu, která je první vlastností a druhou vlastností je číselná hodnota. Řádky se automaticky vytvoří, které jsou založeny na relativní hodnota číselné sloupce.<br><br>Použití `Sort` příkazu v dotazu seřadit záznamy v seznamu. Chcete-li spustit dotaz a vrátí všechny záznamy, můžete vybrat **zobrazit všechny**. |
| Skrýt grafu |Vyberte tento odkaz zakázat grafu na pravé straně číselný sloupec. |
| Povolit minigrafů |Vyberte tento odkaz zobrazíte minigraf místo vodorovném řádku. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Barva |Barva řádky nebo minigrafů. |
| Název a hodnotu oddělovače |Oddělovač jeden znak, který se má použít k analýze vlastnosti textu do více hodnot. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na položku v seznamu.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **seznam** |**> Názvy sloupce** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz na povolit prahové hodnoty. Další informace najdete v tématu [společná nastavení](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dvou čísel a část Seznam
Hlavička obsahuje dvě čísla, která se zobrazí počet záznamy ze samostatné protokolu vyhledávací dotazy. Zobrazí se seznam top deset výsledky z dotazu, s graf, který určuje relativní hodnota je číselný sloupec nebo jeho změn v průběhu času.

![Dvou čísel a zobrazení seznamu](media/log-analytics-view-designer/view-two-numbers-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části zobrazení. |
| Nové skupiny |Vyberte tento odkaz na vytvoření nové skupiny v zobrazení začínající na aktuální zobrazení. |
| Ikona |Zobrazí se vedle výsledek v hlavičce souboru bitové kopie. |
| Použití ikony |Vyberte tento odkaz zobrazíte ikonu. |
| **Název navigační** | |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na záhlaví.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **Název** | |
| Legenda |Text, který se zobrazí v horní části záhlaví. |
| Dotaz |Dotaz pro spuštění pro hlavičku. Zobrazí se počet záznamů vrácených dotazem. |
| **seznam** | |
| Dotaz |Dotaz, aby běžel v seznamu. První dvě vlastnosti pro prvních deset záznamy ve výsledcích se zobrazí. Textovou hodnotu, která je první vlastností a druhou vlastností je číselná hodnota. Řádky se vytvářejí automaticky podle relativní hodnota číselné sloupce.<br><br>Použití `Sort` příkazu v dotazu seřadit záznamy v seznamu. Chcete-li spustit dotaz a vrátí všechny záznamy, můžete vybrat **zobrazit všechny**. |
| Skrýt grafu |Vyberte tento odkaz zakázat grafu na pravé straně číselný sloupec. |
| Povolit minigrafů |Vyberte tento odkaz zobrazíte minigraf místo vodorovném řádku. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Barva |Barva řádky nebo minigrafů. |
| Operace |Operace k provedení pro minigraf. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Název a hodnotu oddělovače |Oddělovač jeden znak, který se má použít k analýze vlastnosti textu do více hodnot. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na položku v seznamu.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **seznam** |**> Názvy sloupce** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz na povolit prahové hodnoty. Další informace najdete v tématu [společná nastavení](#thresholds). |

## <a name="donut-and-list-part"></a>Část prstenec a seznamu
Záhlaví zobrazí jeden číslo, které shrnuje hodnotu sloupce v protokolu dotazu. Prstenec graficky zobrazí výsledky nejvyšší tři záznamy.

![Prstenec a seznamu zobrazení](media/log-analytics-view-designer/view-donut-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části na dlaždici. |
| Nové skupiny |Vyberte tento odkaz na vytvoření nové skupiny v zobrazení začínající na aktuální zobrazení. |
| Ikona |Zobrazí se vedle výsledek v hlavičce souboru bitové kopie. |
| Použití ikony |Vyberte tento odkaz zobrazíte ikonu. |
| **Záhlaví** | |
| Nadpis |Text, který se zobrazí v horní části záhlaví. |
| Podtitul |Text, který se zobrazí pod názvem v horní části záhlaví. |
| **prstenec** | |
| Dotaz |Dotaz, aby běžel prstenec. Textovou hodnotu, která je první vlastností a druhou vlastností je číselná hodnota. |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na záhlaví.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **prstenec** |**> Center** |
| Text |Text, který se zobrazí v části hodnotu uvnitř prstenec. |
| Operace |Operace k plnění vlastnost value pro shrnutí jako jednu hodnotu.<ul><li>Součet: Přidá hodnoty všech záznamů.</li><li>Procento: Poměr záznamy vrácené hodnoty v **způsobit hodnoty použít v operaci center** celkový počet záznamů v dotazu.</li></ul> |
| Výsledek hodnoty použít v operaci center |Volitelně vyberte znaménko plus (+) Chcete-li přidat jednu nebo více hodnot. Výsledky dotazu jsou omezeny na záznamy s hodnotami vlastností, které zadáte. Pokud budou přidávána žádné hodnoty, jsou zahrnuty všechny záznamy v dotazu. |
| **Další možnosti** |**> Barvy** |
| Barva 1<br>Barva 2<br>Barva 3 |Vyberte barvu pro všechny hodnoty, které se zobrazují v prstenec. |
| **Další možnosti** |**> Mapování Upřesnit barev** |
| Hodnota pole |Zadejte název pole, které chcete zobrazit jako barvu, pokud je součástí prstenec. |
| Barva |Vyberte barvu pro pole jedinečný. |
| **seznam** | |
| Dotaz |Dotaz, aby běžel v seznamu. Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt grafu |Vyberte tento odkaz zakázat grafu na pravé straně číselný sloupec. |
| Povolit minigrafů |Vyberte tento odkaz zobrazíte minigraf místo vodorovném řádku. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Barva |Barva řádky nebo minigrafů. |
| Operace |Operace k provedení pro minigraf. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Název a hodnotu oddělovače |Oddělovač jeden znak, který se má použít k analýze vlastnosti textu do více hodnot. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na položku v seznamu.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **seznam** |**> Názvy sloupce** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz na povolit prahové hodnoty. Další informace najdete v tématu [společná nastavení](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Dva časové osy a seznam součástí
Záhlaví zobrazí výsledky dva dotazy protokolu časem jako sloupcové grafy s popisku, který zobrazuje jeden číslo, které shrnuje hodnotu sloupce v dotazu protokolu. Zobrazí se seznam top deset výsledky z dotazu, s graf, který určuje relativní hodnota je číselný sloupec nebo jeho změn v průběhu času.

![Dva časové osy a seznamu zobrazení](media/log-analytics-view-designer/view-two-timelines-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části na dlaždici. |
| Nové skupiny |Vyberte tento odkaz na vytvoření nové skupiny v zobrazení začínající na aktuální zobrazení. |
| Ikona |Zobrazí se vedle výsledek v hlavičce souboru bitové kopie. |
| Použití ikony |Vyberte tento odkaz zobrazíte ikonu. |
| **Název navigační** | |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na záhlaví.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **Nejprve grafu<br>druhý grafu** | |
| Legenda |Text, který se zobrazí v části popisku řady, první. |
| Barva |Barvy, která má použít pro sloupce v řadě. |
| Dotaz |Dotaz, který pro první řady spustit. Počet záznamů v každém časovém intervalu je reprezentována sloupce grafu. |
| Operace |Operace k plnění vlastnost value pro shrnutí jako jednu hodnotu pro popisek.<ul><li>Součet: Součet hodnot ze všech záznamů.</li><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Poslední ukázkové: hodnota od posledního intervalu, který je zahrnut v grafu.</li><li>Nejprve ukázkové: hodnotu z první interval, který je zahrnutý v grafu.</li><li>Počet: Počet všech záznamů vrácených dotazem.</li></ul> |
| **seznam** | |
| Dotaz |Dotaz, aby běžel v seznamu. Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt grafu |Vyberte tento odkaz zakázat grafu na pravé straně číselný sloupec. |
| Povolit minigrafů |Vyberte tento odkaz zobrazíte minigraf místo vodorovném řádku. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Barva |Barva řádky nebo minigrafů. |
| Operace |Operace k provedení pro minigraf. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na položku v seznamu.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **seznam** |**> Názvy sloupce** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz na povolit prahové hodnoty. Další informace najdete v tématu [společná nastavení](#thresholds). |

## <a name="information-part"></a>Část informace
Záhlaví zobrazí statický text a nepovinný odkaz. Zobrazí se seznam jedné nebo více položek s statické nadpis a text.

![Informace o zobrazení](media/log-analytics-view-designer/view-information.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části na dlaždici. |
| Nové skupiny |Vyberte tento odkaz na vytvoření nové skupiny v zobrazení začínající na aktuální zobrazení. |
| Barva |Barva pozadí pro hlavičku. |
| **Záhlaví** | |
| Image |Souboru bitové kopie, který se zobrazí v záhlaví. |
| Štítek |Text, který se zobrazí v záhlaví. |
| **Záhlaví** |**> Link** |
| Štítek |Text odkazu. |
| URL |Adresa Url odkazu. |
| **Informace položky** | |
| Nadpis |Text, který se zobrazí v nadpisu každé položky. |
| Obsah |Text, který se zobrazí pro každou položku. |

## <a name="line-chart-callout-and-list-part"></a>Spojnicový graf, popisků a část Seznam
Záhlaví zobrazuje spojnicový graf s více řad z protokolu dotazu přes čas a popisku s souhrnnou hodnotu. Zobrazí se seznam top deset výsledky z dotazu, s graf, který určuje relativní hodnota je číselný sloupec nebo jeho změn v průběhu času.

![Spojnicový graf, popisků a zobrazení seznamu](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části na dlaždici. |
| Nové skupiny |Vyberte tento odkaz na vytvoření nové skupiny v zobrazení začínající na aktuální zobrazení. |
| Ikona |Zobrazí se vedle výsledek v hlavičce souboru bitové kopie. |
| Použití ikony |Vyberte tento odkaz zobrazíte ikonu. |
| **Záhlaví** | |
| Nadpis |Text, který se zobrazí v horní části záhlaví. |
| Podtitul |Text, který se zobrazí pod názvem v horní části záhlaví. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který spustit spojnicového grafu. Textovou hodnotu, která je první vlastností a druhou vlastností je číselná hodnota. Tento dotaz se obvykle používá *měr* – klíčové slovo pro shrnutí výsledků. Pokud dotaz používá *interval* – klíčové slovo, osy x grafu používá tento časový interval. Pokud dotaz neobsahuje *interval* – klíčové slovo, hodinových intervalech používá osy x. |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na záhlaví.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **Spojnicový graf** |**> Popisku** |
| Název popisku |Text, který se zobrazí větší než hodnota popisku. |
| Název řady |Hodnota vlastnosti řady, které chcete použít pro hodnotu popisku. Pokud je k dispozici žádné řady, použijí se všechny záznamy z dotazu. |
| Operace |Operace k plnění vlastnost value pro shrnutí jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů vrácených dotazem.</li><li>Poslední ukázkové: hodnota od posledního intervalu, který je zahrnut v grafu.</li><li>Maximální počet: Maximální hodnota z intervalů, které jsou zahrnuty v grafu.</li><li>Min: Minimální hodnota z intervalů, které jsou zahrnuty v grafu.</li><li>Součet: Součet hodnot ze všech záznamů.</li></ul> |
| **Spojnicový graf** |**> Osy y** |
| Použít logaritmickou stupnici |Vyberte tento odkaz se má použít hodnota na logaritmické stupnici pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty má být vrácen v dotazu. Tyto informace slouží k zobrazení grafu popisky, které indikují typy hodnot a volitelně k převedení hodnoty. *Jednotky* typ Určuje kategorii jednotky a definuje dostupných *aktuální jednotku* typ hodnoty. Pokud vyberete hodnotu v *převést na*, číselné hodnoty jsou převést z *aktuální jednotku* typ, který má *převést na* typu. |
| Vlastní popisek |Text, který se zobrazí vedle popisek osy y *jednotky* typu. Pokud není zadaný žádný štítek, jenom *jednotky* typu se zobrazí. |
| **seznam** | |
| Dotaz |Dotaz, aby běžel v seznamu. Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt grafu |Vyberte tento odkaz zakázat grafu na pravé straně číselný sloupec. |
| Povolit minigrafů |Vyberte tento odkaz zobrazíte minigraf místo vodorovném řádku. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Barva |Barva řádky nebo minigrafů. |
| Operace |Operace k provedení pro minigraf. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Název a hodnotu oddělovače |Oddělovač jeden znak, který se má použít k analýze vlastnosti textu do více hodnot. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na položku v seznamu.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **seznam** |**> Názvy sloupce** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz na povolit prahové hodnoty. Další informace najdete v tématu [společná nastavení](#thresholds). |

## <a name="line-chart-and-list-part"></a>Část řádku graf a seznamu
Záhlaví zobrazí spojnicový graf s více řad z protokolu dotazu v čase. Zobrazí se seznam top deset výsledky z dotazu, s graf, který určuje relativní hodnota je číselný sloupec nebo jeho změn v průběhu času.

![Řádky – zobrazení grafu a seznamu](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části na dlaždici. |
| Nové skupiny |Vyberte tento odkaz na vytvoření nové skupiny v zobrazení začínající na aktuální zobrazení. |
| Ikona |Zobrazí se vedle výsledek v hlavičce souboru bitové kopie. |
| Použití ikony |Vyberte tento odkaz zobrazíte ikonu. |
| **Záhlaví** | |
| Nadpis |Text, který se zobrazí v horní části záhlaví. |
| Podtitul |Text, který se zobrazí pod názvem v horní části záhlaví. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který spustit spojnicového grafu. Textovou hodnotu, která je první vlastností a druhou vlastností je číselná hodnota. Tento dotaz se obvykle používá *měr* – klíčové slovo pro shrnutí výsledků. Pokud dotaz používá *interval* – klíčové slovo, osy x grafu používá tento časový interval. Pokud dotaz neobsahuje *interval* – klíčové slovo, hodinových intervalech používá osy x. |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na záhlaví.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **Spojnicový graf** |**> Osy y** |
| Použít logaritmickou stupnici |Vyberte tento odkaz se má použít hodnota na logaritmické stupnici pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty má být vrácen v dotazu. Tyto informace slouží k zobrazení grafu popisky, které indikují typy hodnot a volitelně k převedení hodnoty. *Jednotky* typ Určuje kategorii jednotky a definuje dostupných *aktuální jednotku* typ hodnoty. Pokud vyberete hodnotu v *převést na*, číselné hodnoty jsou převést z *aktuální jednotku* typ, který má *převést na* typu. |
| Vlastní popisek |Text, který se zobrazí vedle popisek osy y *jednotky* typu. Pokud není zadaný žádný štítek, jenom *jednotky* typu se zobrazí. |
| **seznam** | |
| Dotaz |Dotaz, aby běžel v seznamu. Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt grafu |Vyberte tento odkaz zakázat grafu na pravé straně číselný sloupec. |
| Povolit minigrafů |Vyberte tento odkaz zobrazíte minigraf místo vodorovném řádku. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Barva |Barva řádky nebo minigrafů. |
| Operace |Operace k provedení pro minigraf. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Název a hodnotu oddělovače |Oddělovač jeden znak, který se má použít k analýze vlastnosti textu do více hodnot. Další informace najdete v tématu [společná nastavení](#sparklines). |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na položku v seznamu.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **seznam** |**> Názvy sloupce** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz na povolit prahové hodnoty. Další informace najdete v tématu [společná nastavení](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Zásobník části grafy řádku
Zásobník spojnicový graf zobrazuje tři samostatné spojnicových grafů, s více řad z protokolu dotazu v čase, jak je vidět tady:

![Zásobník spojnicových grafů](media/log-analytics-view-designer/view-stack-line-charts.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části na dlaždici. |
| Nové skupiny |Vyberte tento odkaz na vytvoření nové skupiny v zobrazení začínající na aktuální zobrazení. |
| Ikona |Zobrazí se vedle výsledek v hlavičce souboru bitové kopie. |
| **Grafu 1<br>grafu 2<br>grafu 3** |**> Záhlaví** |
| Nadpis |Text, který se zobrazí v horní části grafu. |
| Podtitul |Text, který se zobrazí pod názvem v horní části grafu. |
| **Grafu 1<br>grafu 2<br>grafu 3** |**Spojnicový graf** |
| Dotaz |Dotaz, který spustit spojnicového grafu. Textovou hodnotu, která je první vlastností a druhou vlastností je číselná hodnota. Tento dotaz se obvykle používá *měr* – klíčové slovo pro shrnutí výsledků. Pokud dotaz používá *interval* – klíčové slovo, osy x grafu používá tento časový interval. Pokud dotaz neobsahuje *interval* – klíčové slovo, hodinových intervalech používá osy x. |
| Klikněte na tlačítko prostřednictvím navigace | Akce po kliknutí na záhlaví.  Další informace najdete v tématu [společná nastavení](#click-through-navigation). |
| **Chart** |**> Osy y** |
| Použít logaritmickou stupnici |Vyberte tento odkaz se má použít hodnota na logaritmické stupnici pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty má být vrácen v dotazu. Tyto informace slouží k zobrazení grafu popisky, které indikují typy hodnot a volitelně k převedení hodnoty. *Jednotky* typ Určuje kategorii jednotky a definuje dostupných *aktuální jednotku* typ hodnoty. Pokud vyberete hodnotu v *převést na*, číselné hodnoty jsou převést z *aktuální jednotku* typ, který má *převést na* typu. |
| Vlastní popisek |Text, který se zobrazí vedle popisek osy y *jednotky* typu. Pokud není zadaný žádný štítek, jenom *jednotky* typu se zobrazí. |

## <a name="common-settings"></a>Obecná nastavení
Následující části popisují nastavení, které jsou společné pro několik částí vizualizace.

### <a name="name-value-separator"></a>Název a hodnotu oddělovače
Název a hodnotu oddělovače je jeden znak oddělovač, který má použít k analýze vlastnost text z dotazu seznamu do více hodnot. Pokud zadáte oddělovač, můžete zadat názvy pro každé pole oddělené oddělovačem stejné v **název** pole.

Představte si třeba vlastnost s názvem *umístění* , jako například zahrnuty hodnoty *Redmond vytváření 41* a *Bellevue vytváření 12*. Můžete zadat pomlčkou (-) pro název a hodnotu oddělovač a *města vytváření* pro název. Tento přístup analyzuje všechny hodnoty do dvou vlastností názvem *města* a *vytváření*.

### <a name="click-through-navigation"></a>Klikněte na tlačítko prostřednictvím navigace
Klikněte na tlačítko prostřednictvím navigační definuje, jaké akce se provedou, když kliknete na položku hlavičky nebo seznamu v zobrazení.  Otevře se buď v dotazu [hledání protokolů portál](log-analytics-log-search-portals.md#log-search) nebo spusťte jiného zobrazení.

Následující tabulka popisuje nastavení, klikněte na tlačítko prostřednictvím navigace.

| Nastavení           | Popis |
|:--|:--|
| Hledání protokolů (automaticky) | Hledání protokolů spustit, když vyberete položku záhlaví.  Toto je stejný vyhledávání protokolu založenou na položce.
| Prohledávání protokolů        | Hledání protokolů spustit, když vyberete položku v seznamu.  Zadejte dotaz do **navigační dotazu** pole.   Použití *{vybranou položku}* zahrnout syntaxe pro položku, kterou uživatel vybral.  Například, pokud dotaz obsahuje sloupec s názvem *počítače* a dotaz navigace je *{vybranou položku}*, dotazu jako *počítač = "Tento počítač"* se spustí po výběru počítač. Pokud se dotaz navigační *typ = událostí {vybranou položku}*, dotaz *typ = událostí počítač = "Tento počítač"* běží. |
| Zobrazení              | Zobrazení otevřít po výběru položky záhlaví nebo položku v seznamu.  Vyberte název zobrazení v pracovním prostoru v **název zobrazení** pole. |



### <a name="sparklines"></a>Sparklines
Minigraf je malý spojnicový graf, který znázorňuje hodnotu položky seznamu v čase. Pro vizualizaci části seznamu můžete vybrat, jestli se má zobrazit vodorovné řádek, který určuje relativní hodnota je číselný sloupec nebo minigraf, což určuje jeho hodnotu v čase.

Následující tabulka popisuje nastavení minigrafů:

| Nastavení | Popis |
|:--- |:--- |
| Povolit minigrafů |Vyberte tento odkaz zobrazíte minigraf místo vodorovném řádku. |
| Operace |Pokud jsou povolené minigrafů, jedná se operaci provést na každou vlastnost v seznamu k výpočtu hodnot pro minigraf.<ul><li>Poslední ukázkové: poslední hodnotu pro sérii během časového intervalu.</li><li>Maximální počet: Maximální hodnota pro řadu během časového intervalu.</li><li>Min: Minimální hodnota pro řadu během časového intervalu.</li><li>Součet: Součet hodnoty pro řady během časového intervalu.</li><li>Souhrn: Používá stejnou `measure` příkaz jako dotaz v hlavičce.</li></ul> |

### <a name="thresholds"></a>Prahové hodnoty
Pomocí prahové hodnoty můžete zobrazit Barevná ikona vedle jednotlivých položek v seznamu. Prahové hodnoty získáte rychlý vizuální indikátor položek, které překročí určitou hodnotu nebo spadá do určitého rozsahu. Například můžete zobrazit zelené ikony pro položky s přijatelnou hodnotu, žlutý, pokud je hodnota v rozsahu, která určuje, upozornění a červená, pokud překročí chybovou hodnotu.

Když povolíte prahové hodnoty pro část, je nutné zadat jeden nebo více prahové hodnoty. Pokud hodnota položky je větší než prahová hodnota a nižší než další prahová hodnota, použije se barvu pro tuto hodnotu. Pokud položka je větší než nejvyšší prahová hodnota, použije se jiné barvy. 

Každá sada prahová hodnota má jeden prahové hodnoty s hodnotou **výchozí**. Toto je barvu, která bude nastavena, pokud se překročí žádné jiné hodnoty. Můžete přidat nebo odebrat prahové hodnoty výběrem **přidat** (+) nebo **odstranit** (x).

Následující tabulka popisuje nastavení prahové hodnoty:

| Nastavení | Popis |
|:--- |:--- |
| Povolit prahové hodnoty |Vyberte tento odkaz zobrazíte ikonu Barva vlevo od jednotlivých hodnot. Ikona označuje stav hodnotu vzhledem k zadané prahové hodnoty. |
| Název |Název prahovou hodnotu. |
| Mezní hodnota |Hodnota pro mezní hodnotu. Barva stavu pro každou položku seznamu je nastavena na hodnotu barvu nejvyšší prahovou hodnotu, která je překročena hodnotou položky. Pokud byly překročeny žádné mezní hodnoty, použije se výchozí barvu. |
| Barva |Barvu, která určuje prahovou hodnotu. |

## <a name="next-steps"></a>Další postup
* Další informace o [protokolu hledání](log-analytics-log-searches.md) pro podporu dotazů v části vizualizace.
