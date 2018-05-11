---
title: Přesun dat do Azure SQL Database pro Azure Machine Learning | Microsoft Docs
description: Vytvoření tabulky SQL a načtení dat do tabulky SQL
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2018
ms.author: deguhath
ms.openlocfilehash: ce349aedc6b733d34ab61eb2e23b378727e01800
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Přesun dat do služby SQL Database pro Azure Machine Learning
Toto téma popisuje možnosti pro přesun dat z plochých souborů (formáty CSV nebo TSV) nebo z data uložená v SQL serveru místně do Azure SQL database. Tyto úlohy pro přesun dat do cloudu jsou součástí procesu Team dat vědecké účely.

Téma, které popisuje možnosti pro přesun dat na místní Server SQL pro Machine Learning, najdete v části [přesun dat do systému SQL Server na virtuální počítač Azure](move-sql-server-virtual-machine.md).

Následující **nabídky** odkazy na témata, které popisují, jak ingestovat data do cílové prostředí, kde můžete data ukládat a zpracovávat během tým datové vědy procesu (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Následující tabulka shrnuje možnosti pro přesun dat do Azure SQL Database.

| <b>ZDROJ</b> | <b>Cíl: Azure SQL Database</b> |
| --- | --- |
| <b>Plochý soubor (CSV nebo formátu TSV)</b> |[Hromadné vložení SQL dotazu](#bulk-insert-sql-query) |
| <b>Místní SQL Server</b> |1.[exportovat do plochý soubor](#export-flat-file)<br> 2. [Průvodce migrací databáze SQL](#insert-tables-bcp)<br> 3. [Databáze back up a obnovení](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Požadavky
Postupů uvedených v tomto poli vyžadovat, že máte:

* **Předplatné**. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účtu úložiště Azure**. Používáte účet úložiště Azure pro ukládání dat v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Po vytvoření účtu úložiště je třeba získat klíč účtu, který se používá pro přístup k účtu. V tématu [Správa přístupových klíčů úložiště](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Přístup **databáze Azure SQL**. Pokud je potřeba nastavit Azure SQL Database, [Začínáme se službou Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) poskytuje informace o tom, jak zřídit novou instanci třídy Azure SQL Database.
* Nainstalovaný a nakonfigurovaný **prostředí Azure PowerShell** místně. Pokyny najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

**Data**: procesy migrace je ukázán pomocí [datovou sadu NYC taxíkem](http://chriswhong.com/open-data/foil_nyc_taxi/). Datová sada NYC taxíkem obsahuje informace o cestě dat a veletrzích a je k dispozici v úložišti objektů blob v Azure: [NYC taxíkem Data](http://www.andresmh.com/nyctaxitrips/). Ukázka a popis tyto soubory jsou uvedeny v [NYC taxíkem služebních cest datovou sadu popis](sql-walkthrough.md#dataset).

Můžete přizpůsobit podle postupů popsaných v tomto poli na sadu svoje vlastní data, nebo postupujte podle kroků, jak je popsáno pomocí NYC taxíkem datovou sadu. Datová sada NYC taxíkem nahrát do místní databáze systému SQL Server, postupujte podle pokynů uvedených v [hromadně importovat Data do databáze serveru SQL](sql-walkthrough.md#dbload). Tyto pokyny jsou pro systém SQL Server na virtuální počítač Azure, ale postup pro odesílání na místní SQL Server je stejný.

## <a name="file-to-azure-sql-database"></a> Přesun dat ze zdroje plochého souboru do databáze Azure SQL
Data v plochých souborů (formátu CSV nebo TSV) lze přesunout do Azure SQL database pomocí hromadné vložení dotazu SQL.

### <a name="bulk-insert-sql-query"></a> Hromadné vložení SQL dotazu
Kroky pro postup pomocí hromadného vkládání SQL dotazu jsou podobné těm, které jsou popsané v částech pro přesun dat z plochých souborů zdroje k systému SQL Server na virtuálním počítači Azure. Podrobnosti najdete v tématu [hromadné vložení dotazu SQL](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Přesun dat z místního SQL serveru do Azure SQL database
Pokud zdrojová data je uložená v SQL serveru místní, existují různé možnosti pro přesun dat do Azure SQL database:

1. [Exportovat do plochý soubor](#export-flat-file)
2. [Průvodce migrací databáze SQL](#insert-tables-bcp)
3. [Databáze back up a obnovení](#db-migration)
4. [Azure Data Factory](#adf)

Kroky pro první tři jsou velmi podobné tyto oddíly v [přesun dat do systému SQL Server na virtuální počítač Azure](move-sql-server-virtual-machine.md) , zahrnují tyto stejné postupy. Odkazy na příslušné části v tomto tématu jsou uvedeny v následujících pokynech.

### <a name="export-flat-file"></a>Exportovat do plochý soubor
Kroky pro export do plochý soubor jsou podobné těm, které jsou zahrnuté v [exportovat do plochý soubor](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Průvodce migrací databáze SQL
Kroky pro pomocí Průvodce migrací databáze SQL jsou podobné těm, které jsou zahrnuté v [Průvodce migrací databáze SQL](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Databáze back up a obnovení
Kroky pro použití databáze zálohování a obnovení jsou podobné těm, které jsou zahrnuté v [databáze back up a obnovení](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
Postup pro přesun dat do databáze Azure SQL s Azure Data Factory (ADF) najdete v tématu [přesun dat z místního serveru SQL do SQL Azure s Azure Data Factory](move-sql-azure-adf.md). Toto téma ukazuje, jak pro přesun dat z místní databáze SQL serveru do Azure SQL database přes Azure Blob Storage pomocí ADF.

Zvažte použití ADF při dat je potřeba průběžně migrovat v hybridním scénáři, který má přístup k místní a cloudové prostředky, a při dat je zpracován, nebo musí být změněna nebo mít obchodní logiky přidána při migraci. ADF umožňuje na plánování a sledování úloh pomocí jednoduché skripty JSON, které spravují přesun dat v pravidelných intervalech. ADF má také další funkce, například podporu pro komplexní operace.
