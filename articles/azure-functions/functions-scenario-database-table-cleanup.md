---
title: Použití Azure Functions k provádění úlohy čištění databáze | Dokumentace Microsoftu
description: Naplánování úlohy, která se připojuje ke službě Azure SQL Database a pravidelně čistí řádky pomocí služby Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: glenga
ms.openlocfilehash: e59c0b6994a64972b1458c0f295f24d0a615d871
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740106"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Pomocí služby Azure Functions pro připojení ke službě Azure SQL Database

V tomto článku se dozvíte, jak používat Azure Functions k vytvoření naplánované úlohy, která se připojí k instanci Azure SQL Database. Kód funkce vyčistí řádky v tabulce v databázi. Nové C# funkce je vytvořena na základě předem definované časovače triggeru šablony v sadě Visual Studio 2017. Pro podporu tohoto scénáře, musíte také nastavit připojovací řetězec databáze jako nastavení aplikace do aplikace function App. Tento scénář využívá hromadnou operaci v databázi. 

Pokud je toto první práci se službou C# funkce, přečtěte si [Azure Functions C# referenční informace pro vývojáře](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Požadavky

+ Proveďte kroky v následujícím článku [vytvoření první funkce pomocí sady Visual Studio](functions-create-your-first-function-visual-studio.md) k vytvoření aplikace místní funkce, která se zaměřuje modul runtime verze 2.x. Musíte také publikování projektu do aplikace function app v Azure.

+ Tento článek popisuje příkaz jazyka Transact-SQL, který spustí hromadnou operaci vyčištění v **SalesOrderHeader** tabulky v ukázkové databáze AdventureWorksLT. K vytvoření ukázkové databáze AdventureWorksLT, proveďte kroky v následujícím článku [vytvořit databázi Azure SQL na webu Azure Portal](../sql-database/sql-database-get-started-portal.md).

+ Je nutné přidat [pravidlo brány firewall na úrovni serveru](../sql-database/sql-database-get-started-portal-firewall.md) pro veřejnou IP adresu počítače, který používáte pro účely tohoto rychlého startu. Toto pravidlo je třeba, aby přístup k instanci SQL database ze svého místního počítače.  

## <a name="get-connection-information"></a>Získání informací o připojení

Je potřeba získat připojovací řetězec pro databázi, který jste vytvořili, když jste dokončili [vytvořit databázi Azure SQL na webu Azure Portal](../sql-database/sql-database-get-started-portal.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vyberte **databází SQL** z nabídky na levé straně a vyberte svou databázi na **databází SQL** stránky.

1. Vyberte **připojovací řetězce** pod **nastavení** a zkopírujte kompletní **ADO.NET** připojovací řetězec.

    ![Zkopírujte připojovací řetězec ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Nastavit připojovací řetězec

Provádění funkcí v Azure je hostováno v aplikaci funkce. Jako osvědčený postup zabezpečení ukládání připojovacích řetězců a dalších tajných kódů v vaše nastavení aplikace function app. Použití nastavení aplikace zabrání náhodnému zveřejnění připojovacího řetězce s vaším kódem. Nastavení aplikace lze použít pro vaši aplikaci function app přímo ze sady Visual Studio.

Musíte jste dříve publikovali aplikace do Azure. Pokud jste tak již neučinili, [publikujete aplikaci function app v Azure](functions-develop-vs.md#publish-to-azure).

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace funkcí a zvolte **publikovat** > **spravovat nastavení aplikace...** . Vyberte **přidat nastavení**v **nový název nastavení aplikace**, typ `sqldb_connection`a vyberte **OK**.

    ![Nastavení aplikace pro aplikaci function app.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Na novém **sqldb_connection** nastavení, vložte připojovací řetězec, který jste zkopírovali v předchozí části do **místní** pole a nahraďte `{your_username}` a `{your_password}` zástupné symboly real hodnoty. Vyberte **vložit hodnotu z místního** zkopírovat aktualizovanou hodnotu do **vzdálené** pole a pak vyberte **OK**.

    ![Přidáte nastavení připojovacího řetězce SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Připojovací řetězce jsou uloženy v zašifrované podobě v Azure (**vzdálené**). Aby se zabránilo unikající tajných kódů v projektu souboru local.settings.json (**místní**) by se měly vyloučit ze správy zdrojového kódu, například pomocí souboru .gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Do projektu přidejte balíček SqlClient

Budete muset přidat balíček NuGet, který obsahuje knihovnu SqlClient. Tuto knihovnu datového přístupu je potřeba pro připojení k databázi SQL.

1. Otevřete projekt aplikace pro vaše místní funkce v sadě Visual Studio 2017.

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace funkcí a zvolte **spravovat balíčky NuGet**.

1. Na kartě **Procházet** vyhledejte a po nalezení vyberte ```System.Data.SqlClient```.

1. Na stránce **System.Data.SqlClient** klikněte na **Nainstalovat**.

1. Po dokončení instalace zkontrolujte změny a potom kliknutím na **OK** zavřete okno **Náhled**.

1. Pokud se zobrazí okno **Souhlas s podmínkami licence**, klikněte na **Souhlasím**.

Teď můžete přidat funkci kód jazyka C#, která se připojuje ke službě SQL Database.

## <a name="add-a-timer-triggered-function"></a>Přidání funkce aktivované časovačem

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace funkcí a zvolte **přidat** > **funkce nové Azure**.

1. S **Azure Functions** šablony vybrané, pojmenujte novou položku něco jako `DatabaseCleanup.cs` a vyberte **přidat**.

1. V **funkce nové Azure** dialogového okna zvolte **trigger časovače** a potom **OK**. Toto dialogové okno vytvoří soubor kódu pro funkci aktivovanou časovačem.

1. Otevřete nový soubor s kódem a přidejte následující příkazy using v horní části souboru:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Nahraďte existující `Run` funkce s následujícím kódem:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Tato funkce spustí každých 15 sekund na Aktualizovat `Status` sloupec založeny na datu, příjemce. Další informace o aktivační událost časovače, naleznete v tématu [trigger časovače pro službu Azure Functions](functions-bindings-timer.md).

1. Stisknutím klávesy **F5** ke spuštění aplikace function app. [Nástrojů Azure Functions Core](functions-develop-local.md) otevření okna spuštění za Visual Studio.

1. Po 15 sekundách po spuštění je funkce spuštěná. Podívejte se na výstup a poznamenejte si počet řádků v aktualizovat **SalesOrderHeader** tabulky.

    ![Zobrazení protokolů funkce.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Na první spuštění měli byste aktualizovat 32 řádky dat. Následující spuštění aktualizovat žádné řádky dat. Pokud provedete změny SalesOrderHeader tabulkových dat tak, aby se zvolila víc je řádků `UPDATE` příkazu.

Pokud máte v úmyslu [publikovat tuto funkci](functions-develop-vs.md#publish-to-azure), nezapomeňte změnit `TimerTrigger` atribut více rozumné [plán cron](functions-bindings-timer.md#cron-expressions) než každých 15 sekund.

## <a name="next-steps"></a>Další postup

Dále se naučíte používat. Functions s Logic Apps k integraci s jinými službami.

> [!div class="nextstepaction"]
> [Vytvoření funkce, která se integruje s Logic Apps](functions-twitter-email.md)

Další informace o funkcích najdete v následujících článcích:

+ [Referenční informace pro vývojáře Azure Functions](functions-reference.md)  
  Referenční informace pro programátory týkající se kódování funkcí a definování triggerů a vazeb.
+ [Testování Azure Functions](functions-test-a-function.md)  
  Toto téma popisuje různé nástroje a techniky pro testování funkcí.  
