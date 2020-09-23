---
title: Synchronizace dat z Azure SQL Edge pomocí Synchronizace dat SQL
description: Přečtěte si o synchronizaci dat z Azure SQL Edge pomocí Azure Synchronizace dat SQL
keywords: SQL Edge, synchronizace dat z okraje SQL, synchronizace dat SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5830cedfdce54c4cd91eb60a5cd2145309e965b0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904121"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Kurz: synchronizace dat z Edge SQL na Azure SQL Database pomocí Synchronizace dat SQL

V tomto kurzu se naučíte používat *skupinu synchronizace* Azure synchronizace dat SQL k přírůstkové synchronizaci dat z Edge SQL azure na Azure SQL Database. Synchronizace dat SQL je služba založená na Azure SQL Database, která umožňuje synchronizovat data, která jste vybrali v rámci více databází v Azure SQL Database a instancích SQL Server. Další informace o Synchronizace dat SQL najdete v tématu [Azure synchronizace dat SQL](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md).

Vzhledem k tomu, že je SQL Edge postaven na nejnovějších verzích [databázového stroje SQL Server](/sql/sql-server/sql-server-technical-documentation/), můžete k synchronizaci dat do nebo z instance SQL Edge běžící na hraničním zařízení použít také jakýkoliv mechanismus synchronizace dat, který je použitelný pro instanci SQL Server.

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje počítač se systémem Windows nakonfigurovaný s [agentem synchronizace dat pro Azure synchronizace dat SQL](../azure-sql/database/sql-data-sync-agent-overview.md).

## <a name="before-you-begin"></a>Než začnete

* Vytvoří databázi v Azure SQL Database. Informace o tom, jak vytvořit databázi pomocí Azure Portal, najdete [v tématu Vytvoření izolované databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

* V nasazení Azure SQL Database vytvořte tabulky a další potřebné objekty.

* Vytvořte potřebné tabulky a objekty v nasazení Azure SQL Edge. Další informace najdete v tématu [použití SQL Databasech DAC Packages s nástrojem SQL Edge](deploy-dacpac.md).

* Zaregistrujte instanci Azure SQL Edge s agentem synchronizace dat pro Azure Synchronizace dat SQL. Další informace najdete v tématu [Přidání databáze SQL Server](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>Synchronizace dat mezi databází v Azure SQL Database a SQL Edge

Nastavení synchronizace mezi databází v Azure SQL Database a instancí aplikace SQL Edge pomocí Synchronizace dat SQL zahrnuje tři klíčové kroky:  


1. Pomocí Azure Portal vytvořte skupinu synchronizace. Další informace najdete v tématu [Vytvoření skupiny synchronizace](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group). Pomocí jedné databáze *hub* můžete vytvořit více skupin synchronizace pro synchronizaci dat z různých instancí SQL Edge do jedné nebo více databází v Azure SQL Database. 

2. Přidejte členy synchronizace do skupiny synchronizace. Další informace najdete v tématu [Přidání členů synchronizace](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

3. Nastavte skupinu synchronizace pro výběr tabulek, které budou součástí synchronizace. Další informace najdete v tématu [Konfigurace skupiny synchronizace](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

Po dokončení předchozích kroků budete mít skupinu synchronizace, která zahrnuje databázi v Azure SQL Database a instanci okraje SQL Edge.

Další informace o Synchronizace dat SQL najdete v těchto článcích:

* [Agent synchronizace dat pro Azure Synchronizace dat SQL](../azure-sql/database/sql-data-sync-agent-overview.md)

* [Osvědčené postupy](../azure-sql/database/sql-data-sync-best-practices.md) a [řešení potíží s Azure synchronizace dat SQL](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [Monitorování Synchronizace dat SQL pomocí protokolů Azure Monitor](../azure-sql/database/sql-data-sync-monitor-sync.md)

* [Aktualizace schématu synchronizace pomocí jazyka Transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) nebo [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>Další kroky


* [Použití PowerShellu k synchronizaci mezi Azure SQL Database a Azure SQL Edge](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md). V tomto kurzu nahraďte podrobnosti databáze údaji o podrobnostech `OnPremiseServer` Azure SQL Edge.
