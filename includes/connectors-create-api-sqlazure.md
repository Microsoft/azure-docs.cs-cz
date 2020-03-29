---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789188"
---
* Pokud používáte Azure SQL Database, postupujte podle pokynů v části [Připojení k Azure SQL Database](#connect-azure-sql-db).

* Pokud používáte SQL Server, postupujte podle pokynů v části [Připojit k SERVERU SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Připojení k azure sql databázi

Když aktivační událost SQL nebo akce vyzve k zadání informací o připojení, postupujte takto, které fungují pro aktivační události i akce.

1. V **případě názvu připojení**vytvořte název připojení.

1. V části **Název serveru SQL**vyberte server Azure SQL. Po zobrazení seznamu **Název databáze SQL** vyberte databázi. Zadejte uživatelské jméno a heslo pro váš server Azure SQL.

   Tyto informace najdete také na webu Azure Portal ve vlastnostech databáze SQL nebo v připojovacím řetězci:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Vytvoření připojení k Azure SQL Database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Až to budete mít, vyberte **Vytvořit**.

1. Po vytvoření připojení pokračujte [pomocí funkce Přidat aktivační událost SQL](#add-sql-trigger) nebo Přidat akci [SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Připojení k SQL Serveru

Když aktivační událost SQL nebo akce vyzve k zadání informací o připojení, postupujte takto, které fungují pro aktivační události i akce. Pro scénáře, které vyžadují instalaci [místní brány dat](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) do místního počítače a vytvoření prostředku brány dat [Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection), ujistěte se, že tyto požadavky nejprve dokončíte. V opačném případě se prostředek brány při vytváření připojení nezobrazí v seznamu bran.

Chcete-li také použít ověřování systému Windows s konektorem SERVERU SQL Server v [prostředí služby integrace (ISE),](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)použijte verzi konektoru, která není součástí ise, a místní bránu dat. Verze označená službou ISE nepodporuje ověřování systému Windows.

1. V **případě názvu připojení**vytvořte název připojení.

1. V aktivační události nebo akci vyberte **Připojit přes místní bránu dat,** aby se zobrazily možnosti serveru SQL.

1. Název **serveru SQL** a název databáze **SQL**zadejte adresu serveru SQL a název databáze. V **části Uživatelské jméno** a **heslo**zadejte uživatelské jméno a heslo serveru.

   Tyto informace naleznete také v připojovacím řetězci:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Vytvoření připojení k serveru SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Pokud server SQL používá ověřování systému Windows nebo Základní, vyberte **typ ověřování**.

1. V části **Gateways**vyberte předplatné Azure, které je přidružené k dříve vytvořené místní datové bráně, a vyberte název místní brány dat.

   Pokud se brána v seznamu nezobrazuje, zkontrolujte, zda jste [bránu nastavili](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)správně .

   ![Vytvoření připojení serveru SQL Server bylo dokončeno.](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Až to budete mít, vyberte **Vytvořit**.

1. Po vytvoření připojení pokračujte [pomocí akce Přidat aktivační událost SQL](#add-sql-trigger) nebo Přidat akci [SQL](#add-sql-action).
