---
title: Přehled vývoje databázových aplikací služby SQL Database | Dokumentace Microsoftu
description: Další informace o dostupných knihovnách připojení a osvědčených postupech pro aplikace, které se připojují ke službě SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 01c4bcfcea038f3e69620cdce78719c8c5128faf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964793"
---
# <a name="sql-database-application-development-overview"></a>Přehled vývoje databázových aplikací SQL Database

V tomto článku se seznámíte se základními předpoklady, které by měl mít vývojář na zřeteli při zapisování kódu pro připojení ke službě Azure SQL Database. Tento článek se týká všech modelech nasazení služby Azure SQL Database (izolované databáze, elastické fondy, spravované instance).

> [!TIP]
> Podívejte se získání spuštění průvodců pro [izolované databáze](sql-database-single-database-quickstart-guide.md) a [spravované instance](sql-database-managed-instance-quickstart-guide.md) Pokud je potřeba nastavit Azure SQL Database.
>

## <a name="language-and-platform"></a>Jazyk a platforma

Můžete použít různé [programovacích jazyků a platforem](sql-database-connect-query.md) k připojení a dotazování Azure SQL Database. Můžete najít [ukázkové aplikace](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) , můžete použít k připojení ke službě Azure SQL Database.

Můžete využít opensourcové nástroje, jako je [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/). Kromě toho Azure SQL Database pracuje s nástroji Microsoftu jako [Visual Studio](https://www.visualstudio.com/downloads/) a [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Můžete použít také na webu Azure portal, Powershellu a rozhraní REST API, které vám pomůžou získat další produktivitu.

## <a name="authentication"></a>Authentication

Přístup ke službě Azure SQL Database je chránit pomocí brány firewall a přihlášení. Azure SQL Database podporuje systému SQL Server a [ověřování Azure Active Directory (AAD)](sql-database-aad-authentication.md) uživatelé a přihlašování. Přihlašovací údaje AAD jsou k dispozici pouze ve spravované instanci. 

Další informace o [Správa přístupu k databázi a přihlášení](sql-database-manage-logins.md).

## <a name="connections"></a>Připojení

V logice připojování klienta přepište výchozí časový limit na 30 sekund. Výchozí hodnota 15 sekund je příliš krátká pro připojení, která jsou závislá na internetu.

Pokud používáte [fond připojení](https://msdn.microsoft.com/library/8xx3tyca.aspx), ukončete připojení v okamžiku, kdy ho program aktivně nepoužívá a není připravený na opakované použití.

Vyhněte se dlouhotrvajících transakcí, protože jakékoli neúspěchy infrastruktury nebo připojení může vrátit zpět transakci. Pokud je to možné, transakce v několika menších transakce a použijete [dávkování pro zlepšení výkonu](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Odolnost

Azure SQL Database je Cloudová služba, ve kterém by se dalo očekávat přechodné chyby, ke kterým dochází v základní infrastruktuře nebo komunikaci mezi cloudové entity. Sice odolné na selhání přechodné infrastruktury Azure SQL Database, tyto chyby může ovlivnit vaše připojení. V případě přechodné chyby při připojování ke službě SQL Database by měl váš kód [volání zopakovat](sql-database-connectivity-issues.md). Doporučujeme, aby logika dalších pokusů používala logiku opakování, aby služba SQL Database nebyla zaplavená opakovanými pokusy několika klientů současně. Logika opakovaných pokusů závisí [chybové zprávy klientských programů služby SQL Database](sql-database-develop-error-messages.md).

Další informace o tom, jak připravit pro události plánované údržby v Azure SQL database najdete v tématu [plánování údržby Azure událostí ve službě Azure SQL Database](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Důležité informace o síti

- Na počítači, který hostuje klientský program, zajistěte, aby brána firewall umožňovala odchozí komunikaci TCP na portu 1433.  Další informace: [Konfigurace brány firewall Azure SQL Database](sql-database-configure-firewall-settings.md).
- Pokud váš klientský program připojuje ke službě SQL Database, zatímco vašeho klienta běží na virtuálním počítači Azure (VM), je nutné otevřít určité rozsahy portů na virtuálním počítači. Další informace: [Porty nad 1433 pro ADO.NET 4.5 a službu SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Připojení klienta ke službě Azure SQL Database někdy obcházejí proxy a pracovat přímo s databází. Na významu nabývají jiné porty než 1433. Další informace najdete [architektura připojení k Azure SQL Database](sql-database-connectivity-architecture.md) a [porty nad 1433 pro ADO.NET 4.5 a službu SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Sítě configation pro spravovanou instanci, najdete v části [konfiguraci sítě pro spravované instance](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Další postup

Prozkoumejte všechny [schopnosti služby SQL Database](sql-database-technical-overview.md).
