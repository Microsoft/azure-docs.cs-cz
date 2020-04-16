---
title: Přehled - Dotazovat data v úložišti pomocí SQL na vyžádání (preview)
description: Tato část obsahuje ukázkové dotazy, které můžete použít k vyzkoušení prostředku SQL na vyžádání (preview) v rámci Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424899"
---
# <a name="overview-query-data-in-storage"></a>Přehled: Dotazna data v úložišti

Tato část obsahuje ukázkové dotazy, které můžete použít k vyzkoušení prostředku SQL na vyžádání (preview) v rámci Azure Synapse Analytics.
Aktuálně podporované soubory jsou: 
- CSV
- Parketové
- JSON

## <a name="prerequisites"></a>Požadavky

Nástroje, které potřebujete k dotazování:

- SQL klientdle dle vašeho výběru:
    - Azure Synapse Studio (náhled)
    - Azure Data Studio
    - SQL Server Management Studio

Kromě toho jsou parametry následující:

| Parametr                                 | Popis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresa koncového bodu služby SQL na vyžádání    | Bude použit jako název serveru.                                   |
| Oblast koncového bodu služby SQL na vyžádání     | Bude použit k určení skladování použitého ve vzorcích. |
| Uživatelské jméno a heslo pro přístup ke koncovému bodu | Bude použit pro přístup ke koncovému bodu.                               |
| Databáze, kterou použijete k vytvoření zobrazení     | Tato databáze bude použita jako výchozí bod pro vzorky.       |

## <a name="first-time-setup"></a>První nastavení

Před použitím ukázky obsažené dále v tomto článku, máte dva kroky:

- Vytvoření databáze pro vaše zobrazení (v případě, že chcete zobrazit zobrazení)
- Vytvoření přihlašovacích údajů pro přístup k souborům v úložišti pomocí sql na vyžádání

### <a name="create-database"></a>Vytvoření databáze

K vytvoření zobrazení potřebujete databázi. Tuto databázi použijete pro některé ukázkové dotazy v této dokumentaci.

> [!NOTE]
> Databáze se používají pouze pro zobrazení metadat, nikoli pro skutečná data.  Poznamenejte si název databáze, který používáte, budete jej později potřebovat.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Vytvoření přihlašovacích údajů

Před spuštěním dotazů je nutné vytvořit pověření. Toto pověření bude používat služba SQL na vyžádání pro přístup k souborům v úložišti.

> [!NOTE]
> Chcete-li úspěšně spustit how to's v této části, musíte použít token SAS.
>
> Chcete-li začít používat tokeny SAS, musíte vynechat useridentity, která je vysvětlena v následujícím [článku](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> SQL na vyžádání ve výchozím nastavení vždy používá předávací zařízení AAD.

Další informace o správě řízení přístupu k úložišti naleznete na tomto [odkazu](develop-storage-files-storage-access-control.md).

> [!WARNING]
> Musíte vytvořit přihlašovací údaje pro účet úložiště, který se nachází v oblasti koncového bodu. Přestože SQL na vyžádání přístup k úložištím z různých oblastí, s úložiště a koncový bod ve stejné oblasti bude poskytovat lepší výkon prostředí.

Chcete-li vytvořit pověření pro kontejnery CSV, JSON a Parkety, spusťte následující kód:

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

Ukázková data obsahují následující datové sady:

- NYC Taxi - Yellow Taxi Trip Records - část veřejného nyc datového souboru
  - Formát CSV
  - Formát Parquet
- Soubor údajů o populaci
  - Formát CSV
- Ukázkové soubory parket s vnořenými sloupci
  - Formát Parquet
- Knihy JSON
  - Formát JSON

| Cesta ke složce                                                  | Popis                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Nadřazená složka pro data ve formátu CSV                         |
| /csv/populace/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted /csv/population-unix-hdr-quoted /csv/population-unix-hdr-quoted /cs | Složky s datovými soubory populace v různých formátech CSV. |
| /csv/taxi/                                                   | Složka s veřejnými datovými soubory NYC ve formátu CSV              |
| /parkety/                                                    | Nadřazená složka pro data ve formátu Parkety                     |
| /parkety/taxi                                                | NYC veřejné datové soubory ve formátu parket, rozdělené podle roku, a měsíc pomocí Hive / Hadoop dělení schéma. |
| /parkety/vnořené/                                             | Ukázkové soubory parket s vnořenými sloupci                     |
| /json/                                                       | Nadřazená složka pro data ve formátu JSON                        |
| /json/knihy/                                                 | JSON soubory s daty knih                                   |

## <a name="validation"></a>Ověřování

Spusťte následující tři dotazy a zkontrolujte, zda jsou pověření vytvořena správně.

> [!NOTE]
> Všechny identifikátory URI v ukázkových dotazech používají účet úložiště umístěný v oblasti Azure v severní Evropě. Ujistěte se, že jste vytvořili příslušné pověření. Spusťte níže uvedený dotaz a ujistěte se, že je uveden účet úložiště.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Pokud nemůžete najít příslušné přihlašovací údaje, zkontrolujte [první nastavení](#first-time-setup).

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

Výše uvedený dotaz by měl vrátit toto číslo: **8945574**.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni pokračovat v následujících článcích Jak na to:

- [Dotaz na jeden soubor CSV](query-single-csv-file.md)

- [Složky dotazů a více souborů CSV](query-folders-multiple-csv-files.md)

- [Soubory specifické pro dotaz](query-specific-files.md)

- [Soubory parket dotazu](query-parquet-files.md)

- [Typy vnořených par dotazů](query-parquet-nested-types.md)

- [Dotaz na soubory JSON](query-json-files.md)

- [Vytváření a používání zobrazení](create-use-views.md)
