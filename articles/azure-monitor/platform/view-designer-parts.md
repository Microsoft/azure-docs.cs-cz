---
title: Referenční příručka k dílům Návrháře zobrazení v Azure Monitoru | Dokumenty společnosti Microsoft
description: Pomocí Návrháře zobrazení v Azure Monitoru můžete vytvořit vlastní zobrazení, která se zobrazují na webu Azure Portal a obsahují různé vizualizace dat v pracovním prostoru Log Analytics. Tento článek je referenční příručkou k nastavení vizualizačních částí, které jsou k dispozici ve vlastních zobrazeních.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 7dc4164cac1998a02ae62d7682f9630aa4faa619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658553"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Referenční příručka k vizualizačním dílům návrháře zobrazení v Azure Monitoru
Pomocí Návrháře zobrazení v Azure Monitoru můžete na webu Azure Portal vytvořit celou řadu vlastních zobrazení, která vám pomůžou vizualizovat data v pracovním prostoru Log Analytics. Tento článek je referenční příručkou k nastavení vizualizačních částí, které jsou k dispozici ve vlastních zobrazeních.

Další informace o Návrháři zobrazení naleznete v tématu:

* [Návrhář zobrazení](view-designer.md): Poskytuje přehled návrháře zobrazení a postupy pro vytváření a úpravy vlastních zobrazení.
* [Odkaz na dlaždici](view-designer-tiles.md): Poskytuje odkaz na nastavení pro každou dostupnou dlaždici ve vlastních zobrazeních.


Dostupné typy dlaždic Návrhář zobrazení jsou popsány v následující tabulce:

| Typ zobrazení | Popis |
|:--- |:--- |
| [Seznam dotazů](#list-of-queries-part) |Zobrazí seznam dotazů protokolu. Můžete vybrat každý dotaz a zobrazit jeho výsledky. |
| [Číslo a seznam](#number-and-list-part) |V záhlaví se zobrazí jedno číslo, které zobrazuje počet záznamů z dotazu protokolu. Seznam zobrazuje prvních deset výsledků z dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změnu v čase. |
| [Dvě čísla a seznam](#two-numbers-and-list-part) |Záhlaví zobrazuje dvě čísla, která zobrazují počty záznamů ze samostatných dotazů protokolu. Seznam zobrazuje prvních deset výsledků z dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změnu v čase. |
| [Kobliha a seznam](#donut-and-list-part) |V záhlaví se zobrazí jedno číslo, které shrnuje sloupec hodnoty v dotazu protokolu. Kobliha graficky zobrazuje výsledky prvních tří záznamů. |
| [Dvě časové osy a seznam](#two-timelines-and-list-part) |Záhlaví zobrazuje výsledky dvou dotazů protokolu v průběhu času jako sloupcové grafy s popiskem, který zobrazuje jedno číslo, které shrnuje sloupec hodnoty v dotazu protokolu. Seznam zobrazuje prvních deset výsledků z dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změnu v čase. |
| [Informace](#information-part) |Záhlaví zobrazuje statický text a volitelný odkaz. V seznamu se zobrazí jedna nebo více položek se statickým názvem a textem. |
| [Spojnicový graf, popisek a seznam](#line-chart-callout-and-list-part) |Záhlaví zobrazuje spojnicový graf s více řadami z dotazu protokolu v průběhu času a popiskem se souhrnnou hodnotou. Seznam zobrazuje prvních deset výsledků z dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změnu v čase. |
| [Spojnicový graf a seznam](#line-chart-and-list-part) |Záhlaví zobrazuje spojnicový graf s více řadami z dotazu protokolu v průběhu času. Seznam zobrazuje prvních deset výsledků z dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změnu v čase. |
| [Stoh dílu spojnicových grafů](#stack-of-line-charts-part) |Zobrazí tři samostatné spojnicové grafy s více řadami z dotazu protokolu v průběhu času. |

V dalších částech podrobně popisují typy dlaždic a jejich vlastnosti.

> [!NOTE]
> Části v zobrazeních jsou založeny na [dotazech protokolu](../log-query/log-query-overview.md) v pracovním prostoru Log Analytics. V současné době nepodporují [dotazy na příčné prostředky](../log-query/cross-workspace-query.md) k načtení dat z Application Insights.

## <a name="list-of-queries-part"></a>Seznam dotazů část
V části Seznam dotazů se zobrazí seznam dotazů protokolu. Můžete vybrat každý dotaz a zobrazit jeho výsledky. Zobrazení obsahuje ve výchozím nastavení jeden dotaz a můžete vybrat **+ dotaz** a přidat další dotazy.

![Zobrazení seznamu dotazů](media/view-designer-parts/view-list-queries.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Nadpis |Text zobrazený v horní části zobrazení. |
| Nová skupina |Výběrem tohoto odkazu vytvoříte v zobrazení novou skupinu, která začíná v aktuálním zobrazení. |
| Předem vybrané filtry |Seznam vlastností oddělených čárkami, který má být zahrnut do levého podokna filtru při výběru dotazu. |
| Režim vykreslení |Počáteční zobrazení, které se zobrazí při výběru dotazu. Po otevření dotazu můžete vybrat všechna dostupná zobrazení. |
| **Dotazy** | |
| Vyhledávací dotaz |Dotaz, který má být spuštěn. |
| Popisný název | Popisný název, který je zobrazen. |

## <a name="number-and-list-part"></a>Číslo a seznam dílu
V záhlaví se zobrazí jedno číslo, které zobrazuje počet záznamů z dotazu protokolu. Seznam zobrazuje prvních deset výsledků z dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změnu v čase.

![Zobrazení seznamu dotazů](media/view-designer-parts/view-number-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text zobrazený v horní části zobrazení. |
| Nová skupina |Výběrem tohoto odkazu vytvoříte v zobrazení novou skupinu, která začíná v aktuálním zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v záhlaví. |
| Ikona Použít |Výběrem tohoto odkazu zobrazíte ikonu. |
| **Název** | |
| Legenda |Text, který je zobrazen v horní části záhlaví. |
| Dotaz |Dotaz, který má být spuštěn pro záhlaví. Zobrazí se počet záznamů vrácených dotazem. |
| Navigace po prokliku | Akce provedená po kliknutí na záhlaví.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Seznamu** | |
| Dotaz |Dotaz, který má být spuštěn pro seznam. Zobrazí se první dvě vlastnosti prvních deseti záznamů ve výsledcích. První vlastnost je textová hodnota a druhá vlastnost je číselná hodnota. Automaticky se vytvoří pruhy, které jsou založeny na relativní hodnotě číselného sloupce.<br><br>Pomocí `Sort` příkazu v dotazu seřadíte záznamy v seznamu. Chcete-li spustit dotaz a vrátit všechny záznamy, můžete vybrat **možnost Zobrazit vše**. |
| Skrýt graf |Výběrem tohoto odkazu zakážete graf vpravo od číselného sloupce. |
| Povolení minigrafů |Vyberte tento odkaz, chcete-li zobrazit minigraf místo vodorovného pruhu. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Barvy |Barva pruhů nebo minigrafů. |
| Oddělovač názvu a hodnoty |Oddělovač jednoho znaku, který se má použít k rozdělení vlastnosti textu na více hodnot. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Navigace po prokliku | Akce provedená po kliknutí na položku v seznamu.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Seznamu** |**> názvy sloupců** |
| Name (Název) |Text, který je zobrazen v horní části prvního sloupce. |
| Hodnota |Text, který je zobrazen v horní části druhého sloupce. |
| **Seznamu** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace naleznete v [tématu Common Settings](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dvě čísla a část seznamu
Záhlaví má dvě čísla, která zobrazují počet záznamů ze samostatných dotazů protokolu. Seznam zobrazuje prvních deset výsledků z dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změnu v čase.

![Zobrazení seznamu & dvě čísla](media/view-designer-parts/view-two-numbers-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text zobrazený v horní části zobrazení. |
| Nová skupina |Výběrem tohoto odkazu vytvoříte v zobrazení novou skupinu, která začíná v aktuálním zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v záhlaví. |
| Ikona Použít |Výběrem tohoto odkazu zobrazíte ikonu. |
| **Navigace podle názvu** | |
| Navigace po prokliku | Akce provedená po kliknutí na záhlaví.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Název** | |
| Legenda |Text, který je zobrazen v horní části záhlaví. |
| Dotaz |Dotaz, který má být spuštěn pro záhlaví. Zobrazí se počet záznamů vrácených dotazem. |
| **Seznamu** | |
| Dotaz |Dotaz, který má být spuštěn pro seznam. Zobrazí se první dvě vlastnosti prvních deseti záznamů ve výsledcích. První vlastnost je textová hodnota a druhá vlastnost je číselná hodnota. Pruhy jsou automaticky vytvořeny na základě relativní hodnoty číselného sloupce.<br><br>Pomocí `Sort` příkazu v dotazu seřadíte záznamy v seznamu. Chcete-li spustit dotaz a vrátit všechny záznamy, můžete vybrat **možnost Zobrazit vše**. |
| Skrýt graf |Výběrem tohoto odkazu zakážete graf vpravo od číselného sloupce. |
| Povolení minigrafů |Vyberte tento odkaz, chcete-li zobrazit minigraf místo vodorovného pruhu. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Barvy |Barva pruhů nebo minigrafů. |
| Operace |Operace, která má být pro minigraf konfrontujena. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Oddělovač názvu a hodnoty |Oddělovač jednoho znaku, který se má použít k rozdělení vlastnosti textu na více hodnot. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Navigace po prokliku | Akce provedená po kliknutí na položku v seznamu.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Seznamu** |**> názvy sloupců** |
| Name (Název) |Text, který je zobrazen v horní části prvního sloupce. |
| Hodnota |Text, který je zobrazen v horní části druhého sloupce. |
| **Seznamu** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace naleznete v [tématu Common Settings](#thresholds). |

## <a name="donut-and-list-part"></a>Díl donuta a seznamu
V záhlaví se zobrazí jedno číslo, které shrnuje sloupec hodnoty v dotazu protokolu. Kobliha graficky zobrazuje výsledky prvních tří záznamů.

![Zobrazení domaticky a seznamu](media/view-designer-parts/view-donut-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který je zobrazen v horní části dlaždice. |
| Nová skupina |Výběrem tohoto odkazu vytvoříte v zobrazení novou skupinu, která začíná v aktuálním zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v záhlaví. |
| Ikona Použít |Výběrem tohoto odkazu zobrazíte ikonu. |
| **Záhlaví** | |
| Nadpis |Text, který je zobrazen v horní části záhlaví. |
| Podnadpis |Text zobrazený pod nadpisem v horní části záhlaví. |
| **Kobliha** | |
| Dotaz |Dotaz ke spuštění pro koblihu. První vlastnost je textová hodnota a druhá vlastnost je číselná hodnota. |
| Navigace po prokliku | Akce provedená po kliknutí na záhlaví.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Kobliha** |**> centrum** |
| Text |Text, který je zobrazen pod hodnotou uvnitř koblihy. |
| Operace |Operace, která má být v ysusu vlastnosti value, aby byla shrnuta jako jedna hodnota.<ul><li>Součet: Sečte hodnoty všech záznamů.</li><li>Procento: Poměr záznamů vrácených hodnotami v **hodnotách Výsledek použitých v operaci na střed** k celkovým záznamům v dotazu.</li></ul> |
| Výsledné hodnoty použité v provozu středu |Volitelně můžete vybrat znaménko plus (+) a přidat jednu nebo více hodnot. Výsledky dotazu jsou omezeny na záznamy s zadanými hodnotami vlastností. Pokud nejsou přidány žádné hodnoty, jsou do dotazu zahrnuty všechny záznamy. |
| **Další možnosti** |**> barvy** |
| Barva 1<br>Barva 2<br>Barva 3 |Vyberte barvu pro každou z hodnot, které jsou zobrazeny v koblihu. |
| **Další možnosti** |**> rozšířené mapování barev** |
| Hodnota pole |Zadejte název pole, které má být zobrazeno jako jiné barvy, pokud je zahrnuto v koblihu. |
| Barvy |Vyberte barvu jedinečného pole. |
| **Seznamu** | |
| Dotaz |Dotaz, který má být spuštěn pro seznam. Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt graf |Výběrem tohoto odkazu zakážete graf vpravo od číselného sloupce. |
| Povolení minigrafů |Vyberte tento odkaz, chcete-li zobrazit minigraf místo vodorovného pruhu. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Barvy |Barva pruhů nebo minigrafů. |
| Operace |Operace, která má být pro minigraf konfrontujena. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Oddělovač názvu a hodnoty |Oddělovač jednoho znaku, který se má použít k rozdělení vlastnosti textu na více hodnot. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Navigace po prokliku | Akce provedená po kliknutí na položku v seznamu.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Seznamu** |**> názvy sloupců** |
| Name (Název) |Text, který je zobrazen v horní části prvního sloupce. |
| Hodnota |Text, který je zobrazen v horní části druhého sloupce. |
| **Seznamu** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace naleznete v [tématu Common Settings](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Dvě časové osy a část seznamu
Záhlaví zobrazuje výsledky dvou dotazů protokolu v průběhu času jako sloupcové grafy s popiskem, který zobrazuje jedno číslo, které shrnuje sloupec hodnoty v dotazu protokolu. Seznam zobrazuje prvních deset výsledků z dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změnu v čase.

![Dvě časové osy a zobrazení seznamu](media/view-designer-parts/view-two-timelines-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který je zobrazen v horní části dlaždice. |
| Nová skupina |Výběrem tohoto odkazu vytvoříte v zobrazení novou skupinu, která začíná v aktuálním zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v záhlaví. |
| Ikona Použít |Výběrem tohoto odkazu zobrazíte ikonu. |
| **Navigace podle názvu** | |
| Navigace po prokliku | Akce provedená po kliknutí na záhlaví.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **První<br>graf Druhý graf** | |
| Legenda |Text, který se zobrazí pod popiskem pro první řadu. |
| Barvy |Barva, která má být pro sloupce v řadě. |
| Dotaz |Dotaz, který má být spuštěn pro první řadu. Počet záznamů v každém časovém intervalu je reprezentován sloupci grafu. |
| Operace |Operace, která má být provedena s vlastností value, aby ji shrnula jako jednu hodnotu popisku.<ul><li>Součet: Součet hodnot ze všech záznamů.</li><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Poslední ukázka: Hodnota z posledního intervalu, který je součástí grafu.</li><li>První ukázka: Hodnota z prvního intervalu, který je součástí grafu.</li><li>Počet: Počet všech záznamů, které jsou vráceny dotazem.</li></ul> |
| **Seznamu** | |
| Dotaz |Dotaz, který má být spuštěn pro seznam. Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt graf |Výběrem tohoto odkazu zakážete graf vpravo od číselného sloupce. |
| Povolení minigrafů |Vyberte tento odkaz, chcete-li zobrazit minigraf místo vodorovného pruhu. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Barvy |Barva pruhů nebo minigrafů. |
| Operace |Operace, která má být pro minigraf konfrontujena. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Navigace po prokliku | Akce provedená po kliknutí na položku v seznamu.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Seznamu** |**> názvy sloupců** |
| Name (Název) |Text, který je zobrazen v horní části prvního sloupce. |
| Hodnota |Text, který je zobrazen v horní části druhého sloupce. |
| **Seznamu** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace naleznete v [tématu Common Settings](#thresholds). |

## <a name="information-part"></a>Informační část
Záhlaví zobrazuje statický text a volitelný odkaz. V seznamu se zobrazí jedna nebo více položek se statickým názvem a textem.

![Zobrazení informací](media/view-designer-parts/view-information.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který je zobrazen v horní části dlaždice. |
| Nová skupina |Výběrem tohoto odkazu vytvoříte v zobrazení novou skupinu, která začíná v aktuálním zobrazení. |
| Barvy |Barva pozadí záhlaví. |
| **Záhlaví** | |
| Image |Soubor obrázku, který je zobrazen v záhlaví. |
| Popisek |Text, který je zobrazen v záhlaví. |
| **Záhlaví** |**> odkaz** |
| Popisek |Text odkazu. |
| URL |Adresa URL odkazu. |
| **Informační položky** | |
| Nadpis |Text, který je zobrazen pro název každé položky. |
| Obsah |Text, který je zobrazen pro každou položku. |

## <a name="line-chart-callout-and-list-part"></a>Spojnicový graf, popisek a seznam
Záhlaví zobrazuje spojnicový graf s více řadami z dotazu protokolu v průběhu času a popisek se souhrnnou hodnotou. Seznam zobrazuje prvních deset výsledků z dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změnu v čase.

![Spojnicový graf, popisek a zobrazení seznamu](media/view-designer-parts/view-line-chart-callout-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který je zobrazen v horní části dlaždice. |
| Nová skupina |Výběrem tohoto odkazu vytvoříte v zobrazení novou skupinu, která začíná v aktuálním zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v záhlaví. |
| Ikona Použít |Výběrem tohoto odkazu zobrazíte ikonu. |
| **Záhlaví** | |
| Nadpis |Text, který je zobrazen v horní části záhlaví. |
| Podnadpis |Text zobrazený pod nadpisem v horní části záhlaví. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který má být spuštěn pro spojnicový graf. První vlastnost je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *míra* sumarizovat výsledky. Pokud dotaz používá klíčové slovo *interval,* použije tento časový interval osa x grafu. Pokud dotaz neobsahuje klíčové slovo *interval,* používá osa x hodinové intervaly. |
| Navigace po prokliku | Akce provedená po kliknutí na záhlaví.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Spojnicový graf** |**> popisek** |
| Název popisku |Text, který je zobrazen nad hodnotou popisku. |
| Název řady |Hodnota vlastnosti řady, která má být pro hodnotu popisku. Pokud není k dispozici žádná řada, budou použity všechny záznamy z dotazu. |
| Operace |Operace, která má být provedena s vlastností value, aby ji shrnula jako jednu hodnotu popisku.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všech záznamů, které jsou vráceny dotazem.</li><li>Poslední ukázka: Hodnota z posledního intervalu, který je součástí grafu.</li><li>Max: Maximální hodnota z intervalů, které jsou zahrnuty v grafu.</li><li>Min: Minimální hodnota z intervalů, které jsou zahrnuty v grafu.</li><li>Součet: Součet hodnot ze všech záznamů.</li></ul> |
| **Spojnicový graf** |**> osy Y** |
| Použít logaritmické měřítko |Vyberte tento odkaz, chcete-li použít logaritmické měřítko pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty, které mají být vráceny dotazem. Tyto informace slouží k zobrazení popisků grafu, které označují typy hodnot, a volitelně k převodu hodnot. Typ *jednotky* určuje kategorii jednotky a definuje dostupné hodnoty typu *Aktuální jednotka.* Pokud vyberete hodnotu v *příkazu Převést na*, číselné hodnoty se převedou z typu *Aktuální jednotka* na *typ Převést na.* |
| Vlastní popisek |Text, který se zobrazí pro osu y vedle popisku pro typ *jednotky.* Pokud není zadán žádný popisek, zobrazí se pouze typ *jednotky.* |
| **Seznamu** | |
| Dotaz |Dotaz, který má být spuštěn pro seznam. Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt graf |Výběrem tohoto odkazu zakážete graf vpravo od číselného sloupce. |
| Povolení minigrafů |Vyberte tento odkaz, chcete-li zobrazit minigraf místo vodorovného pruhu. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Barvy |Barva pruhů nebo minigrafů. |
| Operace |Operace, která má být pro minigraf konfrontujena. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Oddělovač názvu a hodnoty |Oddělovač jednoho znaku, který se má použít k rozdělení vlastnosti textu na více hodnot. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Navigace po prokliku | Akce provedená po kliknutí na položku v seznamu.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Seznamu** |**> názvy sloupců** |
| Name (Název) |Text, který je zobrazen v horní části prvního sloupce. |
| Hodnota |Text, který je zobrazen v horní části druhého sloupce. |
| **Seznamu** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace naleznete v [tématu Common Settings](#thresholds). |

## <a name="line-chart-and-list-part"></a>Spojnicový graf a část seznamu
Záhlaví zobrazuje spojnicový graf s více řadami z dotazu protokolu v průběhu času. Seznam zobrazuje prvních deset výsledků z dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změnu v čase.

![Zobrazení spojnicového grafu a seznamu](media/view-designer-parts/view-line-chart-callout-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který je zobrazen v horní části dlaždice. |
| Nová skupina |Výběrem tohoto odkazu vytvoříte v zobrazení novou skupinu, která začíná v aktuálním zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v záhlaví. |
| Ikona Použít |Výběrem tohoto odkazu zobrazíte ikonu. |
| **Záhlaví** | |
| Nadpis |Text, který je zobrazen v horní části záhlaví. |
| Podnadpis |Text zobrazený pod nadpisem v horní části záhlaví. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který má být spuštěn pro spojnicový graf. První vlastnost je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *míra* sumarizovat výsledky. Pokud dotaz používá klíčové slovo *interval,* použije tento časový interval osa x grafu. Pokud dotaz neobsahuje klíčové slovo *interval,* používá osa x hodinové intervaly. |
| Navigace po prokliku | Akce provedená po kliknutí na záhlaví.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Spojnicový graf** |**> osy Y** |
| Použít logaritmické měřítko |Vyberte tento odkaz, chcete-li použít logaritmické měřítko pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty, které mají být vráceny dotazem. Tyto informace slouží k zobrazení popisků grafu, které označují typy hodnot, a volitelně k převodu hodnot. Typ *jednotky* určuje kategorii jednotky a definuje dostupné hodnoty typu *Aktuální jednotka.* Pokud vyberete hodnotu v *příkazu Převést na*, číselné hodnoty se převedou z typu *Aktuální jednotka* na *typ Převést na.* |
| Vlastní popisek |Text, který se zobrazí pro osu y vedle popisku pro typ *jednotky.* Pokud není zadán žádný popisek, zobrazí se pouze typ *jednotky.* |
| **Seznamu** | |
| Dotaz |Dotaz, který má být spuštěn pro seznam. Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt graf |Výběrem tohoto odkazu zakážete graf vpravo od číselného sloupce. |
| Povolení minigrafů |Vyberte tento odkaz, chcete-li zobrazit minigraf místo vodorovného pruhu. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Barvy |Barva pruhů nebo minigrafů. |
| Operace |Operace, která má být pro minigraf konfrontujena. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Oddělovač názvu a hodnoty |Oddělovač jednoho znaku, který se má použít k rozdělení vlastnosti textu na více hodnot. Další informace naleznete v [tématu Common Settings](#sparklines). |
| Navigace po prokliku | Akce provedená po kliknutí na položku v seznamu.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Seznamu** |**> názvy sloupců** |
| Name (Název) |Text, který je zobrazen v horní části prvního sloupce. |
| Hodnota |Text, který je zobrazen v horní části druhého sloupce. |
| **Seznamu** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace naleznete v [tématu Common Settings](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Stoh dílu spojnicových grafů
Stoh spojnicového grafu zobrazuje tři samostatné spojnicové grafy s více řadami z dotazu protokolu v průběhu času, jak je znázorněno zde:

![Stoh spojnicových grafů](media/view-designer-parts/view-stack-line-charts.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který je zobrazen v horní části dlaždice. |
| Nová skupina |Výběrem tohoto odkazu vytvoříte v zobrazení novou skupinu, která začíná v aktuálním zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v záhlaví. |
| **Graf<br>1<br>Graf 2 Graf 3** |**> záhlaví** |
| Nadpis |Text zobrazený v horní části grafu. |
| Podnadpis |Text zobrazený pod názvem v horní části grafu. |
| **Graf<br>1<br>Graf 2 Graf 3** |**Spojnicový graf** |
| Dotaz |Dotaz, který má být spuštěn pro spojnicový graf. První vlastnost je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *míra* sumarizovat výsledky. Pokud dotaz používá klíčové slovo *interval,* použije tento časový interval osa x grafu. Pokud dotaz neobsahuje klíčové slovo *interval,* používá osa x hodinové intervaly. |
| Navigace po prokliku | Akce provedená po kliknutí na záhlaví.  Další informace naleznete v [tématu Common Settings](#click-through-navigation). |
| **Graf** |**> osy Y** |
| Použít logaritmické měřítko |Vyberte tento odkaz, chcete-li použít logaritmické měřítko pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty, které mají být vráceny dotazem. Tyto informace slouží k zobrazení popisků grafu, které označují typy hodnot, a volitelně k převodu hodnot. Typ *jednotky* určuje kategorii jednotky a definuje dostupné hodnoty typu *Aktuální jednotka.* Pokud vyberete hodnotu v *příkazu Převést na*, číselné hodnoty se převedou z typu *Aktuální jednotka* na *typ Převést na.* |
| Vlastní popisek |Text, který se zobrazí pro osu y vedle popisku pro typ *jednotky.* Pokud není zadán žádný popisek, zobrazí se pouze typ *jednotky.* |

## <a name="common-settings"></a>Obecná nastavení
Následující části popisují nastavení, která jsou společná pro několik vizualizačních částí.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Oddělovač názvu a hodnoty
Oddělovač názvu a hodnot je oddělovač jednoho znaku, který se má použít k rozdělení vlastnosti textu z dotazu seznamu na více hodnot. Pokud zadáte oddělovač, můžete zadat názvy pro každé pole oddělené stejným oddělovačem v poli **Název.**

Zvažte například vlastnost s názvem *Umístění,* která zahrnovala hodnoty jako *Redmond-Building 41* a *Bellevue-Building 12*. Můžete zadat pomlčku (-) pro oddělovač názvu a hodnot a pro název *budovu města.* Tento přístup analyzuje každou hodnotu do dvou vlastností s názvem *Město* a *budova*.

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Navigace po prokliku
Navigace po prokliku definuje, jaká akce bude provedena po kliknutí na záhlaví nebo položku seznamu v zobrazení.  Tím se buď otevře dotaz v [Log Analytics](../../azure-monitor/log-query/portals.md) nebo spustí jiné zobrazení.

Následující tabulka popisuje nastavení pro proklikávací navigaci.

| Nastavení           | Popis |
|:--|:--|
| Hledání protokolu (automaticky) | Protokolovat dotaz, který se má spustit při výběru položky záhlaví.  Jedná se o stejný dotaz protokolu, na které je položka založena.
| Prohledávání protokolů        | Protokolovat dotaz, který chcete spustit, když vyberete položku v seznamu.  Zadejte dotaz do pole **Navigační dotaz.**   Pomocí *položky {vybraná položka}* můžete zahrnout syntaxi položky, kterou uživatel vybral.  Pokud má například dotaz sloupec s názvem *Počítač* a navigační dotaz je *{selected item}*, spustí se při výběru počítače dotaz, například *Computer="MyComputer".* Pokud je navigační dotaz *Type=Event {selected item}*, je spuštěn dotaz *Type=Event Computer="MyComputer".* |
| Zobrazení              | Zobrazení, které se otevře, když vyberete položku záhlaví nebo položku v seznamu.  V poli **Název zobrazení** vyberte název zobrazení v pracovním prostoru. |



### <a name="sparklines"></a><a name="sparklines"></a>Minigrafy
Minigraf je malý spojnicový graf, který znázorňuje hodnotu položky seznamu v průběhu času. U vizualizačních částí se seznamem můžete vybrat, zda chcete zobrazit vodorovný pruh, který označuje relativní hodnotu číselného sloupce, nebo minigraf, který označuje jeho hodnotu v čase.

Následující tabulka popisuje nastavení minigrafů:

| Nastavení | Popis |
|:--- |:--- |
| Povolení minigrafů |Vyberte tento odkaz, chcete-li zobrazit minigraf místo vodorovného pruhu. |
| Operace |Pokud jsou povoleny minigrafy, jedná se o operaci, která má být u každé vlastnosti v seznamu vypočtena hodnoty minigrafu.<ul><li>Poslední ukázka: Poslední hodnota pro řadu v časovém intervalu.</li><li>Max: Maximální hodnota pro řadu v časovém intervalu.</li><li>Min: Minimální hodnota pro řadu v časovém intervalu.</li><li>Součet: Součet hodnot pro řadu v časovém intervalu.</li><li>Shrnutí: Používá `measure` stejný příkaz jako dotaz v záhlaví.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Prahové hodnoty
Pomocí prahových hodnot můžete vedle každé položky v seznamu zobrazit barevnou ikonu. Prahové hodnoty poskytují rychlý vizuální indikátor položek, které překračují určitou hodnotu nebo spadají do určitého rozsahu. Můžete například zobrazit zelenou ikonu pro položky s přijatelnou hodnotou, žlutou, pokud je hodnota v rozsahu, který označuje upozornění, a červenou, pokud překročí chybovou hodnotu.

Pokud povolíte prahové hodnoty pro součást, je nutné zadat jednu nebo více prahových hodnot. Pokud je hodnota položky větší než prahová hodnota a nižší než další prahová hodnota, použije se barva pro tuto hodnotu. Pokud je položka větší než nejvyšší prahová hodnota, použije se jiná barva. 

Každá sada prahových hodnot má jednu prahovou hodnotu s hodnotou **Default**. Toto je barva, která je nastavena, pokud nejsou překročeny žádné jiné hodnoty. Prahové hodnoty můžete přidat nebo odebrat tak, že vyberete tlačítko **Přidat** (+) nebo **Odstranit** (x).

Následující tabulka popisuje nastavení prahových hodnot:

| Nastavení | Popis |
|:--- |:--- |
| Povolit prahové hodnoty |Výběrem tohoto odkazu zobrazíte ikonu barvy vlevo od každé hodnoty. Ikona označuje stav hodnoty vzhledem k zadané prahové hodnoty. |
| Name (Název) |Název prahové hodnoty. |
| Prahová hodnota |Hodnota prahové hodnoty. Barva stavu pro každou položku seznamu je nastavena na barvu nejvyšší prahové hodnoty, která je překročena hodnotou položky. Pokud nejsou překročeny žádné prahové hodnoty, použije se výchozí barva. |
| Barvy |Barva, která označuje prahovou hodnotu. |

## <a name="next-steps"></a>Další kroky
* Další informace o [dotazech protokolu](../log-query/log-query-overview.md) pro podporu dotazů ve vizualizačních částech.
