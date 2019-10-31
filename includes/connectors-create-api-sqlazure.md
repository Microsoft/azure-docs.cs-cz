---
title: zahrnout soubor
description: zahrnout soubor
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: d60d7727e0674298fa6da7e7330221318da23efd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161619"
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

Když se aktivační procedura nebo akce SQL vyzve k zadání informací o připojení, postupujte podle těchto kroků, které fungují pro aktivační události a akce. Než začnete, ujistěte se, že jste už [nastavili místní bránu dat](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). V opačném případě se brána při vytváření připojení nezobrazí v seznamu bran.

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
