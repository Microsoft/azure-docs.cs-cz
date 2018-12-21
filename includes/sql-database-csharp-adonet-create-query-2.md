---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: e30651cb0ed7d74082163a92acbc428c21018255
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728542"
---
## <a name="c-program-example"></a>C#Příklad programu

Další části tohoto článku, který je k dispozici C# program, který se používá k odesílání příkazů jazyka Transact-SQL (T-SQL) do služby SQL database ADO.NET. C# Program ukazuje následující akce:

- [Připojení k SQL database pomocí ADO.NET](#cs_1_connect)
- [Metody, které vracejí příkazy jazyka T-SQL](#cs_2_return)
    - Vytvoření tabulek
    - Naplnění tabulek daty
    - Aktualizace, odstranění a výběr dat
- [Odeslat T-SQL na databázi](#cs_3_submit)

### <a name="entity-relationship-diagram-erd"></a>Diagram vztahů entit (opravné)

`CREATE TABLE` Zahrnovat příkazy **odkazy** – klíčové slovo k vytvoření *cizí klíč* (Cizíklíč) vztah mezi dvěma tabulkami. Pokud používáte *tempdb*, okomentujte `--REFERENCES` – klíčové slovo pomocí dvojice přední pomlčky.

Opravné zobrazuje vztah mezi dvěma tabulkami. Hodnoty **tabEmployee.DepartmentCode** *podřízené* sloupce jsou omezené na hodnoty z **tabDepartment.DepartmentCode** *nadřazené*sloupce.

![Opravné zobrazující cizí klíč](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)

> [!NOTE]
> Máte možnost úprav jazyka T-SQL pro přidání úvodní `#` názvy tabulek, která je vytvořila jako dočasné tabulky v *tempdb*. To je užitečné pro demonstrační účely, pokud je k dispozici žádné testovací databáze. Všechny odkazy na cizí klíče nejsou vynucená při jejich používání a dočasných tabulek se automaticky odstraní, když se zavře po ukončení programu.

### <a name="to-compile-and-run"></a>Kompilace a spuštění

C# Programu je logicky jeden soubor .cs a fyzicky je rozdělen na několik bloků kódu, aby každý blok snadněji jim porozumíte. Kompilace a spuštění programu, proveďte následující kroky:

1. Vytvoření C# projektu v sadě Visual Studio. Typ projektu by měl být *konzoly*, je uvedený v části **šablony** > **Visual C#**   >  **Windows Desktop**  >  **Aplikace konzoly (.NET Framework)**.

1. V souboru *Program.cs*, starter řádky kódu nahraďte následující kroky:

    1. Kopírování a vkládání v následujícím kódu bloky ve stejném pořadí se zobrazí, naleznete v tématu [připojit k databázi](#cs_1_connect), [generovat T-SQL](#cs_2_return), a [odeslat do databáze](#cs_3_submit).

    1. Změňte následující hodnoty `Main` metody:

        - *označení CB. Zdroj dat*
        - *označení CB. ID uživatele*
        - *označení CB. Heslo*
        - *označení CB. Počáteční katalog*

1. Ověřte, sestavení *System.Data.dll* odkazuje. Chcete-li ověřit, rozbalte **odkazy** uzlu v **Průzkumníka řešení** podokně.

1. Sestavte a spusťte program ze sady Visual Studio, vyberte **Start** tlačítko. Výstup sestavy se zobrazí v okně aplikace, ale identifikátor GUID hodnoty se budou lišit mezi testovacími běhy.

    ```Output
    =================================
    T-SQL to 2 - Create-Tables...
    -1 = rows affected.

    =================================
    T-SQL to 3 - Inserts...
    8 = rows affected.

    =================================
    T-SQL to 4 - Update-Join...
    2 = rows affected.

    =================================
    T-SQL to 5 - Delete-Join...
    2 = rows affected.

    =================================
    Now, SelectEmployees (6)...
    8ddeb8f5-9584-4afe-b7ef-d6bdca02bd35 , Alison , 20 , acct , Accounting
    9ce11981-e674-42f7-928b-6cc004079b03 , Barbara , 17 , hres , Human Resources
    315f5230-ec94-4edd-9b1c-dd45fbb61ee7 , Carol , 22 , acct , Accounting
    fcf4840a-8be3-43f7-a319-52304bf0f48d , Elle , 15 , NULL , NULL
    View the report output here, then press any key to end the program...
    ```

<a name="cs_1_connect"/>

### <a name="connect-to-sql-database-using-adonet"></a>Připojení k SQL database pomocí ADO.NET

```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                var cb = new SqlConnectionStringBuilder();
                cb.DataSource = "your_server.database.windows.net";
                cb.UserID = "your_user";
                cb.Password = "your_password";
                cb.InitialCatalog = "your_database";

                using (var connection = new SqlConnection(cb.ConnectionString))
                {
                    connection.Open();

                    Submit_Tsql_NonQuery(connection, "2 - Create-Tables", Build_2_Tsql_CreateTables());

                    Submit_Tsql_NonQuery(connection, "3 - Inserts", Build_3_Tsql_Inserts());

                    Submit_Tsql_NonQuery(connection, "4 - Update-Join", Build_4_Tsql_UpdateJoin(),
                        "@csharpParmDepartmentName", "Accounting");

                    Submit_Tsql_NonQuery(connection, "5 - Delete-Join", Build_5_Tsql_DeleteJoin(),
                        "@csharpParmDepartmentName", "Legal");

                    Submit_6_Tsql_SelectEmployees(connection);
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }

            Console.WriteLine("View the report output here, then press any key to end the program...");
            Console.ReadKey();
        }
```

<a name="cs_2_return"/>

### <a name="methods-that-return-t-sql-statements"></a>Metody, které vracejí příkazy jazyka T-SQL

```csharp
static string Build_2_Tsql_CreateTables()
{
    return @"
        DROP TABLE IF EXISTS tabEmployee;
        DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.

        CREATE TABLE tabDepartment
        (
            DepartmentCode  nchar(4)          not null    PRIMARY KEY,
            DepartmentName  nvarchar(128)     not null
        );

        CREATE TABLE tabEmployee
        (
            EmployeeGuid    uniqueIdentifier  not null  default NewId()    PRIMARY KEY,
            EmployeeName    nvarchar(128)     not null,
            EmployeeLevel   int               not null,
            DepartmentCode  nchar(4)              null
            REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
        );
    ";
}

static string Build_3_Tsql_Inserts()
{
    return @"
        -- The company has these departments.
        INSERT INTO tabDepartment (DepartmentCode, DepartmentName)
        VALUES
            ('acct', 'Accounting'),
            ('hres', 'Human Resources'),
            ('legl', 'Legal');

        -- The company has these employees, each in one department.
        INSERT INTO tabEmployee (EmployeeName, EmployeeLevel, DepartmentCode)
        VALUES
            ('Alison'  , 19, 'acct'),
            ('Barbara' , 17, 'hres'),
            ('Carol'   , 21, 'acct'),
            ('Deborah' , 24, 'legl'),
            ('Elle'    , 15, null);
    ";
}

static string Build_4_Tsql_UpdateJoin()
{
    return @"
        DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';

        -- Promote everyone in one department (see @parm...).
        UPDATE empl
        SET
            empl.EmployeeLevel += 1
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName1;
    ";
}

static string Build_5_Tsql_DeleteJoin()
{
    return @"
        DECLARE @DName2  nvarchar(128);
        SET @DName2 = @csharpParmDepartmentName;  --'Legal';

        -- Right size the Legal department.
        DELETE empl
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName2

        -- Disband the Legal department.
        DELETE tabDepartment
            WHERE DepartmentName = @DName2;
    ";
}

static string Build_6_Tsql_SelectEmployees()
{
    return @"
        -- Look at all the final Employees.
        SELECT
            empl.EmployeeGuid,
            empl.EmployeeName,
            empl.EmployeeLevel,
            empl.DepartmentCode,
            dept.DepartmentName
        FROM
            tabEmployee   as empl
        LEFT OUTER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        ORDER BY
            EmployeeName;
    ";
}
```

<a name="cs_3_submit"/>

### <a name="submit-t-sql-to-the-database"></a>Odeslat T-SQL na databázi

```csharp
static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("Now, SelectEmployees (6)...");

    string tsql = Build_6_Tsql_SelectEmployees();

    using (var command = new SqlCommand(tsql, connection))
    {
        using (SqlDataReader reader = command.ExecuteReader())
        {
            while (reader.Read())
            {
                Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                    reader.GetGuid(0),
                    reader.GetString(1),
                    reader.GetInt32(2),
                    (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                    (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
            }
        }
    }
}

static void Submit_Tsql_NonQuery(
    SqlConnection connection,
    string tsqlPurpose,
    string tsqlSourceCode,
    string parameterName = null,
    string parameterValue = null
    )
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

    using (var command = new SqlCommand(tsqlSourceCode, connection))
    {
        if (parameterName != null)
        {
            command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                parameterName,
                parameterValue);
        }
        int rowsAffected = command.ExecuteNonQuery();
        Console.WriteLine(rowsAffected + " = rows affected.");
    }
}
} // EndOfClass
}
```