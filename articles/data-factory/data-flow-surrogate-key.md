---
title: Transformace náhradního klíče v toku mapování dat
description: Jak použít transformaci klíče pro mapování toku dat Azure Data Factory k vygenerování sekvenčních hodnot klíče
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606299"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformace náhradního klíče v toku mapování dat 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

K přidání hodnoty přírůstku do každého řádku dat použijte transformaci náhradního klíče. To je užitečné při navrhování tabulek dimenzí v analytickém datovém modelu schématu hvězdičky. V rámci schématu hvězdičky vyžaduje každý člen v tabulkách dimenzí jedinečný klíč, který není obchodním klíčem.

## <a name="configuration"></a>Konfigurace

![Transformace náhradního klíče](media/data-flow/surrogate.png "Transformace náhradního klíče")

**Klíčový sloupec:** Název vygenerovaného sloupce náhradního klíče.

**Počáteční hodnota:** Nejnižší hodnota klíče, která bude vygenerována.

## <a name="increment-keys-from-existing-sources"></a>Zvýšit klíče z existujících zdrojů

Chcete-li začít sekvenci z hodnoty, která existuje ve zdroji, použijte transformaci odvozeného sloupce po transformaci náhradního klíče a přidejte tyto dvě hodnoty dohromady:

![SK – přidat max](media/data-flow/sk006.png "Transformace náhradních klíčů – přidat max")

### <a name="increment-from-existing-maximum-value"></a>Přírůstek z existující maximální hodnoty

K osazení hodnoty klíče pomocí předchozího maxima jsou k dispozici dvě metody, které můžete použít v závislosti na tom, kde jsou zdrojová data.

#### <a name="database-sources"></a>Zdroje databáze

Pomocí možnosti dotazu SQL vyberte MAX () ze zdroje. Například `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Dotaz na náhradní klíč](media/data-flow/sk002.png "Dotaz na transformaci náhradního klíče")

#### <a name="file-sources"></a>Zdroje souborů

Pokud je předchozí maximální hodnota v souboru, použijte `max()` funkci v agregační transformaci k získání předchozí maximální hodnoty:

![Soubor náhradního klíče](media/data-flow/sk008.png "Soubor náhradního klíče")

V obou případech je třeba spojit vaše příchozí nová data společně se zdrojem, který obsahuje předchozí maximální hodnotu.

![Připojení náhradních klíčů](media/data-flow/sk004.png "Připojení náhradních klíčů")

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Příklad

![Transformace náhradního klíče](media/data-flow/surrogate.png "Transformace náhradního klíče")

Skript toku dat pro výše uvedenou konfiguraci náhradního klíče je uvedený v následujícím fragmentu kódu.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Další kroky

V těchto příkladech se používají transformace sloupců [Join](data-flow-join.md) a [Derived](data-flow-derived-column.md) .
