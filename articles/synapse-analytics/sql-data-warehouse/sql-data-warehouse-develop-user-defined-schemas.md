---
title: Použití uživatelem definovaných schémat
description: Tipy pro použití uživatelských schémat T-SQL v Azure SQL Data Warehouse pro vývoj řešení.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9ed4f01aae6ace1af6c1652fe3c5ecfe14dc6bf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351542"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Použití uživatelem definovaných schémat v datovém skladu SQL
Tipy pro použití uživatelských schémat T-SQL v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="schemas-for-application-boundaries"></a>Schémata pro hranice aplikace

Tradiční datové sklady často používají samostatné databáze k vytvoření hranic aplikace na základě zatížení, domény nebo zabezpečení. Tradiční datový sklad serveru SQL Server může například zahrnovat pracovní databázi, databázi datového skladu a některé databáze datového tržiště. V této topologii každá databáze funguje jako hranice úlohy a zabezpečení v architektuře.

Naproti tomu SQL Data Warehouse spouští celé úlohy datového skladu v rámci jedné databáze. Spojení křížové databáze nejsou povolena. Proto SQL Data Warehouse očekává, že všechny tabulky používané ve skladu, které mají být uloženy v jedné databázi.

> [!NOTE]
> SQL Data Warehouse nepodporuje dotazy mezi databázemi jakéhokoli druhu. V důsledku toho implementace datového skladu, které využívají tento vzor bude muset být revidován.
> 
> 

## <a name="recommendations"></a>Doporučení
Jedná se o doporučení pro konsolidaci úloh, zabezpečení, domény a funkčních hranic pomocí uživatelských schémat.

1. Spuštění celého pracovního vytížení datového skladu pomocí jedné databáze datového skladu SQL
2. Konsolidace stávajícího prostředí datového skladu za účelem použití jedné databáze datového skladu SQL
3. Využijte **uživatelem definovaná schémata** k zajištění hranice dříve implementované pomocí databází.

Pokud uživatelem definované schémata nebyly použity dříve pak máte čistý štít. Jednoduše použijte starý název databáze jako základ pro vaše uživatelem definované schémata v databázi datového skladu SQL.

Pokud již byla schémata použita, máte několik možností:

1. Odebrání starších názvů schémat a zahájení nového období
2. Zachovat starší názvy schémat předem čekající na název staršího schématu na název tabulky
3. Zachovat starší názvy schématu implementací zobrazení nad tabulka v další schéma znovu vytvořit strukturu staré schéma.

> [!NOTE]
> Při první kontrole se možnost 3 může zdát jako nejpřitažlivější možnost. Nicméně, ďábel je v detailu. Zobrazení jsou jen pro čtení v datovém skladu SQL. Všechna data nebo změny tabulky by bylo nutné provést proti základní tabulce. Možnost 3 také zavádí vrstvu pohledů do vašeho systému. Možná budete chtít dát tuto další myšlenku, pokud používáte zobrazení v architektuře již.
> 
> 

### <a name="examples"></a>Příklady:
Implementace uživatelem definovaných schémat založených na názvech databází

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

Zachovat starší názvy schémat předem čekající na název tabulky. Použijte schémata pro hranice pracovního vytížení.

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

Zachování starších názvů schémat pomocí zobrazení

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
> Každá změna strategie schématu vyžaduje revizi modelu zabezpečení pro databázi. V mnoha případech může být možné zjednodušit model zabezpečení přiřazením oprávnění na úrovni schématu. Pokud jsou vyžadována podrobnější oprávnění, můžete použít databázové role.
> 
> 

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md).

