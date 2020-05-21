---
title: Synchronizace dat z Edge Azure SQL (Preview) pomocí Synchronizace dat SQL
description: Přečtěte si o synchronizaci dat z Azure SQL Edge (Preview) pomocí Azure Synchronizace dat SQL
keywords: SQL Edge, synchronizace dat z okraje SQL, synchronizace dat SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7971681c3f0c99a11567e6a30e61167c5d42348c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680504"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Kurz: synchronizace dat z Edge SQL na Azure SQL Database pomocí Synchronizace dat SQL

V tomto kurzu se naučíte používat *skupinu synchronizace* Azure synchronizace dat SQL k přírůstkové synchronizaci dat z Edge SQL azure na Azure SQL Database. Synchronizace dat SQL je služba založená na Azure SQL Database, která umožňuje synchronizovat data, která jste vybrali obousměrně napříč několika databázemi SQL a instancemi služby SQL Server. Další informace o Synchronizace dat SQL najdete v tématu [Azure synchronizace dat SQL](../sql-database/sql-database-sync-data.md).

Vzhledem k tomu, že je SQL Edge postaven na nejnovějších verzích [databázového stroje SQL Server](/sql/sql-server/sql-server-technical-documentation/), můžete k synchronizaci dat do nebo z instance SQL Edge běžící na hraničním zařízení použít také jakýkoliv mechanismus synchronizace dat, který je použitelný pro místní instanci SQL Server.

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje počítač se systémem Windows nakonfigurovaný s [agentem synchronizace dat pro Azure synchronizace dat SQL](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Před zahájením

* Vytvoření databáze Azure SQL Informace o tom, jak vytvořit databázi SQL Azure pomocí Azure Portal, najdete [v tématu Vytvoření izolované databáze v Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* V nasazení Azure SQL Database vytvořte tabulky a další potřebné objekty.

* Vytvořte potřebné tabulky a objekty v nasazení Azure SQL Edge. Další informace najdete v tématu [použití SQL Databasech DAC Packages s nástrojem SQL Edge](deploy-dacpac.md).

* Zaregistrujte instanci Azure SQL Edge s agentem synchronizace dat pro Azure Synchronizace dat SQL. Další informace najdete v tématu [Přidání místní databáze SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>Synchronizace dat mezi databází SQL Azure a hraničním serverem SQL

Nastavení synchronizace mezi službou Azure SQL Database a instancí služby SQL Edge pomocí Synchronizace dat SQL zahrnuje tři klíčové kroky:  

1. Pomocí Azure Portal vytvořte skupinu synchronizace. Další informace najdete v tématu [Vytvoření skupiny synchronizace](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). K vytvoření více skupin synchronizace pro synchronizaci dat z různých instancí SQL Edge do jedné nebo více databází SQL v Azure můžete použít jednu databázi *hub* .

2. Přidejte členy synchronizace do skupiny synchronizace. Další informace najdete v tématu [Přidání členů synchronizace](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Nastavte skupinu synchronizace pro výběr tabulek, které budou součástí synchronizace. Další informace najdete v tématu [Konfigurace skupiny synchronizace](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Po dokončení předchozích kroků budete mít skupinu synchronizace, která zahrnuje Azure SQL Database a instanci SQL Edge.

Další informace o Synchronizace dat SQL najdete v těchto článcích:

* [Agent synchronizace dat pro Azure Synchronizace dat SQL](../sql-database/sql-database-data-sync-agent.md)

* [Osvědčené postupy](../sql-database/sql-database-best-practices-data-sync.md) a [řešení potíží s Azure synchronizace dat SQL](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorování Synchronizace dat SQL pomocí protokolů Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Aktualizace schématu synchronizace pomocí jazyka Transact-SQL](../sql-database/sql-database-update-sync-schema.md) nebo [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Další kroky

* [Použití PowerShellu k synchronizaci mezi Azure SQL Database a Azure SQL Edge](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). V tomto kurzu nahraďte podrobnosti databáze údaji o podrobnostech `OnPremiseServer` Azure SQL Edge.
