---
title: Připojení k serveru SQL Server nebo Azure SQL Database – Azure Logic Apps | Dokumentace Microsoftu
description: Jak zobrazit a spravovat databáze SQL v místním prostředí nebo v cloudu díky automatizaci pracovních postupů pomocí Azure Logic Apps
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
ms.openlocfilehash: dccb91c782408a5fed5c3ef1b68f9918823ce402
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38544019"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Připojení k serveru SQL Server nebo databázi Azure SQL z Azure Logic Apps

Tento článek popisuje, jak můžete přistupovat k datům ve službě SQL database z uvnitř aplikace logiky pomocí konektoru systému SQL Server. Tímto způsobem můžete automatizovat úlohy, procesy a pracovní postupy, které spravují vaše SQL data a prostředky tím, že vytvoříte aplikace logiky. Konektor se dá použít pro obě [systému SQL Server v místním prostředí](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) a [Azure SQL Database v cloudu](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Můžete vytvářet aplikace logiky aktivovaného událostmi ve službě SQL database nebo v jiných systémech, jako je například Dynamics CRM Online. Logic apps můžete také získat, vložení a odstranění dat spolu s spouštění dotazů SQL a uložených procedur. Můžete například vytvořit aplikaci logiky, který automaticky zjišťuje nové záznamy v Dynamics CRM Online, přidá položky do databáze SQL pro nové záznamy a poté odešle e-mailové výstrahy.

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Technické informace specifické pro konektor najdete v tématu <a href="https://docs.microsoft.com/connectors/sql/" target="blank">referenční informace ke konektorům systému SQL Server</a>.

## <a name="prerequisites"></a>Požadavky

* Aplikace logiky, kde potřebujete přístup ke službě SQL database. Pokud chcete spustit aplikaci logiky s triggerem SQL, musíte [prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* [Azure SQL database](../sql-database/sql-database-get-started-portal.md) nebo [databáze systému SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Tabulky musí mít data, aby vaše aplikace logiky může vrátit výsledky při volání operace. Pokud jste vytvořili službu Azure SQL Database, můžete použít ukázkové databáze, které jsou zahrnuty. 

* Název vašeho SQL serveru, název databáze, uživatelské jméno a heslo. Budete potřebovat tyto přihlašovací údaje, můžete ověřovat svoji logiku pro přístup k serveru SQL server. 

  * Pro službu Azure SQL Database najdete tyto podrobnosti v připojovacím řetězci, nebo na webu Azure Portal ve vlastnostech SQL Database:

    "Server = tcp: <*název_serveru*>. database.windows.net,1433;Initial katalogu = <*yourDatabaseName*>; Trvalé informace o zabezpečení = False;. ID uživatele = <*uživatelské_jméno*>; Heslo = <*yourPassword*>; Atribut MultipleActiveResultSets = False;. Šifrování = True; TrustServerCertificate = False;. Časový limit připojení = 30; "

  * Tyto podrobnosti můžete najít v připojovacím řetězci pro SQL Server: 

    "Server = <*yourServerAddress*>; Database = <*yourDatabaseName*>; Id uživatele = <*uživatelské_jméno*>; Heslo = <*yourPassword*>; "

* Aplikace logiky se můžete připojit k místním systémům, jako je SQL Server, je nutné nejprve [nastavení místní brány dat](../logic-apps/logic-apps-gateway-install.md). Tímto způsobem brány můžete vybrat, když vytvoříte připojení SQL pro vaši aplikaci logiky.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Přidat aktivační události SQL

V Azure Logic Apps, musí začínat každá aplikace logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje při určité události nebo když je splněna konkrétní podmínka. Pokaždé, když se trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup vaší aplikace.

1. Webu Azure portal nebo Visual Studio vytvořte prázdné aplikace logiky, otevře se návrhář pro Logic Apps. Tento příklad používá na webu Azure portal.

2. Do vyhledávacího pole zadejte jako filtr "sql server". Ze seznamu triggerů vyberte trigger SQL, který chcete. 

   V tomto příkladu vyberte tento trigger: **SQL Server – když je vytvořena položka**

   ![Vyberte "SQL Server – když je vytvořena položka" aktivační události](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Pokud se zobrazí výzva k připojení podrobnosti [vytvořit připojení SQL](#create-connection). 
   Nebo, pokud už připojení existuje, vyberte **název tabulky** , který chcete v seznamu.

   ![Vyberte tabulku](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Nastavte **Interval** a **frekvence** vlastnosti, které určují, jak často aplikace logiky zkontroluje tabulku.

   Tento příklad kontroluje pouze vybrané tabulky, nic dalšího. 
   Chcete-li dělala něco zajímavějšího, přidejte akce, které provádějí úlohy, které chcete. 
   
   Chcete-li zobrazit novou položku v tabulce, například může přidat další akce, jako například vytvořit soubor, který obsahuje pole z tabulky a pak pošle e-mailová upozornění. 
   Další informace o dalších akcích pro tento konektor nebo jiné konektory najdete v tématu [konektory Logic Apps](../connectors/apis-list.md).

5. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**. 

   Tento krok automaticky povolí a publikuje vaši aplikaci logiky živě v Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Přidání akce SQL

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje aktivační události nebo jiné akce. V tomto příkladu aplikace logiky začíná [trigger opakování](../connectors/connectors-native-recurrence.md)a volá akci, která načte řádek z SQL database.

1. Webu Azure portal nebo Visual Studio otevřete aplikaci logiky v návrháři pro Logic Apps. Tento příklad používá na webu Azure portal.

2. V návrháři aplikace logiky podle triggeru nebo akce, zvolte **nový krok** > **přidat akci**.

   ![Zvolte možnost "Nový krok", "Přidat akci"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Přidání akce mezi stávající kroky, najeďte myší na připojení šipku. 
   Vyberte znaménko plus (**+**), který se zobrazí a klikněte na tlačítko **přidat akci**.

2. Do vyhledávacího pole zadejte jako filtr "sql server". Ze seznamu akcí vyberte dělat SQL, který chcete. 

   V tomto příkladu vyberte tuto akci, která načte jeden záznam: **SQL Server – získat řádek**

   ![Zadejte "sql server", vyberte "SQL Server – získat řádek"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Pokud se zobrazí výzva k připojení podrobnosti [vytvořit připojení SQL](#create-connection). 
   Nebo, pokud připojení existuje, vyberte **název tabulky**a zadejte **ID řádku** pro záznam, který chcete.

   ![Zadejte název tabulky a ID řádku](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   V tomto příkladu vrátí pouze jeden řádek z vybrané tabulky, nic dalšího. 
   Chcete-li zobrazit data v tomto řádku, může přidat další akce, které vytvořte soubor s poli z řádku pro pozdější kontrolu a uložte tento soubor v účtu cloudového úložiště. Další informace o dalších akcích v tento konektor nebo jiné konektory najdete v tématu [konektory Logic Apps](../connectors/apis-list.md).

4. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Připojení k databázi

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>Zpracování dat, hromadné

Při práci s velmi velké, že konektor nemá všechny výsledky jsou vráceny ve stejnou dobu, nebo chcete lepší kontrolu nad velikosti a struktuře vaší sady výsledků sad výsledků dotazu, můžete použít *stránkování*, což umožňuje je spravovat výsledky jako menší sady. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Vytvoření uložené procedury

Při načítání nebo vložení více řádků, aplikace logiky můžete iterovat těchto položek pomocí [ *smyčka until* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) v rámci těchto [omezení](../logic-apps/logic-apps-limits-and-config.md). Ale někdy aplikace logiky má pro práci s velmi velké, například tisíce nebo miliony řádků, které chcete minimalizovat náklady na volání do databáze sady záznamů. 

Místo toho můžete vytvořit <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank"> *uloženou proceduru* </a> , která se spouští ve vaší instanci SQL a používá **vyberte - ORDER BY** příkaz k uspořádání výsledky, jak potřebujete. Toto řešení nabízí větší kontrolu nad velikosti a struktuře vaší výsledků. Aplikace logiky pomocí konektoru systému SQL Server volá uloženou proceduru **spustit uloženou proceduru** akce. 

Podrobnosti o řešení najdete v těchto článcích:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">Stránkování SQL pro přenos hromadných dat pomocí funkce Logic Apps</a>

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">Vyberte - ORDER BY – klauzule</a>

## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Technické informace o aktivačních událostech tento konektor, akce a omezení, najdete v článku [konektoru odkazuje na podrobnosti](/connectors/sql/). 

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)

