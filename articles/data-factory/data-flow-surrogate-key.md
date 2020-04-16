---
title: Transformace náhradního klíče v toku dat mapování
description: Jak pomocí mapování datového toku Azure Data Factory pro transformaci náhradního klíče ke generování sekvenčních hodnot klíčů
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: 6f0310f13ab23b5b3d4fba649d0faf0da4dad5b8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413145"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformace náhradního klíče v toku dat mapování 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Transformace náhradního klíče slouží k přidání přírůstkové hodnoty klíče do každého řádku dat. To je užitečné při navrhování tabulek dimenzí v analytickém datovém modelu schématu hvězd. Ve schématu hvězdičky vyžaduje každý člen v tabulkách dimenze jedinečný klíč, který není obchodním klíčem.

## <a name="configuration"></a>Konfigurace

![Transformace náhradního klíče](media/data-flow/surrogate.png "Transformace náhradního klíče")

**Klíčový sloupec:** Název generovaného sloupce náhradního klíče.

**Počáteční hodnota:** Nejnižší hodnota klíče, která bude generována.

## <a name="increment-keys-from-existing-sources"></a>Přírůstek klíče z existujících zdrojů

Chcete-li spustit sekvenci z hodnoty, která existuje ve zdroji, použijte odvozenou transformaci sloupce po transformaci náhradního klíče a přidejte dvě hodnoty dohromady:

![SK přidat Max](media/data-flow/sk006.png "Náhradní transformace klíče Přidat max")

### <a name="increment-from-existing-maximum-value"></a>Přírůstek z existující maximální hodnoty

Chcete-li osivo hodnotu klíče s předchozí max, existují dvě techniky, které můžete použít na základě místa, kde jsou zdrojová data.

#### <a name="database-sources"></a>Zdroje databáze

Pomocí možnosti dotazu SQL vyberte ze zdroje příkaz MAX(). Například`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Dotaz na náhradní klíč](media/data-flow/sk002.png "Dotaz na transformaci náhradního klíče")

#### <a name="file-sources"></a>Zdroje souborů

Pokud je předchozí maximální hodnota v `max()` souboru, použijte funkci v agregované transformaci k získání předchozí maximální hodnoty:

![Soubor náhradního klíče](media/data-flow/sk008.png "Soubor náhradního klíče")

V obou případech je nutné spojit příchozí nová data spolu se zdrojem, který obsahuje předchozí maximální hodnotu.

![Připojení náhradního klíče](media/data-flow/sk004.png "Připojení náhradního klíče")

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Příklad

![Transformace náhradního klíče](media/data-flow/surrogate.png "Transformace náhradního klíče")

Skript toku dat pro výše uvedenou konfiguraci náhradního klíče je ve fragmentu kódu níže.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Další kroky

Tyto příklady používají [spojení](data-flow-join.md) a [odvozené sloupce](data-flow-derived-column.md) transformace.
