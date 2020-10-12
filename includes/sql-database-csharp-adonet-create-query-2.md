---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: bca885f9b19086302fd91d29b8ff6553b7205f28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86544284"
---
## <a name="c-program-example"></a>Příklad programu C#

V dalších částech tohoto článku se nachází program v jazyce C#, který používá ADO.NET k posílání příkazů jazyka Transact-SQL (T-SQL) k SQL Database. Program C# předvádí následující akce:

- [Připojení k SQL Database pomocí ADO.NET](#cs_1_connect)
- [Metody, které vracejí příkazy T-SQL](#cs_2_return)
    - Vytváření tabulek
    - Naplnění tabulek daty
    - Aktualizace, odstranění a výběr dat
- [Odeslat T-SQL do databáze](#cs_3_submit)

### <a name="entity-relationship-diagram-erd"></a>Diagram vztahů mezi entitami (ERD)

`CREATE TABLE`Příkazy zahrnují klíčové slovo **References** k vytvoření vztahu *cizího klíče* (FK) mezi dvěma tabulkami. Pokud používáte *databázi tempdb*, přidejte komentář k `--REFERENCES` klíčovému slovu pomocí dvojice počátečních pomlček.

Záchranná disketa zobrazuje vztah mezi oběma tabulkami. Hodnoty v *podřízeném* sloupci **tabEmployee. DepartmentCode** jsou omezeny na hodnoty z *nadřazeného* sloupce **tabDepartment. DepartmentCode** .

![Záchranná disketa ukazující cizí klíč](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)

> [!NOTE]
> Máte možnost upravovat T-SQL, abyste přidali úvodní `#` název tabulky, který je vytvoří jako dočasné tabulky v *databázi tempdb*. To je užitečné pro demonstrační účely, pokud není k dispozici žádná testovací databáze. Všechny odkazy na cizí klíče se během jejich používání nevynutily a dočasné tabulky se odstraní automaticky, když se připojení ukončí po dokončení programu.

### <a name="to-compile-and-run"></a>Pro zkompilování a spuštění

Program v jazyce C# je logicky jeden soubor. cs a fyzicky je rozdělen do několika bloků kódu, aby bylo snazší pochopit jednotlivé bloky. Chcete-li zkompilovat a spustit program, proveďte následující kroky:

1. Vytvořte projekt C# v aplikaci Visual Studio. Typ projektu by měl být *Konzola*, která se nachází v části **šablony**  >  **Visual C#**  >  **Windows Desktop**  >  **Konzolová aplikace (.NET Framework)**.

1. V souboru *program.cs*nahraďte počáteční řádky kódu následujícím postupem:

    1. Zkopírujte a vložte následující bloky kódu ve stejném pořadí, v jakém jsou prezentovány, viz [připojení k databázi](#cs_1_connect), [generování T-SQL](#cs_2_return)a [odeslání do databáze](#cs_3_submit).

    1. Změňte následující hodnoty v `Main` metodě:

        - *CB. Datového*
        - *CB. UserID*
        - *CB. Zadáno*
        - *cb.InitialCatalog*

1. Ověřte, že je odkazováno na *System.Data.dll* sestavení. Chcete-li ověřit, rozbalte uzel **odkazy** v podokně **Průzkumník řešení** .

1. Chcete-li vytvořit a spustit program ze sady Visual Studio, vyberte tlačítko **Start** . Výstup sestavy se zobrazí v okně programu, i když se hodnoty GUID mezi testovacími běhy budou lišit.

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

<a name="cs_1_connect"></a>

### <a name="connect-to-sql-database-using-adonet"></a>Připojení k SQL Database pomocí ADO.NET

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

<a name="cs_2_return"></a>

### <a name="methods-that-return-t-sql-statements"></a>Metody, které vracejí příkazy T-SQL

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

<a name="cs_3_submit"></a>

### <a name="submit-t-sql-to-the-database"></a>Odeslat T-SQL do databáze

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