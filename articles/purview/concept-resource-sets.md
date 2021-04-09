---
title: Principy sad prostředků
description: Tento článek vysvětluje, jaké jsou sady prostředků a jak je Azure dosah vytvoří.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: cbf070dce056795ad8e4a5f3e4d609e7d36d631e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200820"
---
# <a name="understanding-resource-sets"></a>Principy sad prostředků

Tento článek vám pomůže pochopit, jak Azure dosah používá sady prostředků k mapování datových prostředků na logické prostředky.
## <a name="background-info"></a>Informace na pozadí

Systémy zpracování dat ve velkém měřítku obvykle ukládají jednu tabulku na disk jako více souborů. Tento koncept je reprezentován v Azure dosah pomocí sad prostředků. Sada prostředků je jeden objekt v katalogu, který představuje velký počet prostředků v úložišti.

Předpokládejme například, že váš cluster Spark trval datový rámec na Azure Data Lake Storage (ADLS) Gen2 zdroj dat. I když v Sparku tabulka vypadá jako jeden logický prostředek, na disku jsou pravděpodobně tisíce souborů Parquet, z nichž každý představuje oddíl celkového obsahu datového rámce. Data protokolu IoT a data webového protokolu mají stejnou výzvu. Představte si, že máte senzor, který vypíše soubory protokolu několikrát za sekundu. Netrvá tak dlouho, dokud nebudete mít stovky tisíc souborů protokolu od tohoto jednoho snímače.

K vyřešení výzvy k mapování velkého počtu datových assetů na jeden logický prostředek používá Azure dosah sady prostředků.

## <a name="how-azure-purview-detects-resource-sets"></a>Jak Azure dosah detekuje sady prostředků

Azure dosah podporuje detekci sad prostředků v Azure Blob Storage, ADLS Gen1 a ADLS Gen2.

Azure dosah při kontrole automaticky detekuje sady prostředků. Tato funkce prohlíží všechna data, která se ingestují prostřednictvím kontroly a porovnává je se sadou definovaných vzorů.

Předpokládejme například, že naskenujete zdroj dat, jehož adresa URL je `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure dosah vyhledá segmenty cesty a určí, jestli odpovídají jakýmkoli vestavěným vzorům. Obsahuje předdefinované vzory identifikátorů GUID, čísel, formátů data, lokalizačních kódů (například en-us) atd. V tomto případě vzorec čísla porovnává *23*. Azure dosah předpokládá, že tento soubor je součástí sady prostředků s názvem `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Nebo pro adresu URL, jako `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` je Azure dosah, odpovídá vzoru lokalizace i vzoru čísel, který vytváří sadu prostředků s názvem `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Pomocí této strategie namapuje Azure dosah následující prostředky na stejnou sadu prostředků `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Typy souborů, které Azure dosah nebude zjišťovat jako sady prostředků

Dosah záměrně nepokouší klasifikovat většinu typů souborů dokumentů jako Word, Excel nebo PDF jako sady prostředků. Výjimkou je formát CSV, protože se jedná o běžný formát děleného souboru.

## <a name="how-azure-purview-scans-resource-sets"></a>Jak Azure dosah prohledává sady prostředků

Když Azure dosah detekuje prostředky, které považují za součást sady prostředků, přepne z úplného prohledávání na vzorovou kontrolu. Při ukázkové kontrole se otevře pouze podmnožina souborů, ze kterých se domnívá, že jsou v sadě prostředků. Pro každý soubor, který je otevřen, používá schéma a spustí své třídění. Azure dosah pak najde nejnovější prostředek mezi otevřenými prostředky a v položce použije schéma a klasifikace prostředků pro celou sadu prostředků v katalogu.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Co Azure dosah ukládá o sadách prostředků

Kromě jediného schématu a klasifikací ukládá Azure dosah následující informace o sadách prostředků:

- Data z nejnovějšího prostředku oddílu, který je hluboko prověřený
- Agregovat informace o prostředcích oddílu, které tvoří sadu prostředků.
- Počet oddílů, který ukazuje, kolik prostředků oddílu bylo nalezeno.
- Počet schémat, které ukazují, kolik jedinečných schémat nalezeno v sadě ukázek, na které se provedlo hloubkové prohledávání. Tato hodnota je buď číslo v rozmezí 1 – 5, nebo hodnoty větší než 5, 5 +.
- Seznam typů oddílů, pokud je v sadě prostředků zahrnutý více než jeden typ oddílu. Například, může senzor IoT výstupovat soubory XML i JSON, i když obě jsou logicky součástí stejné sady prostředků.

## <a name="built-in-resource-set-patterns"></a>Předdefinované vzory sady prostředků

Azure dosah podporuje následující vzory sady prostředků. Tyto vzory se můžou zobrazovat jako název v adresáři nebo jako součást názvu souboru.
### <a name="regex-based-patterns"></a>Vzory založené na regulárních výrazech

| Název vzoru | Zobrazovaný název | Popis |
|--------------|--------------|-------------|
| Identifikátor GUID         | HLAVNÍCH       | Globálně jedinečný identifikátor definovaný v [dokumentu RFC 4122](https://tools.ietf.org/html/rfc4122) |
| Číslo       | N          | Jedna nebo více číslic |
| Formáty data a času | Jednolet Měsíčně Dnu N     | Podporujeme různé formáty data a času, ale všechny jsou představovány {Year} [oddělovač] {month} [oddělovač] {Day} nebo řady {N} s. |
| 4ByteHex     | SOUSTAVY        | Čtyřmístné HEXADECIMÁLNÍ číslo. |
| Lokalizace | Loc        | Je podporována značka jazyka definovaná v [BCP 47](https://tools.ietf.org/html/bcp47), názvy obou i _ (například en_CA a en-CA). |

### <a name="complex-patterns"></a>Komplexní vzory

| Název vzoru | Zobrazovaný název | Popis |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Identifikátor souboru oddílu Spark |
| Datum (RRRR/MM/DD) – cesta  | {Year}/{Month}/{Day} | Vzor pro rok/měsíc/den pokrývání více složek |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Jak se v katalogu Azure dosah zobrazují sady prostředků

Když Azure dosah odpovídá skupině prostředků v sadě prostředků, pokusí se extrahovat nejužitečnější informace, které se použijí jako zobrazované jméno v katalogu. Některé příklady výchozích konvencí pojmenování: 

### <a name="example-1"></a>Příklad 1

Kvalifikovaný název: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Zobrazovaný název: "název výstupu Spark"

### <a name="example-2"></a>Příklad 2

Kvalifikovaný název: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Zobrazovaný název: "moje dělená data"

### <a name="example-3"></a>Příklad 3

Kvalifikovaný název: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Zobrazovaný název: "data"

## <a name="known-issues-with-resource-sets"></a>Známé problémy se sadami prostředků

Přestože sady prostředků ve většině případů fungují dobře, může dojít k následujícím problémům, ve kterých Azure dosah:

- Nesprávně označí Asset jako sadu prostředků.
- Vloží Asset do nesprávné sady prostředků.
- Nesprávně označuje prostředek jako sadu prostředků.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít s Azure dosah, najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).
