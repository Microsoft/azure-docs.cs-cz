---
title: Použití .NET Core k dotazování služby Azure SQL Database | Dokumentace Microsoftu
description: V tomto tématu se dozvíte, jak pomocí .NET Core vytvořit program, který se připojuje ke službě Azure SQL Database a dotazy pomocí příkazů jazyka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: 4d2acf64f7798f845c2993b108b7074283c40be6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160011"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Rychlý start: Použití .NET Core (jazyk C#) k dotazování databáze SQL Azure

V tomto rychlém startu použijete [.NET Core](https://www.microsoft.com/net/) a C# kód k připojení k databázi Azure SQL. Pak je potřeba spustit příkaz jazyka Transact-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu potřebujete:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [.NET core pro váš operační systém](https://www.microsoft.com/net/core) nainstalované. 

> [!NOTE]
> Tento rychlý start využívá *mySampleDatabase* databáze. Pokud chcete použít jinou databázi, budete muset změnit odkazy na databázi a upravit `SELECT` v dotazu C# kódu.


## <a name="get-sql-server-connection-information"></a>Získejte informace o připojení SQL serveru

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="get-adonet-connection-information-optional"></a>Získání informací o připojení ADO.NET (volitelné)

1. Přejděte **mySampleDatabase** stránky a v části **nastavení**vyberte **připojovací řetězce**.

2. Zkontrolujte úplný připojovací řetězec **ADO.NET**.

    ![Připojovací řetězec pro ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Kopírovat **ADO.NET** připojovací řetězec, pokud máte v úmyslu použít.
  
## <a name="create-a-new-net-core-project"></a>Vytvoření nového projektu .NET Core

1. Otevřete příkazový řádek a vytvořte složku **sqltest**. Přejděte do této složky a spusťte tento příkaz.

    ```cmd
    dotnet new console
    ```
    Tento příkaz vytvoří novou aplikaci soubory projektu, včetně počáteční C# souboru s kódem (**Program.cs**), konfigurační soubor XML (**sqltest.csproj**) a potřebné binární soubory.

2. V textovém editoru otevřete **sqltest.csproj** a vložte následující kód XML mezi `<Project>` značky. Přidá tato konfigurace XML `System.Data.SqlClient` jako závislost.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Vložení kódu pro dotazování databáze SQL

1. V textovém editoru otevřete **Program.cs**.

2. Nahraďte obsah následujícím kódem a přidejte odpovídající hodnoty pro váš server, databáze, uživatelské jméno a heslo.

> [!NOTE]
> Abyste mohli použít připojovací řetězec ADO.NET, nahraďte 4 řádky v kódu, nastavení serveru, databáze, uživatelské jméno a heslo s dalším řádku. V řetězci nastavte své uživatelské jméno a heslo.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Spuštění kódu

1. Do příkazového řádku spusťte následující příkazy.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Ověřte, že se vrátil prvních 20 řádků.

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. Zvolte **Enter** okna aplikace okno zavřete.

## <a name="next-steps"></a>Další postup

- [Začínáme s .NET Core v systému Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)
- Zjistěte, jak [připojení a dotazování Azure SQL database pomocí rozhraní .NET Framework a sady Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Zjistěte, jak [návrh první databáze Azure SQL pomocí SSMS](sql-database-design-first-database.md) nebo [ návrh služby Azure SQL database a její připojení pomocí C# a ADO.NET](sql-database-design-first-database-csharp.md).
- Další informace o .NET najdete v [dokumentaci rozhraní .NET](https://docs.microsoft.com/dotnet/).
