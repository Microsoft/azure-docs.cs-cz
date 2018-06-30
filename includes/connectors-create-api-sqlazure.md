---
title: zahrnout soubor
description: zahrnout soubor
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 4ffda692da0ab7b63f7376c36dfab0bec914e334
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138061"
---
* Pokud používáte Azure SQL Database, postupujte podle kroků v části [připojit k databázi SQL Azure](#connect-azure-sql-db). 

* Pokud používáte systém SQL Server, postupujte podle kroků v části [připojit k serveru SQL](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Připojení k databázi Azure SQL

1. Když aktivační události SQL nebo akce vás vyzve k zadání informací o připojení, postupujte takto:

   1. Vytvořte název připojení.

   2. Vyberte svůj server SQL a pak vyberte svou databázi. 

      Až po výběru systému SQL server, zobrazí se seznam databází.
 
   3. Zadejte uživatelské jméno a heslo pro váš server.

      Tyto informace můžete najít buď na portálu Azure v rámci vaší vlastnosti databáze SQL nebo v připojovacím řetězci: 
      
      "ID uživatele = <*uživatelské_jméno*>"
      <br>
      "Heslo = <*yourPassword*>"

   Tento příklad ukazuje, informace o připojení pro aktivační událost, ale tento postup funguje pro akce příliš.

   ![Vytvoření připojení k databázi SQL Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Hvězdičky (*) ukazují požadované hodnoty.

   | Vlastnost | Hodnota | Podrobnosti | 
   |----------|-------|---------| 
   | Název připojení | <*Moje připojení sql.*> | Název připojení | 
   | Název serveru SQL | <*My sql server*> | Název systému SQL server |
   | Název databáze SQL | <*Moje databáze sql*>  | Název databáze SQL | 
   | Uživatelské jméno | <*Moje sql-username*> | Uživatelské jméno pro přístup k vaší databázi |
   | Heslo | <*Moje heslo sql*> | Heslo pro přístup k vaší databázi | 
   |||| 

2. Jakmile budete hotoví, vyberte **Vytvořit**.

3. Po vytvoření připojení, pokračujte [aktivační události SQL přidat](#add-sql-trigger) nebo [přidat SQL akce](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Připojení k SQL Serveru

Abyste mohli vybrat bránu, ujistěte se, že jste již [nastavit vaše brána data gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Tímto způsobem bránu se zobrazí v seznamu brány při vytváření připojení.

1. Když aktivační události SQL nebo akce vás vyzve k zadání informací o připojení, postupujte takto:

   1. V aktivační události nebo akce, vyberte **připojit prostřednictvím místní brána dat** tak, aby se zobrazují možnosti serveru SQL.

   2. Vytvořte název připojení.

   3. Zadejte adresu pro SQL server a pak zadejte název pro vaši databázi.
   
      Tyto informace můžete najít v připojovacím řetězci: 
      
      * "Serveru = <*yourServerAddress*>"
      * "Databáze = <*yourDatabaseName*>"

   4. Zadejte uživatelské jméno a heslo pro váš server.

      Tyto informace můžete najít v připojovacím řetězci: 
      
      * "ID uživatele = <*uživatelské_jméno*>"
      * "Heslo = <*yourPassword*>"

   5. Pokud SQL server používá Windows nebo základní ověřování, vyberte typ ověřování.

   6. Vyberte název pro bránu místní dat, který jste dříve vytvořili.
   
      Pokud vaše brána v seznamu nezobrazí, zkontrolujte, že jste správně [nastavili bránu](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Tento příklad ukazuje, informace o připojení pro aktivační událost, ale tento postup funguje pro akce příliš.

   ![Vytvoření připojení serveru SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Hvězdičky (*) ukazují požadované hodnoty.

   | Vlastnost | Hodnota | Podrobnosti | 
   |----------|-------|---------| 
   | Připojit prostřednictvím místní brány | Tuto možnost vyberte nejprve pro nastavení systému SQL Server. | | 
   | Název připojení | <*Moje připojení sql.*> | Název připojení | 
   | Název serveru SQL | <*My sql server*> | Název systému SQL server |
   | Název databáze SQL | <*Moje databáze sql*>  | Název databáze SQL |
   | Uživatelské jméno | <*Moje sql-username*> | Uživatelské jméno pro přístup k vaší databázi |
   | Heslo | <*Moje heslo sql*> | Heslo pro přístup k vaší databázi | 
   | Typ ověření | Windows nebo Basic | Volitelné: Typ ověřování používaný systémem SQL server | 
   | Brány | <*Moje brány dat*> | Název pro vaše místní brána data gateway | 
   |||| 

2. Jakmile budete hotoví, vyberte **Vytvořit**. 

3. Po vytvoření připojení, pokračujte [aktivační události SQL přidat](#add-sql-trigger) nebo [přidat SQL akce](#add-sql-action).
