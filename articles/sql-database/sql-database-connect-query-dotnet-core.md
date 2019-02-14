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
ms.date: 02/12/2019
ms.openlocfilehash: 82b412d7fc9e54ca213fecde783a5e27f8ee93bc
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233554"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Rychlý start: Použití .NET Core (jazyk C#) k dotazování databáze SQL Azure

V tomto rychlém startu použijete [.NET Core](https://www.microsoft.com/net/) a C# kód k připojení k databázi Azure SQL. Pak je potřeba spustit příkaz jazyka Transact-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu potřebujete:

- Databázi SQL Azure. Jeden z těchto rychlých startech můžete vytvořit a potom nakonfigurovat databázi ve službě Azure SQL Database:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Vytvořit| [Azure Portal](sql-database-single-database-get-started.md) | [Azure Portal](sql-database-managed-instance-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | Konfigurace | [pravidlo brány firewall na úrovni serveru IP](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního počítače](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z na místě](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Společnosti Adventure Works načtených za rychlý start|[Obnovit Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo importovat společnosti Adventure Works z [BACPAC](sql-database-import.md) souboru z [githubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku se zapisují do použít databázi společnosti Adventure Works. S managed instance musíte importovat databázi společnosti Adventure Works do instance databáze nebo upravovat skripty v tomto článku pro používání databáze Wide World Importers.

- [.NET core pro váš operační systém](https://www.microsoft.com/net/core) nainstalované.

> [!NOTE]
> Tento rychlý start využívá *mySampleDatabase* databáze. Pokud chcete použít jinou databázi, budete muset změnit odkazy na databázi a upravit `SELECT` v dotazu C# kódu.

## <a name="get-sql-server-connection-information"></a>Získejte informace o připojení SQL serveru

Získejte informace o připojení potřebné pro připojení k databázi Azure SQL. Nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte **databází SQL** nebo **spravované instance SQL** stránky.

3. Na **přehled** stránce si prohlédněte plně kvalifikovaný název vedle **název serveru** pro izolované databáze nebo serveru plně kvalifikovaný název vedle **hostitele** pro spravované instance. Zkopírujte název serveru nebo název hostitele, je ukazatel myši a vyberte **kopírování** ikonu.

## <a name="get-adonet-connection-information-optional"></a>Získání informací o připojení ADO.NET (volitelné)

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
