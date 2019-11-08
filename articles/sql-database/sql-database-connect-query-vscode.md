---
title: Připojení a dotazování pomocí VS Code
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826897"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Rychlý Start: použití Visual Studio Code k připojení a dotazování Azure SQL Database

[Visual Studio Code](https://code.visualstudio.com/docs) je grafický editor kódu pro Linux, MacOS a Windows. Podporuje rozšíření, včetně [rozšíření MSSQL](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Server, Azure SQL Database a SQL Data Warehouse. V tomto rychlém startu použijete Visual Studio Code k připojení k databázi SQL Azure a potom spustíte příkazy jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat.

## <a name="prerequisites"></a>Požadavky

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

## <a name="install-visual-studio-code"></a>Nainstalovat Visual Studio Code

Ujistěte se, že máte nainstalovanou nejnovější [Visual Studio Code](https://code.visualstudio.com/Download) a načetli jste [rozšíření MSSQL](https://aka.ms/mssql-marketplace). Pokyny k instalaci rozšíření MSSQL najdete v tématu [instalace vs Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) a [MSSQL pro Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Konfigurace Visual Studio Code

### <a name="mac-os"></a>**Mac OS**

Pro macOS musíte nainstalovat OpenSSL, což je předpoklad pro .NET Core, který rozšíření MSSQL používá. Otevřete terminál a zadejte následující příkazy, abyste nainstalovali **brew** a **OpenSSL**.

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

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

## <a name="set-language-mode-to-sql"></a>Nastavení jazykového režimu na SQL

V Visual Studio Code nastavte jazykový režim na **SQL** , aby se povolily příkazy MSSQL a T-SQL IntelliSense.

1. Otevřete nové okno nástroje Visual Studio Code.

2. Stiskněte klávesu **Ctrl**+**N**. Otevře se nový soubor s prostým textem.

3. V pravém dolním rohu stavového řádku vyberte **prostý text** .

4. V rozevírací nabídce **Vybrat režim jazyka** vyberte **SQL**.

## <a name="connect-to-your-database"></a>Připojení k databázi

Pomocí nástroje Visual Studio Code navažte připojení k serveru služby Azure SQL Database.

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že máte server a že se přihlásíte k informacím. Když začnete zadávat informace o profilu připojení, budete muset při změně fokusu z Visual Studio Code znovu vytvořit profil.

1. V Visual Studio Code stisknutím **kombinace kláves CTRL + SHIFT + P** (nebo **F1**) otevřete paletu příkazů.

2. Vyberte **MS SQL: Connect** a zvolte **ENTER**.

3. Vyberte **vytvořit profil připojení**.

4. Podle pokynů zadejte vlastnosti připojení nového profilu. Po zadání všech hodnot vyberte **ENTER** a pokračujte.

   | Vlastnost       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Název serveru** | Plně kvalifikovaný název serveru | Něco jako: **mynewserver20170313.Database.Windows.NET**. |
   | **Název databáze** | mySampleDatabase | Databáze, ke které se chcete připojit |
   | **Ověřování** | Přihlášení k SQL serveru| V tomto kurzu se používá ověřování SQL. |
   | **Uživatelské jméno** | Uživatelské jméno | Uživatelské jméno účtu správce serveru použitého k vytvoření serveru. |
   | **Heslo (Přihlášení SQL)** | Heslo | Heslo účtu správce serveru použitého k vytvoření serveru. |
   | **Uložit heslo?** | Ano nebo Ne | Pokud nechcete heslo zadávat pokaždé, vyberte **Ano** . |
   | **Zadejte název pro tento profil.** | Název profilu, například **mySampleProfile** | Uložený profil zrychluje připojení při dalších přihlášeních. |

   V případě úspěchu se zobrazí oznámení, že váš profil je vytvořený a připojený.

## <a name="query-data"></a>Dotazování dat

Spusťte [následující příkaz](https://msdn.microsoft.com/library/ms189499.aspx) jazyka Transact-SQL pro dotaz na prvních 20 produktů podle kategorie.

1. V okně editoru vložte následující dotaz SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Stisknutím **kombinace kláves Ctrl**+**SHIFT**+**E** spustíte dotaz a zobrazíte výsledky z tabulek `Product` a `ProductCategory`.

    ![Dotaz pro načtení dat ze dvou tabulek](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Vložení dat

Chcete-li přidat nový produkt do tabulky `SalesLT.Product`, [Spusťte následující příkaz](https://msdn.microsoft.com/library/ms174335.aspx) jazyka Transact-SQL.

1. Nahraďte předchozí dotaz tímto.

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

2. Stisknutím **kombinace kláves Ctrl**+**SHIFT**+**E** vložte nový řádek do tabulky `Product`.

## <a name="update-data"></a>Aktualizace dat

Pokud chcete aktualizovat přidaný produkt, spusťte následující příkaz Transact-SQL [Update](https://msdn.microsoft.com/library/ms177523.aspx) .

1. Nahraďte předchozí dotaz jedním z těchto:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Stisknutím **kombinace kláves Ctrl**+**SHIFT**+**E** aktualizujete zadaný řádek v tabulce `Product`.

## <a name="delete-data"></a>Odstranění dat

Pokud chcete nový produkt odebrat, spusťte následující příkaz Transact-SQL [Delete](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) .

1. Nahraďte předchozí dotaz jedním z těchto:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Stisknutím **kombinace kláves Ctrl**+**SHIFT**+**E** odstraníte zadaný řádek v tabulce `Product`.

## <a name="next-steps"></a>Další kroky

- Informace o připojení a dotazování pomocí SQL Server Management Studio najdete v tématu [rychlý Start: použití SQL Server Management Studio pro připojení k Azure SQL Database a dotazování na data](sql-database-connect-query-ssms.md).
- Informace o připojení a dotazování pomocí Azure Portal najdete v tématu [rychlý Start: použití Editoru dotazů SQL v Azure Portal k připojení a dotazování dat](sql-database-connect-query-portal.md).
- Článek z časopisu MSDN o použití editoru Visual Studio Code najdete v blogovém příspěvku [Vytvoření databáze IDE s rozšířením MSSQL](https://msdn.microsoft.com/magazine/mt809115).
