---
title: Přehled vývoje databázových aplikací služby SQL Database | Dokumentace Microsoftu
description: Další informace o dostupných knihovnách připojení a osvědčených postupech pro aplikace, které se připojují ke službě SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 02/07/2019
ms.openlocfilehash: 42fc73b5557fba91cc132a0abe8561f0a72bbb64
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568859"
---
# <a name="sql-database-application-development-overview"></a>SQL Database – přehled vývoje aplikací

V tomto článku se seznámíte se základními předpoklady, které by měl mít vývojář na zřeteli při zapisování kódu pro připojení ke službě Azure SQL Database. Tento článek se týká všech modelů nasazení Azure SQL Database (jedna databáze, elastické fondy, spravovaná instance).

> [!TIP]
> Pokud potřebujete nastavit Azure SQL Database, přečtěte [](sql-database-single-database-quickstart-guide.md) si příručky Začínáme s izolovanými databázemi a [spravovanými instancemi](sql-database-managed-instance-quickstart-guide.md) .
>

## <a name="language-and-platform"></a>Jazyk a platforma

K připojení a dotazování Azure SQL Database můžete použít různé [programovací jazyky a platformy](sql-database-connect-query.md) . Můžete najít [ukázkové aplikace](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) , které můžete použít pro připojení k Azure SQL Database.

Můžete využít Open Source nástroje, jako je [Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli), [vs Code](https://code.visualstudio.com/). Kromě toho Azure SQL Database pracuje s nástroji Microsoftu jako [Visual Studio](https://www.visualstudio.com/downloads/) a [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Můžete také využít Azure Portal, PowerShell a rozhraní REST API, které vám pomůžou získat další produktivitu.

## <a name="authentication"></a>Ověřování

Přístup k Azure SQL Database je chráněný pomocí přihlašovacích údajů a bran firewall. Azure SQL Database podporuje i uživatele ověřování SQL Server a [Azure Active Directory (AAD)](sql-database-aad-authentication.md) a přihlašovací údaje. Přihlášení AAD jsou k dispozici pouze ve spravované instanci. 

Přečtěte si další informace o [správě přístupu k databázi a přihlášení](sql-database-manage-logins.md).

## <a name="connections"></a>Připojení

V logice připojování klienta přepište výchozí časový limit na 30 sekund. Výchozí hodnota 15 sekund je příliš krátká pro připojení, která jsou závislá na internetu.

Pokud používáte [fond připojení](https://msdn.microsoft.com/library/8xx3tyca.aspx), ukončete připojení v okamžiku, kdy ho program aktivně nepoužívá a není připravený na opakované použití.

Nepoužívejte dlouhotrvající transakce, protože jakákoli infrastruktura nebo selhání připojení může transakci vrátit zpět. Pokud je to možné, rozdělte transakci v několika menších transakcích a pomocí [dávkování Vylepšete výkon](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Odolnost

Azure SQL Database je cloudová služba, ve které byste mohli očekávat přechodné chyby, ke kterým dochází v základní infrastruktuře nebo v komunikaci mezi entitami cloudu. I když je Azure SQL Database odolná proti chybám přenosných infrastruktur, může to mít vliv na vaše připojení. Pokud při připojování k SQL Database dojde k přechodné chybě, váš kód by měl [zavolat znovu](sql-database-connectivity-issues.md). Doporučujeme, aby logika dalších pokusů používala logiku opakování, aby služba SQL Database nebyla zaplavená opakovanými pokusy několika klientů současně. Logika opakování závisí na [chybových zprávách pro SQL Database klientských programů](sql-database-develop-error-messages.md).

Další informace o přípravě na plánované události údržby ve službě Azure SQL Database najdete v tématu [plánování událostí údržby Azure v Azure SQL Database](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Síťové požadavky

- Na počítači, který hostuje klientský program, zajistěte, aby brána firewall umožňovala odchozí komunikaci TCP na portu 1433.  Další informace: [Nakonfigurujte bránu Azure SQL Database firewall](sql-database-configure-firewall-settings.md).
- Pokud se klientský program připojí k SQL Database v době, kdy klient běží na virtuálním počítači Azure, musíte na VIRTUÁLNÍm počítači otevřít určité rozsahy portů. Další informace: [Porty přesahující 1433 pro ADO.NET 4,5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Klientská připojení k Azure SQL Database někdy obcházejí proxy a komunikují přímo s databází. Na významu nabývají jiné porty než 1433. Další informace najdete v [Azure SQL Database architektury připojení](sql-database-connectivity-architecture.md) a [porty nad 1433 pro ADO.NET 4,5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Konfigurace sítě pro spravovanou instanci najdete v tématu [Konfigurace sítě pro spravované instance](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Další kroky

Prozkoumejte všechny [schopnosti služby SQL Database](sql-database-technical-overview.md).
