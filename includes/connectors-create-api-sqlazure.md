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
ms.openlocfilehash: da03c5247b8ebe0a3305b08a05d661264497663f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "67122220"
---
* Pokud používáte Azure SQL Database, postupujte podle kroků v části [připojit ke službě Azure SQL Database](#connect-azure-sql-db). 

* Pokud používáte systém SQL Server, postupujte podle kroků v části [připojit k SQL serveru](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Připojení ke službě Azure SQL Database

1. Když SQL triggeru nebo akce vás vyzve k zadání informací o připojení, postupujte podle těchto kroků:

   1. Vytvořte název vašeho připojení.

   2. Vyberte váš server SQL a pak vyberte vaši databázi. 

      Seznam databází se zobrazí pouze po vyberte váš server SQL.
 
   3. Zadejte uživatelské jméno a heslo pro váš server.

      Tyto informace můžete najít buď na portálu Azure portal ve vlastnostech vaší SQL database nebo v připojovacím řetězci: 
      
      "ID uživatele = <*uživatelské_jméno*>"
      <br>
      "Password=<*yourPassword*>"

   Tento příklad ukazuje informace o připojení pro aktivační událost, ale tento postup funguje pro akce příliš.

   ![Vytvoření připojení k databázi SQL Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Hvězdičky (*) ukazují požadované hodnoty.

   | Vlastnost | Hodnota | Podrobnosti | 
   |----------|-------|---------| 
   | Název připojení | <*my-sql-connection*> | Název připojení | 
   | Název SQL Serveru | <*my-sql-server*> | Název serveru SQL server |
   | Název databáze SQL | <*my-sql-database*>  | Název pro vaši databázi SQL | 
   | Uživatelské jméno | <*my-sql-username*> | Uživatelské jméno pro přístup k databázi |
   | Heslo | <*my-sql-password*> | Heslo pro přístup k databázi | 
   |||| 

2. Jakmile budete hotoví, vyberte **Vytvořit**.

3. Po vytvoření připojení pokračujte [aktivační události SQL přidat](#add-sql-trigger) nebo [akci přidat SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Připojení k SQL Serveru

Abyste mohli vybrat bránu, ujistěte se, že jste již [nastavit vaši bránu data gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Tímto způsobem, vaše brána zobrazí v seznamu brány při vytváření připojení.

1. Když SQL triggeru nebo akce vás vyzve k zadání informací o připojení, postupujte podle těchto kroků:

   1. V triggeru nebo akce, vyberte **připojit přes místní bránu dat** tak, aby se zobrazí možnosti SQL serveru.

   2. Vytvořte název vašeho připojení.

   3. Zadejte adresu pro SQL server a pak zadejte název pro vaši databázi.
   
      Tyto informace můžete najít v připojovacím řetězci: 
      
      * "Server=<*yourServerAddress*>"
      * "Database=<*yourDatabaseName*>"

   4. Zadejte uživatelské jméno a heslo pro váš server.

      Tyto informace můžete najít v připojovacím řetězci: 
      
      * "ID uživatele = <*uživatelské_jméno*>"
      * "Password=<*yourPassword*>"

   5. Pokud váš SQL server používá Windows nebo základní ověřování, vyberte typ ověřování.

   6. Vyberte název místní brány dat, který jste dříve vytvořili.
   
      Pokud vaše brána není zobrazená v seznamu, zkontrolujte, které jste správně [vaši bránu nastaví](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Tento příklad ukazuje informace o připojení pro aktivační událost, ale tento postup funguje pro akce příliš.

   ![Vytvoření připojení k SQL serveru](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Hvězdičky (*) ukazují požadované hodnoty.

   | Vlastnost | Hodnota | Podrobnosti | 
   |----------|-------|---------| 
   | Připojit přes místní bránu | Tuto možnost vyberte, nejdřív pro nastavení systému SQL Server. | | 
   | Název připojení | <*my-sql-connection*> | Název připojení | 
   | Název SQL Serveru | <*my-sql-server*> | Název serveru SQL server |
   | Název databáze SQL | <*my-sql-database*>  | Název pro vaši databázi SQL |
   | Uživatelské jméno | <*my-sql-username*> | Uživatelské jméno pro přístup k databázi |
   | Heslo | <*my-sql-password*> | Heslo pro přístup k databázi | 
   | Typ ověřování | Windows nebo Basic | Volitelné: Typ ověřování používaný systémem SQL server | 
   | Brány | <*my-data-gateway*> | Název pro místní bránu dat | 
   |||| 

2. Jakmile budete hotoví, vyberte **Vytvořit**. 

3. Po vytvoření připojení pokračujte [aktivační události SQL přidat](#add-sql-trigger) nebo [akci přidat SQL](#add-sql-action).
