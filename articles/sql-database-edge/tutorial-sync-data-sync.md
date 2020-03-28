---
title: Synchronizace dat z Azure SQL Database Edge pomocí synchronizace dat SQL | Dokumenty společnosti Microsoft
description: Informace o synchronizaci dat z Azure SQL Database Edge pomocí Azure SQL Data Sync
keywords: SQL Database Edge,synchronizace dat z okraje databáze SQL, synchronizace dat sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74384185"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Kurz: Synchronizace dat z sql database edge do azure sql databáze pomocí synchronizace dat SQL

V tomto kurzu se dozvíte, jak pomocí *skupiny synchronizace* dat Azure SQL k postupné synchronizaci dat z Azure SQL Database Edge do Azure SQL Database Database. SQL Data Sync je služba postavená na Azure SQL Database, která umožňuje synchronizovat data, která vyberete obousměrně mezi více databází SQL a instancí SQL Serveru. Další informace o synchronizaci dat SQL najdete v [tématu Azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Vzhledem k tomu, že SQL Database Edge je postaven na nejnovějších verzích [sql server databázového stroje](/sql/sql-server/sql-server-technical-documentation/), jakýkoli mechanismus synchronizace dat, který je použitelný pro místní instanci SERVERU SQL Server lze také použít k synchronizaci dat do nebo z instance SQL Database Edge běží na hraničním zařízení.

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje počítač se systémem Windows nakonfigurovaný s [agentem synchronizace dat pro synchronizaci dat Azure SQL](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Než začnete

* Vytvoření databáze Azure SQL Informace o tom, jak vytvořit databázi Azure SQL pomocí portálu Azure, najdete [v tématu Vytvoření jedné databáze v Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Vytvořte tabulky a další potřebné objekty v nasazení Azure SQL Database.

* Vytvořte potřebné tabulky a objekty v nasazení Azure SQL Database Edge. Další informace naleznete [v tématu Using SQL Database DAC packages with SQL Database Edge](stream-analytics.md).

* Zaregistrujte instanci Azure SQL Database Edge s agentem synchronizace dat pro Azure SQL Data Sync. Další informace naleznete [v tématu Přidání místní databáze serveru SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Synchronizace dat mezi databází Azure SQL a SQL Database Edge

Nastavení synchronizace mezi databází Azure SQL a instancí SQL Database Edge pomocí synchronizace dat SQL zahrnuje tři klíčové kroky:  

1. K vytvoření synchronizační skupiny použijte portál Azure. Další informace naleznete [v tématu Vytvoření skupiny synchronizace](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Pomocí jedné databáze *rozbočovače* můžete vytvořit více skupin synchronizace k synchronizaci dat z různých instancí SQL Database Edge do jedné nebo více databází SQL v Azure.

2. Přidejte členy synchronizace do skupiny synchronizace. Další informace naleznete v tématu [Přidání členů synchronizace](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Nastavte skupinu synchronizace tak, aby vyberte tabulky, které budou součástí synchronizace. Další informace naleznete [v tématu Konfigurace skupiny synchronizace](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Po dokončení předchozích kroků budete mít skupinu synchronizace, která obsahuje databázi Azure SQL a instanci SQL Database Edge.

Další informace o synchronizaci dat SQL najdete v těchto článcích:

* [Agent synchronizace dat pro synchronizaci dat Azure SQL](../sql-database/sql-database-data-sync-agent.md)

* [Doporučené postupy](../sql-database/sql-database-best-practices-data-sync.md) a [řešení problémů se synchronizací dat Azure SQL](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorování synchronizace dat SQL pomocí protokolů Azure Monitoru](../sql-database/sql-database-sync-monitor-oms.md)

* [Aktualizace schématu synchronizace pomocí transact-SQL](../sql-database/sql-database-update-sync-schema.md) nebo [powershellu](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Další kroky

* [Pomocí PowerShellu můžete synchronizovat mezi Azure SQL Database a Azure SQL Database Edge](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). V tomto kurzu `OnPremiseServer` nahraďte podrobnosti o databázi podrobnosti Azure SQL Database Edge.
