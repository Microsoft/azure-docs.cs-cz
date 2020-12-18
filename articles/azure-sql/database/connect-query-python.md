---
title: Použití Pythonu k dotazování databáze
description: V tomto tématu se dozvíte, jak pomocí Pythonu vytvořit program, který se připojí k databázi v Azure SQL Database a provede dotaz pomocí příkazů jazyka Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 5bad2293078711456ff06d998c43b87ecc1c9b76
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674256"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Rychlý Start: použití Pythonu k dotazování databáze ve Azure SQL Database nebo spravované instanci SQL Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

V tomto rychlém startu pomocí Pythonu se připojíte k Azure SQL Database nebo spravované instanci SQL Azure a pro dotazování dat použijete příkazy T-SQL.

## <a name="prerequisites"></a>Požadavky

Co budete potřebovat k dokončení tohoto rychlého startu:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 a související software

  # <a name="macos"></a>[macOS](#tab/macos)

  K instalaci homebrew a Pythonu, ovladače ODBC a nástroje SQLCMD a ovladače Pythonu pro SQL Server použijte kroky **1,2**, **1,3** a **2,1** v části [vytváření aplikací v Pythonu pomocí SQL Server na MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Další informace najdete v tématu [ovladač Microsoft ODBC na MacOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Pokud chcete nainstalovat Python a další požadované balíčky, použijte `sudo apt-get install python python-pip gcc g++ build-essential` .

  Informace o instalaci ovladače ODBC, SQLCMD a ovladače Pythonu pro SQL Server najdete v tématu [Konfigurace prostředí pro vývoj Pythonu v pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Další informace najdete v tématu [ovladač Microsoft ODBC na platformě Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Informace o instalaci Pythonu, ovladače ODBC a nástroje SQLCMD a ovladače Pythonu pro SQL Server najdete v tématu [Konfigurace prostředí pro vývoj Pythonu v pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Další informace najdete v tématu [ovladač Microsoft ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---
Další prozkoumání Pythonu a databáze v Azure SQL Database najdete v tématu [knihovny Azure SQL Database pro Python](/python/api/overview/azure/sql), [úložiště pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)a [ukázku pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-server-connection-information"></a>Získat informace o připojení k serveru

Získejte informace o připojení, které potřebujete pro připojení k databázi v Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. Přejít na stránku **databáze SQL**  nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro databázi v Azure SQL Database nebo plně kvalifikovaného názvu serveru (nebo IP adresy) vedle **hostitele** spravované instance Azure SQL nebo SQL Server na virtuálním počítači Azure. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

> [!NOTE]
> Informace o připojení pro SQL Server na virtuálním počítači Azure najdete v tématu [připojení k instanci SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database"></a>Vytvoření kódu pro dotazování databáze 

1. V textovém editoru vytvořte nový soubor s názvem *sqltest.py*.  
   
1. Přidejte následující kód. Nahraďte vlastní hodnoty pro \<server> , \<database> , a \<username> \<password> .
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která můžete zvolit jako zdroj při vytváření databáze. Pokud má vaše databáze jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Spuštění kódu

1. Na příkazovém řádku spusťte následující příkaz:

   ```cmd
   python sqltest.py
   ```

1. Ověřte, že jsou vraceny prvních 20 řádků kategorie/produktu, a pak zavřete příkazové okno.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze v Azure SQL Database](design-first-database-tutorial.md)
- [Ovladače Microsoft Pythonu pro SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Středisko pro vývojáře v Pythonu](https://azure.microsoft.com/develop/python/?v=17.23h)
