---
title: Pomocí schémat definovaných uživateli ve službě SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro používání schémat definovaných uživateli T-SQL ve službě Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ae017461767a207deae1d990980258a1f661df3d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468440"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Pomocí schémat definovaných uživateli ve službě SQL Data Warehouse
Tipy pro používání schémat definovaných uživateli T-SQL ve službě Azure SQL Data Warehouse pro vývoj řešení.

## <a name="schemas-for-application-boundaries"></a>Schémata pro hranice aplikační

Tradičních datových skladů často používají samostatné databáze na vytváření hranic aplikace na základě pracovního vytížení, domény nebo zabezpečení. Tradiční datový sklad SQL serveru může například obsahovat pracovní databáze, databáze datového skladu a některé databáze datového tržiště. V této topologii každou databázi funguje jako úlohu a hranice zabezpečení v architektuře.

Naopak spouští úlohy celého datového skladu v rámci jedné databáze SQL Data Warehouse. Pro různé databáze nejsou povoleny spojení. Proto SQL Data Warehouse očekává, že všechny tabulky používané v mají být uloženy v rámci jedné databáze datového skladu.

> [!NOTE]
> SQL Data Warehouse nepodporuje dotazy napříč databázemi jakéhokoli druhu. V důsledku toho dat skladu implementace, které využívají tento model bude nutné upravit.
> 
> 

## <a name="recommendations"></a>Doporučení
Tady najdete doporučení pro konsolidaci úlohy, zabezpečení, domény a funkční hranice pomocí uživatelem definované schémat

1. Použijte jednu databázi SQL Data Warehouse ke spuštění vaší úlohy celého datového skladu
2. Konsolidace vašeho stávajícího prostředí datového skladu použít jednu databázi SQL Data Warehouse
3. Využijte **schémat definovaných uživateli** stanovit hranici dřív implementovaná pomocí databáze.

Pokud nebyly použity schémat definovaných uživateli dříve měli mít čisté břidlicová. Jednoduše použijte starý název databáze jako základ pro váš schémat definovaných uživateli v databázi SQL Data Warehouse.

Pokud již byl použit schémata máte několik možností:

1. Odebrat názvy starší verze schématu a začněte od začátku
2. Zachovat starší verze schématu názvy předem čeká na starší verze schématu název, který má název tabulky
3. Zachovat starší verze schématu názvy implementací zobrazení tabulku ve schématu navíc k opětovnému vytvoření původní struktura schématu.

> [!NOTE]
> Na první kontrola se může zdát možnost 3 jako možnost s nejvíce přitažlivými. Je však ďábla zařazená do podrobností. Zobrazení jsou číst pouze ve službě SQL Data Warehouse. Všechny úpravy dat nebo tabulky by musel být provedeny v základní tabulce. Možnost 3 také zavádí úroveň zobrazení do vašeho systému. Můžete chtít poskytnout to uvažujete další, pokud používáte zobrazení ve vaší architektuře již.
> 
> 

### <a name="examples"></a>Příklady:
Implementace schémat definovaných uživateli na základě názvů databáze

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

Zachovat starší verze schématu názvy předem čekající je název tabulky. Použijte schémata hranice pracovního vytížení.

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

Zachovat starší verze schématu názvů pomocí zobrazení

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
> Všechny změny ve schématu strategii potřebuje kontrolu model zabezpečení pro databázi. V mnoha případech je možné zjednodušila model zabezpečení pomocí přiřazení oprávnění na úrovni schématu. Pokud potřebujete podrobnější oprávnění můžete použít databázové role.
> 
> 

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

