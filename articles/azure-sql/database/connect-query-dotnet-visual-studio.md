---
title: Použití sady Visual Studio s .NET a C# k dotazování
description: Pomocí sady Visual Studio můžete vytvořit aplikaci v jazyce C#, která se připojuje k databázi ve službě Azure SQL Database nebo Azure SQL Managed instance a spouští dotazy.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1d8859f4790610e72ad517f74bbbbf0cf77d9316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705196"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Rychlý Start: použití .NET a C# v aplikaci Visual Studio k připojení a dotazování databáze
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

V tomto rychlém startu se dozvíte, jak používat kód [.NET Framework](https://www.microsoft.com/net/) a C# v aplikaci Visual Studio k dotazování databáze v Azure SQL nebo synapse SQL pomocí příkazů jazyka Transact-SQL.

## <a name="prerequisites"></a>Požadavky

Co budete potřebovat k dokončení tohoto rychlého startu:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Komunita, edice Professional nebo Enterprise.
- Databáze, ve které lze spustit dotaz.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Vytvořte kód pro dotazování databáze v Azure SQL Database

1. V aplikaci Visual Studio vytvořte nový projekt. 
   
1. V dialogovém okně **Nový projekt** vyberte **Visual C#**, **Konzolová aplikace (.NET Framework)**.
   
1. Jako název projektu zadejte *sqltest* a pak vyberte **OK**. Vytvoří se nový projekt. 
   
1. Vyberte **projekt**  >  **Spravovat balíčky NuGet**. 
   
1. Ve **Správci balíčků NuGet** vyberte kartu **Procházet** , vyhledejte a vyberte **Microsoft. data. SqlClient**.
   
1. Na stránce **Microsoft. data. SqlClient** vyberte **instalovat**. 
   - Pokud se zobrazí výzva, vyberte **OK** a pokračujte v instalaci. 
   - Pokud se zobrazí okno pro **přijetí licence** , vyberte **Souhlasím**.
   
1. Po dokončení instalace můžete zavřít **Správce balíčků NuGet**. 
   
1. V editoru kódu nahraďte obsah **program. cs** následujícím kódem. Nahraďte své hodnoty pro `<your_server>` , `<your_username>` , a `<your_password>` `<your_database>` .
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Spuštění kódu

1. Pokud chcete aplikaci spustit, vyberte **ladění**  >  **Spustit ladění** nebo na panelu nástrojů vyberte **Spustit** nebo stiskněte klávesu **F5**.
1. Ověřte, zda jsou vráceny názvy a kolace databáze, a pak okno aplikace zavřete.

## <a name="next-steps"></a>Další kroky

- Naučte se [připojit a dotazovat databázi v Azure SQL Database pomocí .NET Core](connect-query-dotnet-core.md) v systému Windows, Linux nebo MacOS.  
- Informace o tom, [jak začít s .NET Core v systému Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)
- Naučte se, jak [navrhnout první databázi v Azure SQL Database pomocí SSMS](design-first-database-tutorial.md) nebo [navrhnout svou první databázi v Azure SQL Database pomocí .NET](design-first-database-csharp-tutorial.md).
- Další informace o .NET najdete v [dokumentaci rozhraní .NET](/dotnet/).
- Příklad logiky opakování: [odolné připojení k Azure SQL pomocí ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
