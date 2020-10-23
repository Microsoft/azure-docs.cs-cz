---
title: Připojení k SQL Server, Azure SQL Database nebo spravované instanci Azure SQL
description: Automatizace úloh pro databáze SQL místně nebo v cloudu pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/22/2020
tags: connectors
ms.openlocfilehash: 674d496485f89bee1904e3588a0fb81c6140945b
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426614"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Automatizace pracovních postupů pro databázi SQL pomocí Azure Logic Apps

Tento článek ukazuje, jak můžete přistupovat k datům v databázi SQL z aplikace logiky pomocí konektoru SQL Server. Tímto způsobem můžete automatizovat úlohy, procesy nebo pracovní postupy, které spravují data a prostředky SQL pomocí vytváření Logic Apps. Konektor SQL Server funguje pro [SQL Server](/sql/sql-server/sql-server-technical-documentation) a také pro [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) a [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

Můžete vytvářet aplikace logiky, které se spouštějí při aktivaci událostmi ve vaší databázi SQL nebo v jiných systémech, jako je Dynamics CRM Online. Aplikace logiky můžou také získávat, vkládat a odstraňovat data společně se spuštěnými dotazy SQL a uloženými procedurami. Můžete například vytvořit aplikaci logiky, která automaticky kontroluje nové záznamy v Dynamics CRM Online, přidává položky do vaší databáze SQL pro všechny nové záznamy a pak posílá e-mailové výstrahy týkající se přidaných položek.

Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Informace o technických informacích, omezeních a známých problémech specifických pro konektor najdete na [stránce s referenčními informacemi pro konektor SQL Server](/connectors/sql/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [SQL Server databáze](/sql/relational-databases/databases/create-a-database), [Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)nebo [spravovaná instance Azure SQL](../azure-sql/managed-instance/instance-create-quickstart.md).

  Vaše tabulky musí mít data, aby vaše aplikace logiky mohla vracet výsledky při volání operací. Pokud používáte Azure SQL Database, můžete použít ukázkové databáze, které jsou zahrnuty.

* Název serveru SQL, název databáze, uživatelské jméno a heslo. Tyto přihlašovací údaje budete potřebovat, abyste mohli autorizovat logiku pro přístup k vašemu SQL serveru.

  * V případě místních SQL Server můžete najít tyto podrobnosti v připojovacím řetězci:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * V případě Azure SQL Database můžete najít tyto podrobnosti v připojovacím řetězci.
  
    Chcete-li například najít tento řetězec v Azure Portal, otevřete databázi. V nabídce databáze vyberte buď **připojovací řetězce** , nebo **vlastnosti**:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* V závislosti na tom, jestli se vaše aplikace logiky spouštějí v globálním prostředí Azure nebo [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), najdete tady další požadavky pro připojení k místním SQL Server:

  * Pro Logic Apps v globálním cloudu Azure, který se připojuje k místním SQL Server, musíte mít nainstalovanou místní [bránu dat](../logic-apps/logic-apps-gateway-install.md) v místním počítači a [prostředku brány dat, který už je vytvořený v Azure](../logic-apps/logic-apps-gateway-connection.md).

  * Pro Logic Apps v ISE, které se připojují k místním SQL Server a používají ověřování systému Windows, ISE konektor SQL Server se správou verzí nepodporuje ověřování systému Windows. Takže stále potřebujete používat bránu data Gateway a konektor non-ISE SQL Server. Pro jiné typy ověřování nemusíte používat bránu data Gateway a můžete použít konektor ISE-Version.

* Aplikace logiky, kde potřebujete přístup k vaší databázi SQL. Chcete-li spustit aplikaci logiky pomocí triggeru SQL, budete potřebovat [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Připojení k databázi

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Nyní pokračujte následujícími kroky:

* [Připojení k cloudovým Azure SQL Database nebo spravované instanci](#connect-azure-sql-db)
* [Připojení k místním SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Připojení k Azure SQL Database nebo spravované instanci

Pokud chcete získat přístup ke spravované instanci SQL Azure bez použití místní brány dat nebo prostředí integrační služby, musíte [nastavit veřejný koncový bod na spravované instanci SQL Azure](../azure-sql/managed-instance/public-endpoint-configure.md). Veřejný koncový bod používá port 3342, proto nezapomeňte při vytváření připojení z aplikace logiky zadat toto číslo portu.


Když poprvé přidáte [Trigger SQL](#add-sql-trigger) nebo [akci SQL](#add-sql-action)a ještě jste nevytvořili připojení k vaší databázi, budete vyzváni k provedení těchto kroků:

1. Jako **typ ověřování**vyberte požadované a povolené ověřování ve vaší databázi v Azure SQL Database nebo spravované instanci SQL Azure:

   | Ověřování | Popis |
   |----------------|-------------|
   | [**Integrovaná služba Azure AD**](../azure-sql/database/authentication-aad-overview.md) | -Podporuje SQL Server konektor non-ISE a ISE. <p><p>– Vyžaduje platnou identitu v Azure Active Directory (Azure AD), která má přístup k vaší databázi. <p>Další informace najdete v těchto tématech: <p>- [Přehled zabezpečení Azure SQL – ověřování](../azure-sql/database/security-overview.md#authentication) <br>- [Autorizace přístupu k databázi do Azure SQL – ověřování a autorizace](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL – integrované ověřování Azure AD](../azure-sql/database/authentication-aad-overview.md) |
   | [**Ověřování SQL Serveru**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -Podporuje SQL Server konektor non-ISE a ISE. <p><p>– Vyžaduje platné uživatelské jméno a silné heslo, které se vytvoří a uloží do vaší databáze. <p>Další informace najdete v těchto tématech: <p>- [Přehled zabezpečení Azure SQL – ověřování](../azure-sql/database/security-overview.md#authentication) <br>- [Autorizace přístupu k databázi do Azure SQL – ověřování a autorizace](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Tento příklad pokračuje s **integrací služby Azure AD**:

   ![Snímek obrazovky, který zobrazuje okno připojení "SQL Server" s otevřeným seznamem "typ ověřování" a "integrovaná služba Azure AD".](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Po výběru **integrovaného Azure AD**vyberte **Přihlásit**se. Na základě toho, jestli používáte Azure SQL Database nebo Azure SQL Managed instance, vyberte přihlašovací údaje uživatele pro ověřování.

1. Vyberte tyto hodnoty pro vaši databázi:

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Název serveru** | Yes | Adresa pro váš SQL Server, například `Fabrikam-Azure-SQL.database.windows.net` |
   | **Název databáze** | Ano | Název vaší databáze SQL, například `Fabrikam-Azure-SQL-DB` |
   | **Název tabulky** | Ano | Tabulka, kterou chcete použít, například `SalesLT.Customer` |
   ||||

   > [!TIP]
   > Tyto informace najdete v připojovacím řetězci vaší databáze. Například v Azure Portal vyhledejte a otevřete databázi. V nabídce databáze vyberte buď **připojovací řetězce** , nebo **vlastnosti** , kde můžete najít tento řetězec:
   >
   > `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

   Tento příklad ukazuje, jak tyto hodnoty mohou vypadat:

   ![Vytvoření připojení k databázi SQL](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Nyní pokračujte v krocích, které jste ještě nedokončili, buď [přidáním triggeru SQL](#add-sql-trigger) , nebo [přidáním akce SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Připojení k místním SQL Server

Když poprvé přidáte [Trigger SQL](#add-sql-trigger) nebo [akci SQL](#add-sql-action)a ještě jste nevytvořili připojení k vaší databázi, budete vyzváni k provedení těchto kroků:

1. Pro připojení k místnímu SQL serveru, které vyžadují místní bránu dat, se ujistěte, že jste [tyto požadavky dokončili](#multi-tenant-or-ise).

   V opačném případě se prostředek brány dat při vytváření připojení nezobrazí v seznamu **brány připojení** .

1. Jako **typ ověřování**vyberte požadované a povolené ověřování v SQL Server:

   | Ověřování | Popis |
   |----------------|-------------|
   | [**Ověřování systému Windows**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | – Podporuje jenom konektor non-ISE SQL Server, který vyžaduje prostředek brány dat, který se dřív vytvořil v Azure pro vaše připojení, bez ohledu na to, jestli používáte víceklientské prostředí Azure nebo ISE. <p><p>– Vyžaduje platné uživatelské jméno a heslo systému Windows k potvrzení vaší identity prostřednictvím účtu systému Windows. <p>Další informace najdete v tématu [ověřování systému Windows](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) . |
   | [**Ověřování SQL Serveru**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -Podporuje SQL Server konektor non-ISE a ISE. <p><p>– Vyžaduje platné uživatelské jméno a silné heslo, které se vytvoří a uloží do SQL Server. <p>Další informace najdete v tématu [ověřování SQL Server](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication). |
   |||

   Tento příklad pokračuje s **ověřováním systému Windows**:

   ![Vyberte typ ověřování, který se má použít.](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Vyberte nebo zadejte pro svou databázi SQL následující hodnoty:

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Název systému SQL Server** | Ano | Adresa pro váš SQL Server, například `Fabrikam-Azure-SQL.database.windows.net` |
   | **Název databáze SQL** | Ano | Název databáze SQL Server, například `Fabrikam-Azure-SQL-DB` |
   | **Uživatelské jméno** | Ano | Vaše uživatelské jméno pro SQL Server a databázi |
   | **Heslo** | Ano | Heslo pro SQL Server a databázi |
   | **Předplatné** |  Ano, pro ověřování systému Windows | Předplatné Azure pro prostředek brány dat, který jste dříve vytvořili v Azure |
   | **Brána připojení** | Ano, pro ověřování systému Windows | Název prostředku brány dat, který jste dříve vytvořili v Azure <p><p>**Tip**: Pokud se brána v seznamu nezobrazí, ověřte, že jste správně [nastavili bránu](../logic-apps/logic-apps-gateway-connection.md). |
   |||

   > [!TIP]
   > Tyto informace najdete v připojovacím řetězci vaší databáze:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Tento příklad ukazuje, jak tyto hodnoty mohou vypadat:

   ![Vytvoření připojení SQL Server dokončeno](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Až budete připraveni, vyberte **vytvořit**.

1. Nyní pokračujte v krocích, které jste ještě nedokončili, buď [přidáním triggeru SQL](#add-sql-trigger) , nebo [přidáním akce SQL](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Přidání triggeru SQL

1. V [Azure Portal](https://portal.azure.com) nebo v aplikaci Visual Studio vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky. Tento příklad pokračuje s Azure Portal.

1. V návrháři do vyhledávacího pole zadejte `sql server` . V seznamu triggery vyberte Trigger SQL, který chcete. V tomto příkladu se používá aktivační událost **při vytvoření položky** .

   ![Vyberte, kdy se má vytvářená položka spustit.](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Pokud se k databázi SQL připojujete poprvé, budete vyzváni k [vytvoření připojení k databázi SQL](#create-connection). Po vytvoření tohoto připojení můžete pokračovat dalším krokem.

1. V aktivační události zadejte interval a frekvenci, jak často Trigger kontroluje tabulku.

1. Chcete-li přidat další dostupné vlastnosti pro tuto aktivační událost, otevřete seznam **Přidat nový parametr** .

   Tato aktivační událost vrátí pouze jeden řádek z vybrané tabulky a nic jiného. Chcete-li provést další úkoly, pokračujte přidáním [Akce konektoru SQL](#add-sql-action) nebo [jiné akce](../connectors/apis-list.md) , která v pracovním postupu aplikace logiky požaduje další požadovaný úkol.
   
   Pokud například chcete zobrazit data na tomto řádku, můžete přidat další akce, které vytvoří soubor, který obsahuje pole ze vráceného řádku, a pak pošle e-mailové výstrahy. Další informace o dalších dostupných akcích pro tento konektor najdete na [referenční stránce konektoru](/connectors/sql/).

1. Na panelu nástrojů návrháře vyberte **Uložit**.

   I když tento krok automaticky povolí a publikuje aplikaci logiky v Azure v reálném čase, jedinou akcí, kterou vaše aplikace logiky aktuálně potřebuje, je ověřit vaši databázi na základě zadaného intervalu a frekvence.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Přidat akci SQL

V tomto příkladu aplikace logiky začíná [triggerem opakování](../connectors/connectors-native-recurrence.md)a volá akci, která získá řádek z databáze SQL.

1. V [Azure Portal](https://portal.azure.com) nebo v aplikaci Visual Studio otevřete aplikaci logiky v návrháři aplikace logiky. Tento příklad pokračuje Azure Portal.

1. V rámci triggeru nebo akce, kam chcete přidat akci SQL, vyberte **Nový krok**.

   ![Přidání akce do aplikace logiky](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Nebo pokud chcete přidat akci mezi stávajícími kroky, přesuňte ukazatel myši na šipku připojení. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. V části **zvolit akci**zadejte do vyhledávacího pole `sql server` . V seznamu akce vyberte akci SQL, kterou chcete. V tomto příkladu se používá akce **získat řádek** , která získá jeden záznam.

   ![Vybrat akci SQL "získat řádek"](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Pokud se k databázi SQL připojujete poprvé, budete vyzváni k [vytvoření připojení k databázi SQL](#create-connection). Po vytvoření tohoto připojení můžete pokračovat dalším krokem.

1. Vyberte **název tabulky**, který je `SalesLT.Customer` v tomto příkladu. Zadejte **ID řádku** záznamu, který chcete.

   ![Vyberte název tabulky a zadejte ID řádku.](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Tato akce vrátí pouze jeden řádek z vybrané tabulky bez dalšího. Pokud tedy chcete zobrazit data na tomto řádku, můžete přidat další akce, které vytvoří soubor, který obsahuje pole ze vráceného řádku, a uložit tento soubor do účtu cloudového úložiště. Další informace o dalších dostupných akcích pro tento konektor najdete na [referenční stránce konektoru](/connectors/sql/).

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

   Tento krok automaticky povolí a publikuje aplikaci logiky živě v Azure.

## <a name="handle-bulk-data"></a>Zpracování hromadných dat

V některých případech je nutné pracovat se sadami výsledků, takže konektor nevrátí všechny výsledky ve stejnou dobu nebo chcete lepší kontrolu nad velikostí a strukturou pro sady výsledků. Tady je několik způsobů, jak můžete tyto velké sady výsledků zpracovat:

* Pokud vám pomůžete spravovat výsledky jako menší sady, zapněte *stránkování*. Další informace najdete v tématu [získání hromadných dat, záznamů a položek pomocí stránkování](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md). Další informace najdete v tématu [stránkování SQL pro přenos hromadných dat pomocí Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx).

* Vytvořte [*uloženou proceduru*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , která uspořádá výsledky podle vašich představ. SQL Connector poskytuje mnoho funkcí back-endu, ke kterým můžete přistupovat pomocí Azure Logic Apps, abyste mohli snadněji automatizovat obchodní úkoly, které pracují s tabulkami SQL Database.

  Při načítání nebo vkládání více řádků aplikace logiky může iterovat přes tyto řádky pomocí [*smyčky do*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) v rámci těchto [limitů](../logic-apps/logic-apps-limits-and-config.md). Nicméně, pokud aplikace logiky potřebuje pracovat se sadami záznamů, takže jsou velké, například tisíce nebo miliony řádků, které chcete minimalizovat náklady vyplývající z volání databáze.

  Chcete-li uspořádat výsledky tak, jak chcete, můžete vytvořit uloženou proceduru, která bude spuštěna v instanci SQL a používat příkaz **Select-ORDER by** . Toto řešení vám nabízí větší kontrolu nad velikostí a strukturou vašich výsledků. Vaše aplikace logiky volá uloženou proceduru pomocí akce **Spustit uloženou proceduru** SQL serverového konektoru. Další informace najdete v tématu [klauzule SELECT-ORDER by](/sql/t-sql/queries/select-order-by-clause-transact-sql).

  > [!NOTE]
  > V tomto konektoru je provádění uložených procedur omezeno na [časový limit kratší než 2 minuty](/connectors/sql/#known-issues-and-limitations). Některé uložené procedury mohou trvat déle než tento limit pro zpracování a úplné dokončení, což generuje `504 TIMEOUT` chybu. Některé dlouhotrvající procesy jsou ve skutečnosti kódované jako výslovně uložené procedury pro tento účel. Volání těchto procedur z Azure Logic Apps může vytvořit problémy z důvodu tohoto limitu. Přestože SQL Connector nativně nepodporuje asynchronní režim, můžete tento režim simulovat pomocí triggeru dokončení SQL, nativního předávacího dotazu SQL, tabulky stavu a úloh na straně serveru pomocí [agenta elastické úlohy Azure](../azure-sql/database/elastic-jobs-overview.md).

### <a name="handle-dynamic-bulk-data"></a>Zpracovat dynamická Hromadná data

Když zavoláte uloženou proceduru pomocí konektoru SQL Server, vrácený výstup je někdy dynamický. V takovém případě postupujte podle následujících kroků:

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. Zobrazit výstupní formát provedením testovacího běhu. Zkopírujte a uložte ukázkový výstup.

1. V návrháři v rámci akce, kde zavoláte uloženou proceduru, vyberte **Nový krok**.

1. V části **Vybrat akci**vyhledejte a vyberte akci [**analyzovat JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) .

1. V akci **analyzovat JSON** vyberte **použít ukázkovou datovou část k vygenerování schématu**.

1. Do pole **Zadejte nebo vložte ukázkovou datovou část JSON** vložte vzorový výstup a vyberte **Hotovo**.

   > [!NOTE]
   > Pokud se zobrazí chyba, že Logic Apps nemůže vygenerovat schéma, ověřte, že je správně naformátována syntaxe ve výstupu ukázky. Pokud stále nemůžete generovat schéma, v poli **schématu** zadejte ručně schéma.

1. Na panelu nástrojů návrháře vyberte **Uložit**.

1. Chcete-li odkazovat na vlastnosti obsahu JSON, klikněte do textových polí, kde chcete odkazovat na tyto vlastnosti, aby se zobrazil seznam dynamického obsahu. V seznamu v záhlaví [**JSON analýzy**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) vyberte datové tokeny pro vlastnosti obsahu JSON, které chcete.

## <a name="troubleshoot-problems"></a>Poradce při potížích

* Často dochází k problémům s připojením, aby bylo možné problémy vyřešit a vyřešit tyto druhy problémů, přečtěte si téma [řešení chyb připojení k SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server). Tady je několik příkladů:

  * `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

  * `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

  * `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Technické informace o aktivačních událostech, akcích a omezeních tohoto konektoru najdete na [referenční stránce konektoru](/connectors/sql/), která je vygenerována z popisu Swagger.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech pro Azure Logic Apps](../connectors/apis-list.md)
