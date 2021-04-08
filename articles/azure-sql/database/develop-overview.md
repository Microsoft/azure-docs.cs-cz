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
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92782973"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Přehled vývoje aplikací – SQL Database & spravované instance SQL

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Tento článek vás provede základními informacemi, které by měl vývojář znát při psaní kódu pro připojení k vaší databázi v Azure. Tento článek se týká Azure SQL Database a spravované instance Azure SQL.

## <a name="language-and-platform"></a>Jazyk a platforma

K připojení a dotazování Azure SQL Database můžete použít různé [programovací jazyky a platformy](connect-query-content-reference-guide.md) . Můžete najít [ukázkové aplikace](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) , které můžete použít pro připojení k databázi.

Můžete využít Open Source nástroje, jako je [Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli), [vs Code](https://code.visualstudio.com/). Kromě toho Azure SQL Database pracuje s nástroji Microsoftu jako [Visual Studio](https://www.visualstudio.com/downloads/) a [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms). Můžete také využít Azure Portal, PowerShell a rozhraní REST API, které vám pomůžou získat další produktivitu.

## <a name="authentication"></a>Authentication

Přístup k Azure SQL Database je chráněný pomocí přihlašovacích údajů a bran firewall. Azure SQL Database podporuje uživatele a přihlašovací údaje ověřování SQL Server a [Azure Active Directory](authentication-aad-overview.md) . Přihlášení Azure Active Directory jsou k dispozici pouze ve spravované instanci SQL. 

Přečtěte si další informace o [správě přístupu k databázi a přihlášení](logins-create-manage.md).

## <a name="connections"></a>Připojení

V logice připojování klienta přepište výchozí časový limit na 30 sekund. Výchozí hodnota 15 sekund je příliš krátká pro připojení, která jsou závislá na internetu.

Pokud používáte [fond připojení](/dotnet/framework/data/adonet/sql-server-connection-pooling), ukončete připojení v okamžiku, kdy ho program aktivně nepoužívá a není připravený na opakované použití.

Nepoužívejte dlouhotrvající transakce, protože jakákoli infrastruktura nebo selhání připojení může transakci vrátit zpět. Pokud je to možné, rozdělte transakci v několika menších transakcích a pomocí [dávkování Vylepšete výkon](../performance-improve-use-batching.md).

## <a name="resiliency"></a>Odolnost

Azure SQL Database je cloudová služba, ve které byste mohli očekávat přechodné chyby, ke kterým dochází v základní infrastruktuře nebo v komunikaci mezi entitami cloudu. I když je Azure SQL Database odolná proti chybám přenosných infrastruktur, může to mít vliv na vaše připojení. Pokud při připojování k SQL Database dojde k přechodné chybě, váš kód by měl [zavolat znovu](troubleshoot-common-connectivity-issues.md). Doporučujeme, aby logika opakování použila omezení rychlosti logiku, aby nedošlo k zahlcení služby vícenásobným pokusem o spuštění více klientů současně. Logika opakování závisí na [chybových zprávách pro SQL Database klientských programů](troubleshoot-common-errors-issues.md).

Další informace o přípravě na plánované události údržby v Azure SQL Database najdete v tématu [plánování událostí údržby Azure v Azure SQL Database](planned-maintenance.md).

## <a name="network-considerations"></a>Důležité informace z hlediska využívání sítě

- Na počítači, který hostuje klientský program, zajistěte, aby brána firewall umožňovala odchozí komunikaci TCP na portu 1433.  Další informace: [Konfigurace brány Azure SQL Database firewall](firewall-configure.md).
- Pokud se klientský program připojí k SQL Database v době, kdy klient běží na virtuálním počítači Azure, musíte na VIRTUÁLNÍm počítači otevřít určité rozsahy portů. Další informace: [porty přesahující 1433 pro ADO.NET 4,5 a SQL Database](adonet-v12-develop-direct-route-ports.md).
- Klientská připojení k Azure SQL Database někdy obcházejí proxy a komunikují přímo s databází. Na významu nabývají jiné porty než 1433. Další informace najdete v [Azure SQL Database architektury připojení](connectivity-architecture.md) a [porty nad 1433 pro ADO.NET 4,5 a SQL Database](adonet-v12-develop-direct-route-ports.md).
- Konfigurace sítě pro instanci spravované instance SQL najdete v tématu [Konfigurace sítě pro spravovanou instanci SQL](../managed-instance/how-to-content-reference-guide.md#network-configuration).

## <a name="next-steps"></a>Další kroky

Prozkoumejte všechny možnosti [SQL Database](sql-database-paas-overview.md) a [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md).

Pokud chcete začít, přečtěte si příručky [Azure SQL Database](quickstart-content-reference-guide.md) a [Azure SQL Managed Instances](../managed-instance/quickstart-content-reference-guide.md).