---
title: Připojení k Power BI Professional
description: V tomto kurzu provedeme kroky, jak připojit Power BI Desktop k SQL na vyžádání (Preview).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a9db42bcd69d9a24a454c02c9bb0e2d339cb4860
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185774"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Připojení k synapse SQL pomocí Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

V tomto kurzu provedeme kroky pro připojení Power BI plochy k SQL na vyžádání (Preview).

## <a name="prerequisites"></a>Požadavky

Nástroj pro vydávání dotazů:

- Klient SQL podle vašeho výběru:

  - Azure Data Studio
  - SQL Server Management Studio

- Nainstalovaný Desktop Power BI

Parametry:

| Parametr                                 | Popis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresa koncového bodu služby SQL na vyžádání    | Bude použito jako název serveru.                                   |
| Oblast koncového bodu služby SQL na vyžádání     | Použije se k určení, jaké úložiště se bude používat v ukázkách. |
| Uživatelské jméno a heslo pro přístup ke koncovému bodu | Bude použito pro přístup ke koncovému bodu                               |
| Databáze, kterou použijete k vytvoření zobrazení     | Tato databáze se použije jako výchozí bod v ukázkách.       |

## <a name="first-time-setup"></a>Nastavení při prvním spuštění

Před použitím ukázek jsou k dispozici dva kroky:

1. Vytvoření databáze pro zobrazení
2. Vytvoření přihlašovacích údajů, které budou používat SQL na vyžádání pro přístup k souborům v úložišti

### <a name="create-database"></a>Vytvoření databáze

Pro tento článek Začínáme byste měli vytvořit vlastní databázi, která bude využívat jako ukázku. Pro vytváření zobrazení je zapotřebí databáze. Tuto databázi použijete v některých ukázkových dotazech v této dokumentaci.

> [!NOTE]
> Databáze se používají jenom pro zobrazení metadat, nikoli pro skutečná data.
>
> Poznamenejte si název databáze, kterou používáte, budete ji potřebovat později.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Vytvořit pověření

Než budete moct spustit dotazy, musíme vytvořit přihlašovací údaje. Přihlašovací údaje bude používat služba SQL na vyžádání k přístupu k souborům v úložišti.

> [!NOTE]
> Musíte vytvořit přihlašovací údaje pro přístup k účtu úložiště. I když má SQL na vyžádání přístup k úložišti z různých oblastí, které mají úložiště a Azure synapse Workspace ve stejné oblasti, zajistíte lepší výkon.

**Fragment kódu při vytváření přihlašovacích údajů pro datové kontejnery pro sčítání**, spusťte:

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

## <a name="creating-power-bi-desktop-report"></a>Vytváření sestavy Power BI Desktop

Otevřete Power BI desktopová aplikace a vyberte možnost **získat data** .
![Otevřete Power BI desktopovou aplikaci a vyberte získat data.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Krok 1 – Výběr zdroje dat

V nabídce vyberte **Azure** a pak **Azure SQL Database**.
![Vyberte zdroj dat.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Krok 2 – Výběr databáze

Napište adresu URL pro databázi a název databáze, ve které se zobrazení nachází.
![Na koncovém bodu vyberte databáze.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Další kroky

Přejděte k [souborům úložiště dotazů](get-started-azure-data-studio.md) a Naučte se, jak se připojit k SQL na vyžádání pomocí Azure Data Studio.
 