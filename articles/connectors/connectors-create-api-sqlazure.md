---
title: Připojení k SQL Server nebo Azure SQL Database Azure Logic Apps
description: Automatizace úloh pro databáze SQL místně nebo v cloudu pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam; LADocs
manager: carmonm
ms.topic: conceptual
tags: connectors
ms.date: 11/08/2019
ms.openlocfilehash: a6367e5897e9bd548550b099c0bd2e6186845d6d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826330"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatizace pracovních postupů pro SQL Server nebo Azure SQL Database pomocí Azure Logic Apps

Tento článek ukazuje, jak můžete přistupovat k datům v databázi SQL z aplikace logiky pomocí konektoru SQL Server. Tímto způsobem můžete automatizovat úlohy, procesy nebo pracovní postupy, které spravují data a prostředky SQL pomocí vytváření Logic Apps. Konektor SQL Server funguje pro [místní SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) i pro [cloudové Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

Můžete vytvářet aplikace logiky, které se spouštějí při aktivaci událostmi ve vaší databázi SQL nebo v jiných systémech, jako je Dynamics CRM Online. Aplikace logiky můžou také získávat, vkládat a odstraňovat data společně se spuštěnými dotazy SQL a uloženými procedurami. Můžete například vytvořit aplikaci logiky, která automaticky kontroluje nové záznamy v Dynamics CRM Online, přidává položky do vaší databáze SQL pro všechny nové záznamy a pak posílá e-mailové výstrahy týkající se přidaných položek.

Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Informace o technických informacích, omezeních a známých problémech specifických pro konektor najdete na [stránce s referenčními informacemi pro konektor SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Databáze SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) nebo [databáze Azure SQL](../sql-database/sql-database-get-started-portal.md)

  Vaše tabulky musí mít data, aby vaše aplikace logiky mohla vracet výsledky při volání operací. Pokud vytvoříte Azure SQL Database, můžete použít ukázkové databáze, které jsou zahrnuty.

* Název serveru SQL, název databáze, uživatelské jméno a heslo. Tyto přihlašovací údaje budete potřebovat, abyste mohli autorizovat logiku pro přístup k vašemu SQL serveru.

  * V případě SQL Server můžete najít tyto podrobnosti v připojovacím řetězci:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * V případě Azure SQL Database můžete najít tyto podrobnosti v připojovacím řetězci nebo v Azure Portal ve vlastnostech SQL Database:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* Místní [Brána dat](../logic-apps/logic-apps-gateway-install.md) nainstalovaná na místním počítači a [prostředek služby Azure Data Gateway vytvořeného v Azure Portal](../logic-apps/logic-apps-gateway-connection.md) pro tyto scénáře:

  * Vaše aplikace logiky se nespouštějí v [prostředí integrační služby (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Aplikace logiky *se spouštějí v* prostředí integrační služby, ale pro připojení SQL Server musíte použít ověřování systému Windows. V tomto scénáři použijte kromě ISE verze konektoru SQL Server a zároveň s bránou data Gateway, protože verze ISE nepodporuje ověřování systému Windows.

* Aplikace logiky, kde potřebujete přístup k vaší databázi SQL. Chcete-li spustit aplikaci logiky pomocí triggeru SQL, budete potřebovat [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Přidání triggeru SQL

V Azure Logic Apps musí každá aplikace logiky začínat [triggerem](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup vaší aplikace logiky.

1. V Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře návrháře Logic Apps. V tomto příkladu se používá Azure Portal.

1. V Návrháři zadejte do vyhledávacího pole "SQL Server" jako filtr. V seznamu triggery vyberte Trigger SQL, který chcete.

   V tomto příkladu se používá aktivační událost **při vytvoření položky** .

   ![Vyberte, kdy se má vytvářená položka spustit.](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Pokud se zobrazí výzva k vytvoření připojení, [vytvořte připojení k SQL hned teď](#create-connection). Pokud připojení existuje, vyberte **název tabulky**.

   ![Vyberte tabulku, kterou chcete](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Nastavte vlastnosti **intervalu** a **četnosti** , které určují, jak často aplikace logiky kontroluje tabulku.

   Tato aktivační událost vrátí pouze jeden řádek z vybrané tabulky bez dalšího. Chcete-li provést další úkoly, přidejte další akce, které provádějí požadované úkoly. Pokud například chcete zobrazit data na tomto řádku, můžete přidat další akce, které vytvoří soubor, který obsahuje pole ze vráceného řádku, a pak pošle e-mailové výstrahy. Další informace o dalších dostupných akcích pro tento konektor najdete na [referenční stránce konektoru](https://docs.microsoft.com/connectors/sql/).

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

   Tento krok automaticky povolí a publikuje aplikaci logiky živě v Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Přidat akci SQL

V Azure Logic Apps [Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje po triggeru nebo jiné akci. V tomto příkladu aplikace logiky začíná [triggerem opakování](../connectors/connectors-native-recurrence.md)a volá akci, která získá řádek z databáze SQL.

1. V Azure Portal nebo Visual Studiu otevřete aplikaci logiky v Návrháři Logic Apps. V tomto příkladu se používá Azure Portal.

1. V rámci triggeru nebo akce, kam chcete přidat akci SQL, vyberte **Nový krok**.

   ![Přidání nového kroku do aplikace logiky](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Pokud chcete přidat akci mezi stávajícími kroky, přesuňte ukazatel myši na šipku připojení. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. V části **zvolit akci**zadejte do vyhledávacího pole "SQL Server" jako filtr. V seznamu akce vyberte akci SQL, kterou chcete.

   V tomto příkladu se používá akce **získat řádek** , která získá jeden záznam.

   ![Najít a vybrat akci SQL "získat řádek"](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Tato akce vrátí pouze jeden řádek z vybrané tabulky bez dalšího. Pokud chcete zobrazit data na tomto řádku, můžete přidat další akce, které vytvoří soubor, který obsahuje pole ze vráceného řádku, a uložit tento soubor do účtu cloudového úložiště. Další informace o dalších dostupných akcích pro tento konektor najdete na [referenční stránce konektoru](https://docs.microsoft.com/connectors/sql/).

1. Pokud se zobrazí výzva k vytvoření připojení, [vytvořte připojení k SQL hned teď](#create-connection). Pokud připojení existuje, vyberte **název tabulky**a zadejte **ID řádku** požadovaného záznamu.

   ![Zadejte název a ID řádku tabulky.](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

   Tento krok automaticky povolí a publikuje aplikaci logiky živě v Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Připojení k databázi

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Zpracování hromadných dat

V některých případech je nutné pracovat se sadami výsledků, takže konektor nevrátí všechny výsledky ve stejnou dobu nebo chcete lepší kontrolu nad velikostí a strukturou pro sady výsledků. Tady je několik způsobů, jak můžete tyto velké sady výsledků zpracovat:

* Pokud vám pomůžete spravovat výsledky jako menší sady, zapněte *stránkování*. Další informace najdete v tématu [získání hromadných dat, záznamů a položek pomocí stránkování](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Vytvořte uloženou proceduru, která uspořádá výsledky podle vašich představ.

  Při načítání nebo vkládání více řádků aplikace logiky může iterovat přes tyto řádky pomocí [*smyčky do*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) v rámci těchto [limitů](../logic-apps/logic-apps-limits-and-config.md). Nicméně, pokud aplikace logiky potřebuje pracovat se sadami záznamů, takže jsou velké, například tisíce nebo miliony řádků, které chcete minimalizovat náklady vyplývající z volání databáze.

  Chcete-li uspořádat výsledky tak, jak chcete, můžete vytvořit [*uloženou proceduru*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , která bude spuštěna v instanci SQL a používat příkaz **Select-ORDER by** . Toto řešení vám nabízí větší kontrolu nad velikostí a strukturou vašich výsledků. Vaše aplikace logiky volá uloženou proceduru pomocí akce **Spustit uloženou proceduru** SQL serverového konektoru.

  Další podrobnosti o řešeních najdete v těchto článcích:

  * [Stránkování SQL pro přenos hromadných dat pomocí Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [Klauzule SELECT-ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Technické informace o aktivačních událostech, akcích a omezeních tohoto konektoru najdete na [referenční stránce konektoru](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech pro Azure Logic Apps](../connectors/apis-list.md)
