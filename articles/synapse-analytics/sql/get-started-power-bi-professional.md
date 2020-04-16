---
title: Připojení k Power BI Professional
description: V tomto kurzu projdeme kroky, jak připojit desktop Power BI k SQL na vyžádání (preview).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0ce8f3a447f1896ae6d96d343782f8cdb44d4c6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422561"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Připojení k Synapse SQL s Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

V tomto kurzu projdeme kroky, jak připojit desktop Power BI k SQL na vyžádání (preview).

## <a name="prerequisites"></a>Požadavky

Nástroj pro vydávání dotazů:

- SQL klientdle dle vašeho výběru:

  - Azure Data Studio
  - SQL Server Management Studio

- Nainstalovaná desktopová plocha Power BI

Parametry:

| Parametr                                 | Popis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresa koncového bodu služby SQL na vyžádání    | Bude použit jako název serveru                                   |
| Oblast koncového bodu služby SQL na vyžádání     | Bude použit k určení, jaké skladování budeme používat ve vzorcích |
| Uživatelské jméno a heslo pro přístup ke koncovému bodu | Bude použit pro přístup ke koncovému bodu                               |
| Databáze, kterou použijete k vytvoření zobrazení     | Tato databáze bude použita jako výchozí bod ve vzorcích       |

## <a name="first-time-setup"></a>První nastavení

Před použitím vzorků jsou dva kroky:

1. Vytvoření databáze pro vaše zobrazení
2. Vytvoření přihlašovacích údajů pro přístup k souborům v úložišti pomocí sql na vyžádání

### <a name="create-database"></a>Vytvoření databáze

Vzhledem k tomu, budete používat demo prostředí, měli byste vytvořit vlastní databázi pro demo účely. Databáze je potřeba k vytvoření zobrazení v něm. Tuto databázi použijete v některých ukázkových dotazech v této dokumentaci.

> [!NOTE]
> Všimněte si, že databáze se používají pouze pro zobrazení metadat, nikoli pro skutečná data.
>
> Poznamenejte si název databáze, který používáte, budete jej později potřebovat.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Vytvoření přihlašovacích údajů

Musíme vytvořit pověření před spuštěním dotazů. Toto pověření bude používat služba SQL na vyžádání pro přístup k souborům v úložišti.

> [!NOTE]
> Všimněte si, že je třeba vytvořit přihlašovací údaje pro přístup k účtu úložiště. Přestože SQL na vyžádání přístup k úložištím z různých oblastí, s úložiště a Azure Synapse pracovní ho prostoru ve stejné oblasti bude poskytovat lepší výkon prostředí.

**Fragment kódu o tom, jak vytvořit pověření pro kontejnery dat sčítání**, spusťte:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="creating-power-bi-desktop-report"></a>Vytváření sestavy power bi plochy

Otevřete desktopovou aplikaci Power BI a vyberte možnost "Získat data".
![Otevřete desktopovou aplikaci Power BI a vyberte získat data.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Krok 1 – Výběr zdroje dat

V nabídce vyberte "Azure" a pak "Azure SQL Database".
![Vyberte zdroj dat.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Krok 2 – Vybrat databázi

Napište adresu URL databáze a název databáze, ve které je zobrazení s bydlištěm.
![Vyberte databázi v koncovém bodě.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Další kroky

Přejdete k [souborům úložiště dotazu](get-started-azure-data-studio.md) a zjistěte, jak se připojit k SQL na vyžádání pomocí Azure Data Studia.
 