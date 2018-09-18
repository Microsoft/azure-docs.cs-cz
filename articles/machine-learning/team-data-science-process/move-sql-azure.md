---
title: Přesun dat do služby Azure SQL Database pro Azure Machine Learning | Dokumentace Microsoftu
description: Vytvoření tabulky SQL a načtení dat do tabulky SQL
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2018
ms.author: deguhath
ms.openlocfilehash: f9e456ec28695c63384f1c4fdd4b04ec37fefc14
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45728807"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Přesun dat do služby SQL Database pro Azure Machine Learning
Toto téma popisuje možnosti pro přesun dat z plochých souborů (sdíleného svazku clusteru nebo TSV formátů) nebo z dat uložených v k místním SQL serveru do Azure SQL database. Tyto úlohy pro přesun dat do cloudu jsou součástí vědecké zpracování týmových dat.

Téma, které popisuje možnosti pro přesun dat do SQL serveru v místním pro Machine Learning, naleznete v tématu [přesun dat do SQL serveru na virtuálním počítači Azure](move-sql-server-virtual-machine.md).

Následující **nabídky** odkazy na témata popisující ingestovat data do cílových prostředí, kde můžete data ukládat a zpracovávat během zpracování dat vědy (TDSP) týmu.

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Následující tabulka shrnuje možnosti pro přesun dat do služby Azure SQL Database.

| <b>ZDROJ</b> | <b>Cíl: Azure SQL Database</b> |
| --- | --- |
| <b>Plochý soubor (sdíleného svazku clusteru nebo ve formátu TSV)</b> |[Dotaz SQL pro hromadné vložení](#bulk-insert-sql-query) |
| <b>Na místním SQL serveru</b> |1.[exportovat do plochých souborů](#export-flat-file)<br> 2. [Průvodce migrací služby SQL Database](#insert-tables-bcp)<br> 3. [Databáze back up a obnovení](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Požadavky
Postupy, podle zde uvedeného vyžadují, abyste měli:

* **Předplatného Azure**. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* **Účtu služby Azure storage**. Pro ukládání dat v tomto kurzu použijete účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md). Po vytvoření účtu úložiště je třeba získat klíč účtu, který se používá pro přístup k účtu. Zobrazit [Správa přístupových klíčů úložiště](../../storage/common/storage-account-manage.md#access-keys).
* Přístup **Azure SQL Database**. Pokud je nutné nastavit Azure SQL Database, [Začínáme s Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) poskytuje informace o tom, jak zřídit novou instanci služby Azure SQL Database.
* Nainstalovaný a nakonfigurovaný **prostředí Azure PowerShell** místně. Pokyny najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

**Data**: procesu migrace je ukázán pomocí [datovou sadu NYC taxislužby](http://chriswhong.com/open-data/foil_nyc_taxi/). NYC taxislužby datová sada obsahuje informace o data o jízdách a veletrhů a je k dispozici ve službě Azure blob storage: [Data taxislužby města NYC](http://www.andresmh.com/nyctaxitrips/). Ukázka a popis tyto soubory jsou k dispozici v [NYC taxislužby zkracuje dobu odezvy datovou sadu popis](sql-walkthrough.md#dataset).

Můžete přizpůsobit podle postupů popsaných tady na sadu vlastních dat nebo postupujte podle pokynů, jak je popsáno s použitím datové sady NYC taxislužby. K odeslání NYC taxislužby datovou sadu do místní databáze SQL serveru, postupujte podle postupu uvedeného v [hromadného importu dat do databáze SQL serveru](sql-walkthrough.md#dbload). Tyto pokyny jsou určené pro SQL Server na virtuálním počítači Azure, ale postup pro jeho odeslání do místního SQL serveru je stejný.

## <a name="file-to-azure-sql-database"></a> Přesun dat z plochého souboru použitému jako zdroj do služby Azure SQL database
Data do plochých souborů (sdíleného svazku clusteru nebo TSV ve formátu) můžete přesunout do Azure SQL database pomocí jazyka SQL hromadné vložení.

### <a name="bulk-insert-sql-query"></a> Dotaz SQL pro hromadné vložení
Kroky pro proceduru pomocí hromadného vložení SQL dotazu jsou podobné těm, které jsou popsané v části pro přesun dat z plochého souboru použitému jako zdroj pro SQL Server na Virtuálním počítači Azure. Podrobnosti najdete v tématu [dotaz SQL vložte hromadné](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Přesun dat z místního SQL serveru do Azure SQL database
Pokud je zdroj dat uložený v SQL serveru v místním, existují různé možnosti pro přesun dat do Azure SQL database:

1. [Exportovat do plochých souborů](#export-flat-file)
2. [Průvodce migrací služby SQL Database](#insert-tables-bcp)
3. [Databáze back up a obnovení](#db-migration)
4. [Azure Data Factory](#adf)

Pro první tři kroky jsou velmi podobně jako v těchto částech [přesun dat do SQL serveru na virtuálním počítači Azure](move-sql-server-virtual-machine.md) , které zahrnují tyto stejné postupy. Odkazy na příslušné části v tomto tématu jsou k dispozici v následujících pokynech.

### <a name="export-flat-file"></a>Exportovat do plochých souborů
Kroky pro tento export do souboru bez stromové struktury jsou podobné těm, které jsou zahrnuté v [exportovat do plochých souborů](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Průvodce migrací služby SQL Database
Postup při použití Průvodce migrací databáze SQL jsou podobné těm, které jsou zahrnuté v [Průvodce migrací služby SQL Database](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Databáze back up a obnovení
Pokyny pro použití databáze zálohování a obnovení jsou podobné těm, které jsou zahrnuté v [databáze back up a obnovit](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
Postup pro přesun dat do služby Azure SQL database s Azure Data Factory (ADF) najdete v tématu [přesun dat z místních SQL serveru do SQL Azure s Azure Data Factory](move-sql-azure-adf.md). Toto téma ukazuje, jak pro přesun dat z místní databáze systému SQL Server do Azure SQL database prostřednictvím služby Azure Blob Storage pomocí ADF.

Zvažte použití ADF, když je potřeba data migrovat průběžně v hybridním scénáři, který přistupuje k místním a cloudovým prostředkům a v případě dat je zpracováván jako transakce nebo je potřeba upravit nebo mít obchodní logiky do ní přidat při migraci. ADF umožňuje pro plánování a monitorování úloh pomocí jednoduchých skriptů JSON, které spravují pohybu dat v pravidelných intervalech. ADF má také další funkce, jako třeba podporu pro složité operace.
