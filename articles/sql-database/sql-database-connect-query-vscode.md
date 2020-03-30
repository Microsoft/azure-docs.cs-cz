---
title: Použití kódu VS pro připojení a dotazování
description: Zjistěte, jak se připojit ke službě SQL Database pomocí nástroje Visual Studio Code. Potom spustíte příkazy jazyka Transact-SQL (T-SQL) k dotazování a úpravě dat.
keywords: Připojení k službě SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 28b35f273783b2e4d0b8f59c5bc5be384b933ba2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826897"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Úvodní příručka: Připojení a dotazování na azure SQL databázi pomocí kódu Visual Studia

[Visual Studio Code](https://code.visualstudio.com/docs) je grafický editor kódu pro Linux, macOS a Windows. Podporuje rozšíření, včetně [rozšíření mssql](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Server, Azure SQL Database a SQL Data Warehouse. V tomto rychlém startu použijete kód Visual Studia k připojení k databázi Azure SQL a pak spustíte příkazy Transact-SQL pro dotazování, vkládání, aktualizaci a odstraňování dat.

## <a name="prerequisites"></a>Požadavky

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

## <a name="install-visual-studio-code"></a>Instalace nástroje Visual Studio Code

Ujistěte se, že jste nainstalovali nejnovější [kód sady Visual Studio](https://code.visualstudio.com/Download) a načetli rozšíření [mssql](https://aka.ms/mssql-marketplace). Pokyny k instalaci rozšíření mssql naleznete [v tématu Instalace kódu VS](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) a [mssql pro visual studio kód ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Konfigurace kódu sady Visual Studio

### <a name="mac-os"></a>**Mac OS**

Pro macOS je třeba nainstalovat OpenSSL, což je předpoklad em.NET Core, který používá rozšíření msSQL. Otevřete terminál a zadejte následující příkazy, abyste nainstalovali **brew** a **OpenSSL**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Není potřeba žádná zvláštní konfigurace.

### <a name="windows"></a>**Windows**

Není potřeba žádná zvláštní konfigurace.

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi Azure SQL. Pro nadcházející postupy budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte na stránku **databází SQL** nebo **instancí spravovaných SQL.**

3. Na stránce **Přehled** zkontrolujte úplný název serveru vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Chcete-li zkopírovat název serveru nebo název hostitele, najeďte na něj a vyberte ikonu **Kopírovat.**

## <a name="set-language-mode-to-sql"></a>Nastavení jazykového režimu na SQL

V kódu sady Visual Studio nastavte jazykový režim na **SQL,** abyste povolili příkazy msSQL a T-SQL IntelliSense.

1. Otevřete nové okno nástroje Visual Studio Code.

2. Stiskněte **klávesu Ctrl**+**N**. Otevře se nový soubor ve formátu prostého textu.

3. V pravém dolním rohu stavového řádku vyberte **prostý text.**

4. V rozevírací nabídce **Vybrat jazyk,** která se otevře, vyberte **příkaz SQL**.

## <a name="connect-to-your-database"></a>Připojení k databázi

Pomocí nástroje Visual Studio Code navažte připojení k serveru služby Azure SQL Database.

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že máte server a přihlaste se k informacím. Jakmile začnete zadávat informace o profilu připojení, pokud změníte fokus z kódu sady Visual Studio, budete muset restartovat vytváření profilu.

1. V kódu Visual Studia otevřete paletu příkazů stisknutím **kombinace kláves Ctrl+Shift+P** (nebo **F1).**

2. Vyberte **MS SQL:Připojit** a zvolte **Enter**.

3. Vyberte **Vytvořit profil připojení**.

4. Podle pokynů určete vlastnosti připojení nového profilu. Po zadání jednotlivých hodnot zvolte **Enter,** chcete-li pokračovat.

   | Vlastnost       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Název serveru** | Plně kvalifikovaný název serveru | Něco jako: **mynewserver20170313.database.windows.net**. |
   | **Název databáze** | mySampleDatabase | Databáze, ke které se chcete připojit. |
   | **Ověřování** | Přihlášení k SQL serveru| Tento kurz používá ověřování SQL. |
   | **Uživatelské jméno** | Uživatelské jméno | Uživatelské jméno účtu správce serveru použitého k vytvoření serveru. |
   | **Heslo (Přihlášení SQL)** | Heslo | Heslo účtu správce serveru použitého k vytvoření serveru. |
   | **Uložit heslo?** | Ano nebo Ne | Pokud **Yes** nechcete heslo pokaždé zadávat, vyberte ano. |
   | **Zadejte název pro tento profil.** | Název profilu, například **mySampleProfile** | Uložený profil urychluje připojení při následných přihlášeních. |

   Pokud je úspěšná, zobrazí se oznámení, že váš profil je vytvořen a připojen.

## <a name="query-data"></a>Dotazování dat

Spusťte následující příkaz [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL a dotaznaste se na 20 nejlepších produktů podle kategorií.

1. V okně editoru vložte následující dotaz SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Stisknutím **klávesy Ctrl**+**Shift**+**E** spusťte dotaz a zobrazte výsledky z tabulek `Product` a. `ProductCategory`

    ![Dotaz na načtení dat ze 2 tabulek](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Vložení dat

Spusťte následující příkaz [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL a `SalesLT.Product` přidejte do tabulky nový produkt.

1. Nahraďte předchozí dotaz tímto dotazem.

   ```sql
   INSERT INTO [SalesLT].[Product]
        ( [Name]
        , [ProductNumber]
        , [Color]
        , [ProductCategoryID]
        , [StandardCost]
        , [ListPrice]
        , [SellStartDate]
        )
     VALUES
        ('myNewProduct'
        ,123456789
        ,'NewColor'
        ,1
         ,100
         ,100
         ,GETDATE() );
   ```

2. Stisknutím **klávesy Ctrl**+**Shift**+**E** `Product` vložte do tabulky nový řádek.

## <a name="update-data"></a>Aktualizace dat

Spusťte následující příkaz [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL a aktualizujte přidaný produkt.

1. Nahraďte předchozí dotaz tímto:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Stisknutím **klávesy Ctrl**+**Shift**+**E** `Product` aktualizujte zadaný řádek v tabulce.

## <a name="delete-data"></a>Odstranění dat

Chcete-li odebrat nový produkt, spusťte následující příkaz [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL.

1. Nahraďte předchozí dotaz tímto:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Stisknutím **klávesy Ctrl**+**Shift**+**E** `Product` odstraňte zadaný řádek v tabulce.

## <a name="next-steps"></a>Další kroky

- Pokud se chcete připojit a dotazovat pomocí sql server management studia, [přečtěte si panel Úvodní příručka: Pomocí aplikace SQL Server Management Studio se můžete připojit k databázi Azure SQL database a datům dotazů](sql-database-connect-query-ssms.md).
- Pokud se chcete připojit a dotazovat se pomocí portálu Azure, přečtěte si [panel Rychlý start: Pomocí editoru dotazů SQL na webu Azure Portal se můžete připojit k datům a dotazovat se](sql-database-connect-query-portal.md).
- Článek z časopisu MSDN o použití editoru Visual Studio Code najdete v blogovém příspěvku [Vytvoření databáze IDE s rozšířením MSSQL](https://msdn.microsoft.com/magazine/mt809115).
