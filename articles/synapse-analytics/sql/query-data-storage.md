---
title: Přehled – dotazování dat v úložišti pomocí SQL na vyžádání (Preview)
description: Tato část obsahuje ukázkové dotazy, které můžete použít k vyzkoušení prostředku SQL na vyžádání (Preview) v rámci služby Azure synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8501f9d07ffa2d04915d4d1a351317cc145f9844
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118266"
---
# <a name="overview-query-data-in-storage"></a>Přehled: dotazování na data v úložišti

Tato část obsahuje ukázkové dotazy, které můžete použít k vyzkoušení prostředku SQL na vyžádání (Preview) v rámci služby Azure synapse Analytics.
Aktuálně podporované formáty souborů:  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Požadavky

Nástroje, které potřebujete k vydávání dotazů:

- Klient SQL podle vašeho výběru:
    - Azure synapse Studio (Preview)
    - Azure Data Studio
    - SQL Server Management Studio

Kromě toho parametry jsou následující:

| Parametr                                 | Description                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresa koncového bodu služby SQL na vyžádání    | Bude použito jako název serveru.                                   |
| Oblast koncového bodu služby SQL na vyžádání     | Použije se k určení úložiště používaného v ukázkách. |
| Uživatelské jméno a heslo pro přístup ke koncovému bodu | Bude použit pro přístup ke koncovému bodu.                               |
| Databáze, kterou použijete k vytvoření zobrazení     | Tato databáze se použije jako výchozí bod pro ukázky.       |

## <a name="first-time-setup"></a>Nastavení při prvním spuštění

Prvním krokem je **Vytvoření databáze** , ve které budete spouštět dotazy. Pak inicializujte objekty spuštěním [instalačního skriptu](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) v této databázi. Tento instalační skript vytvoří zdroje dat, přihlašovací údaje v oboru databáze a formáty externích souborů, které se použijí pro čtení dat v těchto ukázkách.

> [!NOTE]
> Databáze se používají jenom pro zobrazení metadat, nikoli pro skutečná data.  Poznamenejte si název databáze, který používáte, budete ho potřebovat později.

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>Poskytnutá ukázková data

Ukázková data obsahují následující sady dat:

- NYC taxislužby – žlutý záznam o cestách taxislužby – součást veřejné sady dat NYC
  - Formát CSV
  - Formát Parquet
- Sada dat naplnění
  - Formát CSV
- Ukázkové soubory Parquet s vnořenými sloupci
  - Formát Parquet
- Kniha JSON
  - Formát JSON

| Cesta ke složce                                                  | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Formát                                                        | Nadřazená složka pro data ve formátu CSV                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Složky s datovými soubory populace v různých formátech CSV. |
| /csv/taxi/                                                   | Složka se soubory veřejných dat NYC ve formátu CSV              |
| Parquet                                                    | Nadřazená složka pro data ve formátu Parquet                     |
| /parquet/taxi                                                | NYC veřejné datové soubory ve formátu Parquet, rozdělené podle roku a měsíčně pomocí schématu dělení na oddíly (Hadoop). |
| /parquet/nested/                                             | Ukázkové soubory Parquet s vnořenými sloupci                     |
| JSON                                                       | Nadřazená složka pro data ve formátu JSON                        |
| /json/books/                                                 | Soubory JSON s daty z knih                                   |

### <a name="sample-query"></a>Ukázkový dotaz

Posledním krokem ověření je spuštění následujícího dotazu:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc;
```

Výše uvedený dotaz by měl vracet toto číslo: **8945574**.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni pokračovat s následujícím článkem:

- [Dotaz na jeden soubor CSV](query-single-csv-file.md)

- [Složky dotazů a více souborů CSV](query-folders-multiple-csv-files.md)

- [Dotazování konkrétních souborů](query-specific-files.md)

- [Dotazování souborů Parquet](query-parquet-files.md)

- [Dotazování vnořených typů Parquet](query-parquet-nested-types.md)

- [Dotazování souborů JSON](query-json-files.md)

- [Vytváření a používání zobrazení](create-use-views.md)
