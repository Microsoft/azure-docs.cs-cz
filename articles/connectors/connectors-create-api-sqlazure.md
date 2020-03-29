---
title: Připojení k SQL Serveru nebo databázi Azure SQL
description: Automatizace úloh pro databáze SQL místně nebo v cloudu pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789187"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatizace pracovních postupů pro SQL Server nebo Azure SQL Database pomocí Azure Logic Apps

Tento článek ukazuje, jak můžete přistupovat k datům v databázi SQL z aplikace logiky s konektorem SQL Server. Tímto způsobem můžete automatizovat úkoly, procesy nebo pracovní postupy, které spravují vaše data a prostředky SQL vytvářením aplikací logiky. Konektor SQL Serveru funguje jak [pro místní SQL Server,](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) tak pro [cloudovou Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

Můžete vytvářet aplikace logiky, které se spouštějí při spuštění událostmi v databázi SQL nebo v jiných systémech, jako je například Dynamics CRM Online. Vaše aplikace logiky můžete také získat, vložit a odstranit data spolu se spuštěnými dotazy SQL a uložené procedury. Můžete například vytvořit aplikaci logiky, která automaticky zkontroluje nové záznamy v aplikaci Dynamics CRM Online, přidá položky do databáze SQL pro všechny nové záznamy a odešle e-mailová upozornění na přidané položky.

Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a úvodní [příručka: Vytvořte si první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Technické informace, omezení a známé problémy specifické pro konektor jsou uvedeny na stránce [s odkazy na konektor serveru SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Databáze SQL Serveru](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) nebo Azure SQL [databáze](../sql-database/sql-database-get-started-portal.md)

  Vaše tabulky musí mít data, aby vaše aplikace logiky může vrátit výsledky při volání operací. Pokud vytvoříte Azure SQL Database, můžete použít ukázkové databáze, které jsou zahrnuty.

* Název serveru SQL, název databáze, uživatelské jméno a heslo. Potřebujete tato pověření, abyste mohli autorizovat svou logiku pro přístup k serveru SQL.

  * Pro SQL Server, můžete najít tyto podrobnosti v připojovacířetězec:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * V azure sql database najdete tyto podrobnosti v připojovacím řetězci nebo na webu Azure Portal pod vlastnostmi databáze SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* [Místní brána dat](../logic-apps/logic-apps-gateway-install.md) nainstalovaná v místním počítači a prostředek brány azure [vytvořeného na webu Azure Portal](../logic-apps/logic-apps-gateway-connection.md) pro tyto scénáře:

  * Aplikace logiky neběží v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Aplikace logiky *spustit* v prostředí služby integrace, ale je třeba použít ověřování systému Windows pro připojení SQL Server. V tomto scénáři použijte verzi konektoru SQL Server bez ISE spolu s bránou dat, protože verze ISE nepodporuje ověřování systému Windows.

* Aplikace logiky, kde potřebujete přístup k databázi SQL. Chcete-li spustit aplikaci logiky s aktivační událostí SQL, potřebujete [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Přidání aktivační události SQL

V Azure Logic Apps musí každá aplikace logiky začínat [aktivační událostí](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se aktivuje, když dojde k určité události nebo když je splněna určitá podmínka. Pokaždé, když se spustí aktivační událost, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup aplikace logiky.

1. Na webu Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře Návrhář aplikací logiky. Tento příklad používá portál Azure.

1. V návrháři zadejte do vyhledávacího pole jako filtr "sql server". Ze seznamu aktivačních událostí vyberte aktivační událost SQL, kterou chcete.

   Tento příklad používá **Při vytvoření položky** aktivační událost.

   ![Vyberte aktivační událost "Při vytvoření položky"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Pokud se zobrazí výzva k vytvoření připojení, [vytvořte nyní připojení SQL](#create-connection). Pokud připojení existuje, vyberte **název tabulky**.

   ![Vyberte požadovanou tabulku.](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Nastavte **interval** a **frekvence** vlastnosti, které určují, jak často aplikace logiky kontroluje tabulku.

   Tato aktivační událost vrátí pouze jeden řádek z vybrané tabulky, nic jiného. Chcete-li provádět další úkoly, přidejte další akce, které provádějí požadované úkoly. Chcete-li například zobrazit data v tomto řádku, můžete přidat další akce, které vytvoří soubor obsahující pole ze vráceného řádku, a potom odeslat e-mailová upozornění. Další informace o dalších dostupných akcích pro tento konektor naleznete na [referenční stránce konektoru](https://docs.microsoft.com/connectors/sql/).

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

   Tento krok automaticky povolí a publikuje aplikaci logiky živě v Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Přidání akce SQL

V Azure Logic Apps [je akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) krokem ve vašem pracovním postupu, který následuje po aktivační události nebo jiné akci. V tomto příkladu aplikace logiky začíná [aktivační událost opakování](../connectors/connectors-native-recurrence.md)a volá akci, která získá řádek z databáze SQL.

1. Na webu Azure Portal nebo Visual Studiu otevřete aplikaci logiky v Návrháři logických aplikací. Tento příklad používá portál Azure.

1. Pod aktivační událostí nebo akcí, do které chcete přidat akci SQL, vyberte **Nový krok**.

   ![Přidání nového kroku do aplikace logiky](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Chcete-li přidat akci mezi existující kroky, přesuňte ukazatel myši na spojovací šipku. Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. V části **Zvolte akci**zadejte do vyhledávacího pole jako filtr "sql server". Ze seznamu akcí vyberte požadovanou akci SQL.

   Tento příklad používá akci **Získat řádek,** která získá jeden záznam.

   ![Najít a vybrat akci SQL "Získat řádek"](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Tato akce vrátí pouze jeden řádek z vybrané tabulky, nic jiného. Chcete-li zobrazit data v tomto řádku, můžete přidat další akce, které vytvoří soubor, který obsahuje pole ze vráceného řádku, a uložit tento soubor do účtu cloudového úložiště. Další informace o dalších dostupných akcích pro tento konektor naleznete na [referenční stránce konektoru](https://docs.microsoft.com/connectors/sql/).

1. Pokud se zobrazí výzva k vytvoření připojení, [vytvořte nyní připojení SQL](#create-connection). Pokud připojení existuje, vyberte **název tabulky**a zadejte **ID řádku** požadovaného záznamu.

   ![Zadejte název tabulky a ID řádku.](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

   Tento krok automaticky povolí a publikuje aplikaci logiky živě v Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Připojení k databázi

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Zpracování hromadných dat

Někdy budete muset pracovat s sadami výsledků tak velké, že spojnice nevrátí všechny výsledky současně, nebo chcete mít lepší kontrolu nad velikostí a strukturou pro sady výsledků. Zde je několik způsobů, jak můžete zpracovat tak velké sady výsledků:

* Chcete-li spravovat výsledky jako menší sady, zapněte *stránkování*. Další informace naleznete v [tématu Získání hromadných dat, záznamů a položek pomocí stránkování](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Vytvořte uloženou proceduru, která uspořádá výsledky požadovaným způsobem.

  Při získávání nebo vkládání více řádků, aplikace logiky můžete iterate prostřednictvím těchto řádků pomocí [*do smyčky*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) v rámci těchto [limitů](../logic-apps/logic-apps-limits-and-config.md). Však při vaší aplikace logiky má pracovat s sadami záznamů tak velké, například tisíce nebo miliony řádků, které chcete minimalizovat náklady vyplývající z volání do databáze.

  Chcete-li uspořádat výsledky požadovaným způsobem, můžete vytvořit [*uloženou proceduru,*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) která se spustí v instanci SQL a použije příkaz **SELECT - ORDER BY.** Toto řešení poskytuje větší kontrolu nad velikostí a strukturou výsledků. Vaše aplikace logiky volá uloženou proceduru pomocí akce **uložené procedury** konektoru SQL Server.

  Další podrobnosti o řešení naleznete v těchto článcích:

  * [Stránkování SQL pro hromadný přenos dat pomocí logic ových aplikací](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - OBJEDNAT Podle doložky](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Podrobnosti specifické pro konektor

Technické informace o aktivačních událostech, akcích a omezeních tohoto konektoru naleznete na [referenční stránce konektoru](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Další kroky

* Další informace o [dalších konektorech pro Azure Logic Apps](../connectors/apis-list.md)
