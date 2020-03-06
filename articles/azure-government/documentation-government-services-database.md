---
title: Azure Government databáze | Microsoft Docs
description: To poskytuje srovnání funkcí a pokynů pro vývoj aplikací pro Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: zakramer
manager: liki
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 03/06/2019
ms.author: zakramer
ms.openlocfilehash: cbf3d59bafa5b11be0fa037487dc64fdb9075619
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355820"
---
# <a name="azure-government-databases"></a>Azure Government databáze
## <a name="sql-database"></a>SQL Database
Další informace naleznete v části<a href="https://msdn.microsoft.com/library/bb510589.aspx"> Microsoft Security Center for SQL Database Engine</a> a [Azure SQL Database dokumentaci](../sql-database/index.yml) , kde najdete další pokyny týkající se konfigurace viditelnosti metadat a osvědčených postupů ochrany.

### <a name="variations"></a>Odlišnost
Databáze SQL V12 je všeobecně dostupná v Azure Government.

Adresa pro SQL Azure servery v Azure Government se liší:

| Typ služby | Veřejné Azure | Azure Government |
| --- | --- | --- |
| SQL Database |*. database.windows.net |*. database.usgovcloudapi.net |

### <a name="considerations"></a>Požadavky
Následující informace identifikují Azure Government hranici pro Azure SQL:

| Povolená řízená/řízená data | Regulovaná/řízená data nejsou povolena. |
| --- | --- |
| Všechna data uložená a zpracovaná v Microsoft Azure SQL mohou obsahovat Azure Government regulovaná data. Použití databázových nástrojů pro přenos dat Azure Government řízených dat. |Pro metadata Azure SQL není povoleno obsahovat data řízená exportem. Tato metadata zahrnují všechna konfigurační data zadaná při vytváření a údržbě produktu úložiště.  Nezadávejte regulovaná nebo řízená data do následujících polí: název databáze, název předplatného, skupiny prostředků, název serveru, přihlašovací jméno správce serveru, názvy nasazení, názvy prostředků, značky prostředků. |

## <a name="sql-data-warehouse"></a>SQL Data Warehouse
Podrobnosti o této službě a o tom, jak ji používat, najdete v [dokumentaci Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

## <a name="sql-server-stretch-database"></a>SQL Server Stretch Database
Podrobnosti o této službě a o tom, jak ji používat, najdete v [dokumentaci k Azure SQL Server Stretch Database](../sql-server-stretch-database/index.md) .

## <a name="azure-cosmos-db"></a>Databáze Azure Cosmos
Podrobnosti o této službě a o tom, jak ji používat, najdete v [dokumentaci Azure Cosmos DB](../cosmos-db/index.yml).

### <a name="variations"></a>Odlišnost
Azure Cosmos DB je všeobecně dostupná v Azure Government s paritou veřejné verze. Jedinou výjimkou je funkce **Přidat Azure kognitivní hledání** aktuálně, která není dostupná v Cosmos DB pro Azure Government.

Adresy URL pro přístup k Cosmos DB v Azure Government se také liší:

| Typ služby | Veřejné Azure | Azure Government |
| --- | --- | --- |
| Databáze Cosmos | *. documents.azure.com | *. documents.azure.us |


### <a name="considerations"></a>Požadavky
Následující informace identifikují Azure Government hranici pro Azure Cosmos DB:

| Povolená řízená/řízená data | Regulovaná/řízená data nejsou povolena. |
| --- | --- |
| Všechna data uložená a zpracovaná v Azure Cosmos DB můžou obsahovat Azure Government regulovaná data. |Metadata Azure Cosmos DB nesmí obsahovat data řízená exportem. Nezadávejte regulovaná nebo řízená data do následujících polí: **název databáze, název předplatného, skupiny prostředků a značky prostředků**. |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Podrobnosti o této službě a její použití najdete v tématu dokumentace k [Azure cache pro Redis](../azure-cache-for-redis/index.yml).

### <a name="variations"></a>Odlišnost
Adresy URL pro přístup a správu Azure cache pro Redis v Azure Government se liší:

| Typ služby | Veřejné Azure | Azure Government |
| --- | --- | --- |
| Koncový bod mezipaměti |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |

> [!NOTE]
> Všechny skripty a kód musí mít účet pro příslušné koncové body a prostředí. Další informace najdete v tématu [jak se připojit k ostatním cloudům](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).
>
>

### <a name="considerations"></a>Požadavky
Následující informace identifikují Azure Government hranici pro Azure cache pro Redis:

| Povolená řízená/řízená data | Regulovaná/řízená data nejsou povolena. |
| --- | --- |
| Všechna data uložená a zpracovaná v Azure cache pro Redis můžou obsahovat data regulovaná Azure Government. |Mezipaměť Azure pro metadata Redis nemá povoleno obsahovat data řízená exportem. Nezadávejte regulovaná nebo řízená data do následujících polí: **Název mezipaměti, název předplatného, skupiny prostředků, značky prostředků, vlastnosti Redis**. |

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Podrobnosti o této službě a o tom, jak ji používat, najdete v [dokumentaci Azure Database for PostgreSQL](../postgresql/index.yml).

### <a name="variations"></a>Odlišnost
Rozšířená ochrana před internetovými útoky, dotazy na výkon a doporučení týkající se výkonu pro Azure Database for PostgreSQL **nejsou k** dispozici v Azure Government.

Adresy URL pro přístup a správu Azure Database for PostgreSQL v Azure Government se liší:

| Typ služby | Veřejné Azure | Azure Government |
| --- | --- | --- |
| Koncový bod PostgreSQL |*. postgres.database.azure.com |*. postgres.database.usgovcloudapi.net |

### <a name="considerations"></a>Požadavky
Následující informace identifikují Azure Government hranici pro Azure Database for PostgreSQL:

| Povolená řízená/řízená data | Regulovaná/řízená data nejsou povolena. |
| --- | --- |
| Všechna data uložená a zpracovaná v Azure Database for PostgreSQL můžou obsahovat Azure Government regulovaná data. Použití databázových nástrojů pro přenos dat Azure Government řízených dat. |Metadata Azure Database for PostgreSQL nesmí obsahovat data řízená exportem. Tato metadata zahrnují všechna konfigurační data zadaná při vytváření a údržbě produktu úložiště.  Nezadávejte regulovaná a řízená data do následujících polí: název databáze, název předplatného, skupiny prostředků, název serveru, přihlašovací jméno správce serveru, názvy nasazení, názvy prostředků a značky prostředků. |

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
Podrobnosti o této službě a o tom, jak ji používat, najdete v [dokumentaci Azure Database for MariaDB](../mariadb/index.yml).

### <a name="variations"></a>Odlišnost
Dotazy na výkon a doporučení týkající se výkonu pro Azure Database for MariaDB **nejsou k** dispozici v Azure Government.

Adresy URL pro přístup a správu Azure Database for MariaDB v Azure Government se liší:

| Typ služby | Veřejné Azure | Azure Government |
| --- | --- | --- |
| Koncový bod MariaDB |*. mariadb.database.azure.com |*. mariadb.database.usgovcloudapi.net |

### <a name="considerations"></a>Požadavky
Následující informace identifikují Azure Government hranici pro Azure Database for MariaDB:

| Povolená řízená/řízená data | Regulovaná/řízená data nejsou povolena. |
| --- | --- |
| Všechna data uložená a zpracovaná v Azure Database for MariaDB můžou obsahovat Azure Government regulovaná data. Použití databázových nástrojů pro přenos dat Azure Government řízených dat. |Metadata Azure Database for MariaDB nesmí obsahovat data řízená exportem. Tato metadata zahrnují všechna konfigurační data zadaná při vytváření a údržbě produktu úložiště.  Nezadávejte regulovaná a řízená data do následujících polí: název databáze, název předplatného, skupiny prostředků, název serveru, přihlašovací jméno správce serveru, názvy nasazení, názvy prostředků a značky prostředků. |

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Podrobnosti o této službě a o tom, jak ji používat, najdete v [dokumentaci Azure Database for MySQL](../mysql/index.yml).

### <a name="variations"></a>Odlišnost
Rozšířená ochrana před internetovými útoky, dotazy na výkon a doporučení týkající se výkonu pro Azure Database for MySQL **nejsou k** dispozici v Azure Government.

Adresy URL pro přístup a správu Azure Database for MySQL v Azure Government se liší:

| Typ služby | Veřejné Azure | Azure Government |
| --- | --- | --- |
| Koncový bod MySQL |*. mysql.database.azure.com |*. mysql.database.usgovcloudapi.net |

### <a name="considerations"></a>Požadavky
Následující informace identifikují Azure Government hranici pro Azure Database for MySQL:

| Povolená řízená/řízená data | Regulovaná/řízená data nejsou povolena. |
| --- | --- |
| Všechna data uložená a zpracovaná v Azure Database for MySQL můžou obsahovat Azure Government regulovaná data. Použití databázových nástrojů pro přenos dat Azure Government řízených dat. |Metadata Azure Database for MySQL nesmí obsahovat data řízená exportem. Tato metadata zahrnují všechna konfigurační data zadaná při vytváření a údržbě produktu úložiště.  Nezadávejte regulovaná a řízená data do následujících polí: název databáze, název předplatného, skupiny prostředků, název serveru, přihlašovací jméno správce serveru, názvy nasazení, názvy prostředků a značky prostředků. |

## <a name="next-steps"></a>Další kroky
Doplňující informace a aktualizace se přihlásí k odběru <a href="https://blogs.msdn.microsoft.com/azuregov/">blogu Microsoft Azure Government.</a>
