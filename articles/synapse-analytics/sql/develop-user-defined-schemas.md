---
title: Uživatelsky definovaná schémata v synapse SQL
description: V níže uvedených částech najdete různé tipy pro vývoj řešení pomocí uživatelsky definovaných schémat T-SQL k vývoji řešení s využitím funkce synapse SQL Azure synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6920d6cd190927747a69fc4dc0f0791278665abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451688"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Uživatelsky definovaná schémata v synapse SQL

V níže uvedených částech najdete různé tipy pro vývoj řešení v synapse SQL pomocí uživatelsky definovaných schémat T-SQL.

## <a name="schemas-for-application-boundaries"></a>Schémata pro hranice aplikace

Tradiční architektura analýzy často používá samostatné databáze k vytvoření hranice aplikace na základě zatížení, domény nebo zabezpečení. Například tradiční analytická infrastruktura SQL Server může zahrnovat pracovní databázi, databázi analýz a databáze datového tržiště. V této topologii každá databáze funguje jako zatížení a hranice zabezpečení v architektuře.

Místo toho synapse SQL spouští celou analýzu úloh v rámci jedné databáze. Spojení mezi databázemi nejsou povolena. Synapse SQL očekává, že všechny tabulky používané datovým skladem budou uloženy v rámci jedné databáze.

> [!NOTE]
> Vyhrazené fondy SQL nepodporují mezidatabázové dotazy jakéhokoli druhu. V důsledku toho bude nutné provést změny analýz, které využívají tento model. Fond SQL bez serveru podporuje databázové dotazy mezi různými databázemi.

## <a name="user-defined-schema-recommendations"></a>Uživatelsky definovaná doporučení schémat

Zahrnuje doporučení pro konsolidaci úloh, zabezpečení, domény a funkční hranice pomocí uživatelsky definovaných schémat:

- Použijte jednu databázi ke spuštění celé analytické úlohy.
- Konsolidujte své existující analytické prostředí pro použití jedné databáze.
- Využijte **uživatelsky definovaných schémat** k poskytnutí hranice dříve implementovaného pomocí databází.

Pokud se dřív definovaná schémata ještě nepoužila, máte čistou SLAT. Jako základ pro uživatelsky definovaná schémata v databázi SQL synapse použijte starý název databáze.

Pokud se už schémata používala, máte několik možností:

- Odebrat starší názvy schémat a začít znovu
- Zachovat starší názvy schémat pomocí předběžného názvu staršího schématu pro název tabulky
- Starší názvy schémat si ponechte implementací zobrazení v tabulce ve dodatečném schématu, které znovu vytvoří starou strukturu schématu.

> [!NOTE]
> Při první inspekci se možnost 3 může zdát jako u nejvyššího opravného rozhodnutí. Zobrazení jsou jen pro čtení v synapse SQL. Veškerá data nebo úpravy tabulek by se musely provést na základě základní tabulky. Možnost 3 také zavádí do systému vrstvu zobrazení. Pokud již používáte zobrazení ve vaší architektuře, je vhodné dát mu další myšlenky.
> 
> 

### <a name="examples"></a>Příklady

Implementujte uživatelsky definovaná schémata na základě názvů databází.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Názvy starších schémat si ponechte předem – čekají na název tabulky. Pro hranici zatížení použijte schémata.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Starší názvy schémat si ponechte pomocí zobrazení.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> Každá změna v strategii schématu vyžaduje přezkoumání modelu zabezpečení pro databázi. V mnoha případech může být možné zjednodušit model zabezpečení přiřazením oprávnění na úrovni schématu.

Pokud potřebujete podrobnější oprávnění, můžete použít databázové role. Další informace o databázových rolích najdete v článku [Správa databázových rolí a uživatelů](../../analysis-services/analysis-services-database-users.md) .

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje synapse SQL](develop-overview.md).
