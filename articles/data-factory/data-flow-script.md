---
title: Skript toku dat mapování
description: Přehled jazyka kódu skriptu toku dat v toku dat
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.openlocfilehash: e0042960c25d58b72bc0ab884de5a2db62e566d9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413445"
---
# <a name="data-flow-script-dfs"></a>Skript toku dat (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Skript toku dat (DFS) je základní metadata, podobně jako kódovací jazyk, který se používá ke spuštění transformace, které jsou zahrnuty v toku dat mapování. Každá transformace je reprezentována řadou vlastností, které poskytují potřebné informace pro správné spuštění úlohy. Skript je viditelný a upravitelný z ADF kliknutím na tlačítko "skript" na horní pásu karet prohlížeče.

![Tlačítko Skript](media/data-flow/scriptbutton.png "Tlačítko Skript")

Například `allowSchemaDrift: true,` ve zdrojové transformace říká službě zahrnout všechny sloupce ze zdrojové datové sady v toku dat i v případě, že nejsou zahrnuty v projekci schématu.

## <a name="use-cases"></a>Případy použití
Systém souborů DFS je automaticky vytvářen uživatelským rozhraním. Chcete-li skript zobrazit a přizpůsobit, klepněte na tlačítko Skript. Můžete také generovat skripty mimo uj. Při ladění komplexní datové toky, může být snazší skenovat kód skriptu napozadí namísto skenování reprezentace grafu u rozhraní toků.

Zde je několik příkladů případů použití:
- Programově produkující mnoho datových toků, které jsou poměrně podobné, tj.
- Složité výrazy, které je obtížné spravovat v unovém uj.
- Ladění a lepší pochopení různých chyb vrácených během provádění.

Při vytváření skriptu toku dat pro použití s Prostředím PowerShell nebo rozhraní API, je nutné sbalit formátovaný text do jednoho řádku. Jako řídicí znaky můžete mít na záložkách a nových řádcích. Ale text musí být formátován tak, aby se vešel do vlastnosti JSON. V dolní části je tlačítko v uzly editoru skriptů, které bude formátovat skript jako jeden řádek pro vás.

![Tlačítko pro kopírování](media/data-flow/copybutton.png "Tlačítko pro kopírování")

## <a name="how-to-add-transforms"></a>Jak přidat transformace
Přidání transformací vyžaduje tři základní kroky: přidání základních transformačních dat, přesměrování vstupního datového proudu a následné přesměrování výstupního datového proudu. To lze vidět nejjednodušší v příkladu.
Řekněme, že začneme s jednoduchým zdrojem pro propad toku dat, jako je následující:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Pokud se rozhodneme přidat odvozenou transformaci, nejprve musíme vytvořit text transformace jádra, který `upperCaseTitle`má jednoduchý výraz pro přidání nového sloupce s názvem :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Potom vezmeme existující DFS a přidáme transformaci:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

A nyní přesměrovádáme příchozí proud určením transformace, kterou chceme, `source1`aby nová transformace přišla po (v tomto případě) a zkopírovali název datového proudu do nové transformace:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Nakonec identifikujeme transformaci, kterou chceme přijít po této nové transformaci, a `sink1`nahradíme její vstupní proud (v tomto případě) názvem výstupního datového proudu naší nové transformace:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Základy DFS
Dfs se skládá z řady propojených transformací, včetně zdrojů, jímky a různé další, které můžete přidat nové sloupce, filtrovat data, spojit data a mnoho dalšího. Skript s začít s jedním nebo více zdrojů následuje mnoho transformací a končí jeden nebo více propadů.

Všechny zdroje mají stejnou základní konstrukci:
```
source(
  source properties
) ~> source_name
```

Například jednoduchý zdroj se třemi sloupci (movieId, název, žánry) by bylo:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Všechny transformace jiné než zdroje mají stejnou základní konstrukci:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Například jednoduchá odvozená transformace, která vezme sloupec (název) a přepíše jej s verzí velkých písmen, bude následující:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

A umyvadlo bez schématu by bylo jednoduše:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Výstřižky skriptů

Úryvky skriptů jsou sdíletelný kód skriptu toku dat, který můžete použít ke sdílení mezi datovými toky. Toto video níže hovoří o tom, jak používat fragmenty skriptů a využití skriptu toku dat ke kopírování a vkládání částí skriptu za grafy toku dat:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Souhrnné souhrnné statistiky
Přidejte agregační transformace do toku dat s názvem "SummaryStats" a pak vložte do tohoto kódu níže pro agregační funkce ve skriptu, nahradí existující SummaryStats. To bude poskytovat obecný vzor pro souhrnné statistiky datového profilu.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Níže uvedenou ukázku můžete také použít k výpočtu počtu jedinečných a počtu odlišných řádků v datech. Následující příklad lze vložit do toku dat s agregační transformace s názvem ValueDistAgg. Tento příklad používá sloupec s názvem "title". Nezapomeňte nahradit "title" řetězcem ve vašich datech, které chcete použít k získání počtu hodnot.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Zahrnutí všech sloupců do agregace
Toto je obecný agregační vzor, který ukazuje, jak můžete zachovat zbývající sloupce ve výstupních metadatech při vytváření agregací. V tomto případě použijeme ```first()``` funkci k výběru první hodnoty v každém sloupci, jehož název není "film". Chcete-li použít, vytvořte agregační transformace s názvem DistinctRows a vložte jej do skriptu přes existující DistinctRows agregační skript.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Vytvoření otisku prstu hash řádku 
Tento kód ve skriptu toku dat použijte ```DWhash``` k vytvoření ```sha1``` nového odvozeného sloupce s názvem hash tří sloupců.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte toky dat tak, že začnete s [článkem přehledu toků dat](concepts-data-flow-overview.md)
