---
title: 'Kurz: připojení SQL na vyžádání (Preview) k Power BI Desktop & vytvoření sestavy'
description: V tomto kurzu se naučíte připojit SQL na vyžádání (Preview) ve službě Azure synapse Analytics a Power BI plochu a vytvořit ukázkovou sestavu založenou na zobrazení.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e0ac6ccde2443a7b374d9eb85f6f960af79c69dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81769481"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>Kurz: připojení SQL na vyžádání (Preview) k Power BI Desktop & vytvoření sestavy

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvořit ukázkovou databázi
> - Vytvořit zobrazení použité pro sestavu
> - Připojení k aplikaci Power BI Desktop
> - Vytvořit sestavu na základě zobrazení

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující software:

- Nástroj SQL Query, například [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), nebo [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- [Power BI Desktop](https://powerbi.microsoft.com/downloads/).

Hodnoty pro následující parametry:

| Parametr                                 | Popis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresa koncového bodu služby SQL na vyžádání    | Používá se jako název serveru.                                   |
| Oblast koncového bodu služby SQL na vyžádání     | Slouží k určení úložiště používaného v ukázkách. |
| Uživatelské jméno a heslo pro přístup ke koncovému bodu | Používá se pro přístup ke koncovému bodu.                               |
| Databáze, kterou použijete k vytváření zobrazení     | Databáze použitá jako výchozí bod v ukázkách       |

## <a name="1---create-database"></a>1. vytvoření databáze

Pro ukázkové prostředí vytvořte vlastní ukázkovou databázi. Tato databáze slouží k zobrazení metadat, nikoli k uložení skutečných dat.

Vytvořte ukázkovou databázi (a v případě potřeby vyřaďte existující databázi) spuštěním následujícího skriptu Transact-SQL (T-SQL):

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.databases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 – Vytvoření přihlašovacích údajů

Přihlašovací údaje jsou nezbytné, aby služba SQL na vyžádání mohla přistupovat k souborům v úložišti. Vytvořte přihlašovací údaje pro účet úložiště, který se nachází ve stejné oblasti jako váš koncový bod. Přestože SQL na vyžádání má přístup k účtům úložiště z různých oblastí, má úložiště a koncový bod ve stejné oblasti lepší výkon.

Přihlašovací údaje vytvořte spuštěním následujícího skriptu Transact-SQL (T-SQL):

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = '';
GO
```

## <a name="3---prepare-view"></a>3. Příprava zobrazení

Vytvořte zobrazení na základě externích ukázkových dat pro Power BI, která se mají spotřebovat spuštěním následujícího skriptu Transact-SQL (T-SQL):

Vytvořte zobrazení `usPopulationView` uvnitř databáze `Demo` pomocí následujícího dotazu:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS uspv;
```

Ukázková data obsahují následující sady dat:

Naplnění žen a rasy u každého 2000 okresu v USA, který je ve formátu Parquet Decennial a 2010.

| Cesta ke složce                                                  | Popis                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /Release                                                    | Nadřazená složka pro data v ukázkovém účtu úložiště               |
| /Release/us_population_county/                               | Datové soubory v USA ve formátu Parquet jsou rozdělené podle roku pomocí schématu dělení na oddíly nebo Hadoop. |

## <a name="4---create-power-bi-report"></a>4. vytvoření sestavy Power BI

Vytvořte sestavu pro Power BI Desktop pomocí následujících kroků:

1. Otevřete aplikaci Power BI Desktop a vyberte **získat data**.

   ![Otevřete Power BI desktopovou aplikaci a vyberte získat data.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Vyberte **Azure** > **Azure SQL Database**. 

   ![Vyberte zdroj dat.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Do pole **Server** zadejte název serveru, kde se databáze nachází, a pak zadejte `Demo` název databáze. Vyberte možnost **Import** a pak vyberte **OK**. 

   ![Na koncovém bodu vyberte databáze.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Vyberte upřednostňovanou metodu ověřování:

    - Příklad pro AAD 
  
    ![Klikněte na Přihlásit se.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Příklad přihlašovacího jména SQL – zadejte své uživatelské jméno a heslo.

    ![Použijte přihlášení SQL.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Vyberte zobrazení `usPopulationView`a pak vyberte **načíst**. 

   ![Vyberte zobrazení vybrané databáze.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Počkejte na dokončení operace a zobrazí se automaticky otevírané okno s oznámením `There are pending changes in your queries that haven't been applied`. Vyberte **použít změny**. 

   ![Klikněte na použít změny.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Počkejte, než dialogové okno **použít změny dotazů** zmizí, což může trvat několik minut. 

   ![Počkejte, až se dotaz dokončí.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Až se zatížení dokončí, vyberte následující sloupce v tomto pořadí, aby se vytvořila sestava:
   - název oblasti
   - výběr
   - stateName

   ![Vyberte sloupce zájmu pro vygenerování sestavy mapy.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení práce s touto sestavou tyto prostředky odstraňte pomocí následujících kroků:

1. Odstranit přihlašovací údaje účtu úložiště

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. Odstranit zobrazení

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Odstranit databázi

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Další kroky

Přejděte do [souborů úložiště dotazů](develop-storage-files-overview.md) a Naučte se, jak zadávat dotazy na soubory úložiště pomocí synapse SQL.
