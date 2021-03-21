---
title: Použití uživatelsky definovaných schémat
description: Tipy pro použití uživatelsky definovaných schémat T-SQL k vývoji řešení pro vyhrazené fondy SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3204c77dd076d9aac6eb5a60b489280caefcbf4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460448"
---
# <a name="user-defined-schemas-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Uživatelsky definovaná schémata pro vyhrazené fondy SQL ve službě Azure synapse Analytics
Tento článek se zaměřuje na poskytnutí několika tipů pro vývoj řešení ve vyhrazeném fondu SQL pomocí uživatelsky definovaných schémat T-SQL.

## <a name="schemas-for-application-boundaries"></a>Schémata pro hranice aplikace

Tradiční datové sklady často používají samostatné databáze k vytvoření hranice aplikace na základě zatížení, domény nebo zabezpečení. 

Například tradiční datový sklad SQL Server může zahrnovat pracovní databázi, databázi datového skladu a některé databáze datového tržiště. V této topologii každá databáze funguje jako zatížení a hranice zabezpečení v architektuře.

Naproti tomu vyhrazený fond SQL spouští celou úlohu datového skladu v rámci jedné databáze. Spojení mezi databázemi nejsou povolena. Vyhrazený fond SQL očekává, že všechny tabulky používané datovým skladem budou uloženy v rámci jedné databáze.

> [!NOTE]
> Fond SQL nepodporuje databázové dotazy jakéhokoli druhu. V důsledku toho bude nutné implementace datového skladu, které využívají tento model, upravit.
> 
> 

## <a name="recommendations"></a>Doporučení
Níže jsou uvedeny doporučení pro konsolidaci úloh, zabezpečení, domény a funkční hranice pomocí uživatelsky definovaných schémat:

- Pomocí jedné databáze ve vyhrazeném fondu SQL spusťte celou úlohu datového skladu.
- Konsolidujte stávající prostředí datového skladu pro použití jedné vyhrazené databáze fondu SQL.
- Využijte **uživatelsky definovaných schémat** k poskytnutí hranice dříve implementovaného pomocí databází.

Pokud se dřív definovaná schémata ještě nepoužila, máte čistou SLAT. Jako základ pro vaše uživatelsky definované schémat ve vyhrazené databázi fondu SQL použijte starý název databáze.

Pokud se už schémata používala, máte několik možností:

- Odeberte starší názvy schémat a začněte znovu.
- Názvy starších schémat si ponechte předem – čeká se na název starší verze schématu na název tabulky.
- Starší názvy schémat zachovejte implementací zobrazení v tabulce ve dodatečném schématu pro opětovné vytvoření staré struktury schématu.

> [!NOTE]
> Při první kontrole 3 se může zdát, že se jedná o možnost nejúčinnějšího odvolání. Nicméně Devil je v podrobnostech. Zobrazení jsou jen pro čtení v vyhrazeném fondu SQL. Veškerá data nebo úpravy tabulek by se musely provést na základě základní tabulky. Možnost 3 také zavádí do systému vrstvu zobrazení. Pokud již používáte zobrazení ve vaší architektuře, je vhodné dát mu další myšlenky.
> 
> 

### <a name="examples"></a>Příklady:
Implementace uživatelsky definovaných schémat na základě názvů databází:

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

Zachovat starší názvy schémat podle názvu tabulky před jejich vyřízením Pro hranici zatížení použijte schémata:

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
> Každá změna v strategii schématu vyžaduje přezkoumání modelu zabezpečení pro databázi. V mnoha případech může být možné zjednodušit model zabezpečení přiřazením oprávnění na úrovni schématu. Pokud potřebujete podrobnější oprávnění, můžete použít databázové role.
> 
> 

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).

