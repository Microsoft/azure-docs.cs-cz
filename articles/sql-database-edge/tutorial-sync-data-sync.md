---
title: Synchronizace dat z Azure SQL Database Edge pomocí Synchronizace dat SQL | Microsoft Docs
description: Další informace o synchronizaci dat mezi Azure SQL Database Edge pomocí Azure Synchronizace dat SQL
keywords: Edge SQL Database, synchronizace dat z okraje SQL Database, synchronizace dat Edge SQL Database
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501313"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>Kurz: synchronizace dat z SQL Database Edge na Azure SQL Database pomocí Synchronizace dat SQL

V tomto kurzu se naučíte, jak používat *skupinu synchronizace* synchronizace dat SQL k přírůstkové synchronizaci dat z Azure SQL Database Edge na Azure SQL Database. Synchronizace dat SQL je služba založená na Azure SQL Database, která umožňuje synchronizovat data, která jste vybrali obousměrně napříč několika databázemi SQL a instancemi služby SQL Server. Další informace o Azure Synchronizace dat SQL najdete v tématu [azure synchronizace dat SQL](../sql-database/sql-database-sync-data.md).

Vzhledem k tomu, že Azure SQL Database Edge se sestavuje na nejnovější verze [databázového stroje Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation/), můžete k synchronizaci dat s SQL Database nebo z něj použít i jakýkoliv mechanismus synchronizace dat, který je použitelný pro místní instanci SQL Server. instance spuštěná na hraničním zařízení.

## <a name="prerequisites"></a>Předpoklady

Tento kurz vyžaduje počítač s Windows nakonfigurovaný pomocí [agenta Azure synchronizace dat SQL](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Než začnete

* Vytvořte Azure SQL Database. Informace o tom, jak vytvořit Azure SQL Database pomocí Azure Portal, najdete [v tématu Vytvoření izolované databáze v Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* V nasazení Azure SQL Database vytvořte tabulky a další potřebné objekty.

* Vytvořte potřebné tabulky a objekty v nasazení Azure SQL Database Edge. Další informace najdete v tématu [použití SQL Databasech DAC Packages s SQL Database Edge](stream-analytics.md).

* Zaregistrujte instanci Azure SQL Database Edge pomocí agenta Azure Synchronizace dat SQL. Další informace najdete v tématu [Přidání místní databáze SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Synchronizace dat mezi Azure SQL Database a hranou SQL Database

Nastavení synchronizace mezi Azure SQL Database a instancí SQL Database Edge pomocí Synchronizace dat SQL zahrnuje tři klíčové kroky.  

1. Pomocí Azure Portal vytvořte skupinu synchronizace. Chcete-li vytvořit skupinu synchronizace, přečtěte si téma [Vytvoření skupiny synchronizace pomocí Azure Portal](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Stejná databáze *centra* se dá použít k vytvoření více různých skupin synchronizace pro synchronizaci dat z různých instancí SQL Database Edge do jedné nebo více databází SQL v Azure.

2. Přidejte členy synchronizace do skupiny synchronizace. Chcete-li přidat členy do skupiny synchronizace, přečtěte si téma [Přidání členů do skupiny synchronizace dat SQL](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Nakonfigurujte skupinu synchronizace pro výběr tabulek, které budou součástí této synchronizace. Chcete-li konfigurovat skupinu synchronizace, přečtěte si téma [Konfigurace skupiny synchronizace](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Po dokončení výše uvedeného postupu máte skupinu synchronizace, která zahrnuje Azure SQL Database a instanci SQL Database Edge.

Další informace o Synchronizace dat SQL najdete v následujících článcích:

* [Agent synchronizace dat pro Azure Synchronizace dat SQL](../sql-database/sql-database-data-sync-agent.md)

* [Osvědčené postupy](../sql-database/sql-database-best-practices-data-sync.md) a [řešení potíží s Azure synchronizace dat SQL](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorování Synchronizace dat SQL pomocí protokolů Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Aktualizace schématu synchronizace pomocí jazyka Transact-SQL](../sql-database/sql-database-update-sync-schema.md) nebo [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Další kroky

* [K synchronizaci mezi Azure SQL Database a Azure SQL Database Edge použijte PowerShell](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). V tomto kurzu nahraďte podrobnosti databáze *OnPremiseServer* podrobnostmi o Azure SQL Database Edge.
