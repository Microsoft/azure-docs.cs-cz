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
ms.openlocfilehash: 1d25f43ef5a694d8b94710055bf1be72a7fcb45c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705207"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Rychlý Start: použití .NET Core (C#) k dotazování databáze
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

V tomto rychlém startu použijete k připojení k databázi kód [.NET Core](https://www.microsoft.com/net/) a C#. Pak spustíte příkaz Transact-SQL k dotazování dat.

> [!TIP]
> Následující Microsoft Learn modul vám pomůže naučit se zdarma [, jak vyvíjet a konfigurovat aplikaci ASP.NET, která se dotazuje databáze v Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Požadavky

Co budete potřebovat k dokončení tohoto rychlého startu:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Rozhraní .NET Core pro nainstalovaný operační systém](https://www.microsoft.com/net/core) .
- Databáze, ve které můžete spustit dotaz. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>Vytvoření nového projektu .NET Core

1. Otevřete příkazový řádek a vytvořte složku **sqltest**. Přejděte do této složky a spusťte tento příkaz.

    ```cmd
    dotnet new console
    ```

    Tento příkaz vytvoří nové soubory projektu aplikace, včetně počátečního souboru kódu jazyka C# (**program. cs**), konfiguračního souboru XML (**sqltest. csproj**) a potřebných binárních souborů.

2. V textovém editoru otevřete soubor **sqltest. csproj** a vložte mezi značky následující kód XML `<Project>` . Tento kód XML `System.Data.SqlClient` se přidá jako závislost.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Vložení kódu pro dotazování databáze v Azure SQL Database

1. V textovém editoru otevřete **program. cs**.

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
