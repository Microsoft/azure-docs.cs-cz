---
title: Podporované zdroje dat a typy souborů
description: Tento článek obsahuje rámcové informace o podporovaných zdrojích dat a typech souborů v dosah.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 6432cc9affd34c0fa9b832aea91932a2b9e7540f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576668"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Podporované zdroje dat a typy souborů v Azure dosah

Tento článek popisuje podporované zdroje dat, typy souborů a koncepty vyhledávání v dosah.

## <a name="supported-data-sources"></a>Podporované zdroje dat

Azure dosah podporuje následující zdroje:

| Typ úložiště | Typ podporovaného ověřování | Nastavení kontrol přes UX/PowerShell |
| ---------- | ------------------- | ------------------------------ |
| Místní SQL Server                   | Ověřování SQL                        | KONCOVÉ                                |
| Azure Synapse Analytics (dříve SQL DW)            | Ověřování SQL, instanční objekt, MSI               | KONCOVÉ                             |
| Azure SQL Database (DB)                  | Ověřování SQL, instanční objekt, MSI               | KONCOVÉ |
| Spravovaná instance Azure SQL Database      | Ověřování SQL, instanční objekt, MSI               | KONCOVÉ    |
| Azure Blob Storage                       | Klíč účtu, instanční objekt, MSI | KONCOVÉ            |
| Průzkumník dat Azure                      | Instanční objekt                              | KONCOVÉ            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | Instanční objekt, MSI                              | KONCOVÉ            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Klíč účtu, instanční objekt, MSI            | KONCOVÉ            |
| Azure Cosmos DB                          | Klíč účtu                                    | KONCOVÉ            |


> [!Note]
> Platforma Azure Data Lake Storage Gen2 je teď obecně dostupná. Doporučujeme začít ji používat ještě dnes. Další informace najdete na [stránce produktu](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-supported-for-scanning"></a>Typy souborů, které se podporují pro skenování

Následující typy souborů jsou podporovány ke skenování, pro extrakci schématu a klasifikace, pokud je to možné:

- Strukturované formáty souborů podporované příponou: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML
- Formáty souborů dokumentů podporované příponou: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Dosah podporuje také vlastní přípony souborů a vlastní analyzátory.

## <a name="sampling-within-a-file"></a>Vzorkování v rámci souboru

V terminologii dosah
- Kontrola L1: extrahuje základní informace a meta data, jako je název souboru, velikost a plně kvalifikovaný název.
- Kontrola L2: extrahuje schéma pro strukturované typy souborů a databázové tabulky.
- Prověřování L3: extrahuje schéma tam, kde je to možné, a subjektům ukázkový soubor pro systémová a vlastní pravidla klasifikace.

U všech strukturovaných formátů souborů ukázky dosah Scanner soubory následujícím způsobem:

- U strukturovaných typů souborů vychází z 128 řádků do každého sloupce nebo 1 MB, podle toho, co je nižší.
- V případě formátů souborů dokumentů má každý soubor vzorek o velikosti 20 MB.
    - Pokud je soubor dokumentu větší než 20 MB, nepodléhá hloubkovým kontrolám (podléhající klasifikaci). V takovém případě dosah zachytí pouze základní meta data, jako je název souboru a plně kvalifikovaný název.

## <a name="resource-set-file-sampling"></a>Vzorkování souborů sady prostředků

Složka nebo skupina souborů oddílů se detekuje jako *sada prostředků* v dosah, pokud se shoduje se zásadou sady prostředků systému nebo nastavením zásad pro sadu prostředků definovaných zákazníkem. Pokud se zjistí sada prostředků, pak dosah vyvzorkuje všechny složky, které obsahuje. Další informace o sadách prostředků najdete [tady](concept-resource-sets.md).

Vzorkování souborů pro sady prostředků podle typů souborů:

- **Soubory s oddělovači (CSV, PSV, SSV, TSV)** – 1 v souborech 100 jsou vzorky (prověřování L3) v rámci složky nebo skupiny souborů oddílů, které se považují za "sadu prostředků".
- **Data Lake typy souborů (Parquet, Avro, orc)** -1 v 18446744073709551615 (dlouhé) soubory jsou vzorky (prověřování L3) v rámci složky nebo skupiny souborů oddílů, které se považují za *sadu prostředků* .
- **Jiné strukturované typy souborů (JSON, XML, txt)** – 1 v 100 souborů jsou vzorkované (prověřování L3) v rámci složky nebo skupiny souborů oddílů, které se považují za "sadu prostředků".
- **Objekty SQL a CosmosDB entity** – jednotlivé soubory jsou zkontrolovány na L3.
- **Typy souborů dokumentů** – jednotlivé soubory jsou zkontrolovány na L3. Pro tyto typy souborů se vzory sady prostředků nevztahují.

## <a name="classification"></a>Classification

Všechna pravidla klasifikace systému 105 se vztahují na formáty strukturovaných souborů. Pouze pravidla klasifikace aplikace MCE se vztahují na typy souborů dokumentů (ne na nativní vzory regulárního výrazu pro kontrolu dat, rozpoznávání založené na filtrech Bloom). Další informace o podporovaných klasifikacích najdete v tématu [podporované klasifikace v Azure dosah](supported-classifications.md).

## <a name="next-steps"></a>Další kroky

- [Kurz: spuštění Starter Kit a kontrola dat](tutorial-scan-data.md)
- [Správa zdrojů dat ve službě Azure dosah (Preview)](manage-data-sources.md)