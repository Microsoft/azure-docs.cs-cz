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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067330"
---
# <a name="sql-database-application-development-overview"></a>SQL Database – přehled vývoje aplikací

V tomto článku se seznámíte se základními předpoklady, které by měl mít vývojář na zřeteli při zapisování kódu pro připojení ke službě Azure SQL Database. Tento článek se týká všech modelů nasazení Azure SQL Database (jedna databáze, elastické fondy, spravovaná instance).

> [!TIP]
> Pokud potřebujete nastavit Azure SQL Database, přečtěte si příručky Začínáme s izolovanými [databázemi](sql-database-single-database-quickstart-guide.md) a [spravovanými instancemi](sql-database-managed-instance-quickstart-guide.md) .
>

## <a name="language-and-platform"></a>Jazyk a platforma

K připojení a dotazování Azure SQL Database můžete použít různé [programovací jazyky a platformy](sql-database-connect-query.md) . Můžete najít [ukázkové aplikace](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) , které můžete použít pro připojení k Azure SQL Database.

Můžete využít Open Source nástroje, jako je [Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli), [vs Code](https://code.visualstudio.com/). Kromě toho Azure SQL Database pracuje s nástroji Microsoftu jako [Visual Studio](https://www.visualstudio.com/downloads/) a [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Můžete také využít Azure Portal, PowerShell a rozhraní REST API, které vám pomůžou získat další produktivitu.

## <a name="authentication"></a>Authentication

Přístup k Azure SQL Database je chráněný pomocí přihlašovacích údajů a bran firewall. Azure SQL Database podporuje i uživatele ověřování SQL Server a [Azure Active Directory (AAD)](sql-database-aad-authentication.md) a přihlašovací údaje. Přihlášení AAD jsou k dispozici pouze ve spravované instanci. 

Přečtěte si další informace o [správě přístupu k databázi a přihlášení](sql-database-manage-logins.md).

## <a name="connections"></a>Připojení

V logice připojování klienta přepište výchozí časový limit na 30 sekund. Výchozí hodnota 15 sekund je příliš krátká pro připojení, která jsou závislá na internetu.

Pokud používáte [fond připojení](https://msdn.microsoft.com/library/8xx3tyca.aspx), ukončete připojení v okamžiku, kdy ho program aktivně nepoužívá a není připravený na opakované použití.

Nepoužívejte dlouhotrvající transakce, protože jakákoli infrastruktura nebo selhání připojení může transakci vrátit zpět. Pokud je to možné, rozdělte transakci v několika menších transakcích a pomocí [dávkování Vylepšete výkon](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Odolnost

Azure SQL Database je cloudová služba, ve které byste mohli očekávat přechodné chyby, ke kterým dochází v základní infrastruktuře nebo v komunikaci mezi entitami cloudu. I když je Azure SQL Database odolná proti chybám přenosných infrastruktur, může to mít vliv na vaše připojení. Pokud při připojování k SQL Database dojde k přechodné chybě, váš kód by měl [zavolat znovu](sql-database-connectivity-issues.md). Doporučujeme, aby logika dalších pokusů používala logiku opakování, aby služba SQL Database nebyla zaplavená opakovanými pokusy několika klientů současně. Logika opakování závisí na [chybových zprávách pro SQL Database klientských programů](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md).

Další informace o přípravě na plánované události údržby ve službě Azure SQL Database najdete v tématu [plánování událostí údržby Azure v Azure SQL Database](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Důležité informace z hlediska využívání sítě

- Na počítači, který hostuje klientský program, zajistěte, aby brána firewall umožňovala odchozí komunikaci TCP na portu 1433.  Další informace: [Konfigurace brány Azure SQL Database firewall](sql-database-configure-firewall-settings.md).
- Pokud se klientský program připojí k SQL Database v době, kdy klient běží na virtuálním počítači Azure, musíte na VIRTUÁLNÍm počítači otevřít určité rozsahy portů. Další informace: [porty přesahující 1433 pro ADO.NET 4,5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Klientská připojení k Azure SQL Database někdy obcházejí proxy a komunikují přímo s databází. Na významu nabývají jiné porty než 1433. Další informace najdete v [Azure SQL Database architektury připojení](sql-database-connectivity-architecture.md) a [porty nad 1433 pro ADO.NET 4,5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Konfigurace sítě pro spravovanou instanci najdete v tématu [Konfigurace sítě pro spravované instance](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Další kroky

Prozkoumejte všechny [možnosti SQL Database](sql-database-technical-overview.md).
