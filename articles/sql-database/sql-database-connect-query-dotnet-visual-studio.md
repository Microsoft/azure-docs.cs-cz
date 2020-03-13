---
title: Použití sady Visual Studio s .NET C# a k dotazování
description: Pomocí sady Visual Studio můžete vytvořit C# aplikaci, která se připojí k Azure SQL Database a provede dotaz pomocí příkazů jazyka Transact-SQL.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209573"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Rychlý Start: použití .NET C# a v aplikaci Visual Studio pro připojení k databázi SQL Azure a dotazování na ně

V tomto rychlém startu se dozvíte, C# jak používat [.NET Framework](https://www.microsoft.com/net/) a kód v aplikaci Visual Studio k dotazování databáze SQL Azure pomocí příkazů jazyka Transact-SQL.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Databázi Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL Database můžete použít jeden z těchto rychlých startů:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Vytvořit| [Azure Portal](sql-database-single-database-get-started.md) | [Azure Portal](sql-database-managed-instance-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního počítače](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z webu](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](sql-database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku jsou určeny k používání databáze Adventure Works. Se spravovanou instancí musíte buď importovat databázi Adventure Works do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Komunita, edice Professional nebo Enterprise.

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** . 

## <a name="create-code-to-query-the-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL

1. V aplikaci Visual Studio vyberte **soubor** > **Nový** > **projekt**. 
   
1. V dialogovém okně **Nový projekt** vyberte **Visual C#** a pak vyberte **Konzolová aplikace (.NET Framework)** .
   
1. Jako název projektu zadejte *sqltest* a pak vyberte **OK**. Vytvoří se nový projekt. 
   
1. Vyberte **projekt** > **Spravovat balíčky NuGet**. 
   
1. Ve **Správci balíčků NuGet**vyberte kartu **Procházet** a pak vyhledejte a vyberte **System. data. SqlClient**.
   
1. Na stránce **System. data. SqlClient** vyberte **nainstalovat**. 
   - Pokud se zobrazí výzva, vyberte **OK** a pokračujte v instalaci. 
   - Pokud se zobrazí okno pro **přijetí licence** , vyberte **Souhlasím**.
   
1. Po dokončení instalace můžete zavřít **Správce balíčků NuGet**. 
   
1. V editoru kódu nahraďte obsah **program.cs** následujícím kódem. Nahraďte své hodnoty pro `<server>`, `<username>`, `<password>`a `<database>`.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která můžete zvolit jako zdroj při vytváření databáze. Pokud má vaše databáze jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 
   
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

1. Chcete-li spustit aplikaci, vyberte možnost **ladění** > **Spustit ladění**nebo vyberte možnost **Spustit** na panelu nástrojů nebo stiskněte klávesu **F5**.
1. Ověřte, že se vrátí prvních 20 řádků/produktů z vaší databáze, a pak okno aplikace zavřete.

## <a name="next-steps"></a>Další kroky

- Naučte se, jak se [připojit k databázi SQL Azure a dotazovat se na ni pomocí .NET Core](sql-database-connect-query-dotnet-core.md) v systému Windows, Linux nebo MacOS.  
- Informace o tom, [jak začít s .NET Core v systému Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)
- Informace o [návrhu první databáze Azure SQL pomocí aplikace SSMS](sql-database-design-first-database.md) nebo [návrhu první databáze Azure SQL pomocí .NET](sql-database-design-first-database-csharp.md)
- Další informace o .NET najdete v [dokumentaci rozhraní .NET](https://docs.microsoft.com/dotnet/).
- Příklad logiky opakování: [odolné připojení k SQL pomocí ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

