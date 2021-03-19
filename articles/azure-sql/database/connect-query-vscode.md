---
title: Připojení a dotazování pomocí Visual Studio Code
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Přečtěte si, jak se připojit k Azure SQL Database nebo ke spravované instanci SQL na Azure pomocí Visual Studio Code. Potom spustíte příkazy jazyka Transact-SQL (T-SQL) k dotazování a úpravě dat.
keywords: Připojení k službě SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: f823b6d04a217328fe2e825e64906460cd9cbae9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92672487"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query"></a>Rychlý Start: použití Visual Studio Code k připojení a dotazování 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Visual Studio Code](https://code.visualstudio.com/docs) je grafický editor kódu pro Linux, MacOS a Windows. Podporuje rozšíření, včetně [rozšíření MSSQL](https://aka.ms/mssql-marketplace) pro dotazování instance SQL Server, Azure SQL Database, spravované instance Azure SQL a databáze ve službě Azure synapse Analytics. V tomto rychlém startu použijete Visual Studio Code k připojení k Azure SQL Database nebo spravované instanci SQL Azure a potom spustíte příkazy jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat.

## <a name="prerequisites"></a>Předpoklady

- Databáze v Azure SQL Database nebo spravované instanci Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL Database můžete použít jeden z těchto rychlých startů:

  | Akce | Azure SQL Database | Spravovaná instance Azure SQL |
  |:--- |:--- |:---|
  | Vytvořit| [Azure Portal](single-database-create-quickstart.md) | [Azure Portal](../managed-instance/instance-create-quickstart.md) |
  || [Rozhraní příkazového řádku](scripts/create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall IP na úrovni serveru](firewall-create-server-level-portal-quickstart.md))| [Připojení z virtuálního počítače (VM)](../managed-instance/connect-vm-instance-configure.md)|
  |||[Připojení z místního prostředí](../managed-instance/point-to-site-p2s-configure.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md)
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku jsou určeny k používání databáze Adventure Works. Pomocí spravované instance SQL je nutné buď importovat databázi Adventure Works do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.

## <a name="install-visual-studio-code"></a>Instalace nástroje Visual Studio Code

Ujistěte se, že máte nainstalovanou nejnovější [Visual Studio Code](https://code.visualstudio.com/Download) a načetli jste [rozšíření MSSQL](https://aka.ms/mssql-marketplace). Pokyny k instalaci rozšíření MSSQL najdete v tématu [instalace Visual Studio Code](/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) a [MSSQL pro Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Konfigurace editoru Visual Studio Code

### <a name="macos"></a>**macOS**

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

## <a name="get-server-connection-information"></a>Získat informace o připojení k serveru

Získejte informace o připojení, které potřebujete pro připojení k Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL**  nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **název serveru** pro SQL Database nebo plně kvalifikovaný název serveru vedle **hostitele** spravované instance SQL. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

## <a name="set-language-mode-to-sql"></a>Nastavení jazykového režimu na SQL

V Visual Studio Code nastavte jazykový režim na **SQL**  , aby se povolily příkazy MSSQL a T-SQL IntelliSense.

1. Otevřete nové okno nástroje Visual Studio Code.

2. Stiskněte klávesu **CTRL** + **N**. Otevře se nový soubor s prostým textem.

3. V pravém dolním rohu stavového řádku vyberte **prostý text** .

4. V rozevírací nabídce **Vybrat režim jazyka** vyberte **SQL**.

## <a name="connect-to-your-database"></a>Připojení k databázi

K navázání připojení k serveru použijte Visual Studio Code.

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
   | **Authentication** | Přihlášení k SQL| V tomto kurzu se používá ověřování SQL. |
   | **Uživatelské jméno** | Uživatelské jméno | Uživatelské jméno účtu správce serveru použitého k vytvoření serveru. |
   | **Heslo (Přihlášení SQL)** | Heslo | Heslo účtu správce serveru použitého k vytvoření serveru. |
   | **Uložit heslo?** | Ano nebo Ne | Pokud nechcete heslo zadávat pokaždé, vyberte **Ano** . |
   | **Zadejte název pro tento profil.** | Název profilu, například **mySampleProfile** | Uložený profil zrychluje připojení při dalších přihlášeních. |

   V případě úspěchu se zobrazí oznámení, že váš profil je vytvořený a připojený.

## <a name="query-data"></a>Dotazování dat

Spusťte [následující příkaz](/sql/t-sql/queries/select-transact-sql) jazyka Transact-SQL pro dotaz na prvních 20 produktů podle kategorie.

1. V okně editoru vložte následující dotaz SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Stisknutím **kombinace kláves CTRL** + **SHIFT +** +  spustíte dotaz a zobrazíte výsledky z `Product` `ProductCategory` tabulek a.

    ![Dotaz pro načtení dat ze dvou tabulek](./media/connect-query-vscode/query.png)

## <a name="insert-data"></a>Vložení dat

Spuštěním následujícího příkazu [INSERT](/sql/t-sql/statements/insert-transact-sql) -SQL přidejte do tabulky nový produkt `SalesLT.Product` .

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

2. Stisknutím **kombinace kláves CTRL** + **SHIFT +** +  vložte nový řádek do `Product` tabulky.

## <a name="update-data"></a>Aktualizace dat

Pokud chcete aktualizovat přidaný produkt, spusťte následující příkaz Transact-SQL [Update](/sql/t-sql/queries/update-transact-sql) .

1. Nahraďte předchozí dotaz jedním z těchto:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Stisknutím **kombinace kláves CTRL** + **SHIFT +** +  aktualizujete zadaný řádek v `Product` tabulce.

## <a name="delete-data"></a>Odstranění dat

Pokud chcete nový produkt odebrat, spusťte následující příkaz Transact-SQL [Delete](/sql/t-sql/statements/delete-transact-sql) .

1. Nahraďte předchozí dotaz jedním z těchto:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Stisknutím **kombinace kláves CTRL** + **SHIFT +** +  odstraňte zadaný řádek v `Product` tabulce.

## <a name="next-steps"></a>Další kroky

- Informace o připojení a dotazování pomocí SQL Server Management Studio najdete v tématu [rychlý Start: použití SQL Server Management Studio k připojení k databázi v Azure SQL Database a dotazování na data](connect-query-ssms.md).
- Informace o připojení a dotazování pomocí Azure Portal najdete v tématu [rychlý Start: použití Editoru dotazů SQL v Azure Portal k připojení a dotazování dat](connect-query-portal.md).
- Článek z časopisu MSDN o použití editoru Visual Studio Code najdete v blogovém příspěvku [Vytvoření databáze IDE s rozšířením MSSQL](/archive/msdn-magazine/2017/june/data-points-visual-studio-code-create-a-database-ide-with-mssql-extension).