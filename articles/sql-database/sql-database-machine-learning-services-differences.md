---
title: Hlavní rozdíly pro Machine Learning Services (s jazykem R) v přehledu Azure SQL Database (Preview)
description: Toto téma popisuje hlavní rozdíly mezi Azure SQL Database Machine Learning Services (s jazykem R) a SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: 4350fb0e75f140e120ba6cd2f074ffa1816a8fce
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237480"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Hlavní rozdíly mezi služby Machine Learning v Azure SQL Database a SQL Server

Funkce služby Machine Learning Services (s jazykem R) ve službě Azure SQL Database jsou podobné jako ve službě [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Níže jsou uvedeny některé hlavní rozdíly mezi nimi.

## <a name="language-support"></a>Podpora jazyků

Podpora jazyků R a Python prostřednictvím má systém SQL Server [extensibility framework](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database nepodporuje oba jazyky. Hlavní rozdíly jsou:

- R je jediný podporovaný jazyk ve službě SQL Database. Python se momentálně nepodporuje.
- Verze jazyka R je 3.4.4.
- Není nutné nakonfigurovat `external scripts enabled` prostřednictvím `sp_configure`. Jakmile budete [zaregistrovali](sql-database-machine-learning-services-overview.md#signup), služby machine learning je povolená pro vaši databázi SQL.

## <a name="package-management"></a>Správa balíčků

Správa balíčků R a instalace fungovat rozdíly mezi SQL Database a SQL Server. Tyto rozdíly jsou:

- Balíčky R instalují přes [sqlmlutils](https://github.com/Microsoft/sqlmlutils) nebo [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Balíčky nelze provést volání odchozí sítě. Toto omezení je podobný [výchozí pravidla brány firewall pro Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) v systému SQL Server, ale nelze změnit ve službě SQL Database.
- Není dostupná podpora pro balíčky, které jsou závislé na externích modulů runtime (jako je Java) nebo potřebují přístup k rozhraní API pro operační systém pro instalaci nebo používání.

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Není možné omezit prostředky R prostřednictvím [správce zdrojů](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) a fondy externích zdrojů. R prostředky jsou procento prostředků SQL Database a závisí na jaká úroveň služby zvolíte. Další informace najdete v tématu [modely nákupu Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

## <a name="security-isolation"></a>Izolace zabezpečení

Vrstva abstrakce platformy SQL (SQLPAL) ve službě Azure SQL Database poskytuje izolaci pro externí proces. Tato izolace poskytuje další úroveň zabezpečení pro spouštění skriptů R.

## <a name="next-steps"></a>Další postup

- Zobrazit [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics) dokumentaci obecné informace
- Zjistěte, jak pomocí služby Machine Learning (s jazykem R) ve službě Azure SQL Database, najdete v článku [příručky rychlý Start](sql-database-connect-query-r.md).
- Další informace najdete [kurzy jazyka SQL Server R](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)