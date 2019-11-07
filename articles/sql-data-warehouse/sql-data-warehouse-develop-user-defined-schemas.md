---
title: Použití uživatelsky definovaných schémat
description: Tipy pro použití uživatelsky definovaných schémat T-SQL v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 697bffa36e9b208c1a027654df81fb356ddfc8ed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685813"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Použití uživatelsky definovaných schémat v SQL Data Warehouse
Tipy pro použití uživatelsky definovaných schémat T-SQL v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="schemas-for-application-boundaries"></a>Schémata pro hranice aplikace

Tradiční datové sklady často používají samostatné databáze k vytvoření hranice aplikace na základě zatížení, domény nebo zabezpečení. Například tradiční datový sklad SQL Server může zahrnovat pracovní databázi, databázi datového skladu a některé databáze datového tržiště. V této topologii každá databáze funguje jako zatížení a hranice zabezpečení v architektuře.

Naproti tomu SQL Data Warehouse spouští celou úlohu datového skladu v rámci jedné databáze. Spojení mezi databázemi nejsou povolena. Proto SQL Data Warehouse očekává, že všechny tabulky používané datovým skladem budou uloženy v rámci jedné databáze.

> [!NOTE]
> SQL Data Warehouse nepodporuje dotazování mezi databázemi jakéhokoli druhu. V důsledku toho bude nutné implementace datového skladu, které využívají tento model, upravit.
> 
> 

## <a name="recommendations"></a>Doporučení
Jedná se o doporučení pro konsolidaci zatížení, zabezpečení, domény a funkční hranice pomocí uživatelsky definovaných schémat.

1. Použití jedné databáze SQL Data Warehouse ke spuštění celé úlohy datového skladu
2. Konsolidace stávajícího prostředí datového skladu pro použití jedné databáze SQL Data Warehouse
3. Využijte **uživatelsky definovaných schémat** k poskytnutí hranice dříve implementovaného pomocí databází.

Pokud se dřív definovaná schémata ještě nepoužila, budete mít čistou SLAT. Jednoduše použijte starý název databáze jako základ pro vaše uživatelsky definované schémat v databázi SQL Data Warehouse.

Pokud se už schémata používala, máte několik možností:

1. Odebrat starší názvy schémat a začít znovu
2. Zachovat starší názvy schémat pomocí předběžného názvu staršího schématu pro název tabulky
3. Starší názvy schémat zachovejte implementací zobrazení v tabulce ve dodatečném schématu pro opětovné vytvoření staré struktury schématu.

> [!NOTE]
> Při první kontrole 3 se může zdát, že se jedná o možnost nejúčinnějšího odvolání. Nicméně Devil je v podrobnostech. Zobrazení jsou jen pro čtení v SQL Data Warehouse. Veškerá data nebo úpravy tabulek by se musely provést na základě základní tabulky. Možnost 3 také zavádí do systému vrstvu zobrazení. Pokud již používáte zobrazení ve vaší architektuře, je vhodné dát mu další myšlenky.
> 
> 

### <a name="examples"></a>Příklady:
Implementace uživatelsky definovaných schémat na základě názvů databází

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

Zachovejte starší názvy schémat – a to předem, než jsou v názvu tabulky. Pro hranici zatížení použijte schémata.

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

Uchování starších názvů schémat pomocí zobrazení

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
> Každá změna v strategii schématu vyžaduje přezkoumání modelu zabezpečení pro databázi. V mnoha případech může být možné zjednodušit model zabezpečení přiřazením oprávnění na úrovni schématu. Pokud jsou vyžadovány podrobnější oprávnění, můžete použít databázové role.
> 
> 

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).

