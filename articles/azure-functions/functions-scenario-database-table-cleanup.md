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
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: a257948c97437d6045f705acb02054928d22ff89
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092865"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Pomocí služby Azure Functions pro připojení ke službě Azure SQL Database
V tomto tématu se dozvíte, jak používat Azure Functions k vytvoření naplánované úlohy, která vyčistí řádky v tabulce v Azure SQL Database. Nová funkce skriptu jazyka C# je vytvořena na základě předem definovaných časovače triggeru šablony na webu Azure Portal. Pro podporu tohoto scénáře, musíte také nastavit připojovací řetězec databáze jako nastavení aplikace do aplikace function App. Tento scénář využívá hromadnou operaci v databázi. 

Aby proces jednotlivé funkce vytvoření, čtení, aktualizace a odstranění (CRUD) operací v tabulce Mobile Apps, je vhodné použít [vazby Mobile Apps](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Požadavky

+ Toto téma používá funkci aktivovanou časovačem. Proveďte kroky v tématu [vytvoření funkce v Azure, která je aktivované časovačem](functions-create-scheduled-function.md) verzi C# této funkce.   

+ Příkaz jazyka Transact-SQL, který spustí hromadnou operaci vyčištění v tomto tématu se dozvíte **SalesOrderHeader** tabulky v ukázkové databáze AdventureWorksLT. K vytvoření ukázkové databáze AdventureWorksLT, proveďte kroky v tématu [vytvořit databázi Azure SQL na webu Azure Portal](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Získání informací o připojení

Je potřeba získat připojovací řetězec pro databázi, který jste vytvořili, když jste dokončili [vytvořit databázi Azure SQL na webu Azure Portal](../sql-database/sql-database-get-started-portal.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
 
3. Vyberte **databází SQL** z nabídky na levé straně a vyberte svou databázi na **databází SQL** stránky.

4. Vyberte **zobrazit databázové připojovací řetězce** a zkopírujte kompletní **ADO.NET** připojovací řetězec. 

    ![Zkopírujte připojovací řetězec ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Nastavit připojovací řetězec 

Provádění funkcí v Azure je hostováno v aplikaci funkce. Je osvědčeným postupem je ukládání připojovacích řetězců a dalších tajných kódů v vaše nastavení aplikace function app. Použití nastavení aplikace zabrání náhodnému zveřejnění připojovacího řetězce s vaším kódem. 

1. Přejděte do aplikace function app vytvoříte [vytvoření funkce v Azure, která je aktivované časovačem](functions-create-scheduled-function.md).

2. Vyberte **funkce platformy** > **nastavení aplikace**.
   
    ![Nastavení aplikace pro aplikaci function app.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Přejděte dolů k položce **připojovací řetězce** a přidejte připojovací řetězec pomocí nastavení uvedená v tabulce.
   
    ![Přidáte připojovací řetězec do nastavení aplikace function app.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Nastavení       | Navrhovaná hodnota | Popis             | 
    | ------------ | ------------------ | --------------------- | 
    | **Název**  |  sqldb_connection  | Používá pro přístup k uložené připojovací řetězec v kódu funkce.    |
    | **Hodnota** | Zkopírovaný řetězec  | Vložte připojovací řetězec, který jste zkopírovali v předchozí části a nahraďte `{your_username}` a `{your_password}` zástupných symbolů skutečné hodnoty. |
    | **Typ** | SQL Database | Použijte výchozí připojení k SQL Database. |   

3. Klikněte na **Uložit**.

Teď můžete přidat funkci kód jazyka C#, která se připojuje ke službě SQL Database.

## <a name="update-your-function-code"></a>Aktualizace kódu funkce

1. Ve vaší aplikaci function app na portálu vyberte funkci aktivovanou časovačem.
 
3. Přidejte následující odkazy na sestavení v horní části existující kód jazyka C# skript funkce:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >Kód v těchto příkladech jsou skript jazyka C# z portálu. Při vývoji předkompilované funkce C# místně, musíte místo toho přidat odkazy na tyto sestaví v místním projektu.  

3. Přidejte následující `using` příkazy funkci:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Nahraďte existující `Run` funkce s následujícím kódem:
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    Tento ukázkový příkaz aktualizuje `Status` sloupec založeny na datu, příjemce. Ji by měl aktualizovat 32 řádků s daty.

5. Klikněte na tlačítko **Uložit**, podívejte **protokoly** windows pro další spuštění funkce a pak Poznámka: počet řádků v aktualizovat **SalesOrderHeader** tabulky.

    ![Zobrazení protokolů funkce.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Další postup

Dále se naučíte používat funkce Logic Apps k integraci s jinými službami.

> [!div class="nextstepaction"] 
> [Vytvoření funkce, která se integruje s Logic Apps](functions-twitter-email.md)

Další informace o funkcích naleznete v následujících tématech:

* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)  
  Referenční informace pro programátory týkající se kódování funkcí a definování triggerů a vazeb.
* [Testování Azure Functions](functions-test-a-function.md)  
  Toto téma popisuje různé nástroje a techniky pro testování funkcí.  
