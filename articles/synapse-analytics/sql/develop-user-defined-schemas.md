---
title: Uživatelem definovaná schémata v rámci synapse SQL
description: V následujících částech najdete různé tipy pro použití uživatelských schémat T-SQL k vývoji řešení s funkcí Synapse SQL azure synapse analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428703"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Uživatelem definovaná schémata v rámci synapse SQL

V následujících částech najdete různé tipy pro použití uživatelských schémat T-SQL k vývoji řešení v rámci synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Schémata pro hranice aplikace

Tradiční analytická architektura často používá samostatné databáze k vytvoření hranic aplikace na základě zatížení, domény nebo zabezpečení. Tradiční analytická infrastruktura serveru SQL Server může například zahrnovat pracovní databázi, analytickou databázi a databáze datového tržiště. V této topologii každá databáze funguje jako pracovní vytížení a hranice zabezpečení v architektuře.

Místo toho Synapse SQL spustí celou úlohu analýzy v rámci jedné databáze. Křížová připojení databáze nejsou povolena. Synapse SQL očekává, že všechny tabulky používané ve skladu, které mají být uloženy v jedné databázi.

> [!NOTE]
> Fondy SQL nepodporují dotazy mezi databázemi jakéhokoli druhu. V důsledku toho bude nutné revidovat analytické implementace, které tento model využívají. SQL na vyžádání (náhled) podporuje dotazy mezi databázemi.

## <a name="user-defined-schema-recommendations"></a>Doporučení schématu definovaná uživatelem

Zahrnuta jsou doporučení pro konsolidaci úloh, zabezpečení, domény a funkčních hranic pomocí schémat definovaných uživatelem:

- Pomocí jedné databáze spusťte celou pracovní zátěž analýzy.
- Konsolidujte stávající analytické prostředí a použijte jednu databázi.
- Využijte **uživatelem definovaná schémata** k zajištění hranice dříve implementované pomocí databází.

Pokud uživatelem definovaná schémata nebyla dříve použita, máte čistý štít. Použijte starý název databáze jako základ pro uživatelem definované schémata v databázi Synapse SQL.

Pokud již byla schémata použita, máte několik možností:

- Odebrání starších názvů schémat a zahájení nového období
- Zachovat starší názvy schémat předem čekající na název staršího schématu na název tabulky
- Zachovat starší názvy schématu implementací zobrazení nad tabulka v další schéma, které znovu vytvoří strukturu staré ho schématu.

> [!NOTE]
> Při první kontrole se možnost 3 může zdát jako nejpřitažlivější volba. Zobrazení jsou jen pro čtení v Synapse SQL. Všechna data nebo změny tabulky by bylo nutné provést proti základní tabulce. Možnost 3 také zavádí vrstvu pohledů do vašeho systému. Možná budete chtít dát tuto další myšlenku, pokud již používáte zobrazení v architektuře.
> 
> 

### <a name="examples"></a>Příklady

Implementujte uživatelem definovaná schémata založená na názvech databází.

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

Zachovat starší názvy schématu předem čekající na název tabulky. Použijte schémata pro hranice pracovního vytížení.

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

Zachovat starší názvy schématpomocí zobrazení.

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
> Každá změna strategie schématu vyžaduje kontrolu modelu zabezpečení pro databázi. V mnoha případech může být možné zjednodušit model zabezpečení přiřazením oprávnění na úrovni schématu.

Pokud jsou vyžadována podrobnější oprávnění, můžete použít databázové role. Další informace o rolích databáze naleznete v článku [Správa rolí databáze a uživatelů.](../../analysis-services/analysis-services-database-users.md)

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete [v tématu Přehled vývoje synapse SQL](develop-overview.md).
