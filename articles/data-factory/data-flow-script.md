---
title: Mapování skriptu toku dat
description: Přehled jazyka kódu Data Factoryho skriptu pro tok dat
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/10/2019
ms.openlocfilehash: d861a4355158dfe18ac3aa40a7f98dc11ebda90b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930262"
---
# <a name="data-flow-script-dfs"></a>Skript toku dat (DFS)

Skript toku dat (DFS) je podkladová metadata, podobně jako kódovací jazyk, který se používá ke spouštění transformací, které jsou zahrnuty v toku dat mapování. Všechny transformace jsou reprezentovány řadou vlastností, které poskytují nezbytné informace pro správné spuštění úlohy. Skript je viditelný a upravitelný z ADF kliknutím na tlačítko "skript" na horním pásu karet uživatelského rozhraní prohlížeče.

![Tlačítko skriptu](media/data-flow/scriptbutton.png "Tlačítko skriptu")

`allowSchemaDrift: true,` ve zdrojové transformaci například instruuje službu, aby zahrnovala všechny sloupce ze zdrojové datové sady v toku dat, i když nejsou zahrnuté do projekce schématu.

## <a name="use-cases"></a>Případy použití
Systém souborů DFS je automaticky vytvořen uživatelským rozhraním. Kliknutím na tlačítko skript můžete zobrazit a přizpůsobit skript. Můžete také vygenerovat skripty mimo uživatelské rozhraní ADF a pak je předat do rutiny PowerShellu. Při ladění složitých toků dat může být snazší vyhledat kód skriptu, místo aby se kontrolovala reprezentace grafu uživatelského rozhraní.

Tady je několik příkladů případů použití:
- Programově vytváření mnoha toků dat, které jsou poměrně podobné, tj. toky dat "razítko".
- Složité výrazy, které se obtížně spravují v uživatelském rozhraní nebo mají za následek problémy s ověřením.
- Ladění a lepší porozumění různým chybám vráceným během provádění.

Když vytváříte skript toku dat pro použití s prostředím PowerShell nebo rozhraním API, je nutné sbalit formátovaný text na jeden řádek. Můžete ponechat tabulátory a newlines jako řídicí znaky. Ale text musí být formátovaný tak, aby se vešel do vlastnosti JSON. V dolní části uživatelského rozhraní editoru skriptů je tlačítko, ve kterém se skript naformátuje jako jeden řádek.

![Tlačítko Kopírovat](media/data-flow/copybutton.png "Tlačítko pro kopírování")

## <a name="how-to-add-transforms"></a>Postup přidání transformací
Přidání transformací vyžaduje tři základní kroky: Přidání základních transformačních dat, přesměrování vstupního datového proudu a následné přesměrování výstupního datového proudu. To se dá v příkladu zobrazit nejjednodušší.
Řekněme, že začneme s jednoduchým zdrojem pro tok dat, jako je následující:

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

Pokud se rozhodnete přidat odvozenou transformaci, nejdřív musíme vytvořit základní transformační text, který má jednoduchý výraz pro přidání nového sloupce s velkými písmeny s názvem `upperCaseTitle`:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Pak převezmeme stávající DFS a přidáme transformaci:
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

A teď přesměrujte příchozí datový proud tím, že určíte, kterou transformaci chceme po (v tomto případě `source1`), a zkopírováním názvu streamu do nové transformace:
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

Nakonec identifikujeme transformaci, kterou chceme přijít po této nové transformaci, a nahraďte její vstupní proud (v tomto případě `sink1`) s názvem výstupního datového proudu naší nové transformace:
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

## <a name="dfs-fundamentals"></a>Základní informace o systému souborů DFS
DFS se skládá z řady propojených transformací, včetně zdrojů, umyvadel a různých dalších, které mohou přidávat nové sloupce, filtrovat data, spojovat data a mnoho dalšího. Skript obvykle začíná s jedním nebo více zdroji následovanými mnoha transformacemi a končí jednou nebo více jímkami.

Všechny zdroje mají stejnou základní konstrukci:
```
source(
  source properties
) ~> source_name
```

Například jednoduchý zdroj se třemi sloupci (movieId, title, žánry) by byl:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Všechny transformace kromě zdrojů mají stejnou základní konstrukci:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Například jednoduchá odvozená transformace, která přebírá sloupec (title) a Přepisuje ho pomocí velké verze, by byla následující:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

A jímka bez schématu by byla jednoduchá:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte toky dat od [článku Přehled toků dat](concepts-data-flow-overview.md)
