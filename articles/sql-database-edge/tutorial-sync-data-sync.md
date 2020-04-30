---
title: Synchronizace dat z Azure SQL Database Edge pomocí Synchronizace dat SQL | Microsoft Docs
description: Další informace o synchronizaci dat z Azure SQL Database Edge pomocí Azure Synchronizace dat SQL
keywords: Edge SQL Database, synchronizace dat z okraje SQL Database, synchronizace dat Edge SQL Database
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74384185"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Kurz: synchronizace dat z SQL Database Edge do Azure SQL Database pomocí Synchronizace dat SQL

V tomto kurzu se naučíte používat *skupinu synchronizace* Azure synchronizace dat SQL k přírůstkové synchronizaci dat z Azure SQL Database Edge na Azure SQL Database. Synchronizace dat SQL je služba založená na Azure SQL Database, která umožňuje synchronizovat data, která jste vybrali obousměrně napříč několika databázemi SQL a instancemi služby SQL Server. Další informace o Synchronizace dat SQL najdete v tématu [Azure synchronizace dat SQL](../sql-database/sql-database-sync-data.md).

Vzhledem k tomu, že SQL Database Edge je postaven na nejnovějších verzích [databázového stroje SQL Server](/sql/sql-server/sql-server-technical-documentation/), můžete k synchronizaci dat do nebo z instance SQL Database Edge běžící na hraničním zařízení použít také jakýkoliv mechanismus synchronizace dat, který je použitelný pro místní instanci SQL Server.

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje počítač se systémem Windows nakonfigurovaný s [agentem synchronizace dat pro Azure synchronizace dat SQL](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Před zahájením

* Vytvoření databáze Azure SQL Informace o tom, jak vytvořit databázi SQL Azure pomocí Azure Portal, najdete [v tématu Vytvoření izolované databáze v Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* V nasazení Azure SQL Database vytvořte tabulky a další potřebné objekty.

* Vytvořte potřebné tabulky a objekty v nasazení Azure SQL Database Edge. Další informace najdete v tématu [použití SQL Databasech DAC Packages s SQL Database Edge](stream-analytics.md).

* Zaregistrujte instanci Azure SQL Database Edge s agentem synchronizace dat pro Azure Synchronizace dat SQL. Další informace najdete v tématu [Přidání místní databáze SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Synchronizace dat mezi databází Azure SQL a SQL Database Edge

Nastavení synchronizace mezi službou Azure SQL Database a instancí SQL Database Edge pomocí Synchronizace dat SQL zahrnuje tři klíčové kroky:  

1. Pomocí Azure Portal vytvořte skupinu synchronizace. Další informace najdete v tématu [Vytvoření skupiny synchronizace](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Pomocí jedné databáze *hub* můžete vytvořit více skupin synchronizace pro synchronizaci dat z různých instancí SQL Database Edge do jedné nebo více databází SQL v Azure.

2. Přidejte členy synchronizace do skupiny synchronizace. Další informace najdete v tématu [Přidání členů synchronizace](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Nastavte skupinu synchronizace pro výběr tabulek, které budou součástí synchronizace. Další informace najdete v tématu [Konfigurace skupiny synchronizace](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Po dokončení předchozích kroků budete mít skupinu synchronizace, která zahrnuje databázi SQL Azure a instanci SQL Database Edge.

Další informace o Synchronizace dat SQL najdete v těchto článcích:

* [Agent synchronizace dat pro Azure Synchronizace dat SQL](../sql-database/sql-database-data-sync-agent.md)

* [Osvědčené postupy](../sql-database/sql-database-best-practices-data-sync.md) a [řešení potíží s Azure synchronizace dat SQL](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorování Synchronizace dat SQL pomocí protokolů Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Aktualizace schématu synchronizace pomocí jazyka Transact-SQL](../sql-database/sql-database-update-sync-schema.md) nebo [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Další kroky

* [K synchronizaci mezi Azure SQL Database a Azure SQL Database Edge použijte PowerShell](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). V tomto kurzu nahraďte podrobnosti `OnPremiseServer` databáze údaji o Azure SQL Database hraně.
