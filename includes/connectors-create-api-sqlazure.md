---
title: zahrnout soubor
description: zahrnout soubor
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 11/08/2019
ms.openlocfilehash: 0be29f6f541aa58e57eb665ebaf29e35f42865e4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826322"
---
* Pokud používáte Azure SQL Database, postupujte podle pokynů v části [připojení k Azure SQL Database](#connect-azure-sql-db).

* Pokud používáte SQL Server, postupujte podle pokynů v části [připojení k SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Připojení k Azure SQL Database

Když se aktivační procedura nebo akce SQL vyzve k zadání informací o připojení, postupujte podle těchto kroků, které fungují pro aktivační události a akce.

1. Pro **název připojení**vytvořte název pro vaše připojení.

1. V části **SQL Server název**vyberte svůj server SQL Azure. Jakmile se zobrazí seznam **SQL Database název** , vyberte svou databázi. Zadejte uživatelské jméno a heslo pro váš server SQL Azure.

   Tyto informace můžete najít také v Azure Portal ve vlastnostech databáze SQL nebo v připojovacím řetězci:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Vytvořit připojení k Azure SQL Database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Až to budete mít, vyberte **Vytvořit**.

1. Po vytvoření připojení pokračujte [přidáním triggeru SQL](#add-sql-trigger) nebo [přidáním akce SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Připojení k SQL Serveru

Když se aktivační procedura nebo akce SQL vyzve k zadání informací o připojení, postupujte podle těchto kroků, které fungují pro aktivační události a akce. Pro scénáře, které vyžadují, abyste nainstalovali místní [bránu dat](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) na místním počítači a [vytvořili prostředek služby Azure Data Gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection), ujistěte se, že jste nejdřív dokončili tyto požadavky. V opačném případě se prostředek brány nezobrazí v seznamu bran při vytváření připojení.

Pokud chcete používat ověřování systému Windows s konektorem SQL Server v [prostředí ISE (Integration Service Environment)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview), použijte neISE verzi konektoru a místní bránu dat. Verze ISE s popiskem nepodporuje ověřování systému Windows.

1. Pro **název připojení**vytvořte název pro vaše připojení.

1. V aktivační události nebo akci vyberte **připojit přes místní bránu dat** , aby se zobrazily možnosti SQL serveru.

1. Do pole **název systému SQL Server** a **název databáze SQL**zadejte adresu pro svůj SQL Server a název pro vaši databázi. Pro **uživatelské jméno** a **heslo**zadejte uživatelské jméno a heslo pro váš server.

   Tyto informace můžete najít také v připojovacím řetězci:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Vytvořit připojení k SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Pokud SQL Server používá ověřování systému Windows nebo základní, vyberte **typ ověřování**.

1. V části **brány**vyberte předplatné Azure, které je přidružené k dříve vytvořené místní bráně dat, a vyberte název pro místní bránu dat.

   Pokud se vaše brána v seznamu nezobrazí, ověřte, že jste správně [nastavili bránu](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![Vytvoření připojení SQL Server dokončeno](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Až to budete mít, vyberte **Vytvořit**.

1. Po vytvoření připojení pokračujte pomocí možnosti [Přidat Trigger SQL](#add-sql-trigger) nebo [přidejte akci SQL](#add-sql-action).
