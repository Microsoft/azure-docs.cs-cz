---
title: Referenční příručka k části návrháře zobrazení v Azure Log Analytics | Dokumentace Microsoftu
description: Pomocí návrháře zobrazení v Log Analytics můžete vytvořit vlastní zobrazení na webu Azure Portal, který zobrazí různé vizualizace dat ve vašem pracovním prostoru Log Analytics. Tento článek je referenční příručka k nastavení části vizualizace, které jsou k dispozici do vlastních zobrazení.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: cdf3fda4228eadc2921c8fbc1bc72702461d04e4
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642813"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-log-analytics"></a>Referenční příručka k části vizualizace návrháře zobrazení v Log Analytics
Pomocí návrháře zobrazení v Azure Log Analytics můžete vytvořit vlastní zobrazení na webu Azure Portal, který k dispozici různé vizualizace dat z pracovního prostoru Log Analytics. Tento článek je referenční příručka k nastavení části vizualizace, které jsou k dispozici do vlastních zobrazení.

Další informace o návrháři zobrazení najdete v tématu:

* [Zobrazit návrháře](view-designer.md): poskytuje přehled o Návrhář zobrazení a postupy pro vytváření a úpravu vlastní zobrazení.
* [Dlaždice odkaz](view-designer-tiles.md): poskytuje odkaz na nastavení pro každou dlaždici k dispozici do vlastních zobrazení.


Dostupné typy dlaždici Návrhář zobrazení jsou popsány v následující tabulce:

| Typ zobrazení | Popis |
|:--- |:--- |
| [Seznam dotazů](#list-of-queries-part) |Zobrazí seznam dotazy prohledávání protokolu. Můžete vybrat každý dotaz k zobrazení jeho výsledky. |
| [Číslo a seznam](#number-and-list-part) |Záhlaví zobrazí jedno číslo, který zobrazuje počet záznamů z protokolu vyhledávacího dotazu. V seznamu zobrazí nejlepších deset výsledků z dotazu, s grafem, který určuje relativní hodnotu číselný sloupec nebo jeho změn v průběhu času. |
| [Dvě čísla a seznam](#two-numbers-and-list-part) |Záhlaví zobrazí dvě čísla, které zobrazují počet záznamů v samostatném protokolu vyhledávací dotazy. V seznamu zobrazí nejlepších deset výsledků z dotazu, s grafem, který určuje relativní hodnotu číselný sloupec nebo jeho změn v průběhu času. |
| [Prstenec a seznam](#donut-and-list-part) |Záhlaví zobrazí jedno číslo, které shrnuje hodnotu sloupce v dotazu protokolu. Prstencový graficky zobrazuje výsledky z prvních tří záznamů. |
| [Dvě časové osy a seznam](#two-timelines-and-list-part) |Záhlaví zobrazí výsledků dvou dotazů na protokoly časem jako sloupcové grafy s popiskem, která zobrazuje jedno číslo, které shrnuje hodnotu sloupce v dotazu protokolu. V seznamu zobrazí nejlepších deset výsledků z dotazu, s grafem, který určuje relativní hodnotu číselný sloupec nebo jeho změn v průběhu času. |
| [Informace o](#information-part) |Záhlaví obsahuje statický text a nepovinný odkaz. V seznamu zobrazí jednu nebo více položek s statické nadpis a text. |
| [Spojnicový graf, popisek a seznam](#line-chart-callout-and-list-part) |Záhlaví zobrazuje spojnicový graf s více řadami z dotazu protokolu průběhu času a popisek s hodnoty souhrnu. V seznamu zobrazí nejlepších deset výsledků z dotazu, s grafem, který určuje relativní hodnotu číselný sloupec nebo jeho změn v průběhu času. |
| [Spojnicový graf a seznam](#line-chart-and-list-part) |Záhlaví se zobrazuje spojnicový graf s několika řadami z dotazu protokolu v čase. V seznamu zobrazí nejlepších deset výsledků z dotazu, s grafem, který určuje relativní hodnotu číselný sloupec nebo jeho změn v průběhu času. |
| [Zásobník část řádku grafy](#stack-of-line-charts-part) |Zobrazí tři samostatné spojnicových grafů s více řadami z dotazu protokolu v čase. |

Následující části popisují typy dlaždic a jejich vlastnosti podrobně.

## <a name="list-of-queries-part"></a>Seznam dotazů část
Seznam dotazů části zobrazí seznam dotazy prohledávání protokolu. Můžete vybrat každý dotaz k zobrazení jeho výsledky. Ve výchozím nastavení obsahuje zobrazení pomocí jediného dotazu a můžete vybrat **+ dotaz** přidáte další dotazy.

![Seznam dotazů zobrazení](media/view-designer-parts/view-list-queries.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Titul |Text, který se zobrazí v horní části stránky zobrazení. |
| Nová skupina |Vyberte tento odkaz můžete vytvořit novou skupinu v zobrazení, od aktuálního zobrazení. |
| Předem vybrané filtry |Čárkami oddělený seznam vlastnosti, které chcete zahrnout do filtru v levém podokně vyberete dotaz. |
| Režim vykreslování |Počáteční zobrazení, které se zobrazí, když je vybrána dotazu. Můžete vybrat libovolné dostupné zobrazení po otevření dotazu. |
| **Dotazy** | |
| Vyhledávací dotaz |Spustit dotaz. |
| Popisný název | Popisný název, který se zobrazí. |

## <a name="number-and-list-part"></a>Část číslo a seznam
Záhlaví zobrazí jedno číslo, který zobrazuje počet záznamů z protokolu vyhledávacího dotazu. V seznamu zobrazí nejlepších deset výsledků z dotazu, s grafem, který určuje relativní hodnotu číselný sloupec nebo jeho změn v průběhu času.

![Seznam dotazů zobrazení](media/view-designer-parts/view-number-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části stránky zobrazení. |
| Nová skupina |Vyberte tento odkaz můžete vytvořit novou skupinu v zobrazení, od aktuálního zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledek v záhlaví. |
| Použít ikonu |Vyberte tento odkaz k zobrazení ikony. |
| **Název** | |
| Legenda |Text, který se zobrazí v horní části záhlaví. |
| Dotaz |Dotaz, který se má spustit pro hlavičku. Zobrazí se počet záznamů, které jsou vrácené dotazem. |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na záhlaví.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Seznam** | |
| Dotaz |Dotaz pro spuštění v seznamu. První dvě vlastnosti pro prvních deseti záznamy ve výsledcích se zobrazí. Textová hodnota, která je první vlastnost a druhá vlastnost je číselná hodnota. Panely se automaticky vytvoří, které jsou založeny na relativní hodnota číselné sloupce.<br><br>Použití `Sort` v dotazu seřadit záznamy v seznamu. Chcete-li spustit dotaz a vrátí všechny záznamy, můžete vybrat **zobrazit všechny**. |
| Skrýt graf |Vyberte tento odkaz pro zakázání grafu v pravém rohu číselný sloupec. |
| Povolit minigrafy |Vyberte tento odkaz zobrazíte minigrafu místo vodorovný pruh. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Barva |Barvy pruhů nebo minigrafy. |
| Oddělovač názvu a hodnoty |Jeden znak oddělovač, který má použít k analýze vlastnost text do více hodnot. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na položku v seznamu.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Seznam** |**> Názvy sloupců** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **Seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz povolit prahové hodnoty. Další informace najdete v tématu [obecná nastavení](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dvě čísla a další část seznamu
Záhlaví má dvě čísla, které zobrazují počet záznamů v samostatném protokolu vyhledávací dotazy. V seznamu zobrazí nejlepších deset výsledků z dotazu, s grafem, který určuje relativní hodnotu číselný sloupec nebo jeho změn v průběhu času.

![Dvě čísla a zobrazení seznamu](media/view-designer-parts/view-two-numbers-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části stránky zobrazení. |
| Nová skupina |Vyberte tento odkaz můžete vytvořit novou skupinu v zobrazení, od aktuálního zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledek v záhlaví. |
| Použít ikonu |Vyberte tento odkaz k zobrazení ikony. |
| **Název navigační** | |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na záhlaví.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Název** | |
| Legenda |Text, který se zobrazí v horní části záhlaví. |
| Dotaz |Dotaz, který se má spustit pro hlavičku. Zobrazí se počet záznamů, které jsou vrácené dotazem. |
| **Seznam** | |
| Dotaz |Dotaz pro spuštění v seznamu. První dvě vlastnosti pro prvních deseti záznamy ve výsledcích se zobrazí. Textová hodnota, která je první vlastnost a druhá vlastnost je číselná hodnota. Panely se automaticky vytvoří na základě relativní hodnoty číselné sloupce.<br><br>Použití `Sort` v dotazu seřadit záznamy v seznamu. Chcete-li spustit dotaz a vrátí všechny záznamy, můžete vybrat **zobrazit všechny**. |
| Skrýt graf |Vyberte tento odkaz pro zakázání grafu v pravém rohu číselný sloupec. |
| Povolit minigrafy |Vyberte tento odkaz zobrazíte minigrafu místo vodorovný pruh. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Barva |Barvy pruhů nebo minigrafy. |
| Operace |Operace, který se má provést pro minigraf. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Oddělovač názvu a hodnoty |Jeden znak oddělovač, který má použít k analýze vlastnost text do více hodnot. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na položku v seznamu.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Seznam** |**> Názvy sloupců** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **Seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz povolit prahové hodnoty. Další informace najdete v tématu [obecná nastavení](#thresholds). |

## <a name="donut-and-list-part"></a>Prstenec a seznam součástí
Záhlaví zobrazí jedno číslo, které shrnuje hodnotu sloupce v dotazu protokolu. Prstencový graficky zobrazuje výsledky z prvních tří záznamů.

![Prstenec a seznam zobrazení](media/view-designer-parts/view-donut-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části dlaždice. |
| Nová skupina |Vyberte tento odkaz můžete vytvořit novou skupinu v zobrazení, od aktuálního zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledek v záhlaví. |
| Použít ikonu |Vyberte tento odkaz k zobrazení ikony. |
| **Záhlaví** | |
| Titul |Text, který se zobrazí v horní části záhlaví. |
| Podtitul |Text, který se zobrazí pod nadpisem v horní části záhlaví. |
| **Prstencový** | |
| Dotaz |Dotaz pro spuštění prstencový. Textová hodnota, která je první vlastnost a druhá vlastnost je číselná hodnota. |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na záhlaví.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Prstencový** |**> System center** |
| Text |Text, který se zobrazí v části v prstencovém hodnotu. |
| Operace |Operace, který se má provést na hodnotu vlastnosti slouží ke shrnutí jako jedinou hodnotu.<ul><li>Součet: Přidá hodnoty všechny záznamy.</li><li>Procento: Poměr záznamů vrácených hodnot v **způsobit hodnot použitých v operaci center** celkový počet záznamů v dotazu.</li></ul> |
| Hodnoty výsledku, které se zobrazí ve středu |Volitelně vyberte znaménko plus (+) přidejte jednu nebo více hodnot. Výsledky dotazu jsou omezené na záznamy s hodnotami vlastností, které zadáte. Pokud jsou přidány žádné hodnoty, jsou zahrnuty všechny záznamy v dotazu. |
| **Další možnosti** |**> Barvy** |
| Barva 1<br>Barva 2<br>Barva 3 |Vyberte barvu pro všechny hodnoty, které jsou zobrazeny v prstencovém. |
| **Další možnosti** |**> Rozšířené mapování barev** |
| Hodnota pole |Zadejte název pole k zobrazení jinou barvu, pokud je zahrnutý v prstencovém. |
| Barva |Vyberte barvu pro pole jedinečný. |
| **Seznam** | |
| Dotaz |Dotaz pro spuštění v seznamu. Zobrazí se počet záznamů, které jsou vrácené dotazem. |
| Skrýt graf |Vyberte tento odkaz pro zakázání grafu v pravém rohu číselný sloupec. |
| Povolit minigrafy |Vyberte tento odkaz zobrazíte minigrafu místo vodorovný pruh. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Barva |Barvy pruhů nebo minigrafy. |
| Operace |Operace, který se má provést pro minigraf. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Oddělovač názvu a hodnoty |Jeden znak oddělovač, který má použít k analýze vlastnost text do více hodnot. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na položku v seznamu.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Seznam** |**> Názvy sloupců** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **Seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz povolit prahové hodnoty. Další informace najdete v tématu [obecná nastavení](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Dvě části časové osy a seznam
Záhlaví zobrazí výsledků dvou dotazů na protokoly časem jako sloupcové grafy s popiskem, která zobrazuje jedno číslo, které shrnuje hodnotu sloupce v dotazu protokolu. V seznamu zobrazí nejlepších deset výsledků z dotazu, s grafem, který určuje relativní hodnotu číselný sloupec nebo jeho změn v průběhu času.

![Dvě časové osy a seznam zobrazení](media/view-designer-parts/view-two-timelines-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části dlaždice. |
| Nová skupina |Vyberte tento odkaz můžete vytvořit novou skupinu v zobrazení, od aktuálního zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledek v záhlaví. |
| Použít ikonu |Vyberte tento odkaz k zobrazení ikony. |
| **Název navigační** | |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na záhlaví.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **První graf<br>druhý graf** | |
| Legenda |Text, který se zobrazí pod popisek pro první řady. |
| Barva |Barvy pro použití pro sloupce v řadě. |
| Dotaz |Dotaz, který se má spustit pro první řady. Počet záznamů v každém časovém intervalu je reprezentován sloupců v grafu. |
| Operace |Operace, který se má provést na hodnotu vlastnosti slouží ke shrnutí jako jednu hodnotu pro popisek.<ul><li>Součet: Součet hodnot ze všech záznamů.</li><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Poslední vzorek: hodnota od posledního intervalu, který je součástí grafu.</li><li>Nejprve ukázkový: hodnota z první interval, který je součástí grafu.</li><li>Počet: Počet všechny záznamy, které jsou vrácené dotazem.</li></ul> |
| **Seznam** | |
| Dotaz |Dotaz pro spuštění v seznamu. Zobrazí se počet záznamů, které jsou vrácené dotazem. |
| Skrýt graf |Vyberte tento odkaz pro zakázání grafu v pravém rohu číselný sloupec. |
| Povolit minigrafy |Vyberte tento odkaz zobrazíte minigrafu místo vodorovný pruh. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Barva |Barvy pruhů nebo minigrafy. |
| Operace |Operace, který se má provést pro minigraf. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na položku v seznamu.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Seznam** |**> Názvy sloupců** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **Seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz povolit prahové hodnoty. Další informace najdete v tématu [obecná nastavení](#thresholds). |

## <a name="information-part"></a>Část věnovaná informacím
Záhlaví obsahuje statický text a nepovinný odkaz. V seznamu zobrazí jednu nebo více položek s statické nadpis a text.

![Zobrazení informací](media/view-designer-parts/view-information.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části dlaždice. |
| Nová skupina |Vyberte tento odkaz můžete vytvořit novou skupinu v zobrazení, od aktuálního zobrazení. |
| Barva |Barva pozadí záhlaví. |
| **Záhlaví** | |
| Image |Soubor obrázku, který se zobrazí v záhlaví. |
| Štítek |Text, který je zobrazený v záhlaví. |
| **Záhlaví** |**> Odkaz** |
| Štítek |Text odkazu. |
| URL |Adresa Url odkazu. |
| **Informační položky** | |
| Titul |Text zobrazený v nadpisu každé položky. |
| Obsah |Text, který se zobrazí pro každou položku. |

## <a name="line-chart-callout-and-list-part"></a>Spojnicový graf, popisek a seznam součástí
Záhlaví zobrazuje spojnicový graf s několika řadami z dotazu protokolu za čas a popisek s hodnoty souhrnu. V seznamu zobrazí nejlepších deset výsledků z dotazu, s grafem, který určuje relativní hodnotu číselný sloupec nebo jeho změn v průběhu času.

![Spojnicový graf, popisek a zobrazení seznamu](media/view-designer-parts/view-line-chart-callout-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části dlaždice. |
| Nová skupina |Vyberte tento odkaz můžete vytvořit novou skupinu v zobrazení, od aktuálního zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledek v záhlaví. |
| Použít ikonu |Vyberte tento odkaz k zobrazení ikony. |
| **Záhlaví** | |
| Titul |Text, který se zobrazí v horní části záhlaví. |
| Podtitul |Text, který se zobrazí pod nadpisem v horní části záhlaví. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který se má spustit pro spojnicový graf. Textová hodnota, která je první vlastnost a druhá vlastnost je číselná hodnota. Tento dotaz se obvykle používá *míru* – klíčové slovo slouží ke shrnutí výsledků. Pokud dotaz používá *interval* – klíčové slovo, osy x grafu používá tento časový interval. Pokud dotaz neobsahuje *interval* – klíčové slovo, hodinových intervalech používá osy x. |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na záhlaví.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Spojnicový graf** |**> Popis obrázku** |
| Název popisku |Text, který se zobrazí nad hodnotou popisku. |
| Název řady |Hodnota vlastnosti řady, použít pro hodnotu popisku. Pokud je k dispozici žádné řady, použijí se všechny záznamy z dotazu. |
| Operace |Operace, který se má provést na hodnotu vlastnosti slouží ke shrnutí jako jednu hodnotu pro popisek.<ul><li>Průměr: Průměr hodnot ze všech záznamů.</li><li>Počet: Počet všechny záznamy, které jsou vrácené dotazem.</li><li>Poslední vzorek: hodnota od posledního intervalu, který je součástí grafu.</li><li>Maximální počet: Maximální hodnota z intervaly, které jsou zahrnuty v grafu.</li><li>Min: Minimální hodnota z intervaly, které jsou zahrnuty v grafu.</li><li>Součet: Součet hodnot ze všech záznamů.</li></ul> |
| **Spojnicový graf** |**> Osa y** |
| Použít logaritmické měřítko |Vyberte tento odkaz použít logaritmické měřítko osy y. |
| Jednotky |Zadejte jednotky pro hodnoty vrácené dotazem. Tyto informace slouží zobrazení popisků, které označují typy hodnot a volitelně pro převedení hodnot. *Jednotky* typ Určuje kategorii jednotky a definuje dostupných *aktuální jednotky* typ hodnoty. Pokud vyberete hodnotu v *převést na*, číselné hodnoty jsou převedeny z *aktuální jednotky* typ, který *převést na* typu. |
| Vlastní popisek |Text, který se zobrazí vedle popisku osy y *jednotky* typu. Pokud není zadán žádný popisek, pouze *jednotky* typ se zobrazí. |
| **Seznam** | |
| Dotaz |Dotaz pro spuštění v seznamu. Zobrazí se počet záznamů, které jsou vrácené dotazem. |
| Skrýt graf |Vyberte tento odkaz pro zakázání grafu v pravém rohu číselný sloupec. |
| Povolit minigrafy |Vyberte tento odkaz zobrazíte minigrafu místo vodorovný pruh. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Barva |Barvy pruhů nebo minigrafy. |
| Operace |Operace, který se má provést pro minigraf. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Oddělovač názvu a hodnoty |Jeden znak oddělovač, který má použít k analýze vlastnost text do více hodnot. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na položku v seznamu.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Seznam** |**> Názvy sloupců** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **Seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz povolit prahové hodnoty. Další informace najdete v tématu [obecná nastavení](#thresholds). |

## <a name="line-chart-and-list-part"></a>Část řádku graf a seznam
Záhlaví zobrazuje spojnicový graf s několika řadami z dotazu protokolu v čase. V seznamu zobrazí nejlepších deset výsledků z dotazu, s grafem, který určuje relativní hodnotu číselný sloupec nebo jeho změn v průběhu času.

![Řádky – zobrazení grafu a seznamu](media/view-designer-parts/view-line-chart-callout-list.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části dlaždice. |
| Nová skupina |Vyberte tento odkaz můžete vytvořit novou skupinu v zobrazení, od aktuálního zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledek v záhlaví. |
| Použít ikonu |Vyberte tento odkaz k zobrazení ikony. |
| **Záhlaví** | |
| Titul |Text, který se zobrazí v horní části záhlaví. |
| Podtitul |Text, který se zobrazí pod nadpisem v horní části záhlaví. |
| **Spojnicový graf** | |
| Dotaz |Dotaz, který se má spustit pro spojnicový graf. Textová hodnota, která je první vlastnost a druhá vlastnost je číselná hodnota. Tento dotaz se obvykle používá *míru* – klíčové slovo slouží ke shrnutí výsledků. Pokud dotaz používá *interval* – klíčové slovo, osy x grafu používá tento časový interval. Pokud dotaz neobsahuje *interval* – klíčové slovo, hodinových intervalech používá osy x. |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na záhlaví.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Spojnicový graf** |**> Osa y** |
| Použít logaritmické měřítko |Vyberte tento odkaz použít logaritmické měřítko osy y. |
| Jednotky |Zadejte jednotky pro hodnoty vrácené dotazem. Tyto informace slouží zobrazení popisků, které označují typy hodnot a volitelně pro převedení hodnot. *Jednotky* typ Určuje kategorii jednotky a definuje dostupných *aktuální jednotky* typ hodnoty. Pokud vyberete hodnotu v *převést na*, číselné hodnoty jsou převedeny z *aktuální jednotky* typ, který *převést na* typu. |
| Vlastní popisek |Text, který se zobrazí vedle popisku osy y *jednotky* typu. Pokud není zadán žádný popisek, pouze *jednotky* typ se zobrazí. |
| **Seznam** | |
| Dotaz |Dotaz pro spuštění v seznamu. Zobrazí se počet záznamů, které jsou vrácené dotazem. |
| Skrýt graf |Vyberte tento odkaz pro zakázání grafu v pravém rohu číselný sloupec. |
| Povolit minigrafy |Vyberte tento odkaz zobrazíte minigrafu místo vodorovný pruh. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Barva |Barvy pruhů nebo minigrafy. |
| Operace |Operace, který se má provést pro minigraf. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Oddělovač názvu a hodnoty |Jeden znak oddělovač, který má použít k analýze vlastnost text do více hodnot. Další informace najdete v tématu [obecná nastavení](#sparklines). |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na položku v seznamu.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Seznam** |**> Názvy sloupců** |
| Název |Text, který se zobrazí v horní části prvního sloupce. |
| Hodnota |Text, který se zobrazí v horní části druhý sloupec. |
| **Seznam** |**> Prahové hodnoty** |
| Povolit prahové hodnoty |Vyberte tento odkaz povolit prahové hodnoty. Další informace najdete v tématu [obecná nastavení](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Zásobník část řádku grafy
Zásobník spojnicový graf zobrazuje tři samostatné spojnicových grafů s více řadami z dotazu protokolu v průběhu času, jak je znázorněno zde:

![Zásobník spojnicových grafů](media/view-designer-parts/view-stack-line-charts.png)

| Nastavení | Popis |
|:--- |:--- |
| **Obecné** | |
| Název skupiny |Text, který se zobrazí v horní části dlaždice. |
| Nová skupina |Vyberte tento odkaz můžete vytvořit novou skupinu v zobrazení, od aktuálního zobrazení. |
| Ikona |Soubor obrázku, který se zobrazí vedle výsledek v záhlaví. |
| **Graf 1<br>graf 2<br>graf 3** |**> Záhlaví** |
| Titul |Text, který se zobrazí v horní části grafu. |
| Podtitul |Text, který se zobrazí pod nadpisem v horní části grafu. |
| **Graf 1<br>graf 2<br>graf 3** |**Spojnicový graf** |
| Dotaz |Dotaz, který se má spustit pro spojnicový graf. Textová hodnota, která je první vlastnost a druhá vlastnost je číselná hodnota. Tento dotaz se obvykle používá *míru* – klíčové slovo slouží ke shrnutí výsledků. Pokud dotaz používá *interval* – klíčové slovo, osy x grafu používá tento časový interval. Pokud dotaz neobsahuje *interval* – klíčové slovo, hodinových intervalech používá osy x. |
| Navigace pomocí kliknutí | Akce provedená v případě, že kliknete na záhlaví.  Další informace najdete v tématu [obecná nastavení](#click-through-navigation). |
| **Graf** |**> Osa y** |
| Použít logaritmické měřítko |Vyberte tento odkaz použít logaritmické měřítko osy y. |
| Jednotky |Zadejte jednotky pro hodnoty vrácené dotazem. Tyto informace slouží zobrazení popisků, které označují typy hodnot a volitelně pro převedení hodnot. *Jednotky* typ Určuje kategorii jednotky a definuje dostupných *aktuální jednotky* typ hodnoty. Pokud vyberete hodnotu v *převést na*, číselné hodnoty jsou převedeny z *aktuální jednotky* typ, který *převést na* typu. |
| Vlastní popisek |Text, který se zobrazí vedle popisku osy y *jednotky* typu. Pokud není zadán žádný popisek, pouze *jednotky* typ se zobrazí. |

## <a name="common-settings"></a>Obecná nastavení
Následující části popisují nastavení, které jsou společné pro několik částí vizualizace.

### <a name="name-value-separator"></a>Oddělovač názvu a hodnoty
Oddělovač názvu a hodnoty je oddělovač jedním znakem, který má použít k analýze vlastnost text ze seznamu dotazu do více hodnot. Pokud chcete zadat oddělovač, můžete zadat názvy pro každé pole oddělené oddělovačem stejné v **název** pole.

Zvažte například vlastnost s názvem *umístění* , které například obsahovat hodnoty *Redmond budování 41* a *Bellevue budování 12*. Oddělovač názvu a hodnoty můžete zadat pomlčku (-) a *město budování* pro název. Tento přístup analyzuje jednotlivé hodnoty do dvě vlastnosti volá *Město* a *vytváření*.

### <a name="click-through-navigation"></a>Navigace pomocí kliknutí
Navigace pomocí kliknutí definuje, jaká akce se provedou, když kliknete na záhlaví a seznam položek v zobrazení.  Tím otevřete dotaz v buď [portál pro prohledávání protokolů](../../log-analytics/log-analytics-log-search-portals.md) nebo spuštění jiného zobrazení.

Následující tabulka popisuje nastavení navigace pomocí kliknutí.

| Nastavení           | Popis |
|:--|:--|
| Prohledávání protokolu (automaticky) | Prohledávání protokolů pro spuštění při výběru položky záhlaví.  Toto je stejný položky založené na prohledávání protokolu.
| Prohledávání protokolů        | Prohledávání protokolů spustit, když vyberete položku v seznamu.  Zadejte dotaz do **navigační dotaz** pole.   Použití *{vybranou položku}* zahrnout syntaxe pro položku, kterou uživatel vybral.  Například, pokud dotaz obsahuje sloupec s názvem *počítače* a navigační dotaz je *{vybranou položku}*, dotaz jako *počítač = "Počítač"* se spustí, když vyberete počítač. Navigační dotaz, je-li *typ = událostí {vybranou položku}*, dotaz *typ = události počítač = "Počítač"* běží. |
| Zobrazení              | Zobrazení se otevře, když vyberete záhlaví položky nebo položky v seznamu.  Vyberte název zobrazení v pracovním prostoru v **název zobrazení** pole. |



### <a name="sparklines"></a>Sparklines
Minigraf je malý spojnicový graf, který znázorňuje hodnoty položky seznamu v čase. Pro části vizualizace se seznamem můžete vybrat, jestli se má zobrazovat vodorovný pruh, který určuje relativní hodnotu číselný sloupec nebo minigrafů, který indikuje její hodnotu v čase.

Následující tabulka popisuje nastavení pro minigrafy:

| Nastavení | Popis |
|:--- |:--- |
| Povolit minigrafy |Vyberte tento odkaz zobrazíte minigrafu místo vodorovný pruh. |
| Operace |Pokud jsou povolené minigrafy, právě tato operace provádět na každou vlastnost v seznamu k výpočtu hodnoty minigraf.<ul><li>Poslední vzorek: poslední hodnotu pro sérii během časového intervalu.</li><li>Maximální počet: Maximální hodnota pro řadu během časového intervalu.</li><li>Min: Minimální hodnota pro řadu během časového intervalu.</li><li>Součet: Součet hodnoty pro řadu během časového intervalu.</li><li>Shrnutí: Používá stejná `measure` příkaz jako dotaz v záhlaví.</li></ul> |

### <a name="thresholds"></a>Prahové hodnoty
Pomocí prahové hodnoty, můžete zobrazit barevné ikonu vedle každé položky v seznamu. Prahové hodnoty poskytnout rychlé vizuální indikátor položek, které překročí určitou hodnotu nebo spadá do určitého rozsahu. Například můžete zobrazit zelená ikona pro položky s přijatelnou hodnotu, žlutou, pokud je hodnota v rozsahu, který zobrazuje varování a červeně při překročení chybovou hodnotu.

Když povolíte prahové hodnoty pro součásti, je nutné zadat jeden nebo více prahové hodnoty. Pokud hodnota položky je větší než prahová hodnota a nižší než prahová hodnota. Další, je použita barva pro tuto hodnotu. Pokud je položka větší než nejvyšší hodnota prahové hodnoty, se používá jinou barvu. 

Každá sada prahová hodnota má mezní hodnotu pro jeden s hodnotou **výchozí**. Toto je barva, která je nastavena překročení žádné jiné hodnoty. Můžete přidat nebo odebrat prahové hodnoty tak, že vyberete **přidat** (+) nebo **odstranit** (x).

Následující tabulka popisuje nastavení prahových hodnot:

| Nastavení | Popis |
|:--- |:--- |
| Povolit prahové hodnoty |Vyberte tento odkaz zobrazíte barvy ikony v levé části jednotlivé hodnoty. Ikona označuje stav hodnotu vzhledem k zadané prahové hodnoty. |
| Název |Název prahovou hodnotu. |
| Prahová hodnota |Hodnota pro mezní hodnotu. Barva stavu pro každou položku seznamu je nastavena na barvu nejvyšší prahovou hodnotu, která je překročena hodnotou položky. Překročení žádné mezní hodnoty se používá výchozí barvu. |
| Barva |Barva, která určuje prahovou hodnotu. |

## <a name="next-steps"></a>Další postup
* Další informace o [prohledávání protokolů](../../log-analytics/log-analytics-queries.md) pro podporu dotazů v části vizualizace.
