---
title: Připojení k SQL Server nebo Azure SQL Database-Azure Logic Apps | Microsoft Docs
description: Jak získat přístup k databázím SQL a spravovat je místně nebo v cloudu díky automatizaci pracovních postupů pomocí Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 804a913d17c3151d07a1ecf229e2db148dc45558
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050769"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Připojení k SQL Server nebo Azure SQL Database z Azure Logic Apps

Tento článek ukazuje, jak můžete přistupovat k datům v databázi SQL z aplikace logiky pomocí konektoru SQL Server. Tímto způsobem můžete automatizovat úlohy, procesy a pracovní postupy, které spravují data a prostředky SQL pomocí vytváření Logic Apps. Konektor funguje jak pro [SQL Server místně](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) , tak pro [Azure SQL Database v cloudu](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Můžete vytvářet aplikace logiky, které se spouštějí při aktivaci událostmi ve vaší databázi SQL nebo v jiných systémech, jako je Dynamics CRM Online. Aplikace logiky mohou také získávat, vkládat a odstraňovat data společně s prováděním dotazů SQL a uložených procedur. Můžete například vytvořit aplikaci logiky, která automaticky kontroluje nové záznamy v Dynamics CRM Online, přidává položky do vaší databáze SQL pro všechny nové záznamy a pak posílá e-mailové výstrahy.

Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvořte svou první aplikaci](../logic-apps/quickstart-create-first-logic-app-workflow.md)logiky. Technické informace specifické pro konektor najdete v referenčních informacích ke [konektoru SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Požadavky

* Aplikace logiky, kde potřebujete přístup k vaší databázi SQL. Chcete-li spustit aplikaci logiky pomocí triggeru SQL, budete potřebovat [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Databáze [SQL Azure](../sql-database/sql-database-get-started-portal.md) nebo [databáze SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Vaše tabulky musí mít data, aby vaše aplikace logiky mohla vracet výsledky při volání operací. Pokud vytvoříte Azure SQL Database, můžete použít ukázkové databáze, které jsou zahrnuty. 

* Název serveru SQL, název databáze, uživatelské jméno a heslo. Tyto přihlašovací údaje budete potřebovat, abyste mohli autorizovat logiku pro přístup k vašemu SQL serveru. 

  * V případě Azure SQL Database můžete najít tyto podrobnosti v připojovacím řetězci nebo v Azure Portal ve vlastnostech SQL Database:

    "Server = TCP: <*yourServerName*>. Database. Windows. NET, 1433; Initial Catalog = <*yourDatabaseName*>; Zachovat informace o zabezpečení = NEPRAVDA; ID uživatele = <*uživatelské_jméno*>; Password = <*yourPassword*>; MultipleActiveResultSets = false; Encrypt = true; TrustServerCertificate = false; Časový limit připojení = 30; "

  * V případě SQL Server můžete najít tyto podrobnosti v připojovacím řetězci: 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

* Než budete moci připojit Logic Apps k místním systémům, jako je například SQL Server, musíte [nastavit místní bránu dat](../logic-apps/logic-apps-gateway-install.md). Tímto způsobem můžete bránu vybrat při vytváření připojení SQL pro vaši aplikaci logiky.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Přidat Trigger SQL

V Azure Logic Apps musí každá aplikace logiky začínat [triggerem](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a začne spouštět pracovní postup vaší aplikace.

1. V Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře návrháře Logic Apps. V tomto příkladu se používá Azure Portal.

2. Do vyhledávacího pole zadejte jako filtr "SQL Server". V seznamu triggery vyberte Trigger SQL, který chcete. 

   V tomto příkladu vyberte tuto aktivační událost: **SQL Server – při vytvoření položky**

   ![Vyberte "SQL Server-když se vytvoří položka" Trigger](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte připojení k SQL hned teď](#create-connection). 
   Nebo, pokud už připojení existuje, vyberte ze seznamu požadovaný **název tabulky** .

   ![Vyberte tabulku](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Nastavte vlastnosti **intervalu** a **četnosti** , které určují, jak často aplikace logiky kontroluje tabulku.

   V tomto příkladu se kontroluje pouze vybraná tabulka bez dalšího. 
   Chcete-li něco zajímavější, přidejte akce, které provádějí požadované úkoly. 
   
   Pokud například chcete zobrazit novou položku v tabulce, můžete přidat další akce, například vytvořit soubor, který obsahuje pole z tabulky, a pak odeslat e-mailové výstrahy. 
   Další informace o dalších akcích pro tento konektor nebo jiné konektory najdete v tématu [Logic Apps konektory](../connectors/apis-list.md).

5. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**. 

   Tento krok automaticky povolí a publikuje aplikaci logiky živě v Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Přidat akci SQL

V Azure Logic Apps [Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje po triggeru nebo jiné akci. V tomto příkladu aplikace logiky začíná [triggerem opakování](../connectors/connectors-native-recurrence.md)a volá akci, která získá řádek z databáze SQL.

1. V Azure Portal nebo Visual Studiu otevřete aplikaci logiky v Návrháři Logic Apps. V tomto příkladu se používá Azure Portal.

2. V návrháři aplikace logiky pod triggerem nebo akcí vyberte **Nový krok** > **přidat akci**.

   ![Vyberte nový krok, přidat akci.](./media/connectors-create-api-sqlazure/add-action.png)
   
   Pokud chcete přidat akci mezi stávajícími kroky, přesuňte ukazatel myši na šipku připojení. 
   Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

2. Do vyhledávacího pole zadejte jako filtr "SQL Server". V seznamu akce vyberte libovolnou akci SQL, kterou chcete. 

   V tomto příkladu vyberte tuto akci, která získá jeden záznam: **SQL Server – získat řádek**

   ![Zadejte SQL Server, vyberte SQL Server-získat řádek.](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte připojení k SQL hned teď](#create-connection). 
   Nebo, pokud připojení existuje, vyberte **název tabulky**a zadejte **ID řádku** požadovaného záznamu.

   ![Zadejte název a ID řádku tabulky.](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Tento příklad vrátí pouze jeden řádek z vybrané tabulky bez dalšího. 
   Pokud chcete zobrazit data na tomto řádku, můžete přidat další akce, které vytvoří soubor s poli z řádku pro pozdější kontrolu, a uložte tento soubor do účtu cloudového úložiště. Další informace o dalších akcích v tomto konektoru nebo jiných konektorech najdete v tématu [Logic Apps konektory](../connectors/apis-list.md).

4. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Připojení k databázi

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Zpracování hromadných dat

V některých případech může být nutné pracovat se sadami výsledků, takže konektor nevrátí všechny výsledky ve stejnou dobu nebo chcete lepší kontrolu nad velikostí a strukturou pro sady výsledků. Tady je několik způsobů, jak můžete tyto velké sady výsledků zpracovat:

* Pokud vám pomůžete spravovat výsledky jako menší sady, zapněte *stránkování*. Další informace najdete v tématu [získání hromadných dat, záznamů a položek pomocí stránkování](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Vytvořte uloženou proceduru, která uspořádá výsledky podle vašich představ.

  Při načítání nebo vkládání více řádků aplikace logiky může iterovat přes tyto řádky pomocí [*smyčky do*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) v rámci těchto limitů [](../logic-apps/logic-apps-limits-and-config.md). 
  Nicméně, pokud aplikace logiky potřebuje pracovat se sadami záznamů, takže jsou velké, například tisíce nebo miliony řádků, které chcete minimalizovat náklady vyplývající z volání databáze.

  Chcete-li uspořádat výsledky tak, jak chcete, můžete vytvořit [*uloženou proceduru*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , která bude spuštěna v instanci SQL a používat příkaz **Select-ORDER by** . 
  Toto řešení vám nabízí větší kontrolu nad velikostí a strukturou vašich výsledků. 
  Vaše aplikace logiky volá uloženou proceduru pomocí akce **Spustit uloženou proceduru** SQL serverového konektoru.

  Podrobnosti o řešeních najdete v těchto článcích:

  * [Stránkování SQL pro přenos hromadných dat pomocí Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [Klauzule SELECT-ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Technické informace o aktivačních událostech, akcích a omezeních tohoto konektoru [](/connectors/sql/)najdete v referenčních podrobnostech konektoru. 

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

