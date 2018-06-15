---
title: Pomocí Azure Functions provádět databázi úlohy čištění | Microsoft Docs
description: Pomocí Azure Functions můžete naplánovat úlohu, která se připojuje k databázi SQL Azure a pravidelně čistí řádky.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 2947fc6da0c4559e81cf97255b8375b020e0b657
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30231272"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Používat Azure Functions k připojení k databázi SQL Azure
Toto téma ukazuje, jak používat Azure Functions vytvořit naplánovanou úlohu, která vyčistí řádky v tabulce v Azure SQL Database. Nové funkce skriptu jazyka C# se vytvoří na základě šablony aktivační událost časovače předem definovaných na portálu Azure. Pro podporu tohoto scénáře, musíte taky nastavit připojovací řetězec databáze jako nastavení aplikace v aplikaci funkce. Tento scénář používá hromadné operace v databázi. 

Mít jednotlivé funkce proces vytváření, čtení, aktualizaci a operace odstranění (CRUD) v tabulce Mobile Apps, měli byste místo toho použít [Mobile Apps vazby](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Požadavky

+ Toto téma používá funkci spustí časovač. Proveďte kroky v tématu [vytvořit funkci v Azure, který je aktivován časovač](functions-create-scheduled-function.md) verzi jazyka C# této funkce.   

+ Příkaz Transact-SQL, který provádí hromadné operace čištění v tomto tématu se dozvíte **SalesOrderHeader** tabulky v ukázkové databáze AdventureWorksLT. K vytvoření ukázkové databáze AdventureWorksLT, proveďte kroky v tématu [vytvoření Azure SQL database na portálu Azure](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Získání informací o připojení

Je nutné získat připojovací řetězec pro databázi, který jste vytvořili, když jste dokončili [vytvoření Azure SQL database na portálu Azure](../sql-database/sql-database-get-started-portal.md).

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).
 
3. Vyberte **databází SQL** z nabídky na levé straně a vyberte svou databázi na **databází SQL** stránky.

4. Vyberte **zobrazit databázové připojovací řetězce** a zkopírujte kompletní **ADO.NET** připojovací řetězec. 

    ![Zkopírujte připojovací řetězec ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Nastavit připojovací řetězec 

Provádění funkcí v Azure je hostováno v aplikaci funkce. Je osvědčeným postupem k ukládání připojovacích řetězců a jiné tajné v aplikaci nastavení funkce. Pomocí nastavení aplikace zabraňuje náhodného zpřístupnění připojovacího řetězce s vašeho kódu. 

1. Přejděte do aplikace funkce, který jste vytvořili [vytvořit funkci v Azure, který je aktivován časovač](functions-create-scheduled-function.md).

2. Vyberte **funkce** > **nastavení aplikace**.
   
    ![Nastavení aplikace pro funkce aplikace.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Přejděte dolů k položce **připojovací řetězce** a přidat připojovací řetězec pomocí nastavení uvedeného v tabulce.
   
    ![Nastavení funkce aplikace přidáte připojovací řetězec.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Nastavení       | Navrhovaná hodnota | Popis             | 
    | ------------ | ------------------ | --------------------- | 
    | **Název**  |  sqldb_connection  | Slouží k přístupu uložené připojovací řetězec v kódu funkce.    |
    | **Hodnota** | Řetězec zkopírovaný  | Vložte připojovací řetězec, který jste zkopírovali v předchozí části a nahraďte `{your_username}` a `{your_password}` zástupné symboly skutečné hodnoty. |
    | **Typ** | Databáze SQL | Použijte výchozí připojení k databázi SQL. |   

3. Klikněte na **Uložit**.

Teď můžete přidat funkce kódu C#, která se připojuje k vaší databázi SQL.

## <a name="update-your-function-code"></a>Aktualizace kódu – funkce

1. V aplikaci funkce na portálu vyberte funkce spustí časovač.
 
3. Přidejte následující odkazy na sestavení v horní části existující kódu C# skript funkce:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >Kód v těchto příkladech jsou C# skript z portálu. Při vývoji předkompilovaných C# funkci místně, je nutné místo toho přidat odkazy na tyto sestaví ve vašem místním projektu.  

3. Přidejte následující `using` příkazy funkce:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Nahradit existující `Run` funkce následujícím kódem:
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

    Tento ukázkový příkaz aktualizuje `Status` sloupec založen na datum expedice. 32 řádků dat je by měl aktualizovat.

5. Klikněte na tlačítko **Uložit**, sledovat **protokoly** windows pro další funkce spuštění a pak Poznámka: počet řádků v aktualizován **SalesOrderHeader** tabulky.

    ![Zobrazte protokoly funkcí.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Další postup

V dalším kroku Další informace o použití funkce s Logic Apps pro integraci s jinými službami.

> [!div class="nextstepaction"] 
> [Vytvoří funkci, která se integruje s Logic Apps](functions-twitter-email.md)

Další informace o funkcích najdete v následujících tématech:

* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)  
  Referenční informace pro programátory týkající se kódování funkcí a definování triggerů a vazeb.
* [Testování Azure Functions](functions-test-a-function.md)  
  Toto téma popisuje různé nástroje a techniky pro testování funkcí.  
