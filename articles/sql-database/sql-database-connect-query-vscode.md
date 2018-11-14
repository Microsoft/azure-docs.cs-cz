---
title: 'VS Code: Připojení a dotazování dat ve službě Azure SQL Database | Dokumentace Microsoftu'
description: Zjistěte, jak se připojit ke službě SQL Database pomocí nástroje Visual Studio Code. Potom spustíte příkazy jazyka Transact-SQL (T-SQL) k dotazování a úpravě dat.
keywords: Připojení k službě SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 627d4a353e2e6582ed7b579a3f9d74594c6c10fb
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912964"
---
# <a name="quickstart-azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Rychlý start: Azure SQL Database – Použití Visual Studio Code k připojení a dotazování dat

[Visual Studio Code](https://code.visualstudio.com/docs) je grafický editor kódu pro Linux, macOS a Windows, který podporuje rozšíření, včetně [rozšíření mssql](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Serveru, Azure SQL Database a SQL Data Warehousu. Tento rychlý start ukazuje použití Visual Studio Code pro připojení k databázi SQL Azure a následné použití příkazů jazyka Transact-SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi.

## <a name="prerequisites"></a>Požadavky

Tento rychlý start používá jako výchozí bod prostředky vytvořené v některém z těchto rychlých startů:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

#### <a name="install-vs-code"></a>Instalace VSCode

Než začnete, ujistěte se, že máte nainstalovanou nejnovější verzi nástroje [Visual Studio Code](https://code.visualstudio.com/Download) a načtené [rozšíření mssql](https://aka.ms/mssql-marketplace). Pokyny k instalaci rozšíření mssql najdete v tématu popisujícím [instalaci nástroje VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) a [mssql pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql). 

## <a name="configure-vs-code"></a>Konfigurace VS Code 

### <a name="mac-os"></a>**Mac OS**
Pro macOS musíte nainstalovat OpenSSL, což je předpoklad pro rozhraní .Net Core, které rozšíření mssql používá. Otevřete terminál a zadejte následující příkazy, abyste nainstalovali **brew** a **OpenSSL**. 

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

## <a name="sql-server-connection-information"></a>Informace o připojení k SQL serveru

Získejte informace o připojení potřebné pro připojení k databázi SQL Azure. V dalších postupech budete potřebovat plně kvalifikovaný název serveru, název databáze a přihlašovací údaje.

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="set-language-mode-to-sql"></a>Nastavení jazykového režimu na SQL

Nastavte v nástroji Visual Studio Code jazykový režim na **SQL**, abyste povolili příkazy mssql a technologii IntelliSense jazyka T-SQL.

1. Otevřete nové okno nástroje Visual Studio Code. 

2. Klikněte na **Prostý text** v pravém dolním rohu stavového řádku.
3. V rozevírací nabídce **Vybrat režim jazyka**, která se otevře, zadejte **SQL** a potom stisknutím klávesy **ENTER** nastavte režim jazyka SQL. 

   ![Režim jazyka SQL](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database"></a>Připojení k databázi

Pomocí nástroje Visual Studio Code navažte připojení k serveru služby Azure SQL Database.

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že máte připravené informace o vašem serveru, databázi a přihlašovacích údajích. Jakmile začnete zadávat informace o profilu připojení a pak změníte fokus na něco jiného než Visual Studio Code, budete muset vytváření profilu připojení začít znovu.
>

1. V nástroji VS Code stisknutím kombinace kláves **CTRL + SHIFT + P** (nebo **F1**) otevřete paletu příkazů.

2. Zadejte **sqlcon** a stiskněte klávesu **ENTER**.

3. Stisknutím klávesy **ENTER** vyberte **Vytvořit profil připojení**. Tím se vytvoří profil připojení pro vaši instanci SQL Serveru.

4. Postupujte podle výzev a zadejte vlastnosti připojení pro nový profil připojení. Po zadání všech hodnot pokračujte stisknutím klávesy **ENTER**. 

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Název serveru | Plně kvalifikovaný název serveru | Název musí vypadat přibližně takto: **mynewserver20170313.database.windows.net**. |
   | **Název databáze** | mySampleDatabase | Název databáze, ke které se chcete připojit. |
   | **Ověřování** | Přihlášení k SQL serveru| Ověřování SQL je jediný typ ověřování, který jsme v tomto kurzu nakonfigurovali. |
   | **Uživatelské jméno** | Účet správce serveru | Jedná se o účet, který jste zadali při vytváření serveru. |
   | **Heslo (Přihlášení SQL)** | Heslo pro účet správce serveru | Jedná se o heslo, které jste zadali při vytváření serveru. |
   | **Uložit heslo?** | Ano nebo Ne | Pokud nechcete pokaždé zadávat heslo, vyberte Ano. |
   | **Zadejte název pro tento profil.** | Název profilu, jako například **mySampleDatabase** | Pokud uložíte název profilu, zrychlíte připojování k dalším přihlašovacím profilům. | 

5. Stisknutím klávesy **ESC** zavřete zprávu informující o vytvoření a připojení profilu.

6. Na stavovém řádku ověřte připojení.

   ![Stav připojení](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>Dotazování dat

Použijte následující kód k zadání dotazu na Top 20 produktů podle kategorie pomocí příkazu jazyka Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx).

1. V okně **Editor** zadejte do prázdného okna dotazu následující dotaz:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Stisknutím kombinace kláves **CTRL + SHIFT + E** načtěte data z tabulek Product a ProductCategory.

    ![Dotaz](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Vložení dat

Použijte následující kód k vložení nového produktu do tabulky SalesLT.Product pomocí příkazu jazyka Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx).

1. V okně **Editor** smažte předchozí dotaz a zadejte následující dotaz:

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

2. Stisknutím kombinace kláves **CTRL + SHIFT + E** vložte nový řádek do tabulky Product.

## <a name="update-data"></a>Aktualizace dat

Použijte následující kód k aktualizaci nového produktu, který jste přidali dříve, pomocí příkazu jazyka Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx).

1.  V okně **Editor** smažte předchozí dotaz a zadejte následující dotaz:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Stisknutím kombinace kláves **CTRL + SHIFT + E** aktualizujte zadaný řádek v tabulce Product.

## <a name="delete-data"></a>Odstranění dat

Použijte následující kód k odstranění nového produktu, který jste přidali dříve, pomocí příkazu jazyka Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql).

1. V okně **Editor** smažte předchozí dotaz a zadejte následující dotaz:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Stisknutím kombinace kláves **CTRL + SHIFT + E** odstraňte zadaný řádek v tabulce Product.

## <a name="next-steps"></a>Další kroky

- Informace o připojení a dotazování pomocí aplikace SQL Server Management Studio najdete v tématu [Připojení a dotazování pomocí SSMS](sql-database-connect-query-ssms.md).
- Informace o připojení a dotazování pomocí webu Azure Portal najdete v tématu[Připojení a dotazování pomocí editoru dotazů SQL webu Azure Portal](sql-database-connect-query-portal.md).
- Článek z časopisu MSDN o použití editoru Visual Studio Code najdete v blogovém příspěvku [Vytvoření databáze IDE s rozšířením MSSQL](https://msdn.microsoft.com/magazine/mt809115).
