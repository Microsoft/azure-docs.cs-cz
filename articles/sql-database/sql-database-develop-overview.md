---
title: Přehled vývoje aplikací
description: Další informace o dostupných knihovnách připojení a osvědčených postupech pro aplikace, které se připojují ke službě SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.openlocfilehash: 741906bbe9de68459b2e4a704a243fde4771b3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067330"
---
# <a name="sql-database-application-development-overview"></a>Přehled vývoje aplikací SQL Database

V tomto článku se seznámíte se základními předpoklady, které by měl mít vývojář na zřeteli při zapisování kódu pro připojení ke službě Azure SQL Database. Tento článek se vztahuje na všechny modely nasazení Azure SQL Database (jedna databáze, elastické fondy, spravovaná instance).

> [!TIP]
> Pokud potřebujete nastavit azure SQL database, podívejte se na [příručky](sql-database-single-database-quickstart-guide.md) začínáme pro jednotlivé databáze a [spravované instance.](sql-database-managed-instance-quickstart-guide.md)
>

## <a name="language-and-platform"></a>Jazyk a platforma

Můžete použít různé [programovací jazyky a platformy](sql-database-connect-query.md) pro připojení a dotazování Azure SQL Database. Můžete najít [ukázkové aplikace,](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) které můžete použít k připojení k azure sql database.

Můžete využít open-source nástroje, jako [je gepard](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/). Kromě toho Azure SQL Database pracuje s nástroji Microsoftu jako [Visual Studio](https://www.visualstudio.com/downloads/) a [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Můžete také použít Azure portal, PowerShell a REST API vám pomůže získat další produktivitu.

## <a name="authentication"></a>Ověřování

Přístup k Azure SQL Database je chráněný pomocí přihlašovacích údajů a firewallů. Azure SQL Database podporuje uživatele ověřování SQL Serveru i [Azure Active Directory (AAD)](sql-database-aad-authentication.md) a přihlášení. Přihlášení aad jsou k dispozici pouze ve spravované instanci. 

Další informace o [správě přístupu k databázi a přihlášení](sql-database-manage-logins.md).

## <a name="connections"></a>Připojení

V logice připojování klienta přepište výchozí časový limit na 30 sekund. Výchozí hodnota 15 sekund je příliš krátká pro připojení, která jsou závislá na internetu.

Pokud používáte [fond připojení](https://msdn.microsoft.com/library/8xx3tyca.aspx), ukončete připojení v okamžiku, kdy ho program aktivně nepoužívá a není připravený na opakované použití.

Vyhněte se dlouhotrvající transakce, protože jakékoli selhání infrastruktury nebo připojení může vrátit zpět transakce. Pokud je to možné, rozdělte transakci do více menších transakcí a použijte [dávkování ke zlepšení výkonu](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Odolnost

Azure SQL Database je cloudová služba, kde můžete očekávat přechodné chyby, ke kterým dochází v základní infrastruktuře nebo v komunikaci mezi cloudovými entitami. Přestože Azure SQL Database je odolný na selhání přenosité infrastruktury, tyto chyby může ovlivnit připojení. Dojde-li k přechodné chybě při připojování k databázi SQL, váš kód by měl [opakovat volání](sql-database-connectivity-issues.md). Doporučujeme, aby logika dalších pokusů používala logiku opakování, aby služba SQL Database nebyla zaplavená opakovanými pokusy několika klientů současně. Logika opakování závisí na [chybových zprávách pro klientské programy databáze SQL](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md).

Další informace o tom, jak připravit na plánované události údržby v databázi Azure SQL, najdete [v tématu plánování událostí údržby Azure v Azure SQL Database](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Důležité informace z hlediska využívání sítě

- Na počítači, který hostuje klientský program, zajistěte, aby brána firewall umožňovala odchozí komunikaci TCP na portu 1433.  Další informace: [Konfigurace brány firewall azure SQL database](sql-database-configure-firewall-settings.md).
- Pokud se váš klientský program připojí k databázi SQL Database, zatímco váš klient běží na virtuálním počítači Azure (VM), musíte otevřít určité rozsahy portů na virtuálním počítači. Další informace: [Porty nad 1433 pro ADO.NET 4.5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Připojení klientů k Azure SQL Database někdy obejít proxy server a komunikovat přímo s databází. Na významu nabývají jiné porty než 1433. Další informace, [architektura připojení Azure SQL Database](sql-database-connectivity-architecture.md) a [porty nad 1433 pro ADO.NET 4.5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Konfigurace sítě pro spravovanou instanci naleznete [v tématu konfigurace sítě pro spravované instance](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Další kroky

Prozkoumejte všechny [možnosti databáze SQL](sql-database-technical-overview.md)Database .
