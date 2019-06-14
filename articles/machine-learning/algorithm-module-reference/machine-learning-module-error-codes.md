---
title: Řešení potíží s chybami modulu
titleSuffix: Azure Machine Learning service
description: Řešení potíží s výjimkami modulu v Azure Machine Learning Studio pomocí kódy chyb
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09a2b616e2bba93be86241c64d37daec7d6dea3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029757"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Výjimky a kódy chyb pro odkaz na modul a algoritmům

Další informace o chybových zprávách a kódy výjimek, které může dojít k používání modulů v Azure Machine Learning Studio. 

Chcete-li problém vyřešit, vyhledejte chybu v tomto článku, přečtěte si informace o běžných příčin. Existují dva způsoby, jak získat textu v plném znění chybové zprávy v sadě Studio:  
 
- Klikněte na odkaz **zobrazení výstupu protokolu**, v pravém podokně a přejděte do dolní části. Podrobná chybová zpráva se zobrazí v posledních dvou řádcích okna.  
  
- Vyberte modul, který obsahuje chybu a klikněte na červený symbol X. Zobrazí se pouze text relevantní chyby.  
  
Pokud je text chybové zprávy není užitečný, pošlete nám informace o kontextu a všechny požadované dodatky nebo změny. Můžete odeslat zpětnou vazbu k tématu chyby, nebo přejděte [fórum pro Azure Machine Learning STUDIO](https://aka.ms/aml-forum-studio) a odeslat dotaz.  


## <a name="error-0001"></a>Chyba 0001  
 Výjimka nastane, pokud jeden nebo více zadaných sloupců datová sada nebyla nalezena.  
  
 Zobrazí se k této chybě a pokud se provádí modul Výběr sloupců, ale vybraných sloupců, které neexistují ve vstupní datové sady. K této chybě může dojít, pokud jste zadali ručně název sloupce, nebo pokud navrhované sloupec, který neexistuje v datové sadě při spuštění experimentu selektor sloupců.  
  
**Řešení:** Návštěvě modulu vyvolání této výjimky a ověřte, že název sloupce nebo názvy jsou správné a že existují všechny sloupce odkazované.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Jeden nebo více zadaných sloupcích nebyly nalezeny.|  
|Sloupec s názvem nebo indexu "{0}" nebyl nalezen|  
|Sloupec s názvem nebo indexu "{0}"neexistuje v"{1}"|  
 

## <a name="error-0002"></a>Chyba 0002  
 Pokud jeden nebo více parametrů nejde kontaktovat analyzovaný nebo převedený ze zadaného typu do vyžadovat typ cílové metody dojde k výjimce.  
  
 Ve službě Azure Machine Learning se této chybě dochází, když určíte jako vstupní parametr a typ hodnoty se liší od typu, který se očekává a implicitní převod nelze provést.  
  
**Řešení:** Zkontrolujte požadavky na modul a určit, jaký typ hodnoty je povinný (string, integer, double, atd.)  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepovedlo se parsovat parametr|  
|Nepovedlo se parsovat "{0}" parametr|  
|Nepovedlo se parsovat (převod) "{0}"parametr"{1}"|  
|Nepovedlo se převést "{0}"parametr z"{1}"do"{2}"|  
|Nepovedlo se převést "{0}"hodnota parametru"{1}"z"{2}"do"{3}"|  
|Nepovedlo se převést hodnotu "{0}"ve sloupci"{1}"z"{2}"do"{3}"s využitím formátu"{4}" k dispozici|  
  

## <a name="error-0003"></a>Chyba 0003  
 Výjimka nastane, pokud jeden nebo více vstupů jsou null nebo prázdné.  
  
 Tato chyba bude zobrazit ve službě Azure Machine Learning, pokud žádné vstupy nebo parametry do modulu hodnotu null nebo prázdný.  Této chybě může dojít například, pokud jste nezadali žádné hodnoty pro parametr. Je také může dojít, pokud jste zvolili datovou sadu, která má chybějící hodnoty nebo prázdnou datovou sadu.  
  
**Řešení:**
 
+ Otevřete modul, který vytvořil výjimku a ověřte, zda byly zadány všechny vstupy. Ujistěte se, že všechny požadované jsou zadané vstupy. 
+ Ujistěte se, že data, která jsou načtená ze služby Azure storage jsou přístupná a že nedošlo ke změně názvu účtu a klíč.  
+ Zkontrolujte vstupní data pro chybějící hodnoty nebo hodnoty Null.
+ Pokud pomocí dotazu na zdroji dat, ověřte, že je vrácení dat ve formátu, které očekáváte. 
+ Zkontrolujte překlepy nebo jiné změny ve specifikaci data.
  
|Zprávy o výjimkách|  
|------------------------|  
|Jeden nebo více vstupů jsou null nebo prázdné|  
|Vstup "{0}" má hodnotu null nebo prázdné|  
  

## <a name="error-0004"></a>Chyba 0004  
 Pokud parametr je menší než nebo rovna hodnotě konkrétní dojde k výjimce.  
  
 Pokud parametr ve zprávě je menší než hodnota hranice požadované pro modul pro zpracování dat, obdrží tuto chybu ve službě Azure Machine Learning.  
  
**Řešení:** Návštěvě modulu, která vyvolává výjimku a upravte parametr být větší než zadaná hodnota.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Parametr musí být větší než hodnota hranice.|  
|Parametr "{0}" hodnota by měla být větší než {1}.|  
|Parametr "{0}"má hodnotu"{1}" který by měl být větší než {2}|  
  


## <a name="error-0005"></a>Chyba 0005  
 Pokud parametr je menší než zadanou hodnotu, dojde k výjimce.  
  
 Se zobrazí tato chyba ve službě Azure Machine Learning, pokud parametr ve zprávě je menší než nebo stejná hodnota hranice pro modul pro zpracování dat vyžaduje.  
  
**Řešení:** Návštěvě modulu, která vyvolává výjimku a upravte parametr být větší než nebo rovna zadané hodnotě.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Parametr musí být větší než nebo rovna hodnotě hranice.|  
|Parametr "{0}" hodnota by měla být větší než nebo rovna hodnotě {1}.|  
|Parametr "{0}"má hodnotu"{1}" který by měl být větší než nebo rovna hodnotě {2}.|  
  

## <a name="error-0006"></a>Chyba 0006  
 Výjimka nastane, pokud parametr je větší než nebo rovna zadané hodnotě.  
  
 Pokud parametr ve zprávě je větší než nebo rovna hodnotě hranice požadované pro modul pro zpracování dat, obdrží tuto chybu ve službě Azure Machine Learning.  
  
**Řešení:** Návštěvě modulu, která vyvolává výjimku a upravte parametr menší než zadanou hodnotou.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Neshoda parametrů. Jeden z parametrů musí být menší než jiný.|  
|Parametr "{0}"hodnota by měla být menší než parametr"{1}" hodnota.|  
|Parametr "{0}"má hodnotu"{1}" který by měl být menší než {2}.|  
  

## <a name="error-0007"></a>Chyba 0007  
 Pokud parametr zadefinujete, přesáhne určitou hodnotu, dojde k výjimce.  
  
 Pokud ve vlastnostech pro modul, jste zadali hodnotu, která je větší, než je povolené, obdrží tuto chybu ve službě Azure Machine Learning. Například můžete například zadat data, která je mimo rozsah podporovaných dat, nebo může znamenat, že pět sloupců použít, pokud jsou k dispozici pouze tři sloupce. 
 
 Může také zobrazit chyba, pokud zadáváte dvě sady dat, která musí odpovídat nějakým způsobem. Například pokud se přejmenování sloupců a určení sloupců podle indexu, počet názvů, které zadáte musí odpovídat počtu indexy sloupců. Dalším příkladem můžou být matematické operace, která využívá dva sloupce, ve kterém sloupci musí mít stejný počet řádků. 
  
**Řešení:**
 
 + Otevřete modul dotyčný a zkontrolujte číselnou vlastnost nastavení.
 + Ujistěte se, že všechny hodnoty parametrů spadat do rozsahu podporovaných hodnot pro tuto vlastnost.
 + Pokud modul přijímá více vstupů, ujistěte se, že vstupy jsou stejné velikosti.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Zkontrolujte, jestli datová sada nebo zdroj dat se změnila. Někdy hodnotu, který pracoval s předchozí verzí dat selže po počet sloupců, datové typy sloupce nebo množství dat se změnilo.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Neshoda parametrů. Jeden z parametrů musí být menší nebo rovna do jiného.|  
|Parametr "{0}"hodnota by měla být menší nebo roven parametru"{1}" hodnota.|  
|Parametr "{0}"má hodnotu"{1}" který by měl být menší než nebo rovno {2}.|  
  

## <a name="error-0008"></a>Chyba 0008  
 Pokud parametr není v rozsahu, dojde k výjimce.  
  
 Pokud parametr ve zprávě je mimo hranice požadované pro modul pro zpracování dat, obdrží tuto chybu ve službě Azure Machine Learning.  
  
 Například se zobrazí tato chyba, pokud se pokusíte použít [přidat řádky](add-rows.md) zkombinovat dvě datové sady, které mají jiný počet sloupců.  
  
**Řešení:** Návštěvě modulu, která vyvolává výjimku a upravte parametr musí být v zadaném rozsahu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Hodnota parametru není v zadaném rozsahu.|  
|Parametr "{0}" hodnota není v rozsahu.|  
|Parametr "{0}" hodnota by měla být v rozsahu [{1}, {2}].|  
  

## <a name="error-0009"></a>Chyba 0009  
 Výjimka nastane, pokud je nesprávně zadán název účtu úložiště Azure nebo název kontejneru.  
  
Tato chyba nastane v Azure Machine Learning Studio můžete zadat parametry pro účet úložiště Azure, ale nelze je přeložit jméno nebo heslo. Pro mnoho důvodů, proč může docházet k chybám na hesla nebo názvy účtů:
 
 + Účet je nesprávného typu. Některé nové typy účtu nejsou podporovány pro použití se službou Machine Learning Studio. Zobrazit [Import dat](import-data.md) podrobnosti.
 + Zadaný název účtu není správné.
 + Tento účet už existuje.
 + Heslo pro účet úložiště je chybná nebo se změnil
 + Jste nezadali název kontejneru nebo kontejner neexistuje.
 + Jste neurčili, nevložily plně cesta k souboru (cestu k objektu blob)
   
**Řešení:**

Tyto problémy často dochází, když zkusíte nutné ručně zadat cestu k jméno, heslo nebo kontejner účtu. Doporučujeme použít Průvodce novým pro [Import dat](import-data.md) modul, který vám pomůže vyhledat a zkontrolovat názvy.

Zkontrolujte také, jestli účet, kontejner nebo objekt blob se odstranil. Zkontrolujte, že název účtu a hesla nebyl zadán správně a zda existuje kontejner pomocí jiného nástroje služby Azure storage. 

Azure Machine Learning nepodporuje některé novější typy účtů. Například nové "horkými" nebo "studenými" úložiště typy nelze používat pro machine learning. Klasických účtů úložiště a účty úložiště vytvořené jako "Obecné použití" fungovat správně.

Pokud nebyla zadána úplná cesta k objektu blob, ověřte, že cesta je určena jako **kontejneru/blobname**, a existence kontejneru a objektu blob v účtu.  
  
 Cesta nesmí obsahovat počáteční lomítko. Například **/kontejner nebo objekt blob** jsou nesprávné a je by měly být zadány jako **kontejner nebo objekt blob**.  

  
|Zprávy o výjimkách|  
|------------------------|  
|Název účtu úložiště Azure nebo název kontejneru není správné.|  
|Název účtu úložiště Azure "{0}"nebo název kontejneru"{1}" není správné; byl očekáván název kontejneru objektu blob nebo kontejneru formátu.|  
  

## <a name="error-0010"></a>Chyba 0010  
 Pokud vstupní datové sady mají názvy sloupců, které by měl odpovídat, ale ne, dojde k výjimce.  
  
 Zobrazí se tato chyba ve službě Azure Machine Learning index sloupce ve zprávě má jiné názvy sloupců v dva vstupní datové sady.  
  
**Řešení:** Použití [upravit Metadata](edit-metadata.md) nebo upravit původní datové sady má stejný název sloupce pro zadaný sloupec indexu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Sloupce s odpovídající index ve vstupní datové sady mají odlišné názvy.|  
|Názvy sloupců nejsou stejné sloupce {0} (založený na nule) vstupní datové sady ({1} a {2} v uvedeném pořadí).|  
  

## <a name="error-0011"></a>Chyba 0011  
 Výjimka nastane, pokud předaný argumentu sady sloupců se nedá použít u kteréhokoli sloupce datové sady.  
  
 Zobrazí se tato chyba ve službě Azure Machine Learning Pokud výběr zadané sloupce neodpovídá žádnému sloupci v dané datové sadě.  
  
 Můžete také získat tuto chybu, pokud jste nevybrali sloupec a pro modul pro práci je vyžadován alespoň jeden sloupec.  
  
**Řešení:** Výběr sloupců v modulu upravte tak, aby se použije k sloupců v datové sadě.  
  
 Pokud modul vyžaduje, vyberte v určitém sloupci, jako je například sloupec popisek, ověřte, že je vybraná v pravém sloupci.  
  
 Pokud se vybere nevhodný sloupců, je odebrat a experiment znovu spustit.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zadaný sloupec sady se nevztahuje na žádné sloupce datové sady.|  
|Zadaná sada sloupců "{0}" se nedá použít u kteréhokoli sloupce datové sady.|  
  

## <a name="error-0012"></a>Chyba 0012  
 Výjimka nastane, pokud nebylo možné vytvořit instanci třídy předaných sadu argumentů.  
  
**Řešení:** Tato chyba není možné použít uživatelem a přestanou používat v budoucí verzi.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nezkušený model, nejprve trénování modelu.|  
|Nezkušený modelu ({0}), použijte trénovaného modelu.|  
  

## <a name="error-0013"></a>Chyba 0013  
 Výjimka nastane, pokud learner předán modulu je neplatného typu.  
  
 K této chybě dochází vždy, když je nekompatibilní s připojených modul pro stanovení skóre pro trénovaný model. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Řešení:**

Určit typ student, který je vytvořen modulem školení a určit, která je vhodná pro learner modul pro stanovení skóre. 

Pokud model se trénuje pomocí kteréhokoli z modulů specializovaná školení, připojte pouze k modulu odpovídající specializované vyhodnocování trénovaného modelu. 


|Typ modelu|Školicí modul| Modul pro stanovení skóre|
|----|----|----|
|žádné třídění|[Trénování modelu](train-model.md) |[Určení skóre modelu](score-model.md)|
|žádné regresní model|[Trénování modelu](train-model.md) |[Určení skóre modelu](score-model.md)|
<!--| Clustering s modely| [Trénování Clusteringový Model](train-clustering-model.md) nebo [oblouku Clustering](sweep-clustering.md)| [Přiřaďte Data do clusterů](assign-data-to-clusters.md)|
| detekce anomálií - SVM jeden – třída | [Trénování modelu detekce anomálií](train-anomaly-detection-model.md) |[Určení skóre modelu](score-model.md)|
| detekce anomálií - DPS |[Trénování modelu](train-model.md) |[Určení skóre modelu](score-model.md) </br> Některé další kroky jsou nutné k vyhodnocení modelu. |
| detekce anomálií - časové řady|  [Čas detekce anomálií řady](time-series-anomaly-detection.md) |Model trénovat z data a generuje skóre. V modulu trained learner nevytvoří a žádné další vyhodnocování se vyžaduje. |
| model doporučení| [Trénování Matchbox doporučení](train-matchbox-recommender.md) | [Doporučené Matchbox skóre](score-matchbox-recommender.md) |
| klasifikace obrázků | [Které je předem vytrénované Cascade klasifikace obrázků](pretrained-cascade-image-classification.md) | [Určení skóre modelu](score-model.md) |
|Vowpal Wabbit modelů| [Trénování modelu 7-4 Vowpal Wabbit verze](train-vowpal-wabbit-version-7-4-model.md) | [Určení skóre Vowpal Wabbit verze 7-4 modelu](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit modelů| [Trénování modelu 7 – 10 Vowpal Wabbit verze](train-vowpal-wabbit-version-7-10-model.md) | [Určení skóre Vowpal Wabbit verze 7 – 10 modelu](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit modelů| [Trénování modelu Vowpal Wabbit verze 8](score-vowpal-wabbit-version-8-model.md) | [Určení skóre modelu verze 8 Vowpal Wabbit](score-vowpal-wabbit-version-8-model.md) |-->
  
|Zprávy o výjimkách|  
|------------------------|  
|Je předán learner neplatného typu.|  
|Learner "{0}" má neplatný typ.|  


## <a name="error-0014"></a>Chyba 0014  
 Pokud je větší než počet povolený počet jedinečných hodnot sloupce, dojde k výjimce.  
  
 Tato chyba nastane, pokud sloupec obsahuje příliš mnoho jedinečné hodnoty.  Například pokud určíte, že sloupec být zpracován jako data v kategoriích, může se zobrazit tato chyba, ale je moc velký počet jedinečných hodnot ve sloupci povolit zpracování dokončí. Může také zobrazit chyba, pokud došlo k neshodě mezi počtem jedinečných hodnot ve dvou vstupů.   
  
**Řešení:**

Otevřete modul, který vytvořil chybu a najděte sloupců použitých jako vstupy. Pro některé moduly, kliknete pravým tlačítkem na datové sadě vstupní a vyberte **vizualizovat** získat statistiky pro jednotlivé sloupce, včetně počtu jedinečné hodnoty a jejich distribuci.

Sloupce, které máte v úmyslu používat pro seskupení nebo kategorizaci Podnikněte kroky ke snížení počtu jedinečných hodnot ve sloupcích. Můžete snížit různými způsoby v závislosti na typu dat sloupce. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Nepovedlo se najít řešení, která odpovídá vašemu scénáři? Poskytnout zpětnou vazbu k tomuto tématu, který obsahuje název modulu, který vygeneruje chybu a datový typ a mohutnosti daného sloupce. Budeme používat údaje k poskytování více cílových řešení potíží pro běžné scénáře.   
  
|Zprávy o výjimkách|  
|------------------------|  
|Počet jedinečných hodnot sloupce je větší než počet povolený.|  
|Počet jedinečných hodnot ve sloupci: "{0}" překračuje počet řazené kolekce členů {1}.|  
  

## <a name="error-0015"></a>Chyba 0015  
 Výjimka nastane, pokud připojení k databázi se nezdařilo.  
  
 Zobrazí se tato chyba a pokud jste zadali nesprávný název účtu SQL, heslo, databázového serveru nebo název databáze, nebo kvůli problémům s databáze nebo serveru nepodaří navázat spojení s databází.  
  
**Řešení:** Ověřte, že název účtu, heslo, databázový server a databáze nebyl zadán správně a zda má zadaný účet správné úrovně oprávnění. Ověřte, že databáze je nyní k dispozici.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Chyba při navazování připojení databáze.|  
|Chyba vytváření připojení k databázi: {0}.|  
  


## <a name="error-0016"></a>Chyba 0016  
 Pokud vstupní datové sady, které jsou předány do modulu by měly mít typy kompatibilní sloupců, ale nejsou dojde k výjimce.  
  
 Pokud nejsou navzájem kompatibilní typy sloupců předaný dva nebo více datových sad, obdrží tuto chybu ve službě Azure Machine Learning.  
  
**Řešení:** Použití [upravit Metadata](edit-metadata.md) nebo upravit původní vstupní datové sady<!--, or use [Convert to Dataset](convert-to-dataset.md)--> k zajištění kompatibilní typy sloupců.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Sloupce s odpovídající index ve vstupní datové sady mají nekompatibilní typy.|  
|Sloupce {0} a {1} jsou nekompatibilní.|  
|Sloupec typy elementů nejsou kompatibilní pro sloupec {0} (založený na nule) vstupní datové sady ({1} a {2} v uvedeném pořadí).|  
  

## <a name="error-0017"></a>Chyba 0017  
 Pokud vybraný sloupec používá datový typ, který není podporován modulem aktuální, dojde k výjimce.  
  
 Například může být tato chyba se zobrazí ve službě Azure Machine Learning Pokud váš výběr sloupce obsahuje sloupec s datovým typem, který nejde zpracovat pomocí modulu, jako je například sloupec řetězcového typu matematické operace, nebo sloupec skóre, kde sloupec zařazené do kategorií funkce je povinné.  
  
**Řešení:**
 1. Sloupec, který se týká se problém identifikujte.
 2. Zkontrolujte požadavky modul.
 3. Upravte sloupce, aby byl v souladu s požadavky. Je třeba provést změny, v závislosti na sloupec a převod, který se pokoušíte pomocí některé z následujících modulů:
    + Použití [upravit Metadata](edit-metadata.md) Změna datového typu sloupců nebo změnit využití sloupce z funkce na číselné literály, zařazené do kategorií na jiných kategorií a tak dále.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Jako poslední možnost je třeba upravit původní vstupní datové sady.

> [!TIP]
> Nepovedlo se najít řešení, která odpovídá vašemu scénáři? Poskytnout zpětnou vazbu k tomuto tématu, který obsahuje název modulu, který vygeneruje chybu a datový typ a mohutnosti daného sloupce. Budeme používat údaje k poskytování více cílových řešení potíží pro běžné scénáře. 
  
|Zprávy o výjimkách|  
|------------------------|  
|Sloupec s aktuálním typem nelze zpracovat. Typ není podporován modulem.|  
|Nemůže zpracovat sloupce typu {0}. Typ není podporován modulem.|  
|Nemůže zpracovat sloupce "{1}" typu {0}. Typ není podporován modulem.|  
|Nemůže zpracovat sloupce "{1}" typu {0}. Typ není podporován modulem. Název parametru: {2}|  
  

## <a name="error-0018"></a>Chyba 0018  
 Výjimka nastane, pokud vstupní datová sada je neplatný.  
  
**Řešení:** Tato chyba ve službě Azure Machine Learning můžete zobrazit v mnoha kontextech, tedy není jednoho řešení. Obecně platí chyba udává, že data zadaná jako vstup do modulu obsahuje nesprávný počet sloupců nebo že datový typ se neshoduje s požadavky modulu. Příklad:  
  
-   Modul vyžaduje popisek sloupce, ale žádný sloupec je označena jako popisek nebo ještě není zvolena popisek sloupce.  
  
-   Modul vyžaduje, aby se data zařazená do kategorií, ale vaše data jsou číselná.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Data jsou v nesprávném formátu.  
  
-   Importovaná data obsahuje neplatné znaky, správné hodnoty, nebo je mimo rozsah hodnot.  
-   Sloupec je prázdný nebo obsahuje příliš mnoho chybějících hodnot.  
  
 K určení požadavků a jak může vaše data, přečtěte si téma nápovědy pro modul, který budou pracovat s datovou sadu jako vstup.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Datové sady není platný.|  
|{0} obsahuje neplatná data.|  
|{0} a {1} by měl být konzistentní vzhledem k aplikacím sloupce z pohledu.|  
  

## <a name="error-0019"></a>Chyba 0019  
 Pokud sloupec má obsahovat seřazené hodnoty, ale ne, dojde k výjimce.  
  
 Zobrazí se tato chyba ve službě Azure Machine Learning Pokud hodnoty zadané sloupce jsou mimo pořadí.  
  
**Řešení:** Seřazení hodnot sloupců tak, že ručně upravíte vstupní datové sady a znovu spusťte modul.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Hodnoty ve sloupci nejsou seřazené.|  
|Hodnoty ve sloupci "{0}" nejsou seřazené.|  
|Hodnoty ve sloupci "{0}"z datové sady"{1}" nejsou seřazené.|  
  

## <a name="error-0020"></a>Chyba 0020  
 Výjimka nastane, pokud počet sloupců v některé z datové sady, předán modulu je příliš malá.  
  
 Zobrazí se tato chyba ve službě Azure Machine Learning, pokud není dostatek sloupců byly vybrány pro modul.  
  
**Řešení:** Návštěvě modulu a ujistěte se, že tento selektor sloupců nemá správný počet vybraných sloupců.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Počet sloupců ve vstupní datová sada je menší než povolené minimum.|  
|Počet sloupců ve vstupní datová sada je menší než povolené minimum {0} sloupce.|  
|Počet sloupců v datové sadě vstupní "{0}" je menší než povolené minimum {1} sloupce.|

## <a name="error-0021"></a>Chyba 0021  
 Výjimka nastane, pokud počet řádků v některé z datové sady, předán modulu je příliš malá.  
  
 Tato chyba v vidět ve službě Azure Machine Learning při existují není dostatečný počet řádků v datové sadě k provedení zadané operace. Tato chyba může například zobrazit, pokud vstupní datová sada je prázdný nebo pokud se pokoušíte provést operaci, která vyžaduje některé minimální počet řádků, které mají být platný. Tyto operace může zahrnovat (ale nejsou omezeny) seskupení nebo klasifikaci založené na statistické metody, některé druhy binningu a učení dle počtů.  
  
**Řešení:**
 
 + Otevřete modul, který se vrátil chybu a zkontrolujte vlastnosti vstupní datové sady a modulu. 
 + Ověřte, že vstupní datová sada není prázdná a že jsou dostatečný počet řádků data, aby splňovala požadavky popsané v nápovědě k modulu.  
 + Pokud vaše data jsou načtena z externího zdroje, ujistěte se, že je zdroj dat dostupný a že se nezobrazí žádná chyba nebo změňte v definici dat, která může způsobit proces importu se získat méně řádků.
 + Pokud provádíte operace s daty upstream modulu, který může mít vliv na typ dat nebo počet hodnot, jako je čištění, oddělení, nebo operace sjednocení, zkontrolujte výstupy z těchto operací určit počet řádků vrácených.  



## <a name="error-0022"></a>Chyba 0022  
 Pokud počet vybraných sloupců v datové sadě vstupní nerovná se očekávaný počet dojde k výjimce.  
  
 Této chybě ve službě Azure Machine Learning může dojít, když podřízené modulu nebo operace vyžaduje konkrétní počet sloupců nebo vstupů a jste zadali příliš málo nebo příliš mnoho sloupců nebo vstupy. Příklad:  
  
-   Zadejte sloupec s názvem bez přípony nebo klíčového sloupce a omylem zvolila více sloupců.  
  
-   Jsou přejmenování sloupců, ale poskytuje názvy více nebo méně, než kolik existuje sloupců.  
  
-   Počet sloupců ve zdroji nebo cíli došlo ke změně nebo neodpovídá počtu sloupců používá modul.  
  
-   Zadáte seznam hodnot oddělených čárkou pro vstupy, ale počet hodnot, který neodpovídá nebo více vstupů nejsou podporovány.  
  
**Řešení:** Návštěvě modulu a zkontrolovat výběr sloupce k zajištění, že je vybrána správný počet sloupců. Ověřte, výstupy modulů nadřazený a požadavky na příjem dat operací.  
  
 Pokud jste použili jedno z výběru možnosti sloupce, které můžete vybrat více sloupců (indexy sloupců, všechny funkce, všechny číselné a tak dále), ověřte přesný počet sloupců vrácených ve výběr.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Ověřte, že nedošlo ke změně číslo nebo typ nadřazeného sloupců.  
  
 Pokud používáte doporučení datové sady pro trénování modelu, mějte na paměti, doporučení očekává, že omezený počet sloupců, odpovídající páry položka uživatele nebo uživatele. položky pořadím. Odeberte další sloupce před zobrazením trénování modelu nebo rozdělení doporučení datových sad. Další informace najdete v tématu [rozdělení dat](split-data.md).  
  
|Zprávy o výjimkách|  
|------------------------|  
|Počet vybraných sloupců v datové sadě vstupní nerovná očekávané číslo.|  
|Počet vybraných sloupců ve vstupní datové sady není rovno {0}.|  
|Sloupec výběr vzoru "{0}" poskytuje počet vybraných sloupců ve vstupní datové sady není rovno {1}.|  
|Sloupec výběr vzoru "{0}" se očekává poskytování {1} sloupců, které se vybrané ve vstupní datové sady, ale {2} sloupců, které je/jsou k dispozici.|  



## <a name="error-0023"></a>Chyba 0023  
 Pokud cílový sloupec vstupní datové sady není platný pro aktuální modul trainer dojde k výjimce.  
  
 Pokud cílový sloupec (jako je vybrané v parametrech modul) nemá platný datový typ, obsahovala všechny chybějící hodnoty nebo nebylo zařazené do kategorií podle očekávání, dojde k této chybě ve službě Azure Machine Learning.  
  
**Řešení:** Návštěvě modulu vstup a zkontrolovat obsah popisek/cílový sloupec. Ujistěte se, že nemá všechny chybějící hodnoty. Pokud modul očekává cílový sloupec být zařazené do kategorií, ujistěte se, že existují více jedinečných hodnot ve sloupci cíl.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Cílový sloupec má nepodporovanou vstupní datové sady.|  
|Vstupní datová sada obsahuje nepodporované cílový sloupec "{0}".|  
|Vstupní datová sada obsahuje nepodporované cílový sloupec "{0}" pro learner typu {1}.|  
 

## <a name="error-0024"></a>Chyba 0024  
Pokud datová sada neobsahuje sloupec popisek dojde k výjimce.  

 Tato chyba ve službě Azure Machine Learning nastane, pokud modul vyžaduje popisek sloupce a datová sada neobsahuje sloupec popisek. Vyhodnocení Vyhodnocená datová sada například obvykle vyžaduje, že je k dispozici pro výpočet metrik přesnost popisek sloupce.  
 
Může také dojít, že je k dispozici v datové sadě popisek sloupce, ale nebyl správně zjištěn technologii Azure Machine Learning.
  
**Řešení:**

+ Otevřete modul, který vytvořil chybu a určení, zda popisek sloupce je k dispozici. Název nebo datový typ sloupce nezáleží, tak dlouho, dokud sloupec obsahuje jeden výsledek (nebo závislé proměnné), který se pokoušíte předpovědět. Pokud si nejste jisti, který sloupec má popisek, Hledat obecný název, jako *třídy* nebo *cílové*. 
+  Pokud je objekt dataset neobsahuje popisek sloupce, je možné, že popisek sloupce explicitně nebo neúmyslně odebral upstream. Také je možné, že datovou sadu není výstup nadřazeného modul pro stanovení skóre.
+ Chcete-li explicitně označit sloupci jako popisek sloupce, přidejte [upravit Metadata](edit-metadata.md) modulu a připojte se datová sada. Vyberte popisek sloupce a vyberte **popisek** z **pole** rozevíracího seznamu. 
+ Pokud je nesprávném sloupci jako popisek, můžete vybrat **vymazat popisek** z **pole** opravit metadata pro sloupec. 
  
|Zprávy o výjimkách|  
|------------------------|  
|Neexistuje žádný popisek sloupec v datové sadě.|  
|Neexistuje žádný sloupec popisek v "{0}".|  
  

## <a name="error-0025"></a>Chyba 0025  
 Pokud datová sada neobsahuje sloupec skóre, dojde k výjimce.  
  
 Pokud vstup do evaluate model neobsahuje platný skóre dojde k této chybě ve službě Azure Machine Learning sloupce. Uživatel například pokusí zjistit datové sady, předtím, než se skóre s správné trénovaného modelu, nebo sloupec skóre byla odstraněna explicitně upstream. Tato výjimka také v případě skóre sloupců na dvě datové sady jsou nekompatibilní. Například může být, že se pokoušíte porovnání přesnost lineární regresor s ním binární třídění.  
  
**Řešení:** Návštěvě vstup do evaluate model a zkontrolujte, zda obsahuje jeden nebo více sloupců skóre. V opačném případě se skóre datovou sadu nebo skóre sloupce, které byly zahozeny nadřazeného modulu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Neexistuje žádný sloupec skóre v datové sadě.|  
|Neexistuje žádný sloupec skóre v "{0}".|  
|Neexistuje žádný sloupec skóre v "{0}", který je vytvořen "{1}". Datové sady pomocí správného typu learner skóre.|  
  

## <a name="error-0026"></a>Chyba 0026  
 Pokud nejsou povoleny sloupce se stejným názvem, dojde k výjimce.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud máte více sloupců se stejným názvem. Jedním ze způsobů, může se zobrazit tato chyba je-li datová sada neobsahuje řádek záhlaví a názvy sloupců jsou automaticky přiřazeny: Col0 Sloupec1, atd.  
  
**Řešení:** Pokud sloupce mají stejný název, vložte [upravit Metadata](edit-metadata.md) modulu mezi vstupní datové sady a modulu. Použít výběr sloupců v [upravit Metadata](edit-metadata.md) vybrat sloupce, které chcete přejmenovat, zadáním do nových názvů **nové názvy sloupců** textového pole.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Názvy sloupců stejné jsou uvedeny v argumenty. Sloupce stejně názvy nejsou povoleny modulem.|  
|Názvy sloupců v argumentech rovno "{0}"a"{1}" nejsou povoleny. Zadejte jiný název.|  
  

## <a name="error-0027"></a>Chyba 0027  
 V případě, pokud dva objekty musí být stejné velikosti, ale nejsou dojde k výjimce.  
  
 To je běžné chyby ve službě Azure Machine Learning a může být způsobena mnoha podmínky.  
  
**Řešení:** Neexistuje žádné konkrétní řešení. Můžete však zjišťovat podmínek, jako je následující:  
  
-   Při přejmenování sloupců, ujistěte se, že každý seznam (vstupní sloupce a seznam nových názvů) má stejný počet položek.  
  
-   Pokud jsou připojení nebo zřetězení dvou datových sad, ujistěte se, že budou mít stejné schéma.  
  
-   Pokud se připojujete dvě datové sady, které mají více sloupců, ujistěte se, že klíčové sloupce obsahovaly stejná data, zadejte a vyberte možnost **povolit vynechávání duplicit a zachovat pořadí sloupců v výběru**.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Velikost objektů předaných je nekonzistentní.|  
|Velikost "{0}"je nekonzistentní s velikostí"{1}".|  
  

## <a name="error-0028"></a>Chyba 0028  
 V případě dojde k výjimce, pokud obsahuje duplicitní sloupec názvy sloupců a není povoleno.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud jsou duplicitní názvy sloupců; To znamená, že není jedinečný.  
  
**Řešení:** Pokud žádné sloupce mají stejný název, přidat instanci [upravit Metadata](edit-metadata.md) mezi vstupní datové sady a modul vyvolání chyby. Pomocí voliče sloupců v [upravit Metadata](edit-metadata.md) vybrat sloupce, které chcete přejmenovat a zadejte nové názvy sloupců do **nové názvy sloupců** textového pole. Pokud jste přejmenovali více sloupců, ujistěte se, že zadáte hodnoty **nové názvy sloupců** musí být jedinečné.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Sadě sloupců obsahuje duplicitní sloupec názvy.|  
|Název "{0}" je duplicitní.|  
|Název "{0}"je duplicitní v"{1}".|  
  

## <a name="error-0029"></a>Chyba 0029  
 V případě, kdy je předán neplatný identifikátor URI, dojde k výjimce.  
  
 V případě, kdy je předán neplatný identifikátor URI, dojde k této chybě ve službě Azure Machine Learning.  Zobrazí se tato chybová zpráva Pokud platí některá z následujících podmínek:, nebo.  
  
-   Veřejný nebo identifikátor URI SAS k dispozici pro úložiště objektů Blob v Azure pro čtení nebo zápisu obsahuje chybu.  
  
-   Časový interval pro SAS vypršela platnost.  
  
-   Adresa URL webové prostřednictvím protokolu HTTP zdroje představuje soubor nebo URI zpětné smyčky.  
  
-   Adresa URL webové přes protokol HTTP obsahuje nesprávně naformátovanou adresu URL.  
  
-   Adresa URL nemůže přeložit vzdáleného zdroje.  
  
**Řešení:** Návštěvě modulu a ověřte formát identifikátoru URI. Pokud je zdrojem dat adresa URL webové přes protokol HTTP, ověřte, že požadovaný zdroj není zpětné smyčky identifikátoru URI (localhost) nebo souboru.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Neplatný identifikátor Uri je předán.|  
  

## <a name="error-0030"></a>Chyba 0030  
 V případě dojde k výjimce, když není možné ke stažení souboru.  
  
 Tato výjimka ve službě Azure Machine Learning nastane, pokud není možné ke stažení souboru. Tato výjimka se zobrazí při pokusu o čtení z zdroje HTTP se nezdařilo po tři (3) opakujte pokusy.  
  
**Řešení:** Zkontrolujte správnost identifikátoru URI zdroje HTTP a, že lokalita je aktuálně přístupná prostřednictvím Internetu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepovedlo se stáhnout soubor.|  
|Při stahování souboru došlo k chybě: {0}.|  
  

## <a name="error-0031"></a>Chyba 0031  
 Pokud počet sloupců v sadě sloupců je menší než je potřeba, dojde k výjimce.  
  
 Pokud počet vybraných sloupců je menší než je potřeba, dojde k této chybě ve službě Azure Machine Learning.  Zobrazí se k této chybě a pokud minimální požadovaný počet sloupců nejsou vybrané.  
  
**Řešení:** Přidání dalších sloupců do výběr sloupce s použitím **selektor sloupců**.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Počet sloupců v sadě sloupců je menší než se požaduje.|  
|{0} sloupce musí být zadaný. Je skutečný počet zadaných sloupců {1}.|  

## <a name="error-0032"></a>Chyba 0032  
 Výjimka nastane, pokud argument není číslo.  
  
 Tato chyba bude zobrazit ve službě Azure Machine Learning, pokud je argumentem typu double nebo NaN.  
  
**Řešení:** Upravte zadaného argumentu, který na platnou hodnotu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Argument není číslo.|  
|"{0}" není číslo.|  
  

## <a name="error-0033"></a>Chyba 0033  
 Pokud je argument nekonečno, dojde k výjimce.  
  
 Pokud je argument nekonečno dojde k této chybě ve službě Azure Machine Learning. Zobrazí se tato chybová zpráva Pokud je argumentem `double.NegativeInfinity` nebo `double.PositiveInfinity`.  
  
**Řešení:** Upravte zadaného argumentu, který na platnou hodnotu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Argument musí být konečná.|  
|"{0}" není omezený.|  
  

## <a name="error-0034"></a>Chyba 0034  
 Pokud existuje více než jeden hodnocení pro danou položku uživatele pár dojde k výjimce.  
  
 Pár položka uživatele má více než jeden hodnocení hodnotu k této chybě ve službě Azure Machine Learning dochází v doporučení.  
  
**Řešení:** Ujistěte se, že položka uživatele pár má pouze jednu hodnotu hodnocení.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Existuje více než jeden hodnocení pro hodnoty v datové sadě.|  
|Více než jeden hodnocení pro uživatele {0} a položky {1} v tabulce dat hodnocení předpovědi.|  
  

## <a name="error-0035"></a>Chyba 0035  
 Pokud byly k dispozici žádné funkce pro konkrétního uživatele nebo položky dojde k výjimce.  
  
 K této chybě ve službě Azure Machine Learning dojde, se pokoušíte použít model doporučení pro vyhodnocení, ale funkce vector nebyl nalezen.  
  
**Řešení:**

Doporučené Matchbox má několik požadavků, které musí být splněny, při použití funkce položky nebo uživatelské funkce.  Tato chyba označuje, že je funkce vector u uživatele nebo položky, které jste zadali jako vstup chybí.  Ujistěte se, že vektor funkce je dostupná v datech pro každého uživatele nebo položky.  
  
 Například pokud jste školení doporučení model pomocí funkce, jako je věk uživatele, umístění nebo příjem, ale teď chcete vytvořit skóre pro nové uživatele, kteří se můžou vyskytnout během školení, je nutné zadat některé ekvivalentní sadu funkcí (konkrétně, věk, umístění, a příjem hodnoty) pro nové uživatele, aby bylo možné provádět příslušné předpovědi je. 
 
 Pokud nemáte žádné funkce pro tyto uživatele, zvažte vytváření funkcí pro generování odpovídající funkce.  Například pokud věku a příjmů hodnoty jednotlivých uživatelů, můžete vygenerovat přibližné hodnoty pro skupinu uživatelů. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Řešení není k dispozici pro váš případ? Určitě můžete odeslat zpětnou vazbu v tomto článku a poskytnout informace o scénáře, včetně modulu a počet řádků ve sloupci. Použije tyto informace poskytují podrobnější kroků pro řešení potíží v budoucnu.
   
|Zprávy o výjimkách|  
|------------------------|  
|Pro požadovaného uživatele nebo položky byly k dispozici žádné funkce.|  
|Funkce pro {0} povinné, ale není k dispozici.|  
  

## <a name="error-0036"></a>Chyba 0036  
 Pokud bylo zadáno více vektory funkce pro konkrétního uživatele nebo položky dojde k výjimce.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud funkce vektor je definován více než jednou.  
  
**Řešení:** Ujistěte se, že funkce vector není definován více než jednou.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Duplicitní definice funkce pro uživatele nebo položku.|  
|Duplicitní definice funkce pro {0}.|  
  

## <a name="error-0037"></a>Chyba 0037  
 Pokud je zadáno více sloupců popisek a je povolená jenom jedna dojde k výjimce.  
  
 Pokud je vybrán více než jeden sloupec bude nový popisek sloupce dojde k této chybě ve službě Azure Machine Learning. Většina pod dohledem learning algoritmy vyžadují jeden sloupec označen jako cíl nebo popisek.  
  
**Řešení:** Je nutné vybrat jeden sloupec jako sloupec nový popisek.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Je zadáno více sloupců popisek.|  
  

## <a name="error-0038"></a>Chyba 0038  
 Výjimka nastane, pokud počet očekávaných elementů musí být přesnou hodnotu, ale není.  
  
 Nastane, pokud počet očekávaných elementů by měl být přesná hodnota tuto chybu ve službě Azure Machine Learning, ale není.  Pokud počet prvků, které se nerovná platný očekávaná hodnota dostanete k této chybě.  
  
**Řešení:** Úprava vstupu správný počet prvků.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Počet prvků, které není platný.|  
|Počet prvků v objektu "{0}" není platný.|  
|Počet prvků v objektu "{0}" se nerovná platný počet {1} elementy.|  
  

## <a name="error-0039"></a>Chyba 0039  
 Výjimka nastane, pokud operace se nezdařila.  
  
 Tato chyba ve službě Azure Machine Learning nastane interní operaci nejde dokončit.  
  
**Řešení:** Tato chyba je způsobena mnoha podmínky a neexistuje žádná konkrétní náhrada.  
 Následující tabulka obsahuje obecné zprávy této chyby, které jsou následuje popis konkrétní podmínky. 
 
 Pokud nejsou k dispozici žádné podrobnosti [poslat svůj názor](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) a zadejte informace o modulech, které generují chyby a související podmínky.
  
|Zprávy o výjimkách|  
|------------------------|  
|Operace se nezdařila.|  
|Při dokončení operace došlo k chybě: {0}.|  
  

## <a name="error-0040"></a>Chyba 0040  
 Dojde k výjimce při volání metody zastaralý modul.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen při volání metody zastaralý modul.  
  
**Řešení:** Nahraďte zastaralý modul podporován. Naleznete v protokolu modulu výstupní informace o modulu, které místo toho použít.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Přístup k zastaralý modul.|  
|Modul "{0}" je zastaralá. Použití modulu "{1}" místo.|  
 

## <a name="error-0041"></a>Chyba 0041  
 Dojde k výjimce při volání metody zastaralý modul.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen při volání metody zastaralý modul.  
  
**Řešení:** Nahraďte nepoužívané modul sady podporované struktur. Tato informace by měly být viditelné v protokolu výstupu modulu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Přístup k zastaralý modul.|  
|Modul "{0}" je zastaralá. Použijte moduly "{1}" pro požadované funkce.|  
 

## <a name="error-0042"></a>Chyba 0042  
 Pokud není možné převést sloupce na jiný typ, dojde k výjimce.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud není možné převést na zadaný typ sloupce.  Tato chyba bude zobrazit, pokud modul vyžaduje konkrétní datový typ, jako je například datum a čas, text, číslo s plovoucí desetinnou nebo celé číslo, ale není možné převést na požadovaný typ existující sloupec.  
 
Může například vyberte sloupec a zkuste převod na číselný datový typ pro použití v rámci matematické operace a získat tuto chybu, pokud sloupec obsahuje neplatná data. 

Dalším důvodem tato chyba může zobrazit, pokud se pokusíte použít sloupec obsahující čísla s plovoucí desetinnou nebo počet jedinečných hodnot jako sloupec zařazené do kategorií. 
  
**Řešení:**

+ Otevřete stránku nápovědy pro modul, který vytvořil chybu a ověřte požadavky na datové typy.
+ Kontrola datových typů sloupců ve vstupní datové sady.
+ Zkontrolujte, zda datový pocházejícím takzvané daty bez schématu zdroje.
+ Zkontrolujte datovou sadu pro chybějící hodnoty nebo speciální znaky, které mohou blokovat převod na typ požadovaná data. 
    + Číselné datové typy by měly být konzistentní vzhledem k aplikacím: například zkontrolovat pro plovoucí desetinnou čárkou ve sloupci celých čísel.
    + Hledejte text řetězce nebo NA hodnoty v číselné sloupce. 
    + Logické hodnoty lze převést na odpovídající reprezentace v závislosti na typu požadovaná data.
    + Prozkoumejte textové sloupce pro znaky kódování unicode, tabulátory nebo řídicí znaky
    + Data typu DateTime by měl být konzistentní vzhledem k aplikacím, aby modelování chyby, ale vyčištění může být složité kvůli mnoha formátů. Zvažte použití <!--the [Execute R Script](execute-r-script.md) or -->[Spusťte skript Pythonu](execute-python-script.md) moduly pro vyčištění.  
+ V případě potřeby upravte hodnoty ve vstupní datové sady, tak, aby úspěšně je možné převést na sloupec. Úpravy mohou zahrnovat binning, zkrácení nebo zaokrouhlování činnost, úplného oproštění od odlehlé hodnoty nebo sloužil chybějící hodnoty. Naleznete v následujících článcích pro některé běžné scénáře transformace dat ve službě machine learning:
    + [Vyčištění chybějících dat](clean-missing-data.md)
    + [Normalizovat Data](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Rozlišení nejasné, nebo není k dispozici pro váš případ? Určitě můžete odeslat zpětnou vazbu v tomto článku a zadejte informace o scénáři, včetně modulu a typu dat sloupce. Použije tyto informace poskytují podrobnější kroků pro řešení potíží v budoucnu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Převod není povolen.|  
|Nebylo možné převést na sloupec typu {0} pro sloupec typu {1}.|  
|Nebylo možné převést sloupce "{2}" typu {0} pro sloupec typu {1}.|  
|Nebylo možné převést sloupce "{2}" typu {0} na sloupec "{3}" typu {1}.|  
  

## <a name="error-0043"></a>Chyba 0043  
 Výjimka nastane, pokud typ elementu explicitně neimplementuje rovná se.  
  
 Tato chyba ve službě Azure Machine Learning nepoužívá a přestanou používat.  
  
**Řešení:** Žádné  
  
|Zprávy o výjimkách|  
|------------------------|  
|Žádná dostupná metoda explicitní rovná se nenašel.|  
|Nejde porovnat hodnoty pro sloupec \\"{0}\\" typu {1}. Žádná dostupná metoda explicitní rovná se nenašel.|  


## <a name="error-0044"></a>Chyba 0044  
 Pokud není možné odvodit typ elementu sloupce z existující hodnoty, dojde k výjimce.  
  
 Ve službě Azure Machine Learning tato chyba nastane, pokud není možné odvodit typ sloupce nebo sloupců v datové sadě. To obvykle proběhne při zřetězení dvou nebo více datových sad s jiným prvkem typy. Azure Machine Learning je schopen určit společný typ, který může představovat všechny hodnoty ve sloupci nebo sloupcích bez ztráty informací, vygeneruje se této chybě.  
  
**Řešení:** Ujistěte se, že všechny hodnoty v daném sloupci v obou datových sadách, která jsou buď stejného typu (číselné, logická hodnota, kategorií, řetězec, datum atd.) nebo lze převést na stejný typ.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nelze odvodit typ elementu sloupce.|  
|Nejde odvodit typ elementu pro sloupec "{0}"--všechny prvky jsou odkazy s hodnotou null.|  
|Nejde odvodit typ elementu pro sloupec "{0}"z datové sady"{1}"--všechny prvky jsou odkazy s hodnotou null.|  
  

## <a name="error-0045"></a>Chyba 0045  
 Výjimka nastane, pokud není možné vytvořit sloupec z důvodu typy smíšené elementů ve zdroji.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud element typy dvě datové sady, která se liší.  
  
**Řešení:** Ujistěte se, že všechny hodnoty v daném sloupci v obou datových sadách, která jsou stejného typu (číselné, logická hodnota, kategorií, řetězec, datum atd.).  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nejde vytvořit sloupec s typy smíšené prvků.|  
|Nejde vytvořit sloupec s ID "{0}" z elementu smíšené typy: \n\tType dat [{1}, {0}] je {2}\n\tType dat [{3}, {0}] je {4}.|  
  

## <a name="error-0046"></a>Chyba 0046  
 Výjimka nastane, pokud není možné vytvořit adresář na zadané cestě.  
  
 Ve službě Azure Machine Learning tato chyba nastane, pokud není možné k vytvoření adresáře na zadané cestě. Zobrazí se k této chybě a pokud libovolné části cesty k adresáři výstupu dotazu Hive je nesprávná nebo nedostupná.  
  
**Řešení:** Návštěvě modulu a ověřte, že je cesta k adresáři správně naformátován a aby byl přístupný pomocí aktuálních pověření.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zadejte platnou výstupní adresář.|  
|Adresář: {0} nelze vytvořit. Zadejte platnou cestu.|  
  

## <a name="error-0047"></a>Chyba 0047  
 Výjimka nastane, pokud počet sloupců funkce v některé z datové sady, předán modulu je příliš malá.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud vstupní datové sady pro trénování neobsahuje minimální počet sloupců, které jsou vyžadované algoritmus. Obvykle buď datové sady je prázdný nebo obsahuje pouze sloupce školení.  
  
**Řešení:** Návštěvě vstupní datové sady na Ujistěte se, že neexistuje nejmíň jeden další sloupce kromě popisek sloupce.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Počet sloupců funkce v vstupní datová sada je menší než povolené minimum.|  
|Počet sloupců funkce ve vstupní datová sada je menší než povolené minimum {0} sloupce.|  
|Počet sloupců funkce ve vstupní datové sady "{0}" je menší než povolené minimum {1} sloupce.|  
  

## <a name="error-0048"></a>Chyba 0048  
 V případě dojde k výjimce, když není možné otevřít soubor.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud není možné otevřít soubor pro čtení nebo zápisu. K této chybě může dojít z těchto důvodů:  
  
-   Kontejner nebo soubor (objektů blob) neexistuje  
  
-   Úroveň přístupu k souboru nebo kontejneru neumožňuje přístup k souboru  
  
-   Soubor je příliš velký pro čtení nebo má nesprávný formát  
  
**Řešení:** Návštěvě modulu a soubor, který se pokoušíte načíst.  
  
 Ověřte správnost názvu kontejneru a soubor.  
  
 Ověřte, že máte oprávnění k přístupu k souboru pomocí portálu Azure classic nebo nástroj pro Azure storage.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Zprávy o výjimkách|  
|------------------------|  
|Nelze otevřít soubor.|  
|Při otevírání souboru došlo k chybě: {0}.|  


## <a name="error-0049"></a>Chyba 0049  
 V případě dojde k výjimce, když není možné analyzovat soubor.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud není možné analyzovat soubor. Zobrazí se tato chybová zpráva Pokud vybraný formát souboru [Import dat](import-data.md) modulu neodpovídá skutečné formát souboru, nebo pokud soubor obsahuje znak nelze rozpoznat.  
  
**Řešení:** Návštěvě modulu a opravte výběr formátu souboru, pokud neodpovídá formátu souboru. Pokud je to možné zkontrolujte soubor potvrďte, že neobsahuje žádné neplatné znaky.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepovedlo se parsovat soubor.|  
|Při analýze souboru došlo k chybě: {0}.|  
  

## <a name="error-0050"></a>Chyba 0050  
 Došlo k výjimce v případě, kdy vstupní a výstupní soubory jsou stejné.  
  
**Řešení:** Tato chyba ve službě Azure Machine Learning nepoužívá a přestanou používat.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zadané soubory pro vstup a výstup nemůže být stejné.|


## <a name="error-0051"></a>Chyba 0051  
 V případě dojde k výjimce při několika výstupní soubory jsou stejné.  
  
**Řešení:** Tato chyba ve službě Azure Machine Learning nepoužívá a přestanou používat.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zadané soubory pro výstupy nemůže být stejné.|


## <a name="error-0052"></a>Chyba 0052  
 Výjimka nastane, pokud klíč účtu úložiště Azure je nesprávně zadán.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud je nesprávný klíč pro přístup k účtu úložiště Azure. Například může se zobrazit tato chyba Pokud klíč služby Azure storage byla zkrácena, když zkopírovali a vložili, nebo pokud byl použit nesprávný klíč.  
  
 Další informace o tom, jak získat klíč pro účet úložiště Azure najdete v tématu [zobrazení, kopírování a znovu vygenerovat přístupové klíče úložiště](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Řešení:** Návštěvě modulu a ověřte správnost klíče úložiště Azure pro účet Zkopírujte klíč znovu z portálu Azure classic v případě potřeby.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Klíč účtu úložiště Azure je nesprávný.|  
  

## <a name="error-0053"></a>Chyba 0053  
 V případě dojde k výjimce, pokud žádné uživatele funkcí nebo položky matchbox doporučení.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud funkce vector nebyl nalezen.  
  
**Řešení:** Ujistěte se, že funkce vektor je k dispozici ve vstupní sadě.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Uživatelské funkce nebo / a položky jsou povinné, ale není k dispozici.|  

## <a name="error-0054"></a>Chyba 0054  
 Výjimka nastane, pokud existuje příliš málo jedinečných hodnot ve sloupci pro dokončení operace.  
  
**Řešení:** Tato chyba ve službě Azure Machine Learning nepoužívá a přestanou používat.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Data obsahují příliš málo jedinečných hodnot v zadaném sloupci k dokončení operace.|  
|Data obsahují příliš málo jedinečných hodnot v zadaném sloupci k dokončení operace. Požadované minimum je {0} elementy.|  
|Data obsahují příliš málo jedinečných hodnot ve sloupci "{1}" pro dokončení operace. Požadované minimum je {0} elementy.|  
  

## <a name="error-0055"></a>Chyba 0055  
 Dojde k výjimce při volání metody zastaralý modul.  Tato chyba ve službě Azure Machine Learning se zobrazí, pokud se pokusíte volat modul, který se už nepoužívá.
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Přístup k zastaralý modul.|  
|Modul "{0}" je zastaralá.|  

## <a name="error-0056"></a>Chyba 0056  
 Pokud jste vybrali pro operaci sloupce v rozporu s požadavky, dojde k výjimce.  
  
 Tato chyba ve službě Azure Machine Learning nastane při výběru sloupce pro operace, která vyžaduje sloupec být konkrétní datového typu. 
 
 K této chybě může dojít, pokud je správný datový typ. sloupec, ale modul, který používáte, vyžaduje, že sloupec být také označen jako funkce, popisek nebo kategorií sloupce.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Řešení:**
  
1.  Zkontrolujte datový typ sloupce, které jsou aktuálně vybrány. 

2. Ověřit, jestli jsou vybrané sloupce zařazené do kategorií, popisek nebo sloupce.  
  
3.  Přečtěte si téma nápovědy pro modul, ve které jste provedli výběr sloupce, chcete-li zjistit, zda jsou konkrétní požadavky pro použití dat typu nebo sloupec.  
  
3.  Použití [upravit Metadata](edit-metadata.md) změnit typ sloupce pro dobu trvání této operace. Nezapomeňte změnit typ sloupce zpět na původní hodnotu, pomocí jiné instance systému [upravit Metadata](edit-metadata.md), pokud ho budete potřebovat pro příjem dat operace.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Jeden nebo více vybraných sloupcích nebyly v kategorii povolené.|  
|Sloupec s názvem "{0}" není v povolených kategorii.|  
  

## <a name="error-0057"></a>Chyba 0057  
 Při pokusu o vytvoření souboru nebo objekt blob, který již existuje, dojde k výjimce.  
  
 Tato výjimka nastane, pokud používáte [exportovat Data](export-data.md) modulu nebo jiných modulu, který chcete uložit výsledky experimentu v Azure Machine Learning do úložiště objektů blob v Azure, ale pokus o vytvoření souboru nebo objekt blob, který již existuje.   
  
**Řešení:**
 
 Zobrazí se tato chybová zpráva pouze v případě, že jste nastavili vlastnost **režimu zápisu do úložiště objektů blob v Azure** k **chyba**. Záměrné tento modul vyvolá chybu, pokud se pokusíte zapsat datovou sadu do objektu blob, který již existuje.
 
 - Otevřete vlastnosti modulu a změňte vlastnost **režimu zápisu do úložiště objektů blob v Azure** k **přepsat**.
 - Můžete případně zadejte název souboru nebo jiné cílové objektů blob a nezapomeňte zadat objekt blob, který ještě neexistuje.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Soubor nebo objekt Blob již existuje.|  
|Soubor nebo objekt Blob "{0}" již existuje.|  
  

## <a name="error-0058"></a>Chyba 0058  
 Pokud datová sada neobsahuje sloupec očekával se argument label dojde k této chybě ve službě Azure Machine Learning.  
  
 Tato výjimka může také dojít, když k dispozici popisek sloupce se neshoduje s daty nebo s datový typ, očekávání tím, Student nebo má nesprávné hodnoty. Například tato výjimka je vytvořen při použití s hodnotou reálném popisek sloupce při cvičení binární třídění.  
  
**Řešení:** Řešení závisí na Student nebo trainer, kterou používáte a datové typy sloupců v datové sadě. Nejprve ověřte požadavky algoritmu strojového učení nebo modulu školení.  
  
 Návštěvě vstupní datové sady. Ověřte, že sloupec očekáváte, bude považovat za popisek má správný datový typ modelu, který vytváříte.  
  
 Zkontrolujte vstupy pro chybějící hodnoty a eliminovat nebo je v případě potřeby nahradit.  
  
 V případě potřeby přidat [upravit Metadata](edit-metadata.md) modulu a ujistěte se, že popisek sloupec je označen jako popisek.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Popisek sloupce není podle očekávání|  
|Popisek sloupce není podle očekávání v "{0}".|  
|Popisek sloupce "{0}"neočekává v"{1}".|  
  

## <a name="error-0059"></a>Chyba 0059  
 Výjimka nastane, pokud sloupec index zadaný ve vlastnosti ovládacího prvku pro výběr sloupce nelze analyzovat.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud index sloupce zadané při použití selektor sloupců nelze analyzovat.  Zobrazí se tato chyba a při index sloupce je v neplatném formátu, který nelze analyzovat.  
  
**Řešení:** Upravte index sloupce, který chcete použít hodnotu s platný index.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Jeden nebo více indexů zadaný sloupec nebo rozsahy indexu se nepovedlo parsovat.|  
|Index sloupce nebo rozsah "{0}" nelze analyzovat.|  
  

## <a name="error-0060"></a>Chyba 0060  
 Výjimka nastane, pokud je mimo rozsah sloupec rozsah zadaný v ovládacího prvku pro výběr sloupce.  
  
 Tato chyba ve službě Azure Machine Learning nastane v selektoru sloupců je určen rozsah sloupce mimo rozsah. Obdržíte tuto chybu rozsahu sloupce ve výběru sloupců neodpovídá sloupců v datové sadě.  
  
**Řešení:** Upravte rozsah sloupec výběr sloupce tak, aby odpovídaly sloupců v datové sadě.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Neplatný nebo je mimo rozsah sloupce indexu rozsah zadaný.|  
|Rozsah sloupec "{0}" je neplatný nebo je mimo rozsah.|  
  

## <a name="error-0061"></a>Chyba 0061  
 Při pokusu o přidání řádku do objektu DataTable, který má jiný počet sloupců, než tabulka dojde k výjimce.  
  
 Při pokusu o přidání řádku do datové sady, který má jiný počet sloupců, než je objekt dataset dojde k této chybě ve službě Azure Machine Learning.  Obdržíte této chyby na řádek, který se přidává do datové sady má jiný počet sloupců, než vstupní datové sady.  Řádek nejde připojit k datové sadě, pokud počet sloupců se liší.  
  
**Řešení:** Změnit vstupní datová sada mít stejný počet sloupců jako řádek přidat nebo upravit řádky přidané mít stejný počet sloupců jako datovou sadu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Všechny tabulky musí mít stejný počet sloupců.|  
  

## <a name="error-0062"></a>Chyba 0062  
 Při pokusu o porovnání dvou modelů vedle s typy různých learner dojde k výjimce.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud nelze porovnat metrik pro dvě různé Vyhodnocená datové sady. V takovém případě není možné porovnat efektivitu modely použité k vytvoření dvou Vyhodnocená datové sady.  
  
**Řešení:** Ověřte, že skóre výsledky jsou vytvářeny společný model strojového učení (binární klasifikace, regrese, klasifikace roc, doporučení, clustering, detekce anomálií, atd.) Všechny modely, které můžete porovnat musí mít stejný typ learner.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Všechny modely musí mít stejný typ learner.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the experiment to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the experiment.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to experiments in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Zprávy o výjimkách|  
|------------------------|  
|Při vyhodnocení skriptu R došlo k chybě.|  
|Během vyhodnocení skriptu R došlo k následující chybě:---začátek chybová zpráva z R--- {0} ---konec chybová zpráva z R---|  
|Během vyhodnocování skript jazyka R "{1}" došlo k následující chybě:---začátek chybová zpráva z R--- {0} ---konec chybová zpráva z R---|  
  


## <a name="error-0064"></a>Chyba 0064  
 Výjimka nastane, pokud je nesprávně zadán název účtu úložiště Azure nebo klíč úložiště.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud je nesprávně zadán název účtu úložiště Azure nebo klíč úložiště. Zobrazí se tato chyba a pokud jste zadali jste nesprávný účet jméno nebo heslo pro účet úložiště. To může dojít, pokud je ručně zadat název účtu nebo heslo. Může také dojít, pokud byl účet odstraněn.  
  
**Řešení:** Ověřte, že název účtu a heslo správně zadaný a že účet existuje.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Název účtu úložiště Azure nebo klíč úložiště jsou nesprávné.|  
|Název účtu úložiště Azure "{0}", nebo klíč úložiště pro název účtu je nesprávné.|  
  

## <a name="error-0065"></a>Chyba 0065  
 Výjimka nastane, pokud je nesprávně zadán název objektu blob Azure.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud je nesprávně zadán název objektu blob Azure.  Pokud se zobrazí chyba:  
  
-   Objekt blob se nenašel v zadaném kontejneru.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Pouze kontejneru byl zadán jako zdroj v [Import dat](import-data.md) žádosti při formátu aplikace Excel nebo sdíleného svazku clusteru s kódováním; zřetězení obsah všech objektů BLOB v kontejneru není povolen u těchto formátů.  
  
-   Identifikátor URI SAS neobsahuje název platný objekt blob.  
  
**Řešení:** Návštěvě modulu, která vyvolává výjimku. Ověřte, že se zadaný objekt blob v kontejneru v účtu úložiště a že oprávnění umožňují zobrazit objektu blob. Ověřte, zda vstup formuláře **containername/filename** Pokud máte s kódovacích formátech, Excelu nebo CSV. Ověřte, že identifikátor URI SAS obsahuje název platný objekt blob.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Azure storage blobu není správný.|  
|Název objektu blob úložiště Azure "{0}" je nesprávný|  
  

## <a name="error-0066"></a>Chyba 0066  
 Pokud prostředek se nepovedlo nahrát do Azure Blob, dojde k výjimce.  
  
 Pokud prostředek se nepovedlo nahrát do Azure Blob dojde k této chybě ve službě Azure Machine Learning.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Obojí se uloží do stejného účtu úložiště Azure jako účet, který obsahuje vstupní soubor.  
  
**Řešení:** Návštěvě modulu. Ověřte, že název účtu Azure, klíč úložiště a kontejneru jsou správné a zda má účet oprávnění k zápisu do kontejneru.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Prostředek nešlo nahrát do služby Azure storage.|  
|Soubor "{0}" nebylo možné nahrát do Azure storage jako {1}.|  
  

## <a name="error-0067"></a>Chyba 0067  
 Pokud datová sada obsahuje jiný počet sloupců, než se očekávalo, dojde k výjimce.  
  
 Pokud datová sada obsahuje jiný počet sloupců, než se očekávalo dojde k této chybě ve službě Azure Machine Learning.  Tato chyba bude zobrazit, když počet sloupců v datové sadě se liší od počtu sloupců, které modul očekává, že během provádění.  
  
**Řešení:** Změnit vstupní datové sady nebo parametry.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Neočekávaný počet sloupců v objektu datatable.|  
|Byl očekáván "{0}"sloupců ale nalezeno"{1}" sloupců místo.|  
  

## <a name="error-0068"></a>Chyba 0068  
 Výjimka nastane, pokud zadaný skript Hive není správný.  
  
 Pokud existují chyby syntaxe skriptu Hive QL, nebo pokud překladač Hive dojde k chybě při provádění dotazu nebo skriptu dojde k této chybě ve službě Azure Machine Learning.  
  
**Řešení:**

Chybová zpráva z Hive je obvykle hlášeny v protokolu chyb tak, aby mohli podniknout kroky podle konkrétní chyba. 

+ Otevřete modul a zkontrolujte dotaz na chyby.  
+ Ověřte, zda dotaz funguje správně mimo Azure Machine Learning pomocí přihlášení do konzoly Hive pro Hadoop cluster a spouštění dotazu.  
+ Pokuste se umístit komentáře ve skriptu Hive v samostatném řádku, na rozdíl od kombinace spustitelné příkazy a komentáře na jednom řádku.  

### <a name="resources"></a>Zdroje a prostředky

Naleznete v následujících článcích pro pomoc s dotazy Hive pro machine learning:

+ [Vytváření tabulek Hive a načtení dat z úložiště objektů Blob v Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Zkoumání dat v tabulkách pomocí dotazů Hivu](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Vytvoření funkcí pro data v clusteru Hadoop pomocí dotazů Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive for SQL Users Cheat Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Zprávy o výjimkách|  
|------------------------|  
|Skript Hive je nesprávný.|  
|Skript podregistru {0} není správný.|  
  

## <a name="error-0069"></a>Chyba 0069  
 Výjimka nastane, pokud zadaný skript SQL není správný.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud zadaný skript SQL má problémy syntaxe, nebo pokud sloupce nebo tabulky zadané ve skriptu je neplatný. 
 
 Pokud modul SQL zaznamená všechny chyby při provádění dotazu nebo skript dostanete k této chybě. Chybová zpráva SQL je obvykle hlášeny v protokolu chyb tak, aby mohli podniknout kroky podle konkrétní chyba.  
  
**Řešení:** Návštěvě modulu a zkontrolovat dotaz SQL pro chyby.  
  
 Ověřte, zda dotaz funguje správně mimo Azure ML přímo přihlášení k databázovému serveru a spuštěním dotazu.  
  
 Pokud je SQL, které jsou generovány zpráv hlášených výjimek modulu, provádějte akci podle oznámenou chybu. Chybové zprávy v některých případech například konkrétní pokyny pravděpodobně chyba:
+ *Žádný odpovídající sloupec nebo chybějící databáze*, která udává, že může být zadali jste nesprávný název sloupce. Pokud jste si jistí, že je správný název sloupce, zkuste pomocí závorek nebo použijte sloupec identifier.
+ *Logická chyba SQL téměř \<SQL – klíčové slovo\>* , označující, že bude pravděpodobně chyba syntaxe před zadané klíčové slovo

  
|Zprávy o výjimkách|  
|------------------------|  
|Skript jazyka SQL není správný.|  
|Příkaz jazyka SQL "{0}" není správný.|  
|Příkaz jazyka SQL "{0}" není správná: {1}|  
  

## <a name="error-0070"></a>Chyba 0070  
 Při pokusu o přístup k neexistující tabulek v Azure dojde k výjimce.  
  
 Při pokusu o přístup k neexistující tabulce Azure dojde k této chybě ve službě Azure Machine Learning. Zobrazí se k této chybě a pokud zadáte tabulku ve službě Azure storage, který neexistuje při čtení nebo zápisu do služby Azure Table Storage. To může nastat, pokud napíšete název požadovanou tabulku, nebo už využíváte nesoulad mezi cílový název a typ úložiště. Například určený ke čtení z tabulky, ale místo toho zadat název objektu blob.  
  
**Řešení:** Návštěvě modulu ověřte správnost názvu tabulky.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Tabulka Azure neexistuje.|  
|Tabulka Azure "{0}" neexistuje.|  
  
## <a name="error-0071"></a>Chyba 0071  
 Výjimka nastane, pokud za předpokladu, že přihlašovací údaje jsou nesprávné.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud zadané přihlašovací údaje jsou nesprávné.  
  
 Tato chyba může také zobrazit, pokud modul se nemůže připojit ke clusteru HDInsight.  
  
**Řešení:** Ve vstupech do modulu a ověřte název účtu a heslo.  
  
 Zkontrolujte následující problémy, které může způsobit chybu:  
  
-   Schéma datové sady, která neodpovídá schématu cílové objektu DataTable.  
  
-   Názvy sloupců jsou chybějící nebo chybně  
  
-   Psaní do tabulky obsahující názvy sloupců s obsahuje neplatné znaky. Obvykle můžete uzavřít tyto názvy sloupců v hranatých závorkách, ale pokud to nepomůže, upravte názvy sloupců, aby použijte jenom písmena a podtržítka (_)  
  
-   Řetězce, které se pokoušíte zápisu obsahovat jednoduché uvozovky  
  
 Pokud se pokoušíte připojit ke clusteru služby HDInsight, ověřte, že cílový cluster je přístupná pomocí zadané přihlašovací údaje.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Jsou předány nesprávné přihlašovací údaje.|  
|Nesprávné uživatelské jméno "{0}" nebo heslo je předán.|  
  

## <a name="error-0072"></a>Chyba 0072  
 V případě vypršení časového limitu připojení dojde k výjimce.  
  
 Ve službě Azure Machine Learning tato chyba nastane, pokud připojení vyprší časový limit. Zobrazí se tato chyba a pokud aktuálně nejsou potíže s připojením zdroje dat nebo cíl, jako je například pomalá připojení k Internetu, nebo pokud se velké datové sady a/nebo jazyka SQL pro čtení dat provádí složité zpracování.  
  
**Řešení:** Určete, jestli neexistují problémy s pomalým síťovým připojením k úložišti Azure nebo k Internetu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Došlo k vypršení časového limitu připojení.|  
  

## <a name="error-0073"></a>Chyba 0073  
 Pokud dojde k chybě při převodu sloupec na jiný typ, dojde k výjimce.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud není možné převést na jiný typ sloupce.  Tato chyba bude zobrazit, pokud modul vyžaduje určitý typ a není možné převést sloupce na nový typ.  
  
**Řešení:** Vstupní datová sada upravte tak, že sloupec lze převést na základě v popisu vnitřní výjimky.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepovedlo se převést sloupce.|  
|Nepovedlo se převést sloupce na {0}.|  
  

## <a name="error-0074"></a>Chyba 0074  
 Došlo k výjimce při [upravit Metadata](edit-metadata.md) pokusí převést sadu zhuštěných sloupců na zařazené do kategorií.  
  
 Ve službě Azure Machine Learning k této chybě dochází při [upravit Metadata](edit-metadata.md) pokusí převést sadu zhuštěných sloupců na zařazené do kategorií.  Tato chyba se zobrazí při pokusu o převod zhuštěné sloupce do kategorií pomocí **zkontrolujte zařazené do kategorií** možnost.  Azure machine Learning nepodporuje řídká pole kategorií, takže dojde k selhání modulu.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Zprávy o výjimkách|  
|------------------------|  
|Sadu zhuštěných sloupců nelze převést na zařazené do kategorií.|  
  

## <a name="error-0075"></a>Chyba 0075  
Při použití neplatné funkce binningu při quantizing datové sady dojde k výjimce.  
  
Pokud se do adresáře bin data pomocí nepodporované metody, nebo když jsou neplatné kombinace parametrů dojde k této chybě ve službě Azure Machine Learning.  
  
**Řešení:**

Zpracování chyb pro tuto událost byla zavedena ve starší verzi služby Azure Machine Learning, která další přizpůsobení binning metody. Aktuálně binningu všechny metody jsou založeny na výběr z rozevíracího seznamu, proto technicky že by měl být již možné se tato chyba.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Zprávy o výjimkách|  
|------------------------|  
|Neplatný binningu funkce použité.|  
  

## <a name="error-0077"></a>Chyba 0077  
 Když soubor neznámý objekt blob zapíše režimu předaný dojde k výjimce.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud je předán neplatný argument specifikace zdrojového a cílového souboru objektu blob.  
  
**Řešení:** V téměř všechny moduly, které import a export dat do a z úložiště objektů blob v Azure jsou přiřazeny hodnoty parametrů řízení režimu zápisu pomocí rozevíracího seznamu; proto není možné předat neplatnou hodnotu a neměla by se zobrazit tato chyba. Tato chyba se přestanou používat v novější verzi.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepodporovaný objekt blob zapíše režimu.|  
|Nepodporovaný objekt blob zapíše režimu: {0}.|  
  

## <a name="error-0078"></a>Chyba 0078  
 Došlo k výjimce při možnost HTTP [Import dat](import-data.md) obdrží 3xx stavový kód označující přesměrování.  
  
 Ve službě Azure Machine Learning k této chybě dochází při možnost HTTP [Import dat](import-data.md) obdrží 3xx (301, 302, 304, atd.) stavový kód označující přesměrování. Zobrazí se tato chyba a při pokusu připojit se k HTTP zdroji, který prohlížeč přesměruje na jinou stránku. Pro zabezpečení z důvodu přesměrování websites nejsou povoleny jako zdroje dat pro Azure Machine Learning.  
  
**Řešení:** Pokud webová stránka se důvěryhodné webu, zadejte adresu URL přesměrovaného přímo.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Není povoleno přesměrování protokolu HTTP|  
  

## <a name="error-0079"></a>Chyba 0079  
 Výjimka nastane, pokud je nesprávně zadán název kontejneru úložiště Azure.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud je nesprávně zadán název kontejneru úložiště Azure. Zobrazí se tato chybová zpráva v případě špatných kontejneru a názvem pomocí objektů blob (soubor) **cestu k začátku s kontejnerem objektů blob** možnost při zápisu do úložiště objektů Blob v Azure.  
  
**Řešení:** Opakování [exportovat Data](export-data.md) modulu a ověřte, zda zadaná cesta k objektu blob obsahuje kontejneru a název souboru ve formátu **kontejneru/filename**.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Název kontejneru úložiště Azure je nesprávný.|  
|Název kontejneru úložiště Azure "{0}" není správné; byl očekáván název kontejneru objektu blob nebo kontejneru formátu.|  
  

## <a name="error-0080"></a>Chyba 0080  
 Sloupec se všemi hodnotami chybí není povolen modul dojde k výjimce.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud jeden nebo více sloupců využité modulem obsahuje všechny chybějící hodnoty. Například pokud modul je computing souhrnné statistiky pro každý sloupec, nemůže pracovat na sloupec obsahující žádná data. V takovém případě zastavení spuštění modulu s touto výjimkou.  
  
**Řešení:** Návštěvě vstupní datové sady a odeberte všechny sloupce, které obsahují všechny chybějící hodnoty.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nejsou povoleny sloupce s všechny hodnoty chybí.|  
|Sloupec {0} obsahuje všechny hodnoty chybí.|  
  

## <a name="error-0081"></a>Chyba 0081  
 Pokud počet rozměrů pro je rovna počtu sloupců funkce ve vstupní datové sady, který obsahuje alespoň jeden sloupec funkce řídký, dojde k výjimce v modulu DPS.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud jsou splněny následující podmínky: (a) se vstupní datová sada obsahuje alespoň jednu sadu zhuštěných sloupců a (b) konečný počet rozměrů požadovaný je stejný jako počet vstupních dimenzí.  
  
**Řešení:** Zvažte snížení počtu dimenzí v výstupem bude menší než počet rozměrů ve vstupu. To je typické u aplikací DPS.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Zprávy o výjimkách|  
|------------------------|  
|Pro datovou sadu obsahující sloupce funkce řídký počet dimenzí ke snížení by měl být menší než počet sloupců funkce.|  
 

## <a name="error-0082"></a>Chyba 0082  
 Model nelze úspěšně deserializovat dojde k výjimce.  
  
 Tato chyba v Azure Machine Learning nastane uložené model strojového učení nebo transformace nelze načíst pomocí novější verze modulu runtime Azure Machine Learning v důsledku zásadní změnu.  
  
**Řešení:** Výukový experiment, který vytvořil modelu nebo transformace musí být znovu spustit a modelu nebo musí být resaved transformace.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Model nelze deserializovat, protože je pravděpodobně serializovat s příznakem ve starším formátu serializace. Přetrénujete a znovu uložit model.|  
  

## <a name="error-0083"></a>Chyba 0083  
 Pokud datová sada používá pro školení nelze použít pro konkrétní typ learner dojde k výjimce.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud je tato datová sada je nekompatibilní s learner se školení. Například datová sada může obsahovat alespoň jeden chybí hodnota v každém řádku a v důsledku toho by při školení vynechána celá datová sada. V ostatních případech některé algoritmů strojového učení jako je detekce anomálií Nečekejte, aby byly k dispozici a může vyvolat tuto výjimku, pokud popisky jsou k dispozici v datové sadě.  
  
**Řešení:** V dokumentaci student používá ke kontrole požadavků pro vstupní datovou sadu. Prozkoumejte sloupce, které chcete zjistit všechny požadované sloupce jsou k dispozici.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Datové sady používané pro vzdělávání je neplatný.|  
|{0} obsahuje neplatná data pro vzdělávání.|  
|{0} obsahuje neplatná data pro vzdělávání. Typ learner: {1}.|  
  

## <a name="error-0084"></a>Chyba 0084  
 Při hodnocení vytvořenými skript R jsou vyhodnocovány, dojde k výjimce. To není aktuálně podporováno.  
  
 Pokud se pokusíte použít jeden z modulů za vaše rozhodnutí vyzkoušet model se výstup ze skriptu R, který obsahuje skóre dojde k této chybě ve službě Azure Machine Learning.  
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Vyhodnocení skóre, které jsou vytvořené pomocí jazyka R se momentálně nepodporuje.|  
  

## <a name="error-0085"></a>Error 0085  
 Při vyhodnocení skriptů se nezdaří s chybou, dojde k výjimce.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud používáte vlastní skript, který obsahuje chyby syntaxe.  
  
**Řešení:** Zkontrolujte kód v externím editoru a zkontrolujte chyby.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Při vyhodnocení skriptu došlo k chybě.|  
|Došlo k následující chybě během vyhodnocení skriptů, zobrazit výstup protokolu pro další informace:---začátek chybová zpráva z {0} překladač--- {1} ---konec chybová zpráva z {0} překladač--- ------|  
  

## <a name="error-0086"></a>Chyba 0086  
 Dojde k výjimce při počítání transformace je neplatná.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud vyberete transformace na základě počtu tabulky, ale je nekompatibilní s aktuálními daty nebo s novou tabulku počet vybraných transformace.  
  
**Řešení:** Modul podporuje ukládání počty a pravidla, která tvoří transformace ve dvou různých formátech. Pokud počet tabulek, které provádíte sloučení, ověřte, že obě tabulky, kterou chcete sloučit používají stejný formát.  
  
Obecně platí transformace na základě počtu může používat jedině pro datové sady, které mají stejné schéma jako datovou sadu původně vytvořil transformace.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Zprávy o výjimkách|  
|------------------------|  
|Zadán neplatný počítání transformace.|  
|Počítání transformace na vstupním portem "{0}' je neplatný.|  
|Počítání transformace na vstupním portem "{0}"nejde sloučit s počítání transformace na vstupním portem"{1}". Kontrolujte, abyste ověřili metadata používaná pro počítání shody.|  
  

## <a name="error-0087"></a>Chyba 0087  
 Pokud je zadán typ tabulky neplatný počet pro učení s moduly počty dojde k výjimce.  
  
 Při pokusu o import existující tabulky count, ale v tabulce je nekompatibilní s aktuálními daty nebo s novou tabulku počet dojde k této chybě ve službě Azure Machine Learning.  
  
**Řešení:** Existují různé formáty pro ukládání počty a pravidla, která tvoří transformace. Pokud počet tabulek, které provádíte sloučení, ověřte, že obě používají stejný formát.  
  
 Obecně platí transformace na základě počtu může používat jedině pro datové sady, které mají stejné schéma jako datovou sadu původně vytvořil transformace.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Chyba 0088  
 Dojde k výjimce při neplatný počítání, že typ je určen pro učení s počty moduly.  
  
 Tato ve službě Azure Machine Learning dojde k chybě při pokusu použít jinou metodu inventur, než se podporuje pro snadné na základě počtu.  
  
**Řešení:** Obecně platí počítání metody je možné zvolit rozevírací seznam, takže by se neměly zobrazovat tato chyba.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Zprávy o výjimkách|  
|------------------------|  
|Je zadán neplatný typ inventury.|  
|Zadaný typ počítání "{0}' není platný typ počítání.|  
  

## <a name="error-0089"></a>Chyba 0089  
 Zadaný počet tříd je menší než je skutečný počet tříd v datové sadě používané pro počítání dojde k výjimce.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud při vytváření tabulky count a popisek sloupce obsahuje odlišný počet tříd, než kterou jste zadali v parametry modulu.  
  
**Řešení:** Zkontrolujte vaše datová sada a zjistěte přesně počet jedinečných hodnot (je to možné třídy) existují ve sloupci popisek. Při vytváření tabulky count, je potřeba určit nejmíň tento počet tříd.  
  
 Počet tabulek nelze automaticky určit počet tříd, které jsou k dispozici.  
  
 Při vytváření tabulky počet nelze zadat 0 nebo jakékoli číslo, které je menší než je skutečný počet tříd ve sloupci popisek.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Počet tříd je nesprávný. Ujistěte se, že počet tříd, které zadáte v podokně parametr je větší než nebo rovno počtu instancí třídy ve sloupci popisek.|  
|Počet tříd uvedených je "{0}", která není větší než hodnota popisku"{1}" v sadě dat použít k počítání. Ujistěte se, že počet tříd, které zadáte v podokně parametr je větší než nebo rovno počtu instancí třídy ve sloupci popisek.|  
  

## <a name="error-0090"></a>Chyba 0090  
 Selhání vytvoření tabulky Hive, dojde k výjimce.  
  
 Při použití dojde k této chybě ve službě Azure Machine Learning [exportovat Data](export-data.md) nebo nelze vytvořit jinou možnost ukládání dat do clusteru služby HDInsight a zadané tabulky Hive.  
  
**Řešení:** Zkontrolujte název účtu úložiště Azure, přidružené ke clusteru a ověřte, že používáte stejný účet ve vlastnostech modulu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nebylo možné vytvořit tabulku Hive. Pro cluster HDInsight ověřte název účtu služby Azure storage související s clusterem je stejný jako co se předává v parametru module.|  
|Tabulka Hive "{0}" nebylo možné vytvořit. Pro cluster HDInsight ověřte název účtu služby Azure storage související s clusterem je stejný jako co se předává v parametru module.|  
|Tabulka Hive "{0}" nebylo možné vytvořit. Pro HDInsight cluster, ověřte název účtu služby Azure storage související s clusterem je "{1}".|  
 

## <a name="error-0100"></a>Chyba 0100  
 Pokud je zadán nepodporovaný jazyk pro vlastní modul dojde k výjimce.  
  
 Při vytváření vlastní modul a vlastnost name atributu dojde k této chybě ve službě Azure Machine Learning **jazyk** element v souboru definice xml vlastního modulu má neplatnou hodnotu. V současné době je jedinou platnou hodnotou pro tuto vlastnost `R`. Příklad:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Řešení:** Ověřte, že vlastnost name atributu **jazyk** element v souboru definice xml vlastní modul je nastaven na `R`. Uložte soubor, aktualizovat balíček zip vlastní modul a zkuste to znovu přidat vlastní modul.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zadaný jazyk nepodporované vlastního modulu|  
  

## <a name="error-0101"></a>Chyba 0101  
 Všechna čísla portu a parametr ID musí být jedinečný.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud jeden nebo více portů nebo parametry jsou přiřazena stejná hodnota ID v souboru definice XML pro vlastní modul.  
  
**Řešení:** Zkontrolujte, zda jsou jedinečná ID hodnoty na všech portech a parametry. Soubor xml uložte, aktualizujte balíček zip vlastního modulu a zkuste to znovu přidat vlastní modul.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Všechny portu a parametr ID pro modul musí být jedinečné|  
|Modul '{0}"má duplicitní port/argument ID. Všechny identifikátory port/argument musí být jedinečný pro modul.|  
  

## <a name="error-0102"></a>Chyba 0102  
 Vyvolána, když není možné extrahovat soubor ZIP.  
  
 K této chybě ve službě Azure Machine Learning dochází při importování komprimovaný balíček s příponou .zip, ale balíček je buď není soubor zip nebo soubor nepoužívá formátu zip podporované.  
  
**Řešení:** Ujistěte se, že vybraný soubor je soubor ZIP platný a že byla komprimována pomocí jedné z podporovaných komprese algoritmy.  
  
 Pokud k této chybě dojde při importu datových sad v komprimovaném formátu, ověřte, že všechny soubory použijte jednu z podporovaných formátů a jsou ve formátu Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Zkuste to znovu přidat požadované soubory do nového komprimované složky ZIP a zkuste to znovu přidat vlastního modulu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zadaný ZIP soubor není ve správném formátu|  


## <a name="error-0103"></a>Chyba 0103  
 Vyvolána, když soubor ZIP obsahuje všechny soubory XML  
  
 Tato chyba ve službě Azure Machine Learning nastane zazipovaný balíček s vlastní modul neobsahuje žádné soubory modulu definice (.xml). Tyto soubory musí být umístěn v kořenové složce zip balíčku (například není v rozsahu podsložku.)  
  
**Řešení:** Ověřte, že jeden nebo více souborů definice xml modulu v kořenové složce balíčku zip extrahováním do dočasné složky na pevném disku. Všechny soubory xml by měly být přímo ve složce, které jste extrahovali balíček zip. Ujistěte se, že když vytvoříte balíček zip, který vyberete složku, která obsahuje soubory xml se komprimovat, protože tím vytvoříte dílčí složky v rámci zazipovaný balíček s názvem, složky, kterou jste vybrali se komprimovat.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zadaný ZIP soubor neobsahuje žádné soubory definice modulu (soubory .xml)|  


## <a name="error-0104"></a>Chyba 0104  
 Vyvolána, když odkazuje skript, který nejde najít soubor definice modulu  
  
 Tato chyba ve službě Azure Machine Learning je vyvolána, když vlastní modul definiční soubor xml odkazuje na soubor skriptu v **jazyk** element, který neexistuje v zip balíčku. Cesta k souboru skriptu je definován v **zdrojový soubor** vlastnost **jazyk** elementu. Cesta ke zdrojovému souboru je relativní vůči kořenu zip balíčku (stejné umístění jako soubory xml definice modulu). Pokud je soubor skriptu v dílčí složce, musí zadat relativní cestu k souboru skriptu. Například pokud všechny skripty byly uloženy v **myScripts** složky, které obsahuje balíček zip **jazyk** element bude muset přidat tuto cestu do **zdrojový soubor** vlastnost jako níže. Příklad:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Řešení:** Ujistěte se, že hodnota **zdrojový soubor** vlastnost **jazyk** – element xml definice vlastního modulu je správný a zda zdrojový soubor existuje ve správné relativní cestu v zazipovaný balíček.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Odkazovaný soubor skriptu jazyka R neexistuje.|  
|Odkazovaný soubor skriptu jazyka R "{0}' nebyl nalezen. Ujistěte se, že je relativní cesta k souboru správná z umístění definice.|  


## <a name="error-0105"></a>Chyba 0105  
 Tato chyba se zobrazí, když soubor definice modulu obsahuje nepodporovaný typ parametru  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen při vytvoření definice xml vlastního modulu a typu parametru nebo argument v definici neodpovídá podporovaným typem.  
  
**Řešení:** Ujistěte se, že vlastnost type žádné **Arg** prvku v souboru definice xml vlastní modul je podporovaného typu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepodporovaný typ parametru.|  
|Nepodporovaný typ parametru '{0}"zadaný.|  


## <a name="error-0106"></a>Chyba 0106  
 Vyvolána, když soubor definice modulu definuje nepodporovaný typ vstupu  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud typ vstupního portu v modulu vlastní definici XML neodpovídá podporovaným typem.  
  
**Řešení:** Ujistěte se, že je vlastnost typ elementu vstupu v souboru definice XML vlastní modul podporovaného typu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepodporovaný typ vstupu.|  
|Nepodporovaný typ vstupu '{0}"zadaný.|  


## <a name="error-0107"></a>Chyba 0107  
 Vyvolána, když soubor definice modulu definuje nepodporovaný výstupní typ.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud typ výstupní port v definici vlastního modulu xml neodpovídá podporovaným typem.  
  
**Řešení:** Ujistěte se, že vlastnost type výstup elementu v souboru definice xml vlastní modul není podporovaného typu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Typ nepodporovaný výstupu.|  
|Nepodporovaný typ výstupu "{0}" zadaný.|  


## <a name="error-0108"></a>Chyba 0108  
 Vyvolána, když soubor definice modulu definuje více vstupních nebo výstupních portů, než jsou podporovány  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud je moc velký počet vstupních nebo výstupních portů jsou definované v definici vlastního modulu xml.  
  
**Řešení:** Zajišťuje, že maximální počet vstupních a výstupních portů, které jsou definované v definici vlastního modulu xml nepřekračuje maximální počet podporovaných portů.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Došlo k překročení podporovaný počet vstupních nebo výstupních portů.|  
|Byl překročen počet podporovaný "{0}" porty. Maximální povolený počet "{0}portů je{1}".| 

## <a name="error-0109"></a>Chyba 0109  
 Vyvolána, když soubor definice modulu nesprávně definuje ovládacího prvku pro výběr sloupce  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud syntaxe pro argument sloupce výběr obsahuje chybu v definici vlastního modulu xml.  
  
**Řešení:** Tato chyba je vytvořen, pokud syntaxe pro argument sloupce výběr obsahuje chybu v definici vlastního modulu xml.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepodporovaná syntaxe pro výběr sloupce.|  
  

## <a name="error-0110"></a>Chyba 0110  
 Vyvolána, když soubor definice modulu definuje sloupce ovládacího prvku pro výběr, který odkazuje na neexistující vstupního portu ID  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen při *identifikátor portId* vlastnosti v rámci elementu Properties argument typu ColumnPicker se neshoduje s ID hodnotu vstupního portu.  
  
**Řešení:** Ujistěte se, že vlastnost identifikátor portId shoduje s hodnotou ID vstupního portu definované v definici vlastního modulu xml.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Výběr sloupce odkazuje na neexistující vstupní port ID.|  
|Výběr sloupce odkazuje na neexistující vstupního portu ID "{0}".|  
  

## <a name="error-0111"></a>Chyba 0111  
 Vyvolána, když soubor definice modulu definuje neplatná vlastnost  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud neplatná vlastnost je přiřazena na prvek v vlastního modulu definice XML.  
  
**Řešení:** Ujistěte se, že vlastnost podporuje elementu vlastního modulu.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Definice vlastnosti je neplatná.|  
|Definice vlastnosti '{0}"je neplatný.|  
  

## <a name="error-0112"></a>Chyba 0112  
 Vyvolána, když nelze analyzovat soubor definice modulu  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud dojde k chybě ve formátu xml, který zabraňuje modulu vlastní definici XML nejde parsovat jako platný soubor XML.  
  
**Řešení:** Ujistěte se, že je každý prvek otevřel a uzavřel správně. Ujistěte se, že nejsou žádné chyby formátování, XML.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepovedlo se parsovat soubor definice modulu.|  
|Nepovedlo se parsovat soubor definice modulu '{0}".|  
  

## <a name="error-0113"></a>Chyba 0113  
 Vyvolána, když soubor definice modulu obsahuje chyby.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud modul vlastního souboru definice XML může být analyzován, ale obsahuje chyby, jako jsou definice prvků nepodporuje vlastní moduly.  
  
**Řešení:** Ujistěte se, že soubor definice modulu vlastní definuje prvky a vlastnosti, které jsou podporovány vlastní moduly.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Soubor definice modulu obsahuje chyby.|  
|Soubor definice modulu '{0}' obsahuje chyby.|  
|Soubor definice modulu '{0}' obsahuje chyby. {1}|  
  

## <a name="error-0114"></a>Chyba 0114  
 Vyvolána při vytváření vlastního modulu nezdaří.  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud dojde k chybě sestavení vlastního modulu. V takovém případě, že jedna nebo další vlastní chyby související s modulu jsou došlo při přidávání vlastního modulu. Další chyby jsou hlášeny v rámci této chybové zprávě.  
  
**Řešení:** Řešení chyb zaznamenaný v rámci této zpráva o výjimce.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepovedlo se vytvořit vlastní modul.|  
|Vlastní modul sestavení selhala s chybou: {0}|  
  

## <a name="error-0115"></a>Chyba 0115  
 Vyvolána, když výchozí skript vlastního modulu má nepodporovanou příponu.  
  
 Tato chyba ve službě Azure Machine Learning nastane poskytnout skripty pro vlastní modul, který používá neznámá přípona souboru.  
  
**Řešení:** Ověřte formát a název souboru příponu žádné soubory skriptu, který je součástí vlastní modul.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepodporované rozšíření pro výchozí skript.|  
|Nepodporovaný soubor rozšíření {0} pro výchozí skript.|  
  

## <a name="error-0121"></a>Chyba 0121  
 Vyvolána, když SQL zapíše selže, protože tabulka je nelze zapsat  
  
 Tato chyba ve službě Azure Machine Learning je vytvořen, pokud používáte [exportovat Data](export-data.md) modulu, který chcete uložit výsledky do tabulky ve službě SQL database a tabulky nelze zapisovat. Obvykle se tato chyba zobrazí, pokud [exportovat Data](export-data.md) modulu úspěšně naváže připojení k instanci systému SQL Server, ale je pak nemůže zapsat obsah datové sady Azure ML do tabulky.  
  
**Řešení:**
 - Otevřete v podokně vlastností [exportovat Data](export-data.md) modulu a ověřit, zda jsou správně zadány názvy databáze a tabulky. 
 - Zkontrolujte schéma datové sady se exportováním a ujistěte se, že data nejsou kompatibilní s cílovou tabulkou.
 - Ověřte, že SQL přihlásit přidružené uživatelské jméno a heslo má oprávnění k zápisu do tabulky. 
 - Pokud výjimka obsahuje další informace o chybě v systému SQL Server, pomocí těchto informací a opravte.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Připojení k serveru nelze zapisovat do tabulky.|  
|Nelze zapisovat do tabulky Sql: {0}|  


## <a name="error-0122"></a>Chyba 0122  
 Pokud je zadáno více sloupců váha a je povolená jenom jedna dojde k výjimce.  
  
 Tuto chybu ve službě Azure Machine Learning nastane, pokud je vybraných příliš mnoho sloupců jako sloupců váhy.  
  
**Řešení:** Zkontrolujte vstupní datové sady a jeho metadata. Zajistěte, že aby pouze jeden sloupec obsahuje váhy.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Je zadáno více sloupců váhu.|  


## <a name="error-0123"></a>Chyba 0123  
 Pokud je popisek sloupce zadané sloupce vektorů dojde k výjimce.  
  
 K této chybě ve službě Azure Machine Learning dochází, pokud používáte vektor jako popisek sloupce.  
  
**Řešení:** Změnit formát dat sloupce v případě potřeby, nebo zvolte jiný sloupec.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Sloupec vektorů je zadán jako sloupec popisek.|  


## <a name="error-0124"></a>Chyba 0124  
 Pokud je zadáno nečíselné sloupců bude sloupec váha dojde k výjimce.  
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Nečíselné sloupec byl zadán jako sloupec váhu.|  
  


## <a name="error-0125"></a>Chyba 0125  
 Vyvolána, když neodpovídá schématu pro více datových sad.  
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Schéma datové sady se neshoduje.|  


## <a name="error-0126"></a>Chyba 0126  
 Pokud uživatel zadá SQL domény, který není podporován v Azure ML dojde k výjimce.  
  
 Tato chyba je vytvořen, pokud uživatel zadá SQL domény, který není podporován ve službě Azure Machine Learning. Zobrazí se tato chyba a pokud se pokoušíte připojit k databázovému serveru v doméně, která není na seznamu povolených. V současné době jsou povolené domény SQL: ". database.windows.net",". cloudapp.net", nebo ". database.secure.windows.net". To znamená server musí být server Azure SQL nebo server na virtuálním počítači v Azure.  
  
**Řešení:** Návštěvě modulu. Ověřte, že server služby SQL database patří k jednomu z přijetí domén:  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nepodporovaná doména SQL.|  
|Domény SQL {0} se aktuálně nepodporuje v Azure ML|  
  

## <a name="error-0127"></a>Chyba 0127  
 Velikost obrázku v pixelech překračuje povolený limit.  
  
 K této chybě dochází, pokud při čtení Image z image datové sady pro klasifikaci a bitové kopie jsou větší, než dokáže zpracovat modelu.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Zprávy o výjimkách|  
|------------------------|  
|Velikost obrázku v pixelech překračuje povolený limit.|  
|Velikost pixelů v souboru obrázku '{0}' překračuje povolený limit: "{1}.|  


## <a name="error-0128"></a>Chyba 0128  
 Počet podmíněné pravděpodobnosti zařazené do kategorií sloupců překračuje limit.  
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Počet podmíněné pravděpodobnosti zařazené do kategorií sloupců překračuje limit.|  
|Počet podmíněné pravděpodobnosti zařazené do kategorií sloupců překračuje limit. Sloupce{0}"a"{1}"jsou problematické pár.|  


## <a name="error-0129"></a>Chyba 0129  
 Počet sloupců v datové sadě překračuje povolený limit.  
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Počet sloupců v datové sadě překračuje povolený limit.|  
|Počet sloupců v datové sadě v "{0}"překračuje povolené."|  
|Počet sloupců v datové sadě v "{0}"překračuje povolený limit"{1}". "|  
|Počet sloupců v datové sadě v "{0}překračuje povolenou{1}"omezit na"{2}". "|  
## <a name="error-0130"></a>Chyba 0130  
 Výjimka nastane, pokud všechny řádky v objektu dataset školení obsahovat chybějící hodnoty.  
  
 K tomu dojde, když některé sloupce v trénovací datové sady je prázdný.  
  
**Řešení:** Použití [vyčištění chybějících dat](clean-missing-data.md) modulu a odeberte sloupce se všemi chybějícími hodnotami.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Všechny řádky v objektu dataset školení obsahovat chybějící hodnoty.  Zvažte použití modulu vyčištění chybějících dat odebrat chybějící hodnoty.|  
 

## <a name="error-0131"></a>Chyba 0131  
 Výjimka nastane, pokud jeden nebo více datových sad v souboru zip selže odblokujte a správně zaregistrován  
  
 Tato chyba je vytvořen, pokud jeden nebo více datových sad v souboru zip selže odblokujte a správně přečíst. Pokud při rozbalování selže, protože samotný soubor zip nebo soubory v něm je poškozený nebo dochází k chybě systému při pokusu o rozbalení a rozbalte soubor obdržíte k této chybě.  
  
**Řešení:** Podrobné informace uvedené v chybové zprávě použijte k určení jak pokračovat dál.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nahrát ZIP datové sady se nezdařilo|  
|Datová sada je zazipovaný {0} selhalo s následující zprávou: {1}|  
|Datová sada je zazipovaný {0} nebyl úspěšný {1} výjimka se zprávou: {2}|  
  

## <a name="error-0132"></a>Chyba 0132  
 Pro rozbalení; nebyl zadán žádný název souboru více souborů nebyly nalezeny v souboru zip.  
  
 Tato chyba je vytvořen, pokud nebyl zadán žádný název souboru pro rozbalení; více souborů nebyly nalezeny v souboru zip. Zobrazí se tato chyba je-li soubor ZIP obsahuje více než jeden komprimovaný soubor, ale které nejsou specifikovány soubor pro extrahování v **datovou sadu, která dekomprese** do textového pole **vlastnost** podokně modulu. V současné době pouze jeden soubor může být extrahována pokaždé, když je spuštěn modul.  
  
**Řešení:** Chybová zpráva obsahuje seznam souborů a složek nalezených v souboru ZIP. Název požadovaného souboru zkopírujte a vložte ho do **datovou sadu, která dekomprese** textového pole.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Soubor ZIP obsahuje více souborů. musíte zadat soubor rozbalte.|  
|Tento soubor obsahuje více než jeden soubor. Zadejte soubor rozbalte. Následující soubory nebyly nalezeny: {0}|  
  

## <a name="error-0133"></a>Chyba 0133  
 Zadaný soubor nebyl nalezen v souboru zip  
  
 Tato chyba je vytvořen, pokud název souboru zadaný v **datovou sadu, která dekomprese** pole **vlastnost** podokně neodpovídá názvu souboru v souboru .zip. Mezi nejběžnější příčiny této chyby jsou zadáním chyba nebo hledání nesprávné archivnímu souboru pro soubor rozbalte.  
  
**Řešení:** Návštěvě modulu. Pokud název souboru, který je určený pro dekompresi se zobrazí v seznamu nebyly nalezeny soubory, název souboru zkopírujte a vložte ho do **datovou sadu, která dekomprese** vlastnosti. Pokud se nezobrazí požadovaný název souboru v seznamu, ověřte, zda máte správné .zip soubor a správný název požadovaného souboru.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zadaný soubor nebyl nalezen int soubor zip.|  
|Zadaný soubor se nenašel. Najít následující soubory: {0}|  
  

## <a name="error-0134"></a>Chyba 0134
Pokud popisek sloupce chybí nebo má nedostatečný počet řádků s popiskem, dojde k výjimce.  
  
K této chybě dochází, když modul vyžaduje popisek sloupce, ale nejsou zahrnuty v výběr sloupce nebo popisek sloupce chybí příliš mnoho hodnot.

K této chybě může dojít také při předchozí operace změny datové sady, tak, aby byly k dispozici pro příjem dat operace dostatečná řádků. Předpokládejme například, použijte výraz v **rozdělení a vzorky** modul pro dělení datovou sadu hodnot. Pokud výraz se nenašly žádné shody, jeden z datových sad vyplývající z oddílu bude prázdný.

Řešení: 

 Pokud zahrnete popisek sloupce výběr sloupce, ale nebyla rozpoznána, použijte [upravit Metadata](edit-metadata.md) modulu označte ji jako sloupec popisek.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Potom můžete použít [vyčištění chybějících dat](clean-missing-data.md) modulu k odebrání řádků s chybějící hodnoty ve sloupci popisek. 

 Zkontrolujte vaše vstupní datové sady, abyste měli jistotu, že sloupce obsahují platná data a dostatečný počet řádků vyhovět jejich požadavkům operace. Mnoho algoritmů bude generovat chybovou zprávu, pokud se vyžadují některé minimální počet řádků dat, ale data obsahují jenom pár řádků nebo pouze záhlaví.
  
|Zprávy o výjimkách|
|------------------------|
|Pokud popisek sloupce chybí nebo má nedostatečný počet řádků s popiskem, dojde k výjimce.|  
|Výjimka nastane, pokud sloupec popisek chybí nebo má méně než {0} označené řádky|  
  

## <a name="error-0135"></a>Chyba 0135  
 Je podporován pouze na základě těžiště clusteru.  
  
**Řešení:** Tato chybová zpráva může dojít, pokud jste se pokusili vyhodnotit model clusteringu, která je založena na vlastního algoritmu clusteringu, které nepoužívají centroids inicializovat clusteru.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Zprávy o výjimkách|  
|------------------------|  
|Je podporován pouze na základě těžiště clusteru.|  
  

## <a name="error-0136"></a>Chyba 0136  
 Byl vrácen žádný název souboru; nelze zpracovat v důsledku souboru.  
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Byl vrácen žádný název souboru; nelze zpracovat v důsledku souboru.|  
  

## <a name="error-0137"></a>Chyba 0137  
 Sady SDK služby Azure Storage došlo k chybě převodu mezi vlastnosti tabulky a sloupce datové sady během čtení nebo zápisu.  
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Chyba převodu mezi sloupci vlastnost a datová sada úložiště tabulek v Azure.|  
|Chyba převodu mezi sloupci vlastnost a datová sada úložiště tabulek v Azure. Další informace: {0}|  

## <a name="error-0138"></a>Chyba 0138  
 Paměť se vyčerpá, nelze k dokončení běhu modulu. Ke zmírnění problému mohou pomoci převzorkování datové sady.  
  
 K této chybě dochází, když modul, na kterém běží vyžaduje více paměti, než je k dispozici v kontejneru Azure. To může nastat, pokud pracujete s velkou datovou sadu a aktuální operace nemůže vejít do paměti.  
  
**Řešení:** Pokud se pokoušíte načíst velkou datovou sadu a operace nemohla být dokončena, převzorkování datové sady mohou pomoci při.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Zprávy o výjimkách|  
|------------------------|  
|Paměť se vyčerpá, nelze k dokončení běhu modulu.|  
  

## <a name="error-0139"></a>Chyba 0139  
 Pokud není možné převést sloupce na jiný typ, dojde k výjimce.  
  
 Této chybě ve službě Azure Machine Learning dochází při pokusu převést sloupce na jiný datový typ, ale typ není podporován aktuální operaci správy nebo modulu.  
  
 Chyba může zobrazit i při modulu se snaží implicitně převést data pro splnění požadavků aktuálního modulu, ale převod není možný.  
  
**Řešení:**

1. Zkontrolujte vstupní data a určit přesný datový typ sloupce, který chcete použít a datový typ sloupce, který je chyba. Někdy můžete uvažovat datového typu je správný, ale najít, že nadřazený operace změnila typ dat nebo použití sloupce. Použití [upravit Metadata](edit-metadata.md) modulu, který chcete obnovit metadata sloupce do původního stavu. 
2. Podívejte se na stránce nápovědy modulu k ověření požadavků pro danou operaci. Určete typy dat, které jsou podporovány v rámci aktuálního modulu, a jaké rozsah hodnot je podporována. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Zvažte, zda je možné převést nebo přetypován na jiný datový typ sloupce. Všechny následující moduly poskytuje značnou flexibilitu a výkon pro úpravu dat: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Spusťte skript Pythonu](execute-python-script.md).  

> [!NOTE]
> Pořád nefunguje? Vezměte v úvahu další názor na příslušný problém, které pomáhají vyvíjet lepší řešení problémů s pokyny. Právě odeslat názor na tuto stránku a zadejte název modulu, který vygeneruje chybu a převod typu dat, která selhala.
  
|Zprávy o výjimkách|  
|------------------------|  
|Převod není povolen.|  
|Nebylo možné převést: {0}.|  
|Nebylo možné převést: {0}, na řádku {1}.|  
|Nebylo možné převést na sloupec typu {0} pro sloupec typu {1} na řádku {2}.|  
|Nebylo možné převést sloupce "{2}" typu {0} pro sloupec typu {1} na řádku {3}.|  
|Nebylo možné převést sloupce "{2}" typu {0} na sloupec "{3}" typu {1} na řádku {4}.| 

## <a name="error-0140"></a>Chyba 0140  
 Výjimka nastane, pokud předaný argumentu sady sloupec neobsahuje další sloupce kromě sloupce popisek.  
  
 K této chybě dochází, pokud je připojené datové sadě k modulu, který vyžaduje více sloupců, včetně funkcí, ale zadáte pouze se sloupcem popisek.  
  
**Řešení:** Vyberte alespoň jeden sloupec funkce mají být zahrnuty datové sady.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zadaný sloupec sada neobsahuje další sloupce kromě sloupce popisek.|  
  

## <a name="error-0141"></a>Chyba 0141  
 Výjimka nastane, pokud počet vybraných číselné sloupce a jedinečné hodnoty v zařazené do kategorií a řetězcové sloupce je příliš malá.  
  
 K této chybě ve službě Azure Machine Learning dochází, když již nejsou dostatek jedinečných hodnot ve vybraném sloupci k provedení této operace.  
  
**Řešení:** Některé operace provádí statistické operace na funkce a kategorií sloupce, a pokud nejsou k dispozici dostatek hodnot, může operace selhat nebo neplatný výsledek. Zkontrolujte vaše datová sada zobrazíte počet hodnot, které existují ve sloupcích funkcí a označení a určit, zda je operace, kterou se pokoušíte provést statisticky platný.  
  
 Pokud zdrojová datová sada je platný, může také zkontrolovat, zda má některé upstreamové datové manipulaci s poli nebo metadata operace změnit data a odebrat některé hodnoty.  
  
 Pokud nadřazeného operace rozdělení, vzorkování nebo převzorkování, zkontrolujte, zda výstupy obsahují očekávaného počtu řádků a hodnoty.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Počet vybraných číselné sloupce a jedinečné hodnoty v zařazené do kategorií a řetězcové sloupce je příliš malá.|  
|Celkový počet vybraných číselné sloupce a jedinečné hodnoty v zařazené do kategorií a řetězcové sloupce (aktuálně {0}) musí být nejméně {1}|  
  

## <a name="error-0142"></a>Chyba 0142  
 Výjimka nastane, pokud systém nemůže načíst certifikát pro ověření.  
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Certifikát nelze načíst.|  
|Certifikát {0} nelze načíst. Je jeho kryptografický otisk {1}.|  
  

## <a name="error-0143"></a>Chyba 0143  
 Nelze analyzovat uživatelem zadané adresy URL, která by měla být z Githubu.  
  
 K této chybě ve službě Azure Machine Learning dochází, když zadáte neplatnou adresu URL a že modul vyžaduje platnou adresu URL Githubu.  
  
**Řešení:** Ověřte, že adresa URL odkazuje na platný úložiště GitHub. Jiné typy lokality nejsou podporované.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Adresa URL není z webu github.com.|  
|Adresa URL není z webu github.com: {0}|  

## <a name="error-0144"></a>Chyba 0144  
 Uživatelem zadaný adresu url Githubu chybí očekávaný část.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud zadáte zdroj souborů GitHub pomocí má neplatný formát adresy URL.  
  
**Řešení:** Zkontrolujte, že adresa URL úložiště GitHub je platný a končí \blob\ nebo \tree\\.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Nelze analyzovat adresu URL Githubu.|  
|Nelze analyzovat adresu URL Githubu (byl očekáván ' \blob\\"nebo" \tree\\' za název úložiště): {0}|  

## <a name="error-0145"></a>Chyba 0145  
 Nelze vytvořit adresář replikace z nějakého důvodu.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud modul se nepodařilo vytvořit zadaný adresář.  
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Nelze vytvořit adresář replikace.|  
  

## <a name="error-0146"></a>Chyba 0146  
 Když soubory uživatele jsou extrahovány do místního adresáře, kombinované cesta může být příliš dlouhý.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud jsou extrahování souborů, ale některé názvy souborů jsou příliš dlouhé při rozzipoval.  
  
**Řešení:** Upravit názvy souborů tak, aby kombinovat cestu a název souboru je delší než 248 znaků.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Replikace cesta je delší než 248 znaků, zkraťte název skriptu nebo cestu.|  

## <a name="error-0147"></a>Chyba 0147  
 Nepovedlo se stáhnout věci z Githubu z nějakého důvodu  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud nejde přečíst nebo zadané soubory stáhnout z webu GitHub.  
  
**Řešení:** Tento problém může být dočasné; Můžete se pokusit přístupu k souborům v jinou dobu. Nebo ověřte, že máte potřebná oprávnění a že je platný zdroj.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Chyba přístupu k Githubu.|  
|Chyba přístupu k Githubu. {0}|  
  

## <a name="error-0148"></a>Chyba 0148  
 Před neoprávněným přístupem problémy při extrahování dat nebo vytvoření adresáře.  
  
 K této chybě ve službě Azure Machine Learning dochází, když se pokoušíte vytvořit adresář nebo číst data z úložiště, ale nemají potřebná oprávnění.  
  
**Řešení:**
  
|Zprávy o výjimkách|  
|------------------------|  
|Při extrahování dat došlo k výjimce neoprávněného přístupu.|  
  

## <a name="error-0149"></a>Chyba 0149  
 Soubor uživatele neexistuje uvnitř sady Githubu.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud zadaný soubor nebyl nalezen.  
  
Řešení: 
  
|Zprávy o výjimkách|  
|------------------------|  
|Nenašel se soubor Githubu.|  
|Nenašel se soubor Githubu.: {0}|  
  

## <a name="error-0150"></a>Chyba 0150  
 Skripty, které pocházejí z balíčku uživatele nelze rozbalit, pravděpodobně z důvodu kolize s souborů Githubu.  
  
 Tato chyba ve službě Azure Machine Learning nastane skriptu nelze extrahovat, obvykle po existující soubor se stejným názvem.  
  
Řešení:
  
|Zprávy o výjimkách|  
|------------------------|  
|Nelze rozbalit svazku. je to možné název kolizí se soubory Githubu.|  
  

## <a name="error-0151"></a>Chyba 0151  
 Došlo k chybě při zápisu do cloudového úložiště. Zkontrolujte adresu URL.  
  
 Tato chyba ve službě Azure Machine Learning nastane, pokud modul pokusí o zápisu dat do cloudového úložiště, ale adresa URL je k dispozici nebo je neplatný.  
  
Řešení: Zkontrolujte adresu URL a ověřte, zda je zapisovatelný.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Chyba při zápisu do cloudového úložiště (pravděpodobně chybné url).|  
|Chyba při zápisu do cloudového úložiště: {0}. Zkontrolujte adresu url.|  
  
## <a name="error-0152"></a>Chyba 0152  
 V kontextu modulu byl nesprávně zadán typ cloudu Azure.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Typ chybný cloudu Azure|  
|Typ chybný cloudu Azure: {0}|  
  
## <a name="error-0153"></a>Chyba 0153  
 Zadaný koncový bod úložiště je neplatný.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Typ chybný cloudu Azure|  
|Chybný úložiště koncový bod: {0}|  

## <a name="error-0154"></a>Chyba 0154  
 Zadaný název serveru nelze rozpoznat.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zadaný název serveru nelze rozpoznat.|  
|Zadaný server {0}. nebylo možné přeložit adresu documents.azure.com|

## <a name="error-0155"></a>Chyba 0155  
 DocDb klienta došlo k výjimce  
  
|Zprávy o výjimkách|  
|------------------------|  
|DocDb klienta došlo k výjimce|  
|DocDb Client: {0}|

## <a name="error-0156"></a>Chyba 0156  
 Chybná odpověď serveru HCatalog.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Chybná odpověď serveru HCatalog. Zkontrolujte, jestli jsou spuštěné všechny služby.|  
|Chybná odpověď serveru HCatalog. Zkontrolujte, jestli jsou spuštěné všechny služby. Podrobnosti o chybě: {0}|

## <a name="error-0157"></a>Chyba 0157  
 Došlo k chybě při čtení ze služby Azure Cosmos DB z důvodu schémata nekonzistentní nebo jiného dokumentu. Čtečka vyžaduje všech dokumentů, mají stejné schéma.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Zjištěné dokumentů s různými schématy. Ujistěte se, že všechny dokumenty, mají stejné schéma|

## <a name="error-1000"></a>Chyba 1000  
Interní knihovna došlo k výjimce.  
  
Tato chyba je k dispozici pro zachycení v opačném případě neošetřené chyby interní modul. Proto příčinou této chyby může lišit v závislosti na modul, který vytvořil chybu.  
  
Chcete-li získat další pomoc, doporučujeme pošlete podrobná zpráva, která doprovází chyby do fóra Azure Machine Learning, společně s popis tohoto scénáře, včetně dat použít jako vstupy. Tato zpětná vazba pomůže nám a upřednostnit chyby a identifikovat nejdůležitější problémy pro další práci.  
  
|Zprávy o výjimkách|  
|------------------------|  
|Knihovna došlo k výjimce.|  
|Knihovna výjimka: {0}|  
|{0} Knihovna výjimka: {1}|  
