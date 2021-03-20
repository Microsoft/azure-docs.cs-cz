---
title: Přesun dat do procesu Azure SQL Database – tým pro datové vědy
description: Přesuňte data z plochých souborů (formáty CSV nebo TSV) nebo z dat uložených v SQL Server do Azure SQL Database.
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
ms.openlocfilehash: 42bac2df7abe00be8c0e6ffddcc9bef7ef28ba9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93309528"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Přesun dat do služby SQL Database pro Azure Machine Learning

Tento článek popisuje možnosti přesunu dat buď z plochých souborů (formáty CSV nebo TSV), nebo z dat uložených v SQL Server do Azure SQL Database. Tyto úlohy pro přesun dat do cloudu jsou součástí vědeckého procesu týmového zpracování dat.

Téma, které popisuje možnosti přesunutí dat na SQL Server pro Machine Learning najdete v tématu [přesun dat do SQL Server na virtuálním počítači Azure](move-sql-server-virtual-machine.md).

Následující tabulka shrnuje možnosti přesunutí dat do Azure SQL Database.

| <b>Zdrojová</b> | <b>CÍL: Azure SQL Database</b> |
| --- | --- |
| <b>Plochý soubor (ve formátu CSV nebo TSV)</b> |[Hromadné vložení dotazu SQL](#bulk-insert-sql-query) |
| <b>Místní SQL Server</b> |1.[Export do plochého souboru](#export-flat-file)<br> 2. [Průvodce migrací SQL Database](#insert-tables-bcp)<br> 3. [zálohování a obnovení databáze](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Předpoklady
Tady popsané postupy vyžadují:

* **Předplatné Azure** Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účet služby Azure Storage**. K ukládání dat v tomto kurzu použijete účet služby Azure Storage. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště je třeba získat klíč účtu, který se používá pro přístup k účtu. Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
* Přístup k **Azure SQL Database**. Pokud musíte nastavit Azure SQL Database, [Začínáme s Microsoft Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md) poskytuje informace o tom, jak zřídit novou instanci Azure SQL Database.
* Instalace a konfigurace **Azure PowerShell** lokálně. Pokyny najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).

**Data**: procesy migrace jsou znázorněny pomocí [datové sady taxislužby NYC](https://chriswhong.com/open-data/foil_nyc_taxi/). Datová sada taxislužby NYC obsahuje informace o datech na cestách a jejich veletrzích a je dostupná v Azure Blob Storage: [data NYC taxislužby](https://www.andresmh.com/nyctaxitrips/). Ukázka a popis těchto souborů jsou k dispozici v [popisu datové sady NYC taxislužby TRIPS](sql-walkthrough.md#dataset).

Můžete buď upravit popsané postupy na sadu vlastních dat, nebo postupovat podle pokynů popsaných v datové sadě taxislužby NYC. Pokud chcete nahrát datovou sadu taxislužby NYC do databáze SQL Server, postupujte podle pokynů uvedených v [hromadném importu dat do SQL Server databáze](sql-walkthrough.md#dbload).

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a> Přesun dat ze zdroje plochého souboru do Azure SQL Database
Data v plochých souborech (ve formátu CSV nebo ve formátu TSV) se dají přesunout do Azure SQL Database pomocí hromadného vložení dotazu SQL.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a> Hromadné vložení dotazu SQL
Postup použití příkazu hromadného vkládání SQL se podobá Postup přesunutí dat z plochého zdroje souborů na SQL Server na virtuálním počítači Azure. Podrobnosti najdete v tématu [hromadné vložení dotazu SQL](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="moving-data-from-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a> Přesun dat z SQL Server do Azure SQL Database
Pokud jsou zdrojová data uložená v SQL Server, existují různé možnosti, jak data přesouvat do Azure SQL Database:

1. [Exportovat do plochého souboru](#export-flat-file)
2. [Průvodce migrací SQL Database](#insert-tables-bcp)
3. [Zálohování a obnovení databáze](#db-migration)
4. [Azure Data Factory](#adf)

Postup pro první tři jsou podobný těm oddílům [přesunu dat na SQL Server na virtuálním počítači Azure](move-sql-server-virtual-machine.md) , který pokrývá tyto stejné postupy. Odkazy na příslušné části tohoto tématu jsou uvedené v následujících pokynech.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportovat do plochého souboru
Postup pro export do plochého souboru je podobný těm směrům popsaným v části [Export do plochého souboru](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Průvodce migrací SQL Database
Postup použití Průvodce migrací SQL Database je podobný těm směrům popsaným v [Průvodci migrací SQL Database](move-sql-server-virtual-machine.md#sql-migration).

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Zálohování a obnovení databáze
Postup použití zálohování a obnovení databáze je podobný těm směrům uvedeným v části [zálohování a obnovení databáze](move-sql-server-virtual-machine.md#sql-backup).

### <a name="azure-data-factory"></a><a name="adf"></a>Azure Data Factory
Naučte se, jak přesunout data do Azure SQL Database pomocí Azure Data Factory (ADF) v tomto tématu, [přesuňte data z SQL Server do SQL Azure s Azure Data Factory](move-sql-azure-adf.md). V tomto tématu se dozvíte, jak pomocí ADF přesunout data z databáze SQL Server do Azure SQL Database prostřednictvím Azure Blob Storage.

Pokud je potřeba průběžně migrovat data s využitím hybridních místních i cloudových zdrojů, zvažte použití ADF.  ADF také pomáhá s tím, jak data potřebují transformovat, nebo potřebuje během migrace novou obchodní logiku. ADF umožňuje plánovat a monitorovat úlohy pomocí jednoduchých skriptů JSON, které pravidelně spravují pohyb dat. ADF má také další možnosti, jako je podpora složitých operací.