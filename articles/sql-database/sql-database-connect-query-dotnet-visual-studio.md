---
title: Pomocí sady Visual Studio s využitím .NET a C# k dotazování služby Azure SQL Database | Dokumentace Microsoftu
description: Pomocí sady Visual Studio k vytvoření C# aplikaci, která se připojuje ke službě Azure SQL Database a dotazů s příkazy jazyka Transact-SQL.
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
ms.date: 12/11/2018
ms.openlocfilehash: 04a0abd0fba7ec53aebeb481ac80d36653d118b6
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384934"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Rychlý start: Použití .NET a C# v sadě Visual Studio k připojení a dotazování Azure SQL database

Tento rychlý start ukazuje, jak používat [rozhraní .NET framework](https://www.microsoft.com/net/) a C# kódu v sadě Visual Studio k dotazování databáze Azure SQL pomocí příkazů jazyka Transact-SQL.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- A [pravidlo brány firewall na úrovni serveru](sql-database-get-started-portal-firewall.md) umožňující veřejnou IP adresu počítače, který používáte.
  
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) Community, Professional nebo Enterprise edition.

## <a name="get-sql-server-connection-information"></a>Získejte informace o připojení SQL serveru

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-the-sql-database"></a>Vytvořit kód k dotazování databáze SQL

1. V sadě Visual Studio, vyberte **souboru** > **nový** > **projektu**. 
   
1. V **nový projekt** dialogového okna, vyberte **Visual C#** a pak vyberte **Konzolová aplikace (.NET Framework)**.
   
1. Zadejte *sqltest* pro název projektu a pak vyberte **OK**. Vytvoření nového projektu. 
   
1. Vyberte **projektu** > **spravovat balíčky NuGet**. 
   
1. V **Správce balíčků NuGet**, vyberte **Procházet** kartu, vyhledejte a vyberte **System.Data.SqlClient**.
   
1. Na **System.Data.SqlClient** stránce **nainstalovat**. 
   - Pokud se zobrazí výzva, vyberte **OK** pokračujte v instalaci. 
   - Pokud **přijetí licence** okna se zobrazí, vyberte **souhlasím**.
   
1. Po dokončení instalace můžete zavřít **Správce balíčků NuGet**. 
   
1. V editoru kódu nahraďte **Program.cs** obsah následujícím kódem. Dosaďte svoje hodnoty `<server>`, `<username>`, `<password>`, a `<database>`.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu se používá ukázková data AdventureWorksLT, které můžete použít jako zdroj při vytváření databáze. Pokud vaše databáze má jiná data, pomocí tabulek z vlastní databázi v dotazu SELECT. 
   
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
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Spuštění kódu

1. Chcete-li spustit aplikaci, vyberte **ladění** > **spustit ladění**, nebo vyberte **Start** na panelu nástrojů nebo stisknete klávesu **F5**.
1. Ověřte, že se vrátí prvních 20 řádků kategorie nebo produktů z databáze a potom zavřete okno aplikace.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [připojení a dotazování databáze Azure SQL pomocí .NET Core](sql-database-connect-query-dotnet-core.md) na Windows, Linux nebo macOS.  
- Informace o tom, [jak začít s .NET Core v systému Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)
- Informace o [návrhu první databáze SQL Azure pomocí aplikace SSMS](sql-database-design-first-database.md) nebo [návrhu první databáze SQL Azure pomocí .NET](sql-database-design-first-database-csharp.md)
- Další informace o .NET najdete v [dokumentaci rozhraní .NET](https://docs.microsoft.com/dotnet/).
- Příklad logiky opakování: [Odolné připojení k SQL pomocí ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

