---
title: Přesun dat do služby Azure SQL Database – vědecké zpracování týmových dat
description: Přesuňte data z plochých souborů (formáty CSV nebo TSV) nebo z dat uložených v místní SQL Server do Azure SQL Database.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722454"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Přesun dat do služby SQL Database pro Azure Machine Learning

Tento článek popisuje možnosti přesunu dat buď z plochých souborů (formáty CSV nebo TSV), nebo z dat uložených v místních SQL Server do Azure SQL Database. Tyto úlohy pro přesun dat do cloudu jsou součástí vědecké zpracování týmových dat.

Téma, které popisuje možnosti pro přesun dat do SQL serveru v místním pro Machine Learning, naleznete v tématu [přesun dat do SQL serveru na virtuálním počítači Azure](move-sql-server-virtual-machine.md).

Následující tabulka shrnuje možnosti pro přesun dat do služby Azure SQL Database.

| <b>ZDROJ</b> | <b>Cíl: Azure SQL Database</b> |
| --- | --- |
| <b>Plochý soubor (sdíleného svazku clusteru nebo ve formátu TSV)</b> |[Dotaz SQL pro hromadné vložení](#bulk-insert-sql-query) |
| <b>Na místním SQL serveru</b> |1.[exportovat do plochých souborů](#export-flat-file)<br> 2. [Průvodce migrací SQL Database](#insert-tables-bcp)<br> 3. [zálohování a obnovení databáze](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Požadavky
Postupy, podle zde uvedeného vyžadují, abyste měli:

* **Předplatného Azure**. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účtu služby Azure storage**. Pro ukládání dat v tomto kurzu použijete účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště je třeba získat klíč účtu, který se používá pro přístup k účtu. Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
* Přístup **Azure SQL Database**. Pokud je nutné nastavit Azure SQL Database, [Začínáme s Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) poskytuje informace o tom, jak zřídit novou instanci služby Azure SQL Database.
* Nainstalovaný a nakonfigurovaný **prostředí Azure PowerShell** místně. Pokyny najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

**Data**: procesu migrace je ukázán pomocí [datovou sadu NYC taxislužby](https://chriswhong.com/open-data/foil_nyc_taxi/). NYC taxislužby datová sada obsahuje informace o data o jízdách a veletrhů a je k dispozici ve službě Azure blob storage: [Data taxislužby města NYC](https://www.andresmh.com/nyctaxitrips/). Ukázka a popis tyto soubory jsou k dispozici v [NYC taxislužby zkracuje dobu odezvy datovou sadu popis](sql-walkthrough.md#dataset).

Můžete přizpůsobit podle postupů popsaných tady na sadu vlastních dat nebo postupujte podle pokynů, jak je popsáno s použitím datové sady NYC taxislužby. K odeslání NYC taxislužby datovou sadu do místní databáze SQL serveru, postupujte podle postupu uvedeného v [hromadného importu dat do databáze SQL serveru](sql-walkthrough.md#dbload). Tyto pokyny jsou určené pro SQL Server na virtuálním počítači Azure, ale postup pro jeho odeslání do místního SQL serveru je stejný.

## <a name="file-to-azure-sql-database"></a>Přesun dat ze zdroje plochého souboru do Azure SQL Database
Data v plochých souborech (ve formátu CSV nebo ve formátu TSV) se dají přesunout do Azure SQL Database pomocí hromadného vložení dotazu SQL.

### <a name="bulk-insert-sql-query"></a> Dotaz SQL pro hromadné vložení
Postup použití příkazu hromadného vkládání SQL se podobá Postup přesunutí dat z plochého zdroje souborů na SQL Server na virtuálním počítači Azure. Podrobnosti najdete v tématu [dotaz SQL vložte hromadné](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Přesun dat z místních SQL Server do Azure SQL Database
Pokud jsou zdrojová data uložená v místních SQL Server, existují různé možnosti, jak data přesouvat do Azure SQL Database:

1. [Exportovat do plochých souborů](#export-flat-file)
2. [Průvodce migrací služby SQL Database](#insert-tables-bcp)
3. [Databáze back up a obnovení](#db-migration)
4. [Azure Data Factory](#adf)

Postup pro první tři jsou podobný těm oddílům [přesunu dat na SQL Server na virtuálním počítači Azure](move-sql-server-virtual-machine.md) , který pokrývá tyto stejné postupy. Odkazy na příslušné části v tomto tématu jsou k dispozici v následujících pokynech.

### <a name="export-flat-file"></a>Exportovat do plochých souborů
Postup pro export do plochého souboru je podobný těm směrům popsaným v části [Export do plochého souboru](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Průvodce migrací služby SQL Database
Postup použití Průvodce migrací SQL Database je podobný těm směrům popsaným v [Průvodci migrací SQL Database](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Databáze back up a obnovení
Postup použití zálohování a obnovení databáze je podobný těm směrům uvedeným v části [zálohování a obnovení databáze](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
Naučte se, jak přesunout data do Azure SQL Database pomocí Azure Data Factory (ADF) v tomto tématu, [přesuňte data z místního SQL serveru na SQL Azure s Azure Data Factory](move-sql-azure-adf.md). V tomto tématu se dozvíte, jak pomocí ADF přesunout data z místní databáze SQL Server do Azure SQL Database prostřednictvím Azure Blob Storage.

Pokud je potřeba průběžně migrovat data s využitím hybridních místních i cloudových zdrojů, zvažte použití ADF.  ADF také pomáhá s tím, jak data potřebují transformovat, nebo potřebuje během migrace novou obchodní logiku. ADF umožňuje pro plánování a monitorování úloh pomocí jednoduchých skriptů JSON, které spravují pohybu dat v pravidelných intervalech. ADF má také další funkce, jako třeba podporu pro složité operace.
