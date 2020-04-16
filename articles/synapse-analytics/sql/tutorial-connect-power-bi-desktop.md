---
title: 'Kurz: Připojení SQL na vyžádání (preview) k Power BI Desktopu & vytvoření sestavy'
description: V tomto kurzu se dozvíte, jak připojit SQL na vyžádání (preview) v Azure Synapse Analytics k desktopu Power BI a vytvořit ukázkovou sestavu na základě zobrazení.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0df8ac495b6aca81e46dffc248019483b1c82202
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422449"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>Kurz: Připojení SQL na vyžádání (preview) k Power BI Desktopu & vytvoření sestavy

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvořit demo databázi
> - Vytvořit zobrazení použité pro sestavu
> - Připojení k Power BI Desktopu
> - Vytvořit sestavu na základě zobrazení

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující software:

- Nástroj pro dotazy SQL, jako je [například Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)nebo [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- [Power BI Desktop](https://powerbi.microsoft.com/downloads/).

Hodnoty pro následující parametry:

| Parametr                                 | Popis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresa koncového bodu služby SQL na vyžádání    | Používá se jako název serveru                                   |
| Oblast koncového bodu služby SQL na vyžádání     | Používá se k určení skladování použitého ve vzorcích |
| Uživatelské jméno a heslo pro přístup ke koncovému bodu | Slouží k přístupu ke koncovému bodu.                               |
| Databáze, kterou použijete k vytvoření zobrazení     | Databáze použitá jako výchozí bod ve vzorcích       |

## <a name="1---create-database"></a>1 - Vytvořit databázi

Pro ukázkové prostředí vytvořte vlastní demo databázi. Tato databáze slouží k zobrazení metadat, nikoli k ukládání skutečných dat.

Vytvořte demo databázi (a v případě potřeby přetáhněte existující databázi) spuštěním následujícího skriptu Transact-SQL (T-SQL):

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 - Vytvořit pověření

Pověření je nezbytné pro službu SQL na vyžádání pro přístup k souborům v úložišti. Vytvořte pověření pro účet úložiště, který se nachází ve stejné oblasti jako koncový bod. Přestože SQL na vyžádání přístup k účtům úložiště z různých oblastí, s úložiště a koncový bod ve stejné oblasti poskytuje lepší výkon.

Vytvořte pověření spuštěním následujícího skriptu Transact-SQL (T-SQL):

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

## <a name="3---prepare-view"></a>3 - Připravit pohled

Vytvořte zobrazení založené na externích ukázkových datech, která Power BI spotřebovává, spuštěním následujícího skriptu Transact-SQL (T-SQL):

Vytvořte `usPopulationView` zobrazení uvnitř `Demo` databáze s následujícím dotazem:

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

Us populace podle pohlaví a závod pro každý kraj USA pochází z 2000 a 2010 Decennial sčítání lidu v parketovém formátu.

| Cesta ke složce                                                  | Popis                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /uvolnění/                                                    | Nadřazená složka pro data v účtu ukázkového úložiště               |
| /uvolnění/us_population_county/                               | Usa populace datové soubory ve formátu parket, rozdělena podle roku pomocí Hive / Hadoop dělení schéma. |

## <a name="4---create-power-bi-report"></a>4 – Vytvoření sestavy Power BI

Vytvořte sestavu pro Power BI Desktop pomocí následujících kroků:

1. Otevřete aplikaci Power BI Desktop a vyberte **Získat data**.

   ![Otevřete desktopovou aplikaci Power BI a vyberte získat data.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Vyberte **Azure** > **Azure SQL Database**. 

   ![Vyberte zdroj dat.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Zadejte název serveru, na kterém je **Server** databáze umístěna, `Demo` do pole Server a zadejte název databáze. Vyberte volbu **Importovat** a pak vyberte **OK**. 

   ![Vyberte databázi v koncovém bodě.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Vyberte upřednostňovanou metodu ověřování:

    - Příklad pro AAD 
  
    ![Klikněte na Přihlásit se.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Příklad pro přihlášení sql - zadejte své uživatelské jméno a heslo.

    ![Použijte přihlášení SQL.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Vyberte `usPopulationView`zobrazení a pak vyberte **Načíst**. 

   ![Vyberte zobrazení v databázi, která je vybrána.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Počkejte na dokončení operace a zobrazí se vyskakovací okno s uvedením `There are pending changes in your queries that haven't been applied`. Vyberte **Použít změny**. 

   ![Klikněte na Použít změny.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Počkejte, až dialogové okno **Použít změny dotazu** zmizí, což může trvat několik minut. 

   ![Počkejte na dokončení dotazu.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Po dokončení načítání vyberte v tomto pořadí následující sloupce a vytvořte sestavu:
   - countyName
   - Populace
   - název_stavu

   ![Vyberte sloupce, které jsou zajímavé, a vygenerujte sestavu mapy.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení používání této sestavy odstraňte prostředky pomocí následujících kroků:

1. Odstranění přihlašovacích údajů pro účet úložiště

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. Odstranění zobrazení

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Přetažení databáze

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Další kroky

Přejdete k [souborům úložiště dotazu](develop-storage-files-overview.md) a zjistěte, jak dotazovat soubory úložiště pomocí synapse SQL.
