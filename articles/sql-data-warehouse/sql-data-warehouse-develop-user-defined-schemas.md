---
title: Použití vlastní schémata v SQL Data Warehouse | Microsoft Docs
description: Tipy pro používání schémata uživatelem definované T-SQL v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d30434bf3c5e5f27f3a95bcb70bddaf3d92967bd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Použití vlastní schémata v SQL Data Warehouse
Tipy pro používání schémata uživatelem definované T-SQL v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="schemas-for-application-boundaries"></a>Schémata pro hranice aplikace

Samostatné databáze tradičních datových skladů často používají k vytvoření hranice aplikace na základě zatížení, domény nebo zabezpečení. Tradiční datového skladu SQL serveru může například obsahovat pracovní databáze, databáze datového skladu a některé databáze datového tržiště. V této topologii každou databázi funguje jako hranice zabezpečení v architektuře a zatížení.

Naopak SQL Data Warehouse spouští úlohy celého datového skladu v rámci jedné databáze. Mezi databáze nejsou povoleny spojení. Proto SQL Data Warehouse očekává, že všechny tabulky použité ve skladu ukládaly v rámci jedné databáze.

> [!NOTE]
> SQL Data Warehouse nepodporuje křížové databázové dotazy jakéhokoli druhu. V důsledku toho datového skladu implementace, které využívají tento vzor bude nutné upravit.
> 
> 

## <a name="recommendations"></a>Doporučení
Toto jsou doporučení pro konsolidaci úlohy, zabezpečení, domény a funkční hranice pomocí uživatelsky definované schémata

1. Použít jednu databázi SQL Data Warehouse ke spuštění úlohy celého datového skladu
2. Konsolidovat svého stávajícího prostředí datového skladu použít jednu databázi SQL Data Warehouse
3. Využívání **uživatelem definované schémata** zajistit hranic dřív implementovaná pomocí databáze.

Pokud nebyly použity uživatelem definované schémata dříve máte čistou projektem. Jednoduše použijte původní název databáze jako základ pro váš vlastní schémata v databázi SQL Data Warehouse.

Pokud již byl použit schémata máte několik možností:

1. Odebrat starší verze schématu názvy a začít pracovat
2. Zachovat starší verze schématu názvy předem čekající starší verze schématu název, který má název tabulky
3. Zachovat starší verze schématu názvy implementací zobrazení tabulky v schémat, která je navíc k opětovnému vytvoření původní struktura schématu.

> [!NOTE]
> Možnost 3 může na první kontroly jevit jako nejvíce přitažlivými možnost. Ďábla je však v podrobností. Zobrazení se čtou jenom v SQL Data Warehouse. Všechny změny dat nebo tabulky by bylo potřeba provést u základní tabulky. Možnost 3 také zavádí vrstvu zobrazení do systému. Můžete chtít poskytnout to některé další myšlenku, pokud používáte zobrazení ve vaší architektury již.
> 
> 

### <a name="examples"></a>Příklady:
Implementovat vlastní schémata podle názvy databází

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Zachovat starší verze schématu názvy předem čekající je k názvu tabulky. Použijte schémata hranice zatížení.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Zachovat starší verze schématu názvy pomocí zobrazení

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Všechny změny ve schématu strategii pro databázi potřebuje kontrolu modelu zabezpečení. V mnoha případech je možné zjednodušit přiřazením oprávnění na úrovni schématu modelu zabezpečení. Pokud jsou požadována oprávnění podrobnější můžete použít role databáze.
> 
> 

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj, najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

