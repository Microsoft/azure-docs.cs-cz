---
title: Použití visual studia s rozhraními .NET a C# k dotazování
description: Pomocí Visual Studia vytvořte aplikaci C#, která se připojí k azure sql databázi a nařazuje ji pomocí příkazů Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/31/2019
ms.openlocfilehash: 75f84f87fc59a870a0c88fd24cb1b1b8894b3e2a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79209573"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Úvodní příručka: Připojení k databázi Azure SQL a dotazování na databázi Azure SQL pomocí .NET a C# v sadě Visual Studio

Tento rychlý start ukazuje, jak použít [.NET Framework](https://www.microsoft.com/net/) a C# kód v sadě Visual Studio k dotazování databáze Azure SQL s příkazy Transact-SQL.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

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

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Verze Community, Professional nebo Enterprise.

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi Azure SQL. Pro nadcházející postupy budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte na stránku **databází SQL** nebo **instancí spravovaných SQL.**

3. Na stránce **Přehled** zkontrolujte úplný název serveru vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Chcete-li zkopírovat název serveru nebo název hostitele, najeďte na něj a vyberte ikonu **Kopírovat.** 

## <a name="create-code-to-query-the-sql-database"></a>Vytvoření kódu pro dotazování na databázi SQL

1. V sadě Visual Studio vyberte **Soubor** > **nový** > **projekt**. 
   
1. V dialogovém okně **Nový projekt** vyberte **Visual C#** a pak vyberte **Console App (.NET Framework)**.
   
1. Zadejte *sqltest* pro název projektu a pak vyberte **OK**. Nový projekt je vytvořen. 
   
1. Vyberte **možnost Spravovat** > **balíčky NuGet projektu**. 
   
1. Ve **Správci balíčků NuGet**vyberte kartu **Procházet** a vyhledejte a vyberte **System.Data.SqlClient**.
   
1. Na stránce **System.Data.SqlClient** vyberte **Instalovat**. 
   - Pokud se zobrazí výzva, pokračujte v instalaci vyberte **OK.** 
   - Pokud se zobrazí okno **Přijetí licence,** vyberte **Souhlasím**.
   
1. Po dokončení instalace můžete zavřít **NuGet Package Manager**. 
   
1. V editoru kódu nahraďte **Program.cs** obsah následujícím kódem. Nahraďte `<server>`hodnoty `<username>` `<password>`pro `<database>`, , a .
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která si můžete vybrat jako zdroj při vytváření databáze. Pokud databáze obsahuje jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 
   
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
                       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
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

1. Chcete-li aplikaci spustit, vyberte **možnost Ladění** > **zahájit ladění**nebo vyberte možnost **Start** na panelu nástrojů nebo stiskněte **klávesu F5**.
1. Ověřte, že je vráceno prvních 20 řádků kategorie a produktu z databáze, a zavřete okno aplikace.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [připojit a zadat dotaz na databázi Azure SQL pomocí .NET Core](sql-database-connect-query-dotnet-core.md) ve Windows/Linuxu/macOS.  
- Informace o tom, [jak začít s .NET Core v systému Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)
- Informace o [návrhu první databáze Azure SQL pomocí aplikace SSMS](sql-database-design-first-database.md) nebo [návrhu první databáze Azure SQL pomocí .NET](sql-database-design-first-database-csharp.md)
- Další informace o .NET najdete v [dokumentaci rozhraní .NET](https://docs.microsoft.com/dotnet/).
- Příklad logiky opakování: [Připojení k SQL s ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

