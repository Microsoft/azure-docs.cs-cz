---
title: Přehled vývoje databázových aplikací služby SQL Database | Dokumentace Microsoftu
description: Další informace o dostupných knihovnách připojení a osvědčených postupech pro aplikace, které se připojují ke službě SQL Database
services: sql-database
author: stevestein
manager: craigg
ms.reviewer: genemi
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: sstein
ms.openlocfilehash: 4236372370858e940d7e3a1671c0e8a8278796fb
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055042"
---
# <a name="sql-database-application-development-overview"></a>Přehled vývoje databázových aplikací SQL Database
V tomto článku se seznámíte se základními předpoklady, které by měl mít vývojář na zřeteli při zapisování kódu pro připojení ke službě Azure SQL Database.

> [!TIP]
> Pokud potřebujete kurz, ve kterém se naučíte vytvořit server, vytvořit bránu firewall založenou na serveru, zobrazit vlastnosti serveru, připojit se pomocí nástroje SQL Server Management Studio, vytvářet dotazy v hlavní databázi, vytvořit vzorovou databázi a prázdnou databázi nebo se připojit pomocí nástroje SQL Server Management Studio a vytvářet dotazy ve vzorové databázi, přejděte na stránku [kurzu Začínáme](sql-database-get-started-portal.md).
>

## <a name="language-and-platform"></a>Jazyk a platforma
K dispozici jsou ukázky kódu pro různé programovací jazyky a platformy. Odkazy na ukázky kódu najdete tady: 

* Další informace: [připojení knihoven pro službu SQL Database a SQL Server](sql-database-libraries.md).

## <a name="tools"></a>Nástroje 
Můžete využít opensourcové nástroje, jako je [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/). Kromě toho Azure SQL Database pracuje s nástroji Microsoftu jako [Visual Studio](https://www.visualstudio.com/downloads/) a [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).  Můžete taky použít Portál pro správu Azure, PowerShell a rozhraní REST API, které vám pomůžou získat další produktivitu.

## <a name="resource-limitations"></a>Omezení prostředků
Azure SQL Database spravuje prostředky dostupné pro databázi pomocí dvou různých mechanismů: řízení prostředků a vynucení limitů. Další informace naleznete v tématu:

- [Omezení model založený na DTU prostředků – jedné databáze](sql-database-dtu-resource-limits-single-databases.md)
- [Omezení model založený na DTU prostředků – Elastických fondů](sql-database-dtu-resource-limits-elastic-pools.md)
- [omezení prostředků na základě virtuálních jader – izolované databáze](sql-database-vcore-resource-limits-single-databases.md)
- [omezení prostředků založený na virtuálních jádrech - Elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md)

## <a name="security"></a>Zabezpečení
Azure SQL Database poskytuje prostředky pro omezení přístupu, ochranu dat a sledování aktivit služby SQL Database.

* Další informace: [zabezpečení služby SQL Database](sql-database-security-overview.md).

## <a name="authentication"></a>Authentication
* Azure SQL Database podporuje uživatele a přihlašování systému SQL Server i uživatele a přihlašování ověřování [Azure Active Directory](sql-database-aad-authentication.md).
* Musíte zadat konkrétní databázi místo nastavení na výchozí *hlavní* databázi.
* Nemůžete použít příkaz Transact-SQL **USE myDatabaseName;** ve službě SQL Database pro přechod na jinou databázi.
* Další informace: [zabezpečení služby SQL Database: Správa databáze přístup a zabezpečení přihlašování](sql-database-manage-logins.md).

## <a name="resiliency"></a>Odolnost
V případě přechodné chyby při připojování ke službě SQL Database by měl váš kód volání zopakovat.  Doporučujeme, aby logika dalších pokusů používala logiku opakování, aby služba SQL Database nebyla zaplavená opakovanými pokusy několika klientů současně.

* Ukázky kódu: ukázky kódu, které ilustrují logiku opakovaných pokusů, najdete v části Ukázky jazyka podle vašeho výběru v: [připojení knihoven pro službu SQL Database a SQL Server](sql-database-libraries.md).
* Další informace: [chybové zprávy klientských programů služby SQL Database](sql-database-develop-error-messages.md).

## <a name="managing-connections"></a>Správa připojení
* V logice připojování klienta přepište výchozí časový limit na 30 sekund.  Výchozí hodnota 15 sekund je příliš krátká pro připojení, která jsou závislá na internetu.
* Pokud používáte [fond připojení](http://msdn.microsoft.com/library/8xx3tyca.aspx), ukončete připojení v okamžiku, kdy ho program aktivně nepoužívá a není připravený na opakované použití.

## <a name="network-considerations"></a>Důležité informace o síti
* Na počítači, který hostuje klientský program, zajistěte, aby brána firewall umožňovala odchozí komunikaci TCP na portu 1433.  Další informace: [konfigurace brány firewall Azure SQL Database](sql-database-configure-firewall-settings.md).
* Pokud váš klientský program připojuje ke službě SQL Database, zatímco vašeho klienta běží na virtuálním počítači Azure (VM), je nutné otevřít určité rozsahy portů na virtuálním počítači. Další informace: [porty nad 1433 pro ADO.NET 4.5 a službu SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
* Připojení klienta ke službě Azure SQL Database někdy obcházejí proxy a pracovat přímo s databází. Na významu nabývají jiné porty než 1433. Další informace najdete [architektura připojení k Azure SQL Database](sql-database-connectivity-architecture.md) a [porty nad 1433 pro ADO.NET 4.5 a službu SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="data-sharding-with-elastic-scale"></a>Horizontální dělení dat s elastickým Škálováním
Pružné škálování zjednodušuje proces škálování (a). 

* [Způsoby návrhu pro aplikace SaaS s více tenanty s databází Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* [Směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md).
* [Začínáme s Azure SQL Database Elastic Scale Preview](sql-database-elastic-scale-get-started.md).

## <a name="next-steps"></a>Další postup
Prozkoumejte všechny [schopnosti služby SQL Database](sql-database-technical-overview.md).
