---
title: Připojení a dotazování databáze pomocí .NET Core
description: V tomto tématu se dozvíte, jak pomocí .NET Core vytvořit program, který se připojí k databázi v Azure SQL Database nebo Azure SQL Managed instance a provede dotazování pomocí příkazů jazyka Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 37cd2051670221b8b78c075f249f633f9f447099
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674293"
---
# <a name="quickstart-use-net-core-c-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Rychlý Start: použití .NET Core (C#) k dotazování databáze v Azure SQL Database nebo spravované instanci SQL Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

V tomto rychlém startu použijete k připojení k databázi kód [.NET Core](https://www.microsoft.com/net/) a C#. Pak spustíte příkaz Transact-SQL k dotazování dat.

> [!TIP]
> Následující Microsoft Learn modul vám pomůže naučit se zdarma [, jak vyvíjet a konfigurovat aplikaci ASP.NET, která se dotazuje databáze v Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Požadavky

Co budete potřebovat k dokončení tohoto rychlého startu:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Databáze. K vytvoření a konfiguraci databáze můžete použít jeden z těchto rychlých startů:

  | Akce | Databáze SQL | Spravovaná instance SQL | SQL Server na virtuálním počítači Azure | Azure Synapse Analytics |
  |:--- |:--- |:---|:---|:---|
  | Vytvořit| [Azure Portal](single-database-create-quickstart.md) | [Azure Portal](../managed-instance/instance-create-quickstart.md) | [Azure Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) | [Azure Portal](/azure/synapse-analytics/quickstart-create-workspace.md) |
  || [Rozhraní příkazového řádku](scripts/create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) | | [Rozhraní příkazového řádku](/azure/synapse-analytics/quickstart-create-workspace-cli.md) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md) | [PowerShell](/azure/synapse-analytics/quickstart-create-workspace-powershell.md) |
  || | | [Šablona nasazení](/azure/azure-sql/virtual-machines/windows/create-sql-vm-resource-manager-template.md) | [Šablona nasazení](/azure/synapse-analytics/quickstart-deployment-template-workspaces.md) | 
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](firewall-create-server-level-portal-quickstart.md)| [Připojení z virtuálního počítače](../managed-instance/connect-vm-instance-configure.md)| |
  |||[Připojení z místního prostředí](../managed-instance/point-to-site-p2s-configure.md) | [Připojení k instanci SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) |
  ||||

- [Rozhraní .NET Core pro nainstalovaný operační systém](https://www.microsoft.com/net/core) .

## <a name="get-server-connection-information"></a>Získat informace o připojení k serveru

Získejte informace o připojení, které potřebujete pro připojení k databázi v Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL**  nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro databázi v Azure SQL Database nebo plně kvalifikovaného názvu serveru (nebo IP adresy) vedle **hostitele** spravované instance Azure SQL nebo SQL Server na virtuálním počítači Azure. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

> [!NOTE]
> Informace o připojení pro SQL Server na virtuálním počítači Azure najdete v tématu [připojení k instanci SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>Získat informace o připojení ADO.NET (jenom volitelné SQL Database)

1. V Azure Portal přejděte do okna databáze a v části **Nastavení** vyberte **připojovací řetězce**.

2. Zkontrolujte úplný připojovací řetězec **ADO.NET**.

    ![Připojovací řetězec pro ADO.NET](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Pokud ho máte v úmyslu používat, zkopírujte připojovací řetězec **ADO.NET** .
  
## <a name="create-a-new-net-core-project"></a>Vytvoření nového projektu .NET Core

1. Otevřete příkazový řádek a vytvořte složku **sqltest**. Přejděte do této složky a spusťte tento příkaz.

    ```cmd
    dotnet new console
    ```

    Tento příkaz vytvoří nové soubory projektu aplikace, včetně počátečního souboru kódu jazyka C# (**program.cs**), konfiguračního souboru XML (**sqltest. csproj**) a potřebných binárních souborů.

2. V textovém editoru otevřete soubor **sqltest. csproj** a vložte mezi značky následující kód XML `<Project>` . Tento kód XML `System.Data.SqlClient` se přidá jako závislost.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Vložení kódu pro dotazování databáze v Azure SQL Database

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

                    String sql = "SELECT name, collation_name FROM sys.databases";

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

2. Ověřte, zda jsou vráceny řádky.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Kliknutím na tlačítko **ENTER** zavřete okno aplikace.

## <a name="next-steps"></a>Další kroky

- [Začínáme s .NET Core v systému Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)
- Naučte se [připojit a dotazovat Azure SQL Database nebo spravované instance Azure SQL pomocí .NET Framework a sady Visual Studio](connect-query-dotnet-visual-studio.md).  
- Přečtěte si, jak [navrhnout první databázi pomocí SSMS](design-first-database-tutorial.md) nebo [navrhnout databázi a jak se připojit pomocí C# a ADO.NET](design-first-database-csharp-tutorial.md).
- Další informace o .NET najdete v [dokumentaci rozhraní .NET](/dotnet/).
