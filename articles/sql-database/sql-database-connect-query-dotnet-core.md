---
title: Použít .NET Core k dotazování
description: V tomto tématu se dozvíte, jak pomocí .NET Core vytvořit program, který se připojí k Azure SQL Database a provede dotaz pomocí příkazů jazyka Transact-SQL.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827093"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Rychlý start: Použití .NET Core (jazyk C#) k dotazování databáze Azure SQL

V tomto rychlém startu použijete [.NET Core](https://www.microsoft.com/net/) a C# Code pro připojení k databázi SQL Azure. Pak spustíte příkaz Transact-SQL k dotazování dat.

> [!TIP]
> Následující Microsoft Learn modul vám pomůže naučit se zdarma [, jak vyvíjet a konfigurovat aplikaci ASP.NET, která se dotazuje Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Požadavky

Pro tento kurz potřebujete:

- Databázi Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL Database můžete použít jeden z těchto rychlých startů:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Vytvoření| [Azure Portal](sql-database-single-database-get-started.md) | [Azure Portal](sql-database-managed-instance-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního počítače](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z webu](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](sql-database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku jsou určeny k používání databáze Adventure Works. Se spravovanou instancí musíte buď importovat databázi Adventure Works do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.

- [Rozhraní .NET Core pro nainstalovaný operační systém](https://www.microsoft.com/net/core) .

> [!NOTE]
> V tomto rychlém startu se používá databáze *mySampleDatabase* . Pokud chcete použít jinou databázi, budete muset změnit odkazy databáze a upravit `SELECT` dotaz v C# kódu.

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

## <a name="get-adonet-connection-information-optional"></a>Získat informace o připojení ADO.NET (volitelné)

1. Přejděte na stránku **mySampleDatabase** a v části **Nastavení**vyberte **připojovací řetězce**.

2. Zkontrolujte úplný připojovací řetězec **ADO.NET**.

    ![Připojovací řetězec pro ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Pokud ho máte v úmyslu používat, zkopírujte připojovací řetězec **ADO.NET** .
  
## <a name="create-a-new-net-core-project"></a>Vytvoření nového projektu .NET Core

1. Otevřete příkazový řádek a vytvořte složku **sqltest**. Přejděte do této složky a spusťte tento příkaz.

    ```cmd
    dotnet new console
    ```
    Tento příkaz vytvoří nové soubory projektu aplikace, včetně počátečního C# souboru kódu (**program.cs**), konfiguračního souboru XML (**sqltest. csproj**) a potřebných binárních souborů.

2. V textovém editoru otevřete soubor **sqltest. csproj** a vložte následující kód XML mezi značky `<Project>`. Tento kód XML přidá `System.Data.SqlClient` jako závislost.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Vložení kódu pro dotazování databáze SQL

1. V textovém editoru otevřete **program.cs**.

2. Nahraďte obsah následujícím kódem a přidejte odpovídající hodnoty pro váš server, databázi, uživatelské jméno a heslo.

> [!NOTE]
> Chcete-li použít připojovací řetězec ADO.NET, nahraďte 4 řádky v kódu nastavení server, databáze, uživatelské jméno a heslo následujícím řádkem. V řetězci nastavte své uživatelské jméno a heslo.
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

1. Na příkazovém řádku spusťte následující příkazy.

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
3. Kliknutím na tlačítko **ENTER** zavřete okno aplikace.

## <a name="next-steps"></a>Další kroky

- [Začínáme s .NET Core v systému Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)
- Naučte se, jak se [připojit k databázi SQL Azure a dotazovat ji pomocí .NET Framework a sady Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Přečtěte si, jak [navrhnout první databázi SQL Azure pomocí SSMS](sql-database-design-first-database.md) nebo [navrhnout databázi SQL Azure a připojit se C# pomocí a ADO.NET](sql-database-design-first-database-csharp.md).
- Další informace o .NET najdete v [dokumentaci rozhraní .NET](https://docs.microsoft.com/dotnet/).
