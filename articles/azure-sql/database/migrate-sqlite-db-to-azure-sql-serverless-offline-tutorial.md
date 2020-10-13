---
title: 'Kurz: jak migrovat databázi SQLite na Azure SQL Database bez serveru'
description: Naučte se provádět offline migraci z SQLite do Azure SQL Database bez serveru pomocí Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: 6e60403344a0341b4aee74a001287c09ba67e114
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448903"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Postup migrace databáze SQLite na Azure SQL Database bez serveru
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pro mnoho lidí poskytuje program SQLite své první zkušenosti s databázemi a programováním SQL. Zahrnutím do mnoha operačních systémů a oblíbených aplikací se bude posazovat jedna z nejoblíbenějších nasazených a využitých databázových strojů na světě. A vzhledem k tomu, že je pravděpodobný, že první databázový stroj používá mnoho lidí, může často končit jako centrální součást projektů nebo aplikací. V takových případech, kdy projekt nebo aplikace přesáhne počáteční implementaci SQLite, mohou vývojáři potřebovat migrovat svá data do spolehlivého a centralizovaného úložiště dat.

Azure SQL Database bez serveru je výpočetní vrstva pro izolované databáze, která automaticky škáluje výpočetní výkon na základě požadavků na zatížení a účtuje množství výpočetních operací za sekundu. Výpočetní vrstva bez serveru taky automaticky pozastaví databáze během neaktivních období, kdy se účtují jenom úložiště, a automaticky obnoví databáze při návratu aktivity.

Až budete postupovat podle následujících kroků, vaše databáze se migruje na Azure SQL Database bez serveru a umožní vám zpřístupnit databázi ostatním uživatelům nebo aplikacím v cloudu a platit jenom za to, co používáte, s minimálními změnami kódu aplikace.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure
- Databáze SQLite2 nebo SQLite3, kterou chcete migrovat
- Prostředí systému Windows
  - Pokud nemáte místní prostředí systému Windows, můžete k migraci použít virtuální počítač s Windows v Azure. Přesuňte a zpřístupněte svůj soubor databáze SQLite na VIRTUÁLNÍm počítači pomocí souborů Azure a Průzkumník služby Storage.

## <a name="steps"></a>Postup

1. Zřídí novou Azure SQL Database na výpočetní úrovni bez serveru.

    ![Snímek obrazovky Azure Portal znázorňující příklad zřizování pro Azure SQL Database bez serveru](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. Ujistěte se, že máte ve svém prostředí Windows k dispozici soubor databáze SQLite. Nainstalujte ovladač pro rozhraní ODBC pro SQLite, pokud ho ještě nemáte (v otevřeném zdroji je dostupná řada), třeba http://www.ch-werner.de/sqliteodbc/) .

3. Vytvořte systémový název DSN pro databázi. Ujistěte se, že používáte aplikaci Správce zdrojů dat, která odpovídá architektuře systému (32 bitů vs 64 bitů). Verzi, kterou používáte, najdete v nastavení systému.

    - Otevřete Správce zdrojů dat ODBC ve vašem prostředí.
    - Klikněte na kartu systémové DSN a pak klikněte na Přidat.
    - Vyberte konektor rozhraní ODBC SQLite, který jste nainstalovali, a zadejte smysluplný název, například sqlitemigrationsource.
    - Nastavte název databáze na soubor. DB.
    - Uložit a ukončit

4. Stáhněte a nainstalujte prostředí Integration runtime v místním prostředí. Nejjednodušší způsob, jak to provést, je možnost Expresní instalace, jak je popsáno v dokumentaci. Pokud si vyžádáte ruční instalaci, budete ji muset zadat pomocí ověřovacího klíče, který může být umístěný v instanci Data Factory:

    - Spouští se ADF (vytváření a monitorování ze služby v Azure Portal).
    - Klikněte na kartu Autor (modrá tužka) vlevo.
    - Klikněte na připojení (vlevo dole) a potom na modul runtime integrace.
    - Přidejte novou Self-Hosted Integration Runtime, zadejte název, vyberte *možnost 2*.

5. Vytvořte novou propojenou službu pro zdrojovou databázi SQLite ve vašem Data Factory.

    ![Snímek obrazovky znázorňující okno prázdné propojené služby v Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. V části **připojení**klikněte v části **propojená služba**na **Nový**.

7. Vyhledejte a vyberte konektor "ODBC".

   ![Snímek obrazovky znázorňující logo konektoru ODBC v okně propojených služeb v Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. Poskytněte propojené službě smysluplný název, například "sqlite_odbc". Z rozevíracího seznamu připojit přes modul Integration runtime vyberte prostředí Integration runtime. Do připojovacího řetězce zadejte níže, nahraďte počáteční proměnnou katalogu příponou FilePath pro soubor. DB a DSN s názvem připojení k systémovému DSN:

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Nastavit typ ověřování na anonymní

10. Otestování připojení

    ![Snímek obrazovky znázorňující úspěšné připojení v Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. Vytvořte další propojenou službu pro cílový SQL Server bez serveru. Vyberte databázi pomocí Průvodce propojenou službou a zadejte přihlašovací údaje pro ověřování SQL.

    ![Snímek obrazovky zobrazující Azure SQL Database vybraný v Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. Extrahujte CREATE TABLE příkazy z databáze SQLite. To můžete provést tak, že v souboru databáze spustíte níže uvedený skript Pythonu.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Vytvořte cílové tabulky v cílovém prostředí SQL bez serveru tak, že zkopírujete příkazy CREATE TABLE ze souboru CreateTables. SQL a spustíte příkazy SQL v editoru dotazů v Azure Portal.

14. Vraťte se na domovskou obrazovku Data Factory a kliknutím na "Kopírování dat" spusťte Průvodce vytvořením úlohy.

    ![Snímek obrazovky s logem Průvodce Kopírování dat v Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. Vyberte všechny tabulky ze zdrojové databáze SQLite pomocí zaškrtávacích políček a namapujte je na cílové tabulky v Azure SQL. Po spuštění úlohy jste úspěšně migrovali data z SQLite do Azure SQL.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu [rychlý Start: vytvoření izolované databáze v Azure SQL Database pomocí Azure Portal](single-database-create-quickstart.md).
- Omezení prostředků najdete v tématu [omezení prostředků výpočetní vrstvy bez serveru](../../sql-database/sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
