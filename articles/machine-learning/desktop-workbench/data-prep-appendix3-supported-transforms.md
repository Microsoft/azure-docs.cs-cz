---
title: Použití transformací dat pro přípravu dat ve službě Azure Machine Learning | Dokumentace Microsoftu
description: Tento článek obsahuje úplný seznam transformací pro přípravu dat Azure Machine Learning k dispozici.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: c47d9bc72ad1d197b5030076456f9dc9efc422bc
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642776"
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Použití transformací dat pro přípravu dat ve službě Azure Machine Learning

A *transformace* ve službě Azure Machine Learning používá data v daném formátu, provede operace s daty (jako je například změna datového typu) a potom vytvoří data v novém formátu. Každá transformace má vlastní rozhraní a chování. Pomocí zřetězení několika transformací pomocí kroků v toku dat, můžete provádět komplexní a opakovatelné transformace na vaše data. Toto je základní funkcí pro přípravu dat.

Níže jsou k dispozici ve službě Azure Machine Learning transformací. 

## <a name="column-selection"></a>Výběr sloupce 
Řadu transformací v tomto seznamu pracovat na jeden sloupec nebo mnoho. Pokud chcete vybrat více sloupců, použijte klávesu Ctrl. Vybrat rozsah sloupců, použijte klávesu Shift.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>Transformace z hlavní nabídky nebo záhlaví tabulky 
Transformuje přístup z možnosti transformace v hlavní nabídce. Můžete také vybrat transformace kliknutím pravým tlačítkem myši název sloupce v datové mřížce. Pokud se vybere více sloupců, pravým tlačítkem myši na některé z nich poskytuje nabídku transformací.

V místní nabídce zobrazí pouze platné transformace pro vybraný typ data. V hlavní nabídce nabízí všechny transformace, ale zakáže ty nejsou důležité pro vybrané sloupce.

Malou podmnožinu kontextové transformace je k dispozici kliknutím pravým tlačítkem na buňku. Tyto transformace jsou kopie, nahradit a filtrovat. Toto jsou data typu vědět, tak možnosti pro číslo sloupce jsou jiné než u sloupec řetězcového typu.

## <a name="derive-column-by-example"></a>Derive Column by Example (Odvodit sloupec podle příkladu)
Pomocí této transformace vytvoří nový sloupec, jako jejich odvozené z jednoho nebo více existujících sloupců. Transformací, která se zabývá vstupní (vybraných) sloupcích a příkladu a pak určuje požadované výstupu v tomto novém sloupci. 

Pokud chcete použít tuto transformaci, vyberte jeden nebo více sloupců. Přidáte novou (prázdnou) odvozených sloupců podle příkladu. Zadejte příklad co byste chtěli vidět v odvozených sloupců (za předpokladu, že je odvozen od ostatních sloupců) a "V příkladu" technologie pokusí Vyplňte všechny ostatní buňky ve sloupci. 

Příklady složitý může být potřeba zadat více než jedním z příkladů. Chcete-li to provést, vyberte jinou buňku a zadejte jiný příklad.

Technologie "Vzorový" pokusit určit význam příklad pomocí vybraných sloupcích. Nebyly vybrány žádné sloupce, když uživatel vyvolá tuto transformaci, se používají všechny buňky v aktuálním řádku. Výběr pouze vyžadované sloupce vede k přesnější výsledky.

Můžete vybrat sloupce, před vyvoláním transformace. Když se otevřel editor transformace zaškrtávací políčka v horní části každého sloupce určit, jaké sloupce jsou vybrány jako vstupy. Můžete přidat nebo odebrat sloupce z výběru pomocí zaškrtávacích políček v záhlaví sloupců.

Podrobnější vysvětlení **odvozené odvodit sloupec podle příkladu** transformace, společně s další ukázky, naleznete v tématu [odvodit sloupec podle příkladu odkazu](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Rozdělit sloupec podle příkladu
Tato transformace má existující sloupec a pomocí modulu "Vzorový", pokusí se rozdělení sloupce do *n* ostatní sloupce. Můžete spustit automatické rozdělení na následné generované sloupce.

Podrobnější vysvětlení **rozdělit sloupec podle příkladu** transformace, společně s další ukázky, naleznete v tématu [rozdělit sloupec podle příkladu odkazu](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Rozbalení notace JSON

Tato transformace umožňuje přidat více sloupců tak, že rozbalíte sloupec s platný text JSON.

Podrobnější vysvětlení **rozbalte JSON** transformace, společně s další ukázky, naleznete v tématu [rozbalte JSON odkaz](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Sloučení sloupců podle příkladu

Tato transformace přidá nový sloupec kombinací hodnot z více sloupců. 

Podrobnější vysvětlení **zkombinovat sloupce podle příkladu** transformace, společně s další ukázky, naleznete v tématu [zkombinovat sloupce podle příkladu odkaz](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Duplikovat sloupec
Tato transformace umožňuje přesnou kopii jeden nebo více vybraných sloupců a poskytuje každý nový název je odvozen z původní název sloupce.

## <a name="text-clustering"></a>Text Clustering 
Tato transformace je navržené tak, aby nekonzistentní hodnoty, které by měl být stejný a seskupit dohromady.  

Tato transformace jsou hodnoty v jednom sloupci analyzován z hlediska podobnosti a seskupeny do clusterů. Pro každý cluster je Kanonická hodnota, která je hodnota, která nahradí všechny instance v clusteru a všechny hodnoty instance. Kompletní clustery je možné odebrat a upravit Kanonická hodnota. Instance je odebrat z daného clusteru. Filtr podobnosti skóre prahovou hodnotu, která se používá k skupiny instancí do clusteru je možné změnit.

Ve výchozím nastavení nahradí tato transformace všechny hodnoty instance clusteru Kanonická hodnota pro tento cluster, vytvoření nového sloupce tak, aby obsahovala nové hodnoty. Pro pozdější použití v toku dat můžete také přidat podobnosti skóre pro každou instanci pro nový sloupec (který může mít název).

## <a name="replace-values"></a>Nahradit hodnoty
Tato transformace použijte k nahrazení jednoho řetězce s jinou. Zdrojový řetězec může být dílčí řetězec nebo celé buňky a nahrazení můžou použít jeden sloupec nebo mnoho. Hledaný řetězec podporuje vyhledávání pro zvláštní znaky stejně jako u pravidelné znaků. 

## <a name="replace-na-values"></a>Nahraďte hodnoty není k dispozici
Tato transformace použít k nahrazení různé formy není k dispozici (není k dispozici, není k dispozici, null, NaN, atd.), nebo prázdné řetězce nahradit jednu hodnotu, aby byly konzistentní vzhledem k aplikacím. Tato transformace podporuje jeden nebo více sloupců a je uveden pouze pokud je vybrána sloupec. Není k dispozici v nabídce Hlavní transformace, když nebyly vybrány žádné sloupce.

## <a name="replace-missing-values"></a>Nahraďte chybějící hodnoty
Tato transformace nahradí jednu hodnotu chybějící data a podporuje jeden nebo více sloupců. Tato transformace je uveden pouze pokud je vybrána sloupec. Není k dispozici v nabídce Hlavní transformace, když nebyly vybrány žádné sloupce.

## <a name="replace-error-values"></a>Nahrazení chybových hodnot
Tato transformace nahradí jednu hodnotu chyby, a podporuje jeden nebo více sloupců. Tato transformace je uveden pouze pokud je vybrána sloupec. Není k dispozici v nabídce Hlavní transformace, když nebyly vybrány žádné sloupce.

## <a name="trim-string"></a>Oříznout řetězec
Tato transformace odstraní úvodní a koncové znaky "prázdné znaky" (včetně mezery, tabulátory, atd.) z jednoho nebo více sloupců.

## <a name="adjust-precision"></a>Upravit přesnost
Tato transformace nastaví počet desetinných míst pro číselné sloupce.

## <a name="rename-column"></a>Přejmenování sloupce
Tato transformace změní název ve vybraném sloupci. Můžete také vyvolat je vložený v záhlaví sloupce kliknutím na název sloupce.

## <a name="remove-column"></a>Odebrat sloupec
Tato transformace Odebere vybraného sloupce a funguje na jeden sloupec nebo mnoho. 

## <a name="keep-column"></a>Zachovat sloupec
Tato transformace zachová jenom vybrané sloupce a funguje na jeden sloupec nebo mnoho.

## <a name="handle-path-column"></a>Path Column popisovač
Při importu souboru, cesta sloupec se automaticky přidá do datové sady pomocí **přidat zdroj dat** funkce. Obsahuje plně kvalifikovaný název souboru, který tvoří cestu k datové sadě. Tato transformace přidá nebo odebere nadbytečné sloupce datové sady.

## <a name="convert-field-type-to-numeric"></a>Převést typ pole na číselný datový typ
Tato transformace změní typ sloupců na číselný datový typ. Můžete zadat oddělovač data jiných než celých čísel. Ve výchozím nastavení, nebude tato transformace výzvu k zadání oddělovače, proto **upravit** položku nabídky, který má být vyvolán editoru. Tato transformace funguje na jeden sloupec nebo mnoho.

## <a name="convert-field-type-to-date"></a>Převést typ pole Datum
Tato transformace změní typ sloupce pro datum. Použít výchozí formát data a času, ale lze přepsat pomocí `strftime` direktivy. Můžete také předřaďte hodnoty time s pevného data.

Ve výchozím nastavení, nebude tato transformace výzvu k zadání formátu, proto **upravit** položky nabídky na výslednou krok k vyvolání editoru. Tato transformace funguje na jeden sloupec nebo mnoho.

Pouze data mezi. 1677 9 22 a 4. 11 2262 lze převést na typ date.

## <a name="convert-field-type-to-boolean"></a>Převést typ pole na datový typ Boolean
Tato transformace změní typ sloupce na hodnotu true nebo false. Podporuje mapování více hodnot na hodnotu true nebo false, a můžete upravit tato mapování. Podporuje také konstantu, do kterého můžete namapovat hodnoty, které neexistují v tabulkách mapování true nebo false. Tato transformace funguje na jeden sloupec nebo mnoho.

## <a name="convert-field-type-to-string"></a>Převést typ pole na řetězec
Tato transformace změní typ sloupce na řetězec a funguje na jeden sloupec nebo mnoho.

## <a name="convert-unix-timestamp-to-datetime"></a>Unixové časové razítko převést na typ DateTime
Tato transformace rozumí formát časového razítka systému Unix, i když je datum vyjádřeno jako řetězec v datech. Převede časové razítko správně typ datum v rámci přípravy dat.

## <a name="filter"></a>Filtr
Tato transformace podporuje filtrování řádků na základě hodnot v jedné nebo více sloupců. Podmínky filtru závisí na typu dat každého sloupce, takže řetězcové sloupce můžete filtrovat podle "obsahuje" nebo "neobsahuje." Číselné sloupce můžete filtrovat podle "větší než" nebo "menší než" podmínky.

Když **filtr** transformace je vyvolána z hlavní nabídky, nebo z pravým tlačítkem myši na záhlaví sloupce, je k dispozici možnost vytvořit fork selhání řádků do jiného datového toku. Hlavní datový tok pokračuje filtrované **v** řádků a nového toku dat je vytvořen, který obsahuje všechny řádky, které byly zjištěny **si**.

## <a name="use-first-row-as-headers"></a>Použít první řádek jako záhlaví
Tato transformace zvýší úroveň prvního řádku z datové sady, aby názvy sloupců. Pokud některé sloupce nemají data v prvním řádku, název se generuje automaticky. Pomocí této transformace znamená, že import dat začíná na řádku 2 nejdříve. V závislosti na tom **přeskočit řádky** nastavení importu můžete spustit i další dolů v datové sadě. Také můžete ho odebrat podporu a použít automaticky generované pouze názvy.

## <a name="join"></a>Spojit
Tato transformace slouží ke spojení dvou datové toky. Můžete vybrat, které výstup spojení bude výsledek: úspěšné řádky z spojení, "levou" selhání řádky z spojení nebo "právo" selhání řádky z spojení.

**Spojení** transformace začíná od jednoho datového toku a vybere tento tok dat jako jedna strana spojení. Pak budete vyzváni k výběru jiné toku dat pro jiné strany spojení. Po výběru dva toky, transformace vyžaduje jeden sloupec na každé straně spojení se rozhodli spojit. Pokud spojení potřebuje více než jeden sloupec, vytvořte před zahájením transformací, která chcete vytvořit novou, zřetězených sloupec má být použit pouze pro spojení odvozených sloupců. Transformací, která se pokusí navrhnout spojení klíče a pokud je to možné automaticky generovat odvozených sloupců.

Po dokončení připojení se zobrazí zobrazení diagramu Sankey spojení. Šířka čáry vzhledem k ostatním představuje počet řádků, procházení část toku spojení. Pomocí panelu na pravé straně, můžete vybrat úspěšné řádky levé straně selhání řádků nebo napravo výhradně selhání řádků. Můžete také jedna větev.

## <a name="append-rows"></a>Připojit řádky
Tato transformace připojí data z jiného tok dat do aktuálního. Mapuje se sloupce podle pozice přidat nové řádky na konci.

## <a name="append-columns"></a>Přidat sloupce
Tato transformace přidá nové sloupce z jiné tok dat do aktuálního. Přidá nové sloupce vpravo. Nebude se pokoušet řádek dat v řádcích.

## <a name="summarize"></a>shrnutí
Tato transformace vypočítá agregace pro kombinace jedinečných hodnot v jedné nebo více vybraných sloupcích. 

Agregace, podporovány jsou: počet, SUM, MIN, MAX, průměr, odchylka a SMĚRODATNOU ODCHYLKU. V seznamu agregací pro daný sloupec vyfiltrují jenom agregace, které se vztahují na datový typ. Agregace, které se nevztahují jsou zakázané. Například není možné vypočítat průměr sloupec řetězcového typu, ale je možné vypočítat MIN a MAX.

Pokud editor není k dispozici, přetáhněte z záhlaví sloupce do panelu nahoře vlevo, ve kterém se zobrazují sloupce, které chcete agregovat. Tento panel je hierarchické, abyste mohli vnořené agregace. Editor panelu v pravé horní části slouží k výběru které agregace použít na sloupec. Jeden sloupec se dají agregovat jednou nebo vícekrát. Poté, co byl vybrán alespoň jeden agregace, mřížky v pravém dolním rohu zobrazí náhled dat v agregované podobě. 

Tato transformace je obdobou `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Odebrat duplicitní položky
Tato transformace odstraní celý řádek existují duplicitní hodnoty v jedné nebo více vybraných sloupcích. Pokud žádné sloupce je možné zvolit, odebrat pouze řádky jsou ty, kde jsou všechny hodnoty ve sloupcích stejné.

## <a name="sort"></a>Seřadit
Tato transformace seřadí data. Řazení lze provést jedním sloupcem nebo mnoha a každý sloupec lze seřadit vzestupně (výchozí) nebo descending (které je možné změnit z editoru).

Tato transformace je obdobou `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Výstup transformace
Následující transformace výstupní data. Více bloků zápisu může mít v jedné toku k zápisu dat v různých fázích.

### <a name="write-to-csv"></a>Zápis do sdíleného svazku clusteru
Tato transformace dat ve formě CSV zapíše z aktuálního místa v toku dat. Určuje umístění (místní nebo vzdálené) a různá nastavení kolem souboru.

### <a name="write-to-parquet"></a>Zápis do Parquet
Tato transformace dat v podobě Parquet zapíše z aktuálního místa v toku dat. Určuje umístění (místní nebo vzdálené) a různá nastavení kolem souboru.

## <a name="script-based-transforms"></a>Transformace založené na skriptu
Transformace následující funkci, která chybí v základním produktu pomocí skriptu (Python). 

>[!NOTE]
>Než použijete některý z těchto transformací, přečtěte si [rozšíření pomocí Pythonu](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Přidat sloupec (skript)
Tato transformace přidá sloupec data s využitím výraz Pythonu.
Další informace najdete v tématu [kódu Pythonu vzorku pro odvození nového sloupce](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Rozšířený filtr (skript)
Tato transformace použijte k zápisu Python úrovně Filtr řádků.
Další informace najdete v tématu [výrazů filtru příklad](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transform Dataflow (script)
Tato transformace platí pro celou datovou sadu Python.
Další informace najdete v tématu [transformace toku dat transformace příklad](data-prep-appendix7-sample-transform-data-flow-python.md).

Tato transformace lze také použít Python do oddílu veškerá data.
Další informace najdete v tématu [transformace toku dat transformace příklad](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Zápis Dataflow (script)
Tato transformace používá Python vypsat celou datovou sadu.
Další informace najdete v tématu [Python vzorku pro odvození nového sloupce](data-prep-appendix9-sample-destination-connections-python.md).



