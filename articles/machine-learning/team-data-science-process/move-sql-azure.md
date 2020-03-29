---
title: Přesunutí dat do databáze Azure SQL – proces vědecké analýzy týmových dat
description: Přesuňte data z plochých souborů (formáty CSV nebo TSV) nebo z dat uložených v místním SQL Serveru do databáze Azure SQL Database.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722454"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Přesun dat do služby SQL Database pro Azure Machine Learning

Tento článek popisuje možnosti pro přesun dat z plochých souborů (formáty CSV nebo TSV) nebo z dat uložených v místním SQL Serveru do databáze Azure SQL Database. Tyto úkoly pro přesun dat do cloudu jsou součástí procesu vědecké ho sloužiny týmových dat.

Téma, které popisuje možnosti pro přesun dat do místního SQL Serveru pro strojové učení, najdete v [tématu Přesun dat na SQL Server na virtuálním počítači Azure](move-sql-server-virtual-machine.md).

Následující tabulka shrnuje možnosti pro přesun dat do databáze Azure SQL Database.

| <b>Zdroj</b> | <b>CÍL: Databáze Azure SQL</b> |
| --- | --- |
| <b>Plochý soubor (ve formátu CSV nebo TSV)</b> |[Hromadný vložit dotaz SQL](#bulk-insert-sql-query) |
| <b>Místní SQL Server</b> |1.[Exportovat do plochého souboru](#export-flat-file)<br> 2. [Průvodce migrací databáze SQL](#insert-tables-bcp)<br> 3. [Zálohování a obnovení databáze](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Požadavky
Zde uvedené postupy vyžadují, abyste měli:

* Předplatné **Azure**. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účet úložiště Azure**. Účet úložiště Azure použít pro ukládání dat v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště je třeba získat klíč účtu, který se používá pro přístup k účtu. Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
* Přístup k **databázi Azure SQL**. Pokud musíte nastavit Azure SQL Database, [Začínáme s Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) poskytuje informace o tom, jak zřídit novou instanci Azure SQL Database.
* Nainstalované a nakonfigurované **Azure PowerShell** místně. Pokyny najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

**Data**: Procesy migrace jsou demonstrovány pomocí [datové sady NYC Taxi](https://chriswhong.com/open-data/foil_nyc_taxi/). Datová sada NYC Taxi obsahuje informace o datech a veletrzích a je dostupná v úložišti objektů blob Azure: [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). Ukázka a popis těchto souborů jsou uvedeny v [NYC Taxi Výlety Dataset Popis](sql-walkthrough.md#dataset).

Můžete buď přizpůsobit zde popsané postupy sadě vlastních dat, nebo postupovat podle pokynů popsaných pomocí datové sady NYC Taxi. Chcete-li nahrát datovou sadu NYC Taxi do místní databáze serveru SQL Server, postupujte podle postupu popsaného v části [Hromadná import dat do databáze serveru SQL Server](sql-walkthrough.md#dbload). Tyto pokyny jsou pro SQL Server na virtuálním počítači Azure, ale postup pro nahrávání do místního SQL Serveru je stejný.

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a>Přesunutí dat ze zdroje plochých souborů do databáze Azure SQL Database
Data v plochých souborech (ve formátu CSV nebo TSV) lze přesunout do databáze Azure SQL pomocí hromadného vložení SQL query.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a>Hromadný vložit dotaz SQL
Kroky pro postup pomocí hromadné ho vložit SQL query jsou podobné pokyny pro přesun dat ze zdroje plochý soubor sql server na virtuálním počítači Azure. Podrobnosti naleznete v [tématu Hromadné vkládání dotazu SQL Query](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="moving-data-from-on-premises-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a>Přesunutí dat z místního SQL Serveru do databáze Azure SQL Database
Pokud jsou zdrojová data uložena v místním SQL Serveru, existují různé možnosti pro přesun dat do databáze Azure SQL:

1. [Exportovat do plochého souboru](#export-flat-file)
2. [Průvodce migrací databáze SQL](#insert-tables-bcp)
3. [Zálohování a obnovení databáze](#db-migration)
4. [Azure Data Factory](#adf)

Kroky pro první tři jsou podobné těm oddílům v [Přesunutí dat na SQL Server na virtuálním počítači Azure,](move-sql-server-virtual-machine.md) které pokrývají stejné postupy. Odkazy na příslušné části v tomto tématu jsou uvedeny v následujících pokynech.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportovat do plochého souboru
Kroky pro tento export do plochého souboru jsou podobné směrům zahrnutým v [části Export do plochého souboru](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Průvodce migrací databáze SQL
Kroky pro použití Průvodce migrací databáze SQL jsou podobné pokynům zahrnutým v [Průvodci migrací databáze SQL](move-sql-server-virtual-machine.md#sql-migration).

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Zálohování a obnovení databáze
Kroky pro použití zálohování a obnovení databáze jsou podobné pokynům uvedeným v [části Zálohování a obnovení databáze](move-sql-server-virtual-machine.md#sql-backup).

### <a name="azure-data-factory"></a><a name="adf"></a>Azure Data Factory
Zjistěte, jak přesunout data do databáze Azure SQL pomocí Azure Data Factory (ADF) v tomto tématu [Přesunout data z místního serveru SQL do SQL Azure s Azure Data Factory](move-sql-azure-adf.md). Toto téma ukazuje, jak pomocí adf přesunout data z místní databáze SQL Serveru do databáze Azure SQL prostřednictvím úložiště objektů blob Azure.

Zvažte použití ADF v případě, že data musí být neustále migrována s hybridními místními a cloudovými zdroji.  ADF také pomáhá, když data potřebují transformace nebo potřebuje novou obchodní logiku během migrace. ADF umožňuje plánování a sledování úloh pomocí jednoduchých skriptů JSON, které řídí pohyb dat v pravidelných intervalech. ADF má také další funkce, jako je například podpora složitých operací.
