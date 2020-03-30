---
title: Dotaz pomocí rozhraní .NET Core
description: Toto téma ukazuje, jak pomocí .NET Core vytvořit program, který se připojí k azure SQL database a dotazy pomocí příkazů Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 369c708fd3181076c6deb9d7ac9134c57a18f819
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73827093"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Rychlý start: Použití .NET Core (jazyk C#) k dotazování databáze Azure SQL

V tomto rychlém startu použijete kód [.NET Core](https://www.microsoft.com/net/) a C# pro připojení k databázi Azure SQL. Potom spustíte příkaz Transact-SQL pro dotazování dat.

> [!TIP]
> Následující modul Microsoft Learn vám pomůže zdarma se naučit [vyvíjet a konfigurovat ASP.NET aplikaci, která se dotazuje na Azure SQL Database.](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu potřebujete:

- Databázi Azure SQL. Pomocí jednoho z těchto rychlých startů můžete vytvořit a pak nakonfigurovat databázi v Azure SQL Database:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Vytvořit| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Cli](scripts/sql-database-create-and-configure-database-cli.md) | [Cli](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall IP na úrovni serveru](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního virtuálního zařízení](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z místa na místě](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Adventure Works načtený podle rychlého startu|[Obnovit wide world dovozci](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo import adventure works ze souboru [BACPAC](sql-database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku jsou zapsány k použití databáze Adventure Works. Se spravovanou instancí je nutné buď importovat databázi adventure works do databáze instancí, nebo upravit skripty v tomto článku, aby bylo nutné používat databázi Wide World Importers.

- [Rozhraní .NET Core pro nainstalovaný operační systém.](https://www.microsoft.com/net/core)

> [!NOTE]
> Tento rychlý start používá databázi *mySampleDatabase.* Pokud chcete použít jinou databázi, budete muset změnit odkazy `SELECT` na databázi a upravit dotaz v kódu C#.

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi Azure SQL. Pro nadcházející postupy budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte na stránku **databází SQL** nebo **instancí spravovaných SQL.**

3. Na stránce **Přehled** zkontrolujte úplný název serveru vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Chcete-li zkopírovat název serveru nebo název hostitele, najeďte na něj a vyberte ikonu **Kopírovat.**

## <a name="get-adonet-connection-information-optional"></a>Získání informací o připojení ADO.NET (volitelné)

1. Přejděte na stránku **mySampleDatabase** a v části **Nastavení**vyberte **připojovací řetězce**.

2. Zkontrolujte úplný připojovací řetězec **ADO.NET**.

    ![Připojovací řetězec pro ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Zkopírujte připojovací řetězec **ADO.NET,** pokud jej chcete použít.
  
## <a name="create-a-new-net-core-project"></a>Vytvoření nového projektu .NET Core

1. Otevřete příkazový řádek a vytvořte složku **sqltest**. Přejděte do této složky a spusťte tento příkaz.

    ```cmd
    dotnet new console
    ```
    Tento příkaz vytvoří nové soubory projektu aplikace, včetně počátečního souboru kódu C# (**Program.cs**), konfiguračního souboru XML (**sqltest.csproj**) a potřebných binárních souborů.

2. V textovém editoru otevřete **sqltest.csproj** a `<Project>` vložte mezi tagy následující XML. Tento xml `System.Data.SqlClient` přidá jako závislost.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Vložení kódu pro dotazování databáze SQL

1. V textovém editoru otevřete **Program.cs**.

2. Nahraďte obsah následujícím kódem a přidejte příslušné hodnoty pro server, databázi, uživatelské jméno a heslo.

> [!NOTE]
> Chcete-li použít připojovací řetězec ADO.NET, nahraďte 4 řádky v nastavení kódu serveru, databáze, uživatelského jména a hesla níže uvedenou řádkou. V řetězci nastavte uživatelské jméno a heslo.
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

1. Na výzvu spusťte následující příkazy.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Ověřte, zda je vráceno prvních 20 řádků.

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
3. Chcete-li zavřít okno aplikace, zvolte **Enter.**

## <a name="next-steps"></a>Další kroky

- [Začínáme s .NET Core v systému Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)
- Zjistěte, jak [připojit a dotazovat se na databázi Azure SQL pomocí rozhraní .NET Framework a Visual Studia](sql-database-connect-query-dotnet-visual-studio.md).  
- Naučte se [navrhovat první databázi Azure SQL pomocí SSMS](sql-database-design-first-database.md) nebo [navrhnout databázi Azure SQL a spojit se s C# a ADO.NET](sql-database-design-first-database-csharp.md).
- Další informace o .NET najdete v [dokumentaci rozhraní .NET](https://docs.microsoft.com/dotnet/).
