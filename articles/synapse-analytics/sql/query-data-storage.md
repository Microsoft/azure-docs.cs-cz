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
ms.openlocfilehash: e18fc765385e6d703e735a1ca15c539c32f36e93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82116243"
---
# <a name="overview-query-data-in-storage"></a>Přehled: dotazování na data v úložišti

Tato část obsahuje ukázkové dotazy, které můžete použít k vyzkoušení prostředku SQL na vyžádání (Preview) v rámci služby Azure synapse Analytics.
Aktuálně podporované soubory jsou: 
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

| Parametr                                 | Popis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresa koncového bodu služby SQL na vyžádání    | Bude použito jako název serveru.                                   |
| Oblast koncového bodu služby SQL na vyžádání     | Použije se k určení úložiště používaného v ukázkách. |
| Uživatelské jméno a heslo pro přístup ke koncovému bodu | Bude použit pro přístup ke koncovému bodu.                               |
| Databáze, kterou použijete k vytvoření zobrazení     | Tato databáze se použije jako výchozí bod pro ukázky.       |

## <a name="first-time-setup"></a>Nastavení při prvním spuštění

Než začnete používat ukázky uvedené dále v tomto článku, máte dva kroky:

- Vytvoření databáze pro zobrazení (pro případ, že chcete použít zobrazení)
- Vytvoří přihlašovací údaje, které bude SQL na vyžádání používat pro přístup k souborům v úložišti.

### <a name="create-database"></a>Vytvoření databáze

Chcete-li vytvořit zobrazení, potřebujete databázi. Tuto databázi použijete pro některé z ukázkových dotazů v této dokumentaci.

> [!NOTE]
> Databáze se používají jenom pro zobrazení metadat, nikoli pro skutečná data.  Poznamenejte si název databáze, který používáte, budete ho potřebovat později.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Vytvořit pověření

Než budete moct spustit dotazy, musíte vytvořit přihlašovací údaje. Tento přihlašovací údaj bude používat služba SQL na vyžádání pro přístup k souborům v úložišti.

> [!NOTE]
> Aby bylo možné úspěšně spustit postup v této části, je nutné použít token SAS.
>
> Chcete-li začít používat tokeny SAS, je třeba vyřadit UserIdentity, který je vysvětlen v následujícím [článku](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> SQL na vyžádání ve výchozím nastavení vždy používá předávací průchozí služba AAD.

Další informace o tom, jak spravovat řízení přístupu k úložišti, najdete v tomto [odkazu](develop-storage-files-storage-access-control.md).

Pokud chcete vytvořit přihlašovací údaje pro kontejnery CSV, JSON a Parquet, spusťte následující kód:

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
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

| Cesta ke složce                                                  | Popis                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Formát                                                        | Nadřazená složka pro data ve formátu CSV                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Složky s datovými soubory populace v různých formátech CSV. |
| /csv/taxi/                                                   | Složka se soubory veřejných dat NYC ve formátu CSV              |
| Parquet                                                    | Nadřazená složka pro data ve formátu Parquet                     |
| /parquet/taxi                                                | NYC veřejné datové soubory ve formátu Parquet, rozdělené podle roku a měsíčně pomocí schématu dělení na oddíly (Hadoop). |
| /parquet/nested/                                             | Ukázkové soubory Parquet s vnořenými sloupci                     |
| JSON                                                       | Nadřazená složka pro data ve formátu JSON                        |
| /json/books/                                                 | Soubory JSON s daty z knih                                   |

## <a name="validation"></a>Ověřování

Spusťte následující tři dotazy a ověřte, zda jsou pověření vytvořena správně.

> [!NOTE]
> Všechny identifikátory URI v ukázkových dotazech používají účet úložiště umístěný v Severní Evropa oblasti Azure. Ujistěte se, že jste vytvořili příslušné přihlašovací údaje. Spusťte dotaz níže a ujistěte se, že je uvedený účet úložiště.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Pokud nemůžete najít příslušné přihlašovací údaje, ověřte si [nastavení prvního času](#first-time-setup).

### <a name="sample-query"></a>Ukázkový dotaz

Posledním krokem ověření je spuštění následujícího dotazu:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
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
