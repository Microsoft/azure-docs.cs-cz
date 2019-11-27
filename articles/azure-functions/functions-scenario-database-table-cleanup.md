---
title: Použití Azure Functions k provedení úlohy vyčištění databáze
description: Použijte Azure Functions k naplánování úlohy, která se připojuje k Azure SQL Database k pravidelnému vyčištění řádků.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: f70b5b83561e7c580dd7192850c8eb50be5aac0a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230384"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Připojení k Azure SQL Database pomocí Azure Functions

V tomto článku se dozvíte, jak pomocí Azure Functions vytvořit naplánovanou úlohu, která se připojí k Azure SQL Database nebo spravované instanci Azure SQL. Kód funkce vyčistí řádky v tabulce v databázi. Nová C# funkce je vytvořena na základě předdefinované šablony triggeru časovače v aplikaci Visual Studio 2019. Pro podporu tohoto scénáře musíte také nastavit připojovací řetězec databáze jako nastavení aplikace ve Function App. Pro spravovanou instanci Azure SQL musíte [Povolit veřejný koncový bod](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) , abyste se mohli připojit z Azure Functions. Tento scénář používá pro databázi hromadnou operaci. 

Pokud se jedná o vaše první prostředí s C# funkcemi, měli byste si přečíst [referenční C# informace pro vývojáře Azure Functions](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Požadavky

+ Dokončete kroky v článku [Vytvoření první funkce pomocí sady Visual Studio](functions-create-your-first-function-visual-studio.md) k vytvoření místní aplikace Function App, která cílí na modul runtime verze 2. x. Projekt musíte také publikovat do aplikace Function App v Azure.

+ Tento článek ukazuje příkaz Transact-SQL, který provede operaci hromadného vyčištění v tabulce **SalesOrderHeader** v ukázkové databázi AdventureWorksLT. Chcete-li vytvořit ukázkovou databázi AdventureWorksLT, proveďte kroky v článku [Vytvoření databáze SQL Azure v Azure Portal](../sql-database/sql-database-get-started-portal.md).

+ Je nutné přidat [pravidlo brány firewall na úrovni serveru](../sql-database/sql-database-get-started-portal-firewall.md) pro veřejnou IP adresu počítače, který používáte pro účely tohoto rychlého startu. Toto pravidlo je vyžadováno, aby bylo možné získat přístup k instanci SQL Database z místního počítače.  

## <a name="get-connection-information"></a>Získání informací o připojení

Pro databázi, kterou jste vytvořili po dokončení [vytváření databáze SQL Azure](../sql-database/sql-database-get-started-portal.md), musíte získat připojovací řetězec v Azure Portal.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com/).

1. V nabídce na levé straně vyberte **databáze SQL** a na stránce **databáze SQL** vyberte svou databázi.

1. V části **Nastavení** vyberte **připojovací řetězce** a zkopírujte úplný připojovací řetězec **ADO.NET** . Pro připojovací řetězec kopírování spravované instance Azure SQL pro veřejný koncový bod.

    ![Zkopírujte připojovací řetězec ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Nastavit připojovací řetězec

Function App hostuje provádění vašich funkcí v Azure. Osvědčeným postupem zabezpečení je ukládat připojovací řetězce a další tajné kódy do nastavení aplikace Function App. Použití nastavení aplikace brání nechtěnému zveřejnění připojovacího řetězce s vaším kódem. K nastavení aplikace vaší aplikace Function App můžete přistupovat přímo ze sady Visual Studio.

Je potřeba, abyste aplikaci publikovali už dřív v Azure. Pokud jste to ještě neudělali, [publikujte aplikaci Function App do Azure](functions-develop-vs.md#publish-to-azure).

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt aplikace Function App a vyberte **publikovat** > **Spravovat nastavení aplikace..** .. Vyberte **Přidat nastavení**, v **nastavení nový název aplikace**zadejte `sqldb_connection`a pak vyberte **OK**.

    ![Nastavení aplikace pro aplikaci Function App](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. V nastavení nový **sqldb_connection** vložte připojovací řetězec, který jste zkopírovali v předchozí části, do **místního** pole a nahraďte `{your_username}` a `{your_password}` zástupné symboly skutečnými hodnotami. Vyberte možnost **Vložit hodnotu z místní** pro zkopírování aktualizované hodnoty do **vzdáleného** pole a pak vyberte **OK**.

    ![Přidat nastavení připojovacího řetězce SQL](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Připojovací řetězce se ukládají v Azure jako šifrované (**vzdálené**). Aby nedocházelo k únikům tajných kódů, je třeba soubor projektu Local. Settings. JSON (**místní**) vyloučit ze správy zdrojového kódu, jako je například pomocí souboru. gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Přidat do projektu balíček SqlClient

Musíte přidat balíček NuGet, který obsahuje knihovnu SqlClient. Tato knihovna přístupu k datům je nutná pro připojení k databázi SQL.

1. Otevřete svůj projekt místní aplikace Function App v aplikaci Visual Studio 2019.

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt aplikace Function App a vyberte možnost **Spravovat balíčky NuGet**.

1. Na kartě **Procházet** vyhledejte a po nalezení vyberte ```System.Data.SqlClient```.

1. Na stránce **System. data. SqlClient** vyberte verze `4.5.1` a pak klikněte na **nainstalovat**.

1. Po dokončení instalace zkontrolujte změny a potom kliknutím na **OK** zavřete okno **Náhled**.

1. Pokud se zobrazí okno **Souhlas s podmínkami licence**, klikněte na **Souhlasím**.

Nyní můžete přidat kód C# funkce, který se připojí k vašemu SQL Database.

## <a name="add-a-timer-triggered-function"></a>Přidání funkce aktivované časovačem

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt aplikace Function App a vyberte **přidat** > **novou funkci Azure Functions**.

1. Když je vybraná Šablona **Azure Functions** , pojmenujte novou položku jako `DatabaseCleanup.cs` a vyberte **Přidat**.

1. V dialogovém okně **Nová funkce Azure** zvolte **aktivační událost časovače** a pak klikněte na **OK**. Toto dialogové okno vytvoří soubor s kódem pro funkci aktivovanou časovačem.

1. Otevřete nový soubor kódu a na začátek souboru přidejte následující příkazy using:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Existující funkci `Run` nahraďte následujícím kódem:

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

    Tato funkce se spouští každých 15 sekund, aby aktualizovala sloupec `Status` na základě data expedice. Další informace o triggeru časovače najdete v tématu [Trigger časovače pro Azure Functions](functions-bindings-timer.md).

1. Stisknutím klávesy **F5** spusťte aplikaci Function App. Otevře se okno spuštění [Azure Functions Core Tools](functions-develop-local.md) za Visual Studio.

1. Po uplynutí 15 sekund po spuštění funkce se spustí. Sledujte výstup a poznamenejte si počet řádků aktualizovaných v tabulce **SalesOrderHeader** .

    ![Zobrazit protokoly funkcí.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Při prvním spuštění byste měli aktualizovat 32 řádků dat. Následující běhy aktualizují žádné řádky dat, Pokud neprovedete změny v tabulkových datech SalesOrderHeader, aby bylo vybráno více řádků pomocí příkazu `UPDATE`.

Pokud plánujete [publikování této funkce](functions-develop-vs.md#publish-to-azure), nezapomeňte změnit atribut `TimerTrigger` na smysluplný [plán cron](functions-bindings-timer.md#ncrontab-expressions) než každých 15 sekund.

## <a name="next-steps"></a>Další kroky

Dále se naučíte, jak používat. Funkce s Logic Apps pro integraci s dalšími službami.

> [!div class="nextstepaction"]
> [Vytvoření funkce, která se integruje s Logic Apps](functions-twitter-email.md)

Další informace o funkcích naleznete v následujících článcích:

+ [Referenční informace pro vývojáře Azure Functions](functions-reference.md)  
  Referenční informace pro programátory týkající se kódování funkcí a definování triggerů a vazeb.
+ [Testování Azure Functions](functions-test-a-function.md)  
  Toto téma popisuje různé nástroje a techniky pro testování funkcí.  
