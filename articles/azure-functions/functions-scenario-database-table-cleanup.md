---
title: Použití funkce Azure k provedení úlohy vyčištění databáze
description: Pomocí funkce Azure naplánujte úlohu, která se připojuje k Azure SQL Database k pravidelnému čištění řádků.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 2e3f53943d45e90b8aff8e386ce8d0e28670673f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366801"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Připojení k databázi Azure SQL pomocí funkcí Azure

Tento článek ukazuje, jak pomocí funkce Azure vytvořit naplánovanou úlohu, která se připojí k Azure SQL Database nebo Azure SQL Spravované instance. Kód funkce vyčistí řádky v tabulce v databázi. Nová funkce C# je vytvořena na základě předdefinované šablony aktivační události časovače v sadě Visual Studio 2019. Pro podporu tohoto scénáře je nutné také nastavit připojovací řetězec databáze jako nastavení aplikace v aplikaci funkce. Pro Azure SQL spravované instance je nutné [povolit veřejný koncový bod,](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) aby bylo možné se připojit z Funkce Azure. Tento scénář používá hromadnou operaci proti databázi. 

Pokud je to vaše první zkušenosti s prací s funkcemi Jazyka C#, měli byste si přečíst [odkaz na vývojáře Azure Functions C#](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Požadavky

+ Podle kroků v článku [Vytvořte první funkci pomocí sady Visual Studio](functions-create-your-first-function-visual-studio.md) a vytvořte aplikaci pro místní funkce, která se zaměřuje na verzi 2.x nebo novější verzi runtime. Musíte také publikovat projekt do aplikace funkce v Azure.

+ Tento článek ukazuje příkaz Transact-SQL, který provede operaci hromadného vyčištění v tabulce **SalesOrderHeader** v ukázkové databázi AdventureWorksLT. Chcete-li vytvořit ukázkovou databázi AdventureWorksLT, proveďte kroky v článku [Vytvoření databáze Azure SQL na webu Azure Portal](../sql-database/sql-database-get-started-portal.md).

+ Je nutné přidat [pravidlo brány firewall na úrovni serveru](../sql-database/sql-database-get-started-portal-firewall.md) pro veřejnou IP adresu počítače, který používáte pro tento rychlý start. Toto pravidlo je nutné mít přístup k instanci databáze SQL z místního počítače.  

## <a name="get-connection-information"></a>Získání informací o připojení

Musíte získat připojovací řetězec pro databázi, kterou jste vytvořili po dokončení [Vytvořit databázi Azure SQL na webu Azure Portal](../sql-database/sql-database-get-started-portal.md).

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Vyberte **databáze SQL** z nabídky na levé straně a vyberte databázi na stránce **databáze SQL.**

1. V části **Nastavení** vyberte **Připojovací řetězce** a zkopírujte celý **ADO.NET** připojovacířetězec. Pro Azure SQL spravované instance zkopírujte připojovací řetězec pro veřejný koncový bod.

    ![Zkopírujte připojovací řetězec pro ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Nastavit připojovací řetězec

Function App hostuje provádění vašich funkcí v Azure. Jako osvědčený postup zabezpečení uložte připojovací řetězce a další tajné klíče v nastavení aplikace funkce. Použití nastavení aplikace zabraňuje náhodnému zpřístupnění připojovacího řetězce s vaším kódem. K nastavení aplikace pro aplikaci funkcí máte přístup přímo z Visual Studia.

Musíte mít dříve publikovanou aplikaci do Azure. Pokud jste tak ještě neučinili, [publikujte aplikaci funkcí do Azure](functions-develop-vs.md#publish-to-azure).

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt aplikace funkcí a zvolte **Publikovat** > **nastavení služby Azure App Service**. Vyberte **Přidat nastavení**, v **pojmenujete nový název nastavení aplikace**, zadejte `sqldb_connection`a vyberte **OK**.

    ![Nastavení aplikace pro aplikaci funkce.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. V novém **nastavení sqldb_connection** vložte připojovací řetězec, který jste zkopírovali v předchozí části, do pole **Místní** a nahraďte je a `{your_username}` `{your_password}` zástupné symboly měňte skutečné hodnoty. Vyberte **Vložit hodnotu z local,** chcete-li zkopírovat aktualizovanou hodnotu do **pole Vzdálené,** a pak vyberte **OK**.

    ![Přidejte nastavení připojovacího řetězce SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Připojovací řetězce jsou uloženy šifrované v Azure **(Remote).** Chcete-li zabránit úniku tajných kódů, local.settings.json soubor projektu (**Local**) by měly být vyloučeny ze správy zdrojového kódu, například pomocí souboru .gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Přidání balíčku SqlClient do projektu

Je třeba přidat balíček NuGet, který obsahuje knihovnu SqlClient. Tato knihovna přístupu k datům je potřeba pro připojení k databázi SQL.

1. Otevřete projekt místní aplikace funkcí v Visual Studiu 2019.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt aplikace funkce a zvolte **Spravovat balíčky NuGet**.

1. Na kartě **Procházet** vyhledejte a po nalezení vyberte ```System.Data.SqlClient```.

1. Na stránce **System.Data.SqlClient** vyberte verzi `4.5.1` a klepněte na tlačítko **Instalovat**.

1. Po dokončení instalace zkontrolujte změny a potom kliknutím na **OK** zavřete okno **Náhled**.

1. Pokud se zobrazí okno **Souhlas s podmínkami licence**, klikněte na **Souhlasím**.

Nyní můžete přidat kód funkce Jazyka C#, který se připojuje k databázi SQL.

## <a name="add-a-timer-triggered-function"></a>Přidání funkce aktivované časovačem

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt aplikace funkcí a zvolte **Přidat** > **novou funkci Azure**.

1. Když je vybraná šablona **Azure Functions,** pojmenujte novou položku podobně a `DatabaseCleanup.cs` vyberte **Přidat**.

1. V dialogovém okně **Nová funkce Azure** zvolte Časovač aktivační **událost** a pak **OK**. Toto dialogové okno vytvoří soubor kódu pro funkci spuštěnou časovačem.

1. Otevřete nový soubor kódu a v horní části souboru přidejte následující příkazy pomocí příkazů:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Nahraďte `Run` existující funkci následujícím kódem:

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

    Tato funkce se spouští každých `Status` 15 sekund a aktualizuje sloupec na základě data odeslání. Další informace o aktivační události Časovač najdete v [tématu Aktivační událost časovače pro funkce Azure](functions-bindings-timer.md).

1. Stisknutím **klávesy F5** spusťte aplikaci funkce. Za Visual Studio se otevře okno spuštění [základních nástrojů Azure Functions.](functions-develop-local.md)

1. 15 sekund po spuštění se spustí funkce. Podívejte se na výstup a poznamenejte si počet řádků aktualizovaných v tabulce **SalesOrderHeader.**

    ![Zobrazení protokolů funkcí.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Při prvním spuštění byste měli aktualizovat 32 řádků dat. Následující spuštění aktualizovat žádné řádky dat, pokud provedete změny salesorderheader data `UPDATE` tabulky tak, aby více řádků jsou vybrány příkazem.

Pokud máte v plánu [publikovat tuto](functions-develop-vs.md#publish-to-azure) `TimerTrigger` funkci , nezapomeňte změnit atribut na rozumnější [cron plán](functions-bindings-timer.md#ncrontab-expressions) než každých 15 sekund.

## <a name="next-steps"></a>Další kroky

Dále se naučte používat. Funkce s Logic Apps pro integraci s dalšími službami.

> [!div class="nextstepaction"]
> [Vytvoření funkce, která se integruje s logic apps](functions-twitter-email.md)

Další informace o funkcích naleznete v následujících článcích:

+ [Referenční informace pro vývojáře Azure Functions](functions-reference.md)  
  Referenční informace pro programátory týkající se kódování funkcí a definování triggerů a vazeb.
+ [Testování Azure Functions](functions-test-a-function.md)  
  Toto téma popisuje různé nástroje a techniky pro testování funkcí.  
