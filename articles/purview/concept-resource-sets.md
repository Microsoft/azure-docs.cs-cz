---
title: Principy sad prostředků
description: Tento článek vysvětluje, jaké jsou sady prostředků a jak je Azure dosah vytvoří.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 55efa9443fd59b66a7677c9c460e473715f201df
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552711"
---
# <a name="understanding-resource-sets"></a>Principy sad prostředků

Tento článek vám pomůže pochopit, jak Azure dosah používá sady prostředků k mapování datových prostředků na logické prostředky.

## <a name="background-info"></a>Informace na pozadí

Systémy zpracování dat ve velkém měřítku obvykle ukládají jednu tabulku na disk jako více souborů. Tento koncept je reprezentován v Azure dosah pomocí sad prostředků. Sada prostředků je jeden objekt v katalogu, který představuje velký počet prostředků v úložišti.

Předpokládejme například, že váš cluster Spark trval datový rámec pro ADLS Gen2 zdroj dat. I když v Sparku tabulka vypadá jako jeden logický prostředek, na disku jsou pravděpodobně tisíce souborů Parquet, z nichž každý představuje oddíl celkového obsahu datového rámce. Data protokolu IoT a data webového protokolu mají stejnou výzvu. Představte si, že máte senzor, který vypíše soubory protokolu několikrát za sekundu. Netrvá tak dlouho, dokud nebudete mít stovky tisíc souborů protokolu od tohoto jednoho snímače.

K vyřešení výzvy k mapování velkého počtu datových assetů na jeden logický prostředek používá Azure dosah sady prostředků.

## <a name="how-azure-purview-detects-resource-sets"></a>Jak Azure dosah detekuje sady prostředků

Azure dosah podporuje detekci sad prostředků jenom v objektech blob Azure, ADLS Gen1 a ADLS Gen2.

Azure dosah automaticky detekuje sady prostředků pomocí funkce označované jako automatizované zjišťování sady prostředků. Tato funkce prohlíží všechna data, která se ingestují prostřednictvím kontroly a porovnává je se sadou definovaných vzorů.

Předpokládejme například, že naskenujete zdroj dat, jehož adresa URL je `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure dosah vyhledá segmenty cesty a určí, jestli odpovídají jakýmkoli vestavěným vzorům. Obsahuje předdefinované vzory identifikátorů GUID, čísel, formátů data, lokalizačních kódů (například en-us) atd. V tomto případě vzorec čísla porovnává *23*. Azure dosah předpokládá, že tento soubor je součástí sady prostředků s názvem `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Nebo pro adresu URL, jako `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` je Azure dosah, odpovídá vzoru lokalizace i vzoru čísel, který vytváří sadu prostředků s názvem `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Pomocí této strategie namapuje Azure dosah následující prostředky na stejnou sadu prostředků `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> Platforma Azure Data Lake Storage Gen2 je teď obecně dostupná. Doporučujeme začít ji používat ještě dnes. Další informace najdete na [stránce produktu](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Typy souborů, které Azure dosah nebude zjišťovat jako sady prostředků

Dosah záměrně nezkouší klasifikovat většinu typů souborů dokumentů, jako je Word, Excel nebo PDF, jako sady prostředků. Výjimka je CSV, protože se jedná o společný formát děleného souboru.

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

| Název vzoru | Zobrazované jméno | Popis |
|--------------|--------------|-------------|
| Identifikátor GUID         | HLAVNÍCH       | Globálně jedinečný identifikátor definovaný v [dokumentu RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Číslo       | N          | Jednu nebo více číslic. |
| Formáty data a času | N     | Azure dosah podporuje různé druhy formátů data a času, ale všechny jsou omezené na řadu {N} s. |
| 4ByteHex     | SOUSTAVY        | Šestnáctkové číslo se čtyřmi číslicemi. |
| Lokalizace | Loc        | Značka jazyka, jak je definována v [BCP 47](https://tools.ietf.org/html/bcp47). Azure dosah podporuje značky, které obsahují spojovník (-) nebo podtržítko (_). Například en_ca a en-CA. |

## <a name="issues-with-resource-sets"></a>Problémy se sadami prostředků

Přestože sady prostředků ve většině případů fungují dobře, může dojít k následujícím problémům, ve kterých Azure dosah:

- Nesprávně označí Asset jako sadu prostředků.
- Vloží Asset do nesprávné sady prostředků.
- Nesprávně označuje prostředek jako sadu prostředků.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít s Data Catalog, najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).
