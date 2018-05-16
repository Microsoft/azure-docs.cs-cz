---
title: Připojení k SQL serveru nebo Azure SQL Database – Azure Logic Apps | Microsoft Docs
description: Umožňuje vytvořit připojení k systému SQL Server, místní a Azure SQL Database v cloudu z Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 05/15/2018
ms.author: estfan
ms.openlocfilehash: 4917f784c07919155e006711026899ce7712fecb
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Připojení k systému SQL Server nebo databázi Azure SQL z Azure Logic Apps

Tento článek ukazuje, jak můžete přistupovat k datům v databázi SQL z uvnitř aplikace logiky pomocí konektoru systému SQL Server. Tímto způsobem můžete vytvořit logiku aplikace, které automatizaci úloh a pracovní postupy pro správu vaše data. Konektor se dá použít pro obě [SQL Server na místní](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) a [Azure SQL Database v cloudu](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Můžete vytvořit aplikace logiky, které jsou spuštěny při aktivovaného událostmi ve vaší databázi SQL nebo v jiných systémů, jako je například Dynamics CRM Online. Aplikace logiky můžete také získat, vložit, nebo odstranit data a také spouštět dotazy SQL nebo uložené procedury. Například můžete vytvořit aplikaci logiky, která automaticky vyhledává nové záznamy v aplikaci Dynamics CRM Online, přidává položky k vaší databázi SQL pro všechny nové záznamy a pak odešle e-mailové výstrahy.

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. Pokud jste nové aplikace logiky, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Technické informace specifické pro konektor najdete v tématu <a href="https://docs.microsoft.com/connectors/sql/" target="blank">systému SQL Server konektoru odkaz</a>.

## <a name="prerequisites"></a>Požadavky

* Aplikace logiky, kde potřebujete přístup k vaší databázi SQL. Chcete-li spustit aplikaci logiky s aktivační události SQL, musíte [aplikace logiky prázdné](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* [Azure SQL database](../sql-database/sql-database-get-started-portal.md) nebo [databáze systému SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Vaše tabulky musí mít data tak, aby aplikace logiky můžete výsledky při volání operace. Pokud vytvoříte databázi SQL Azure, můžete použít ukázkové databáze, které jsou zahrnuty. 

* Název serveru SQL, název databáze, uživatelské jméno a heslo. Budete potřebovat tyto přihlašovací údaje, aby autorizujete logika pro přístup k systému SQL server. 

  * Pro databázi SQL Azure můžete vyhledat tyto podrobnosti v připojovacím řetězci, nebo na portálu Azure v části Vlastnosti SQL Database:

    "Serveru = tcp: <*název_serveru*>. database.windows.net,1433;Initial katalogu = <*yourDatabaseName*>; Zachovat bezpečnostní údaje = False; ID uživatele = <*uživatelské_jméno*>; Heslo = <*yourPassword*>; MultipleActiveResultSets = False; Šifrování = True; TrustServerCertificate = False; Časový limit připojení = 30; "

  * Tyto informace pro SQL Server, naleznete v připojovacím řetězci: 

    "Serveru = <*yourServerAddress*>; Database = <*yourDatabaseName*>; Id uživatele = <*uživatelské_jméno*>; Heslo = <*yourPassword*>; "

* Než se aplikace logiky můžete připojit k místních systémů, jako je SQL Server, musíte [nastavit bránu místní data](../logic-apps/logic-apps-gateway-install.md). Tímto způsobem bránu můžete vybrat, když vytvoříte připojení SQL pro svou aplikaci logiky.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Přidat aktivační události SQL

V Azure Logic Apps, musí začínat každou aplikaci logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se stane, aktivuje se při určité události, nebo když je splněna určitá podmínka. Pokaždé, když aktivační událost je aktivována, modul Logic Apps vytvoří instanci aplikace logiky a spuštění pracovního postupu vaší aplikace.

1. V portálu Azure nebo v sadě Visual Studio vytvořte prázdnou logiku aplikace, která otevře Návrhář aplikace logiky. Tento příklad používá portál Azure.

2. Do vyhledávacího pole zadejte "sql server" jako filtr. Ze seznamu aktivační události vyberte aktivační události SQL, který chcete. 

   V tomto příkladu vyberte této aktivační události: **SQL Server – při vytvoření nové položky**

   ![Vyberte "SQL Server – při vytváření položky" aktivační události](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Pokud se zobrazí výzva k podrobnosti připojení [vytvořit připojení SQL. nyní](#create-connection). 
   Nebo, pokud připojení již existuje, vyberte **název tabulky** , které chcete v seznamu.

   ![Vyberte tabulku](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Nastavte **Interval** a **frekvence** vlastnosti, které určují, jak často aplikace logiky zkontroluje tabulku.

   Tento příklad zkontroluje pouze ve vybrané tabulce nic jiného. 
   Chcete-li dělala něco zajímavějšího, přidejte akce, které provádějí úlohy, které chcete. 
   
   Chcete-li zobrazit novou položku v tabulce, například může přidat další akce, jako například vytvořit soubor, který má pole z tabulky a pošlete e-mailové výstrahy. 
   Další informace o dalších akcí pro tento konektor nebo jiných konektorů najdete v tématu [Logic Apps konektory](../connectors/apis-list.md).

5. Vyberte, když jste hotovi, na panelu nástrojů návrháře **Uložit**. 

   Tento krok automaticky povolí a publikuje aplikace logiky za provozu v Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Přidat akci SQL

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok ve svém pracovním postupu, který následuje aktivační události nebo jiné akce. V tomto příkladu aplikace logiky začíná [aktivační událost opakování](../connectors/connectors-native-recurrence.md)a volá akci, která se získá řádek z databáze SQL.

1. V portálu Azure nebo Visual Studio otevřete aplikaci logiky v návrháři aplikace logiky. Tento příklad používá portál Azure.

2. V návrháři aplikace logiky pod aktivační události nebo akce, zvolte **nový krok** > **přidat akci**.

   ![Zvolte "Nový krok", "Přidat akci."](./media/connectors-create-api-sqlazure/add-action.png)
   
   A přidejte akci mezi existující kroky, přesuňte ukazatel myši nad připojování šipkou. 
   Vyberte znaménko plus (**+**), se zobrazí a potom vyberte **přidat akci**.

2. Do vyhledávacího pole zadejte "sql server" jako filtr. Ze seznamu Akce vyberte žádnou akci SQL, který chcete. 

   V tomto příkladu vyberte tato akce, který získá jeden záznam: **systému SQL Server - Get řádek**

   ![Zadejte "sql server", vyberte "SQL Server - Get řádek"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Pokud se zobrazí výzva k podrobnosti připojení [vytvořit připojení SQL. nyní](#create-connection). 
   Nebo, pokud připojení existuje, vyberte **název tabulky**a zadejte **ID řádku** záznam, který chcete.

   ![Zadejte název tabulky a ID řádku](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Tento příklad vrátí pouze jeden řádek z vybrané tabulky, nic jiného. 
   Chcete-li zobrazit data v tomto řádku, můžete přidat jiné akce, které vytvořte soubor s pole z řádek pro pozdější revize a uložte tento soubor účet cloudového úložiště. Další informace o dalších akcí v tohoto konektoru nebo jiných konektorů najdete v tématu [Logic Apps konektory](../connectors/apis-list.md).

4. Vyberte, když jste hotovi, na panelu nástrojů návrháře **Uložit**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Připojení k databázi

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>Zpracování dat hromadně

Při práci s sad výsledků dotazu tak velký, že konektor nevrací všechny výsledky ve stejnou dobu, nebo chcete lepší řízení velikosti a struktuře pro sady výsledků, můžete použít *stránkování*, který vám pomůže spravovat ty výsledky jako menší sady. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Vytvoření uložené procedury

Při získávání nebo vložení více řádků, můžete pomocí těchto položek iterace aplikace logiky [ *dokud smyčky* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) v rámci těchto [omezení](../logic-apps/logic-apps-limits-and-config.md). Ale někdy má svou aplikaci logiky pro práci s sad záznamů tak velký, tisíce nebo milióny řádků, které chcete minimalizovat náklady pro volání do databáze. 

Místo toho můžete vytvořit <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank"> *uložené procedury* </a> , běží v instanci SQL a používá **vyberte - ORDER BY** příkaz k uspořádání výsledky požadovaným způsobem. Toto řešení vám dává větší kontrolu nad velikosti a struktuře výsledky. Aplikace logiky volání uložené procedury pomocí konektoru systému SQL Server **spuštění uložené procedury** akce. 

Podrobnosti řešení najdete v těchto článcích:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">Stránkování SQL pro hromadný přenos dat s Logic Apps</a>

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">Vyberte – pořadí BY – klauzule</a>

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Technické informace o aktivační události, akce a omezení tohoto konektoru najdete v tématu [konektoru odkazuje na podrobnosti](/connectors/sql/). 

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)

