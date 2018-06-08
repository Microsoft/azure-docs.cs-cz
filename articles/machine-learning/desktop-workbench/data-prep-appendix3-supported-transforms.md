---
title: Použití transformací dat pro přípravu dat v Azure Machine Learning | Microsoft Docs
description: Tento článek obsahuje úplný seznam transformací pro přípravu Azure Machine Learning dat. k dispozici.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 0ad4202f62aada30a8eab996858d3e972876e5f2
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831277"
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Použití transformací dat pro přípravu dat v Azure Machine Learning

A *transformace* v Azure Machine Learning spotřebuje data v daném formátu, provede operace s daty (jako je například změna datového typu) a potom vytvoří data v novém formátu. Každý transformace má svou vlastní rozhraní a chování. Podle řetězení několik transformací společně pomocí kroků v toku dat, můžete provádět komplexní a opakovatelných transformace na vaše data. Toto je základní funkce datového přípravy.

Níže jsou k dispozici v Azure Machine Learning transformace. 

## <a name="column-selection"></a>Výběr sloupce 
Řadu transformace v tomto seznamu fungovat na jeden sloupec nebo mnoho. Pokud chcete vybrat více sloupců, použijte klávesu Ctrl. Chcete-li vybrat rozsah sloupců, použijte klávesu Shift.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>Transformuje z hlavní nabídky nebo hlavičku mřížky 
Transformuje přístup pomocí volby transformace v hlavní nabídce. Kliknutím pravým tlačítkem na název sloupce v mřížce dat. můžete také vybrat transformací. Pokud je vybraných více sloupců, pravým tlačítkem myši na některý z nich poskytuje nabídku transformací.

V místní nabídce se zobrazí pouze platné transformace pro datovému typu vybranému. V hlavní nabídce nabízí všechny transformací, ale zakáže ty, které nejsou důležité pro vybrané sloupce.

Malou podmnožinu kontextové transformace je k dispozici kliknutím pravým tlačítkem na buňku. Tyto transformace kopírování, nahraďte a filtrovat. Tyto jsou data typu vědět, takže možnosti pro číslo sloupce se liší od pro řetězcový sloupec.

## <a name="derive-column-by-example"></a>Derive Column by Example (Odvodit sloupec podle příkladu)
Vytvoří nový sloupec, jako odvozený jeden nebo více sloupců existující pomocí Tato transformace. Transformovat porovná vstupní sloupce (vybrané) a v příkladu zadané a pak určuje požadovanou výstup do nového sloupce. 

Chcete-li použít tato transformace, vyberte jeden nebo více sloupců. Přidáte nový sloupec odvozené (prázdný) příklad. Zadejte příklad co chcete vidět v odvozených sloupců (za předpokladu, že je odvozený od ostatních sloupců) a "Podle příkladu" technologie pokusí vyplnit všechna ostatní buňky ve sloupci. 

Příklady složitá vám může být nutné zadat více než jedním z příkladů. To pokud chcete udělat, vyberte jinou buňku a zadejte další příklad.

"Příklad" technologie využívá vybrané sloupce při pokusu o určení význam příklad. Nebyly vybrány žádné sloupce, když je volána Tato transformace, se používají všechny buňky na aktuálním řádku. Výběru pouze požadovaných sloupců vede k více přesné výsledky.

Před vyvoláním transformovat můžete vybrat sloupce. Po transformaci editoru někdo otevřel, zaškrtávací políčka v horní části každý sloupec uvádět, jaké sloupce jsou vybrány jako vstupy. Můžete přidat nebo odebrat sloupce z výběru pomocí zaškrtávacích políček v záhlaví sloupců.

Podrobnější vysvětlení **sloupců odvozenou příklad** transformace, společně s další ukázky, najdete v části [odvozena sloupec odkazem příklad](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Rozdělit sloupec podle příkladu
Tato transformace trvá existující sloupec a pomocí "Příklad" modul se pokusí k rozdělení sloupce do *n* ostatních sloupců. Auto rozdělení můžete spustit na následné generovaného sloupce.

Podrobnější vysvětlení **rozdělit sloupec podle příkladu** transformace, společně s další ukázky, najdete v části [rozdělit sloupec podle příkladu reference](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Rozbalení notace JSON

Tato transformace umožňuje přidat více sloupců rozšířením sloupec s platný text JSON.

Podrobnější vysvětlení **rozbalte JSON** transformace, společně s další ukázky, najdete v části [rozbalte JSON odkaz](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Sloučení sloupců příklad

Tato transformace přidá nový sloupec kombinací hodnoty z více sloupců. 

Podrobnější vysvětlení **kombinovat sloupce příklad** transformace, společně s další ukázky, najdete v části [kombinovat sloupce odkazem příklad](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Duplicitní sloupce
Tato transformace umožňuje přesnou kopii jeden nebo více vybraných sloupců a poskytuje každý nový název odvozené z původní název sloupce.

## <a name="text-clustering"></a>Text Clustering 
Tato transformace je navržené tak, aby nekonzistentní hodnoty, které by měl být stejný a je seskupit.  

U této transformace jsou hodnoty do jednoho sloupce analyzován z hlediska podobnosti a seskupené do clusterů. Pro každý cluster je kanonické hodnoty, což je hodnota, která nahradí všechny instance v clusteru a všechny hodnoty instance. Lze odebrat dokončení clustery a kanonické hodnoty upravit. Instance můžete odebrat z daného clusteru. Filtr skóre prahovou hodnotu podobnosti, který se používá na skupinu instancí do clusteru může být změněna.

Ve výchozím nastavení Tato transformace nahradí všechny hodnoty instance clusteru kanonické hodnoty pro tento cluster, vytvoření nového sloupce tak, aby obsahovala s novými hodnotami. Můžete také přidat hodnocení podobnosti pro každou instanci pro nový sloupec, (který může mít název) pro použití později v datový tok.

## <a name="replace-values"></a>Nahraďte hodnoty
Použijte Tato transformace jeden řetězec nahradit jiným. Zdrojový řetězec může být částečné řetězce nebo úplné buňku a nahrazení můžete použít na jeden sloupec nebo mnoho. Hledaný řetězec podporuje vyhledávání pro speciální znaky, stejně jako u regulární znaků. 

## <a name="replace-na-values"></a>Nahraďte hodnoty NA
Tato transformace použít k nahrazení různé formy NA (není k dispozici, NA, null, NaN atd.), nebo nahradit jednu hodnotu, aby byly konzistentní prázdné řetězce. Tato transformace podporuje jeden nebo více sloupců a je uveden pouze pokud je vybrána sloupec. Není přítomen v nabídce Hlavní transformace při nebyly vybrány žádné sloupce.

## <a name="replace-missing-values"></a>Nahraďte chybějící hodnoty
Tato transformace nahradí jednu hodnotu chybějící data a podporuje jeden nebo více sloupců. Tato transformace je uvedena pouze pokud je vybrána sloupec. Není přítomen v nabídce Hlavní transformace při nebyly vybrány žádné sloupce.

## <a name="replace-error-values"></a>Nahradí chybové hodnoty
Tato transformace nahradí jednu hodnotu chyby, a podporuje jeden nebo více sloupců. Tato transformace je uvedena pouze pokud je vybrána sloupec. Není přítomen v nabídce Hlavní transformace při nebyly vybrány žádné sloupce.

## <a name="trim-string"></a>Trim – řetězec
Tato transformace odebere počátečních a koncových znaků "prázdný znak" (včetně mezery, tabulátory, atd.) z jednoho nebo více sloupců.

## <a name="adjust-precision"></a>Upravit přesnost
Tato transformace nastaví počet desetinných míst pro číselnou sloupec.

## <a name="rename-column"></a>Přejmenovat sloupec
Tato transformace změní název vybraného sloupce. Můžete také vyvolat ho vložený v záhlaví sloupce klepnutím na název sloupce.

## <a name="remove-column"></a>Odebere sloupec
Tato transformace odebere vybrané sloupce a funguje na jeden sloupec nebo mnoho. 

## <a name="keep-column"></a>Zachovat sloupce
Tato transformace zachová pouze vybrané sloupce a funguje na jeden sloupec nebo mnoho.

## <a name="handle-path-column"></a>Popisovač cesta sloupce
Při importu souboru, cesta sloupec se automaticky přidá do datové sady pomocí **přidat zdroj dat** funkce. Obsahuje plně kvalifikovaný název souboru, která tvoří cestu k datové sadě. Tato transformace přidá nebo odebere další sloupce v datové sadě.

## <a name="convert-field-type-to-numeric"></a>Převod na číselný typ pole
Tato transformace se změní na číselný typ sloupce. Můžete zadat oddělovač pro data celé číslo. Ve výchozím nastavení není tato transformace výzvu pro oddělovač, takže použijte **upravit** položku nabídky k vyvolání editoru. Tato transformace funguje na jeden sloupec nebo mnoho.

## <a name="convert-field-type-to-date"></a>Typ pole převést na typ Date
Tato transformace změny sloupec typu datum. Formát data a času výchozím nastavení se používá, ale můžete přepsat pomocí `strftime` direktivy. Můžete také předřazení hodnoty time s pevnou datum.

Ve výchozím nastavení není tato transformace výzvu pro formát, takže použijte **upravit** položky nabídky v výsledné kroku k vyvolání editoru. Tato transformace funguje na jeden sloupec nebo mnoho.

Pouze data mezi. 1677 9 22 a 4. 11 2262 můžete převést na typ date.

## <a name="convert-field-type-to-boolean"></a>Typ pole převést na logickou hodnotu
Tato transformace změní typ sloupce na hodnotu true nebo false. Podporuje více hodnot mapování na hodnotu true nebo false, a můžete upravit tato mapování. Podporuje také konstanta, do kterého můžete namapovat hodnoty, které neexistují v tabulkách mapování true nebo false. Tato transformace funguje na jeden sloupec nebo mnoho.

## <a name="convert-field-type-to-string"></a>Typ pole převést na řetězec
Tato transformace změní typ sloupce na řetězec a funguje na jeden sloupec nebo mnoho.

## <a name="convert-unix-timestamp-to-datetime"></a>Časové razítko Unix převést na typ DateTime
Tato transformace rozumí formát časového razítka Unix, i když je reprezentována jako řetězec v datech. Převede časové razítko správně typ data v rámci přípravy data.

## <a name="filter"></a>Filtr
Tato transformace podporuje filtrování na základě hodnot ve sloupcích jednu nebo více řádků. Podmínky filtru, závisí na datový typ jednotlivých sloupců, takže řetězcové sloupce můžete filtrovat podle "obsahuje" nebo "neobsahuje." Číselné sloupce lze filtrovat podle "větší než" nebo "menší než" podmínky.

Když **filtru** transformace je volána z hlavní nabídky, nebo z pravým tlačítkem myši na záhlaví sloupce, je k dispozici možnost, chcete-li rozvětvit selhání řádky do jiného datového toku. Hlavní datový tok pokračuje filtrované **v** řádků a nový tok dat je vytvořen, který obsahuje všechny řádky, které byly zjištěny **out**.

## <a name="use-first-row-as-headers"></a>Použít první řádek jako záhlaví
Tato transformace zvýší úroveň prvního řádku ze sady dat jako názvy sloupců. Pokud některé sloupce nemají data v prvním řádku, název se generuje automaticky. Pomocí této transformace znamená, že import dat začíná na řádek 2 nejdříve. V závislosti na tom **přeskočit řádky** nastavení, import můžete spustit i další dolů v datové sadě. Také můžete jej odebrat povýšení a pomocí automaticky generovaných názvů jenom.

## <a name="join"></a>Spojit
Tato transformace slouží ke spojení dvou datové toky. Můžete vybrat, které výstup spojení v důsledku: úspěšné řádky z připojení, "left" selhání řádků ze spojení nebo "vpravo na" selhání řádky z spojení.

**Spojení** transformace spustí z jednoho datového toku a vybere tento tok dat jako jeden strany spojení. Potom budete vyzváni k výběru jiné tok dat pro připojení k druhé straně. Po výběru dva toky, transformace vyžaduje jeden sloupec na každé straně spojení, které chcete pro připojení na lze vybrat. Pokud spojení potřebuje používat více než jeden sloupec, vytvořte před zahájením transformace k vytvoření nové, zřetězených sloupce má být použit pouze pro spojení odvozených sloupců. Pro transformaci se pokusí navrhnout klíče připojení a pokud je to možné, automaticky generovat odvozených sloupců.

Po dokončení spojení, se zobrazí zobrazení diagramu Sankey připojení k. Šířka čáry relativně k sobě navzájem představuje počet řádků procházení část toku spojení. Použití panelu na pravé straně, můžete vybrat úspěšné řádky doleva selhání právo výhradně selhání řádků nebo řádků. Můžete také jedinou pobočku.

## <a name="append-rows"></a>Připojit řádky
Tato transformace připojí dat z jiného tok dat do stávající. Mapuje sloupce podle pozici pro přidání nové řádky na konci.

## <a name="append-columns"></a>Připojit sloupců
Tato transformace připojí nové sloupce z jiné tok dat pro aktuální. Přidá nové sloupce vpravo. Nepodporuje se pokusí řádek dat v řádcích.

## <a name="summarize"></a>Shrnutí
Tato transformace vypočítá agregace pro kombinace jedinečné hodnoty jeden nebo více vybraných sloupců. 

Agreguje podporována jsou: počet, SUM, MIN, MAX, střední, ODCHYLKU a SMĚRODATNÉ odchylky. Pouze agregace, které se vztahují na datový typ je filtrovaná seznamu agregací pro daný sloupec. Agregace, které se nevztahují jsou zakázány. Například není možné vypočítat střední sloupec řetězce, ale je možné k výpočtu Nejnižší a nejvyšší.

Pokud editor je k dispozici, přetáhněte ze záhlaví sloupce do panelu nahoře vlevo, kde se zobrazují sloupce, které chcete agregovat. Tento panel je hierarchická, abyste mohli vnořené agregace. Editor panelu v pravé horní slouží k výběru agregace, které chcete použít pro sloupec. Jeden sloupec se dají agregovat jeden či více krát. Poté, co byl vybrán alespoň jeden agregace, přináší náhled mřížky v pravé dolní data v agregované podobě. 

Tato transformace je obdobou `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Odeberte duplicitní položky
Tato transformace odebere celý řádek, pokud existují duplicitní hodnoty jeden nebo více vybraných sloupců. Pokud jsou vybraná žádné sloupce, pouze řádky odebrat ty, které jsou, kde všechny hodnoty ve sloupcích jsou stejné.

## <a name="sort"></a>Seřadit
Tato transformace seřadí data. Řazení lze provést jedním sloupcem nebo mnoho a každý sloupec lze seřadit vzestupně (výchozí) nebo sestupném (které je možné změnit z editoru).

Tato transformace je obdobou `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Výstup transformace
Následující transformace výstupní data. Může mít více bloků zápisu v jedním tokem zápis dat v různých fázích.

### <a name="write-to-csv"></a>Zápis do sdíleného svazku clusteru
Tato transformace zapíše data ve formátu CSV z aktuální bodu datový tok. Ovládá umístění (místní nebo vzdálené) a různá nastavení kolem soubor.

### <a name="write-to-parquet"></a>Zápis do Parquet
Tato transformace zapíše data ve formuláři Parquet z aktuální bodu datový tok. Ovládá umístění (místní nebo vzdálené) a různá nastavení kolem soubor.

## <a name="script-based-transforms"></a>Založených na skriptech transformací
Následující transformace skript (Python) použít k provedení funkce, které chybí v základním produktu. 

>[!NOTE]
>Než použijete některou z těchto transformací, přečtěte si [rozšiřitelnost pomocí Python](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Přidat sloupec (skript)
Tato transformace přidá sloupec dat pomocí výrazu jazyka Python.
Další informace najdete v tématu [Python ukázkový kód pro odvození nové sloupce](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Pokročilé filtru (skript)
Tato transformace slouží k zápisu filtru úrovně řádek Python.
Další informace najdete v tématu [příklady filtr výrazů](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transformace toku dat (skript)
Tato transformace platí pro celou sadu dat Python.
Další informace najdete v tématu [toku dat transformace příklad transformace](data-prep-appendix7-sample-transform-data-flow-python.md).

Tato transformace lze také použít Python na oddíl celý data.
Další informace najdete v tématu [toku dat transformace příklad transformace](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Zápis toku dat (skript)
Tato transformace používá Python k zapsání celé datové sady.
Další informace najdete v tématu [ukázka Python pro odvození nové sloupce](data-prep-appendix9-sample-destination-connections-python.md).



