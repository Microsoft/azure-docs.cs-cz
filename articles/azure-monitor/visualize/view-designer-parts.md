---
title: Referenční příručka k částem návrháře zobrazení v Azure Monitor | Microsoft Docs
description: Pomocí návrháře zobrazení v Azure Monitor můžete vytvořit vlastní zobrazení, která jsou zobrazena v Azure Portal a obsahují celou řadu vizualizací dat v pracovním prostoru Log Analytics. Tento článek představuje referenční příručku k nastavením pro části vizualizace, které jsou k dispozici ve vlastních zobrazeních.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 6e971db01322fc031dd0fa8abe82f76a5b45d256
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045254"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Referenční příručka na části vizualizace návrháře zobrazení v Azure Monitor
Pomocí návrháře zobrazení v Azure Monitor můžete v Azure Portal vytvořit nejrůznější vlastní zobrazení, která vám pomůžou vizualizovat data v pracovním prostoru Log Analytics. Tento článek představuje referenční příručku k nastavením pro části vizualizace, které jsou k dispozici ve vlastních zobrazeních.

Další informace o návrháři zobrazení najdete v těchto tématech:

* [Návrhář zobrazení](view-designer.md): poskytuje přehled návrháře zobrazení a postupů pro vytváření a úpravy vlastních zobrazení.
* [Odkaz na dlaždici](view-designer-tiles.md): poskytuje odkaz na nastavení pro každou dostupnou dlaždici ve vlastních zobrazeních.


Dostupné typy dlaždic návrháře zobrazení jsou popsány v následující tabulce:

| Typ zobrazení | Description |
|:--- |:--- |
| [Seznam dotazů](#list-of-queries-part) |Zobrazí seznam dotazů protokolu. Můžete vybrat každý dotaz a zobrazit jeho výsledky. |
| [Číslo a seznam](#number-and-list-part) |V záhlaví se zobrazí jedno číslo, které zobrazuje počet záznamů z dotazu protokolu. V seznamu se zobrazí prvních deset výsledků dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změny v čase. |
| [Dvě čísla a seznam](#two-numbers-and-list-part) |V záhlaví se zobrazí dvě čísla, která zobrazují počty záznamů ze samostatných dotazů protokolu. V seznamu se zobrazí prvních deset výsledků dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změny v čase. |
| [Prstenec a seznam](#donut-and-list-part) |Záhlaví zobrazí jedno číslo, které shrnuje sloupec value v dotazu protokolu. Prstenec graficky zobrazuje výsledky prvních tří záznamů. |
| [Dvě časové osy a seznam](#two-timelines-and-list-part) |Záhlaví zobrazuje výsledky dvou dotazů protokolu v průběhu času jako sloupcové grafy pomocí popisku, který zobrazuje jedno číslo, které shrnuje sloupec hodnoty v dotazu protokolu. V seznamu se zobrazí prvních deset výsledků dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změny v čase. |
| [Informace](#information-part) |V záhlaví se zobrazí statický text a volitelný odkaz. V seznamu se zobrazí jedna nebo více položek se statickým nadpisem a textem. |
| [Spojnicový graf, popisek a seznam](#line-chart-callout-and-list-part) |Záhlaví zobrazí spojnicový graf s několika řadami z dotazu protokolu v průběhu času a popiskem s sumarizovanou hodnotou. V seznamu se zobrazí prvních deset výsledků dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změny v čase. |
| [Spojnicový graf a seznam](#line-chart-and-list-part) |Záhlaví zobrazí spojnicový graf s více řadami z dotazu protokolu v průběhu času. V seznamu se zobrazí prvních deset výsledků dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změny v čase. |
| [Zásobník částí spojnicových grafů](#stack-of-line-charts-part) |Zobrazí tři samostatné spojnicové grafy s více řadami z dotazu protokolu v průběhu času. |

V dalších oddílech jsou podrobně popsány typy dlaždic a jejich vlastnosti.

> [!NOTE]
> Části v zobrazeních jsou založené na [dotazech protokolu](../logs/log-query-overview.md) v pracovním prostoru Log Analytics. V současné době nepodporují [dotazy na více zdrojů](../logs/cross-workspace-query.md) k načtení dat z Application Insights.

## <a name="list-of-queries-part"></a>Seznam částí dotazů
Část Seznam dotazů zobrazuje seznam dotazů protokolu. Můžete vybrat každý dotaz a zobrazit jeho výsledky. Zobrazení obsahuje jeden dotaz ve výchozím nastavení a můžete vybrat **+ dotaz** pro přidání dalších dotazů.

![Snímek obrazovky se seznamem částí vizualizace dotazů v Návrháři zobrazení Azure Monitor.](media/view-designer-parts/view-list-queries.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Nadpis |Text zobrazený v horní části zobrazení |
| Nová skupina |Vyberte tento odkaz k vytvoření nové skupiny v zobrazení, počínaje aktuálním zobrazením. |
| Předem vybrané filtry |Seznam vlastností, které se mají zahrnout do levého podokna filtru při výběru dotazu. |
| Režim vykreslování |Počáteční zobrazení, které se zobrazí při výběru dotazu. Po otevření dotazu můžete vybrat všechna dostupná zobrazení. |
| **Dotazy** | |
| Vyhledávací dotaz |Dotaz, který má být spuštěn. |
| Popisný název | Popisný název, který se zobrazí. |

## <a name="number-and-list-part"></a>Číslo a část seznamu
V záhlaví se zobrazí jedno číslo, které zobrazuje počet záznamů z dotazu protokolu. V seznamu se zobrazí prvních deset výsledků dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změny v čase.

![Snímek obrazovky s částmi vizualizace čísel a seznamů v Návrháři zobrazení Azure Monitor](media/view-designer-parts/view-number-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text zobrazený v horní části zobrazení |
| Nová skupina |Vyberte tento odkaz k vytvoření nové skupiny v zobrazení, počínaje aktuálním zobrazením. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v hlavičce. |
| Použít ikonu |Kliknutím na tento odkaz zobrazíte ikonu. |
| **Název** | |
| Legenda |Text zobrazený v horní části záhlaví |
| Dotaz |Dotaz, který se má spustit pro záhlaví Zobrazí se počet záznamů vrácených dotazem. |
| Navigace kliknutím | Akce provedená po kliknutí na záhlaví  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Seznam** | |
| Dotaz |Dotaz, který se má spustit pro seznam Zobrazí se první dvě vlastnosti pro prvních deset záznamů ve výsledcích. První vlastností je textová hodnota a druhá vlastnost je číselná hodnota. Automaticky se vytvoří pruhy, které jsou založené na relativní hodnotě číselného sloupce.<br><br>Pomocí `Sort` příkazu v dotazu seřaďte záznamy v seznamu. Pokud chcete spustit dotaz a vrátit všechny záznamy, můžete vybrat **Zobrazit vše**. |
| Skrýt graf |Kliknutím na tento odkaz zakážete graf napravo od číselného sloupce. |
| Povolit minigrafy |Vyberte tento odkaz k zobrazení minigrafu místo vodorovného pruhu. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Barva |Barva pruhů nebo minigrafů. |
| Oddělovač názvu a hodnoty |Oddělovač s jedním znakem, který se má použít k analýze vlastnosti text na více hodnot. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Navigace kliknutím | Akce provedená po kliknutí na položku v seznamu.  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Seznam** |**> názvy sloupců** |
| Name |Text zobrazený v horní části prvního sloupce |
| Hodnota |Text zobrazený v horní části druhého sloupce |
| **Seznam** |**Prahové hodnoty>** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace najdete v tématu [Společná nastavení](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dvě čísla a část seznamu
Záhlaví obsahuje dvě čísla, která zobrazují počet záznamů ze samostatných dotazů protokolu. V seznamu se zobrazí prvních deset výsledků dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změny v čase.

![Dvě čísla & zobrazení seznamu](media/view-designer-parts/view-two-numbers-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text zobrazený v horní části zobrazení |
| Nová skupina |Vyberte tento odkaz k vytvoření nové skupiny v zobrazení, počínaje aktuálním zobrazením. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v hlavičce. |
| Použít ikonu |Kliknutím na tento odkaz zobrazíte ikonu. |
| **Navigace v nadpisech** | |
| Navigace kliknutím | Akce provedená po kliknutí na záhlaví  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Název** | |
| Legenda |Text zobrazený v horní části záhlaví |
| Dotaz |Dotaz, který se má spustit pro záhlaví Zobrazí se počet záznamů vrácených dotazem. |
| **Seznam** | |
| Dotaz |Dotaz, který se má spustit pro seznam Zobrazí se první dvě vlastnosti pro prvních deset záznamů ve výsledcích. První vlastností je textová hodnota a druhá vlastnost je číselná hodnota. Pruhy se automaticky vytvoří na základě relativní hodnoty číselného sloupce.<br><br>Pomocí `Sort` příkazu v dotazu seřaďte záznamy v seznamu. Pokud chcete spustit dotaz a vrátit všechny záznamy, můžete vybrat **Zobrazit vše**. |
| Skrýt graf |Kliknutím na tento odkaz zakážete graf napravo od číselného sloupce. |
| Povolit minigrafy |Vyberte tento odkaz k zobrazení minigrafu místo vodorovného pruhu. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Barva |Barva pruhů nebo minigrafů. |
| Operace |Operace, která má být provedena pro minigraf. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Oddělovač názvu a hodnoty |Oddělovač s jedním znakem, který se má použít k analýze vlastnosti text na více hodnot. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Navigace kliknutím | Akce provedená po kliknutí na položku v seznamu.  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Seznam** |**> názvy sloupců** |
| Name |Text zobrazený v horní části prvního sloupce |
| Hodnota |Text zobrazený v horní části druhého sloupce |
| **Seznam** |**Prahové hodnoty>** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace najdete v tématu [Společná nastavení](#thresholds). |

## <a name="donut-and-list-part"></a>Prstenec a část seznamu
Záhlaví zobrazí jedno číslo, které shrnuje sloupec value v dotazu protokolu. Prstenec graficky zobrazuje výsledky prvních tří záznamů.

![Prstenec a zobrazení seznamu](media/view-designer-parts/view-donut-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text zobrazený v horní části dlaždice |
| Nová skupina |Vyberte tento odkaz k vytvoření nové skupiny v zobrazení, počínaje aktuálním zobrazením. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v hlavičce. |
| Použít ikonu |Kliknutím na tento odkaz zobrazíte ikonu. |
| **Hlavička** | |
| Nadpis |Text zobrazený v horní části záhlaví |
| Podnadpis |Text zobrazený pod nadpisem v horní části záhlaví |
| **Prstencový graf** | |
| Dotaz |Dotaz, který se má spustit pro prstenec První vlastností je textová hodnota a druhá vlastnost je číselná hodnota. |
| Navigace kliknutím | Akce provedená po kliknutí na záhlaví  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Prstencový graf** |**Centrum>** |
| Text |Text zobrazený pod hodnotou uvnitř prstence prstence |
| Operace |Operace, která se má provést na vlastnosti Value pro Shrnutí jako jedné hodnoty.<ul><li>Sum: přidá hodnoty všech záznamů.</li><li>Procento: poměr záznamů vrácených hodnotami v **výsledných hodnotách, které se používají v operaci vycentrovat** , na celkový počet záznamů v dotazu.</li></ul> |
| Hodnoty výsledku použité v operaci na středu |Volitelně můžete vybrat znaménko plus (+) a přidat jednu nebo více hodnot. Výsledky dotazu jsou omezeny na záznamy s hodnotami vlastností, které zadáte. Pokud nejsou přidány žádné hodnoty, jsou do dotazu zahrnuty všechny záznamy. |
| **Další možnosti** |**> barvy** |
| Barva 1<br>Barva 2<br>Barva 3 |Vyberte barvu pro každou hodnotu, která se zobrazí v prstenci. |
| **Další možnosti** |**> rozšířené mapování barev** |
| Hodnota pole |Zadejte název pole, chcete-li jej zobrazit jako jinou barvu, pokud je součástí prstence. |
| Barva |Vyberte barvu pro jedinečné pole. |
| **Seznam** | |
| Dotaz |Dotaz, který se má spustit pro seznam Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt graf |Kliknutím na tento odkaz zakážete graf napravo od číselného sloupce. |
| Povolit minigrafy |Vyberte tento odkaz k zobrazení minigrafu místo vodorovného pruhu. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Barva |Barva pruhů nebo minigrafů. |
| Operace |Operace, která má být provedena pro minigraf. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Oddělovač názvu a hodnoty |Oddělovač s jedním znakem, který se má použít k analýze vlastnosti text na více hodnot. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Navigace kliknutím | Akce provedená po kliknutí na položku v seznamu.  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Seznam** |**> názvy sloupců** |
| Name |Text zobrazený v horní části prvního sloupce |
| Hodnota |Text zobrazený v horní části druhého sloupce |
| **Seznam** |**Prahové hodnoty>** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace najdete v tématu [Společná nastavení](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Dvě časové osy a část seznamu
Záhlaví zobrazuje výsledky dvou dotazů protokolu v průběhu času jako sloupcové grafy pomocí popisku, který zobrazuje jedno číslo, které shrnuje sloupec hodnoty v dotazu protokolu. V seznamu se zobrazí prvních deset výsledků dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změny v čase.

![Dvě časové osy a zobrazení seznamu](media/view-designer-parts/view-two-timelines-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text zobrazený v horní části dlaždice |
| Nová skupina |Vyberte tento odkaz k vytvoření nové skupiny v zobrazení, počínaje aktuálním zobrazením. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v hlavičce. |
| Použít ikonu |Kliknutím na tento odkaz zobrazíte ikonu. |
| **Navigace v nadpisech** | |
| Navigace kliknutím | Akce provedená po kliknutí na záhlaví  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **První graf – <br> druhý graf** | |
| Legenda |Text zobrazený pod popiskem prvního seriálu |
| Barva |Barva, která se má použít pro sloupce v řadě |
| Dotaz |Dotaz, který se má spustit pro první řadu Počet záznamů v průběhu každého časového intervalu je reprezentován sloupci grafu. |
| Operace |Operace, která má být provedena s vlastností value pro Shrnutí jako jednu hodnotu pro popisek.<ul><li>Sum: součet hodnot ze všech záznamů.</li><li>Average: průměr hodnot ze všech záznamů.</li><li>Last Sample: hodnota z posledního intervalu, který je zahrnutý v grafu.</li><li>První ukázka: hodnota z prvního intervalu, který je zahrnutý v grafu.</li><li>Count (počet): počet všech záznamů vrácených dotazem.</li></ul> |
| **Seznam** | |
| Dotaz |Dotaz, který se má spustit pro seznam Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt graf |Kliknutím na tento odkaz zakážete graf napravo od číselného sloupce. |
| Povolit minigrafy |Vyberte tento odkaz k zobrazení minigrafu místo vodorovného pruhu. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Barva |Barva pruhů nebo minigrafů. |
| Operace |Operace, která má být provedena pro minigraf. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Navigace kliknutím | Akce provedená po kliknutí na položku v seznamu.  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Seznam** |**> názvy sloupců** |
| Name |Text zobrazený v horní části prvního sloupce |
| Hodnota |Text zobrazený v horní části druhého sloupce |
| **Seznam** |**Prahové hodnoty>** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace najdete v tématu [Společná nastavení](#thresholds). |

## <a name="information-part"></a>Část s informacemi
V záhlaví se zobrazí statický text a volitelný odkaz. V seznamu se zobrazí jedna nebo více položek se statickým nadpisem a textem.

![Zobrazení informací](media/view-designer-parts/view-information.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text zobrazený v horní části dlaždice |
| Nová skupina |Vyberte tento odkaz k vytvoření nové skupiny v zobrazení, počínaje aktuálním zobrazením. |
| Barva |Barva pozadí záhlaví |
| **Hlavička** | |
| Image |Soubor obrázku, který je zobrazen v záhlaví. |
| Popisek |Text zobrazený v záhlaví |
| **Hlavička** |**Odkaz na>** |
| Popisek |Text odkazu |
| URL |Adresa URL odkazu |
| **Informační položky** | |
| Nadpis |Text zobrazený pro název každé položky |
| Content |Text zobrazený pro každou položku |

## <a name="line-chart-callout-and-list-part"></a>Spojnicový graf, popisek a část seznamu
V záhlaví se zobrazuje spojnicový graf s více řadami z dotazu protokolu v průběhu času a popisek s souhrnnou hodnotou. V seznamu se zobrazí prvních deset výsledků dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změny v čase.

![Spojnicový graf, popisek a zobrazení seznamu](media/view-designer-parts/view-line-chart-callout-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text zobrazený v horní části dlaždice |
| Nová skupina |Vyberte tento odkaz k vytvoření nové skupiny v zobrazení, počínaje aktuálním zobrazením. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v hlavičce. |
| Použít ikonu |Kliknutím na tento odkaz zobrazíte ikonu. |
| **Hlavička** | |
| Nadpis |Text zobrazený v horní části záhlaví |
| Podnadpis |Text zobrazený pod nadpisem v horní části záhlaví |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který má být spuštěn pro spojnicový graf. První vlastností je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *Measure* k sumarizaci výsledků. Pokud dotaz používá klíčové slovo *interval* , bude osa x grafu používat tento časový interval. Pokud dotaz nezahrnuje klíčové slovo *interval* , osa x používá hodinové intervaly. |
| Navigace kliknutím | Akce provedená po kliknutí na záhlaví  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Spojnicový graf** |**Popisek>** |
| Nadpis popisku |Text zobrazený nad hodnotou popisku |
| Název řady |Hodnota vlastnosti pro řady, která se má použít pro hodnotu popisku Pokud není zadána žádná řada, budou použity všechny záznamy z dotazu. |
| Operace |Operace, která má být provedena s vlastností value pro Shrnutí jako jednu hodnotu pro popisek.<ul><li>Average: průměr hodnot ze všech záznamů.</li><li>Count (počet): počet všech záznamů vrácených dotazem.</li><li>Last Sample: hodnota z posledního intervalu, který je zahrnutý v grafu.</li><li>Max: maximální hodnota z intervalů, které jsou zahrnuty v grafu.</li><li>Min: minimální hodnota z intervalů, které jsou zahrnuty v grafu.</li><li>Sum: součet hodnot ze všech záznamů.</li></ul> |
| **Spojnicový graf** |**> osa Y** |
| Použití logaritmické stupnice |Vyberte tento odkaz pro použití logaritmické stupnice pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty, které má dotaz vrátit. Tyto informace slouží k zobrazení popisků grafů, které určují typy hodnot a volitelně k převodu hodnot. Typ *jednotky* určuje kategorii jednotky a definuje dostupné hodnoty *aktuálního typu jednotky* . Pokud vyberete hodnotu v poli *převést na*, číselné hodnoty jsou převedeny z *aktuální jednotky* typu na typ *převést na* typ. |
| Vlastní popisek |Text zobrazený pro osu y vedle popisku pro typ *jednotky* Pokud není zadán žádný popisek, zobrazí se pouze typ *jednotky* . |
| **Seznam** | |
| Dotaz |Dotaz, který se má spustit pro seznam Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt graf |Kliknutím na tento odkaz zakážete graf napravo od číselného sloupce. |
| Povolit minigrafy |Vyberte tento odkaz k zobrazení minigrafu místo vodorovného pruhu. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Barva |Barva pruhů nebo minigrafů. |
| Operace |Operace, která má být provedena pro minigraf. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Oddělovač názvu a hodnoty |Oddělovač s jedním znakem, který se má použít k analýze vlastnosti text na více hodnot. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Navigace kliknutím | Akce provedená po kliknutí na položku v seznamu.  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Seznam** |**> názvy sloupců** |
| Name |Text zobrazený v horní části prvního sloupce |
| Hodnota |Text zobrazený v horní části druhého sloupce |
| **Seznam** |**Prahové hodnoty>** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace najdete v tématu [Společná nastavení](#thresholds). |

## <a name="line-chart-and-list-part"></a>Spojnicový graf a část seznamu
Záhlaví zobrazí spojnicový graf s více řadami z dotazu protokolu v průběhu času. V seznamu se zobrazí prvních deset výsledků dotazu s grafem, který označuje relativní hodnotu číselného sloupce nebo jeho změny v čase.

![Spojnicový graf a zobrazení seznamu](media/view-designer-parts/view-line-chart-callout-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text zobrazený v horní části dlaždice |
| Nová skupina |Vyberte tento odkaz k vytvoření nové skupiny v zobrazení, počínaje aktuálním zobrazením. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v hlavičce. |
| Použít ikonu |Kliknutím na tento odkaz zobrazíte ikonu. |
| **Hlavička** | |
| Nadpis |Text zobrazený v horní části záhlaví |
| Podnadpis |Text zobrazený pod nadpisem v horní části záhlaví |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který má být spuštěn pro spojnicový graf. První vlastností je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *Measure* k sumarizaci výsledků. Pokud dotaz používá klíčové slovo *interval* , bude osa x grafu používat tento časový interval. Pokud dotaz nezahrnuje klíčové slovo *interval* , osa x používá hodinové intervaly. |
| Navigace kliknutím | Akce provedená po kliknutí na záhlaví  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Spojnicový graf** |**> osa Y** |
| Použití logaritmické stupnice |Vyberte tento odkaz pro použití logaritmické stupnice pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty, které má dotaz vrátit. Tyto informace slouží k zobrazení popisků grafů, které určují typy hodnot a volitelně k převodu hodnot. Typ *jednotky* určuje kategorii jednotky a definuje dostupné hodnoty *aktuálního typu jednotky* . Pokud vyberete hodnotu v poli *převést na*, číselné hodnoty jsou převedeny z *aktuální jednotky* typu na typ *převést na* typ. |
| Vlastní popisek |Text zobrazený pro osu y vedle popisku pro typ *jednotky* Pokud není zadán žádný popisek, zobrazí se pouze typ *jednotky* . |
| **Seznam** | |
| Dotaz |Dotaz, který se má spustit pro seznam Zobrazí se počet záznamů vrácených dotazem. |
| Skrýt graf |Kliknutím na tento odkaz zakážete graf napravo od číselného sloupce. |
| Povolit minigrafy |Vyberte tento odkaz k zobrazení minigrafu místo vodorovného pruhu. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Barva |Barva pruhů nebo minigrafů. |
| Operace |Operace, která má být provedena pro minigraf. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Oddělovač názvu a hodnoty |Oddělovač s jedním znakem, který se má použít k analýze vlastnosti text na více hodnot. Další informace najdete v tématu [Společná nastavení](#sparklines). |
| Navigace kliknutím | Akce provedená po kliknutí na položku v seznamu.  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Seznam** |**> názvy sloupců** |
| Name |Text zobrazený v horní části prvního sloupce |
| Hodnota |Text zobrazený v horní části druhého sloupce |
| **Seznam** |**Prahové hodnoty>** |
| Povolit prahové hodnoty |Výběrem tohoto odkazu povolíte prahové hodnoty. Další informace najdete v tématu [Společná nastavení](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Zásobník částí spojnicových grafů
Zásobník spojnicového grafu zobrazuje tři samostatné spojnicové grafy s více řadami z dotazu protokolu v průběhu času, jak je znázorněno zde:

![Zásobník spojnicových grafů](media/view-designer-parts/view-stack-line-charts.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text zobrazený v horní části dlaždice |
| Nová skupina |Vyberte tento odkaz k vytvoření nové skupiny v zobrazení, počínaje aktuálním zobrazením. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledku v hlavičce. |
| **Graf 1 graf 2 – graf <br> <br> 3** |**> záhlaví** |
| Nadpis |Text zobrazený v horní části grafu |
| Podnadpis |Text zobrazený pod nadpisem v horní části grafu |
| **Graf 1 graf 2 – graf <br> <br> 3** |**Spojnicový graf** |
| Dotaz |Dotaz, který má být spuštěn pro spojnicový graf. První vlastností je textová hodnota a druhá vlastnost je číselná hodnota. Tento dotaz obvykle používá klíčové slovo *Measure* k sumarizaci výsledků. Pokud dotaz používá klíčové slovo *interval* , bude osa x grafu používat tento časový interval. Pokud dotaz nezahrnuje klíčové slovo *interval* , osa x používá hodinové intervaly. |
| Navigace kliknutím | Akce provedená po kliknutí na záhlaví  Další informace najdete v tématu [Společná nastavení](#click-through-navigation). |
| **Graf** |**> osa Y** |
| Použití logaritmické stupnice |Vyberte tento odkaz pro použití logaritmické stupnice pro osu y. |
| Jednotky |Zadejte jednotky pro hodnoty, které má dotaz vrátit. Tyto informace slouží k zobrazení popisků grafů, které určují typy hodnot a volitelně k převodu hodnot. Typ *jednotky* určuje kategorii jednotky a definuje dostupné hodnoty *aktuálního typu jednotky* . Pokud vyberete hodnotu v poli *převést na*, číselné hodnoty jsou převedeny z *aktuální jednotky* typu na typ *převést na* typ. |
| Vlastní popisek |Text zobrazený pro osu y vedle popisku pro typ *jednotky* Pokud není zadán žádný popisek, zobrazí se pouze typ *jednotky* . |

## <a name="common-settings"></a>Obecná nastavení
V následujících částech jsou popsána nastavení, která jsou společná pro několik částí vizualizace.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Oddělovač názvu a hodnoty
Oddělovač názvu a hodnoty je oddělovač s jedním znakem, který slouží k analýze vlastnosti text z dotazu seznamu na více hodnot. Pokud zadáte oddělovač, můžete zadat názvy pro každé pole oddělené stejným oddělovačem v poli **název** .

Představte si například vlastnost *umístění* , která zahrnuje hodnoty, jako je například *Redmond-Building 41* a *Bellevue-budova 12*. Pro název můžete zadat spojovník (-) pro oddělovač názvů a hodnot a *sestavení měst* . Tento přístup analyzuje každou hodnotu do dvou vlastností označovaných jako *město* a *budova*.

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Navigace kliknutím
Navigace kliknutím definuje akci, která se provede při kliknutí na záhlaví nebo položku seznamu v zobrazení.  Otevře se dotaz ve [Log Analytics](../logs/log-query-overview.md) nebo spustí jiné zobrazení.

Následující tabulka popisuje nastavení pro navigaci kliknutím.

| Nastavení           | Popis |
|:--|:--|
| Prohledávání protokolu (automaticky) | Dotaz protokolu, který se má spustit při výběru položky záhlaví  Jedná se o stejný dotaz protokolu, na kterém je položka založena.
| Prohledávání protokolů        | Dotaz protokolu, který se má spustit při výběru položky v seznamu  Zadejte dotaz do pole **navigační dotaz** .   Pomocí položky *{Selected}* můžete zahrnout syntaxi pro položku, kterou uživatel vybral.  Pokud má například dotaz sloupec s názvem *Computer* a navigační dotaz je *{Selected Item}*, při výběru počítače se spustí dotaz, jako je například *Computer = "mycomputer"* . Pokud je navigační dotaz *typu typ = událost {vybraná položka}*, spustí se dotaz *Type = Event Computer = "mycomputer"* . |
| Zobrazení              | Zobrazení, které se otevře, když vyberete položku záhlaví nebo položku v seznamu.  V poli **název zobrazení** vyberte název zobrazení ve vašem pracovním prostoru. |



### <a name="sparklines"></a><a name="sparklines"></a>Minigrafy
Minigraf je malý spojnicový graf, který znázorňuje hodnotu položky seznamu v průběhu času. Pro části vizualizace se seznamem můžete vybrat, zda se má zobrazit vodorovný pruh, který označuje relativní hodnotu číselného sloupce nebo minigrafu, která označuje jeho hodnotu v čase.

Následující tabulka popisuje nastavení pro minigrafy:

| Nastavení | Popis |
|:--- |:--- |
| Povolit minigrafy |Vyberte tento odkaz k zobrazení minigrafu místo vodorovného pruhu. |
| Operace |Pokud jsou povoleny minigrafy, jedná se o operaci, která má být provedena na každé vlastnosti v seznamu pro výpočet hodnot pro minigraf.<ul><li>Poslední Ukázka: poslední hodnota pro řady v časovém intervalu.</li><li>Max: maximální hodnota pro řady v časovém intervalu.</li><li>Min: minimální hodnota pro řady v časovém intervalu.</li><li>Sum: součet hodnot řady v časovém intervalu.</li><li>Summary: používá stejný `measure` příkaz jako dotaz v hlavičce.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Prahové hodnoty
Pomocí prahových hodnot můžete zobrazit barevné ikony vedle každé položky v seznamu. Prahové hodnoty poskytují rychlý vizuální indikátor pro položky, které překračují určitou hodnotu nebo spadají do konkrétního rozsahu. Můžete například zobrazit zelenou ikonu pro položky s přijatelnou hodnotou, žlutou, pokud je hodnota v rozsahu, který označuje upozornění, a červené, pokud překročí chybovou hodnotu.

Pokud povolíte prahové hodnoty pro součást, je nutné zadat jeden nebo více prahových hodnot. Pokud je hodnota položky větší než prahová hodnota a nižší než další prahová hodnota, použije se barva této hodnoty. Pokud je položka větší než nejvyšší prahová hodnota, použije se další barva. 

Každá prahová sada má jednu prahovou hodnotu s hodnotou **výchozí**. Toto je barva, která je nastavena, pokud nejsou překročeny žádné jiné hodnoty. Prahové hodnoty můžete přidat nebo odebrat tak, že vyberete tlačítko **Přidat** (+) nebo **Odstranit** (x).

Následující tabulka popisuje nastavení prahových hodnot:

| Nastavení | Popis |
|:--- |:--- |
| Povolit prahové hodnoty |Kliknutím na tento odkaz zobrazíte ikonu barvy vlevo od každé hodnoty. Ikona určuje stav hodnoty relativní vzhledem k určeným prahovým hodnotám. |
| Name |Název prahové hodnoty. |
| Prahová hodnota |Hodnota prahové hodnoty. Barva stavu každé položky seznamu je nastavena na barvu nejvyšší prahové hodnoty, která je překročena hodnotou položky. Pokud nejsou překročeny žádné prahové hodnoty, použije se výchozí barva. |
| Barva |Barva, která označuje prahovou hodnotu. |

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolu](../logs/log-query-overview.md) pro podporu dotazů v částech vizualizace.