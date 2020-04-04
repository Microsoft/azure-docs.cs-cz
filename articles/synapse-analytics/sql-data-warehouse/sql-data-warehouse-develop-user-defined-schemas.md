---
title: Použití uživatelem definovaných schémat
description: Tipy pro použití t-SQL uživatelem definované schémata pro vývoj řešení v synapse fondu SQL.
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
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633457"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Uživatelem definovaná schémata ve fondu SYNAPse SQL
Tento článek se zaměřuje na poskytování několika tipů pro použití t-SQL uživatelem definované schémata pro vývoj řešení ve fondu SQL Synapse.

## <a name="schemas-for-application-boundaries"></a>Schémata pro hranice aplikace

Tradiční datové sklady často používají samostatné databáze k vytvoření hranic aplikace na základě zatížení, domény nebo zabezpečení. 

Například tradiční datový sklad serveru SQL Server může zahrnovat pracovní databázi, databázi datového skladu a některé databáze datového tržiště. V této topologii každá databáze funguje jako pracovní vytížení a hranice zabezpečení v architektuře.

Naproti tomu fond SQL spouští celé úlohy datového skladu v rámci jedné databáze. Křížová připojení databáze nejsou povolena. Fond SQL očekává, že všechny tabulky používané ve skladu, které mají být uloženy v rámci jedné databáze.

> [!NOTE]
> Fond SQL nepodporuje dotazy mezi databázemi jakéhokoli druhu. V důsledku toho implementace datového skladu, které využívají tento vzor bude muset být revidován.
> 
> 

## <a name="recommendations"></a>Doporučení
Následuje doporučení pro konsolidaci úloh, zabezpečení, domény a funkčních hranic pomocí schémat definovaných uživatelem:

- Pomocí jedné databáze fondu SQL spusťte celou úlohu datového skladu.
- Konsolidujte stávající prostředí datového skladu a použijte jednu databázi fondu SQL.
- Využijte **uživatelem definovaná schémata** k zajištění hranice dříve implementované pomocí databází.

Pokud uživatelem definované schémata nebyly použity dříve, pak máte čistý štít. Použijte starý název databáze jako základ pro vaše uživatelem definované schémata v databázi fondu SQL.

Pokud již byla schémata použita, máte několik možností:

- Odeberte starší názvy schémat a začněte znovu.
- Zachovat starší názvy schématu předem čekající název staršího schématu na název tabulky.
- Zachovat starší názvy schématu implementací zobrazení nad tabulka v další schéma znovu vytvořit strukturu staré schéma.

> [!NOTE]
> Při první kontrole se možnost 3 může zdát jako nejpřitažlivější možnost. Nicméně, ďábel je v detailu. Zobrazení jsou jen pro čtení ve fondu SQL. Všechna data nebo změny tabulky by bylo nutné provést proti základní tabulce. Možnost 3 také zavádí vrstvu pohledů do vašeho systému. Možná budete chtít dát tuto další myšlenku, pokud používáte zobrazení v architektuře již.
> 
> 

### <a name="examples"></a>Příklady:
Implementujte uživatelem definovaná schémata založená na názvech databází:

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

Zachovat starší názvy schémat u předem čekající na jejich název tabulky. Použijte schémata pro hranice pracovního vytížení:

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

Zachovat starší názvy schémat pomocí zobrazení:

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

