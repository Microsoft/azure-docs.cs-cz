---
title: 'Kurz: Jak migrovat databázi SQLite do azure SQL databázového serveru bez'
description: Pomocí Azure Data Factory se naučte provádět offline migraci z SQLite na azure sql databázový server bez.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780506"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Jak migrovat databázi SQLite do azure SQL databázového serveru bez
Pro mnoho lidí, SQLite poskytuje své první zkušenosti s databázemi a SQL programování. Je to zařazení do mnoha operačních systémů a populárních aplikací dělá SQLite jeden z nejrozšířenějších a používaných databázových strojů na světě. A protože je pravděpodobné, že první databázový stroj, který používá mnoho lidí, může často skončit jako centrální součást projektů nebo aplikací. V takových případech, kdy projekt nebo aplikace přeroste počáteční implementaci SQLite, vývojáři mohou potřebovat migrovat svá data do spolehlivého, centralizovanéúložiště dat.

Azure SQL Database serverless je výpočetní vrstva pro jednotlivé databáze, které automaticky škáluje výpočetní prostředky na základě požadavku na pracovní vytížení a účty za množství výpočetních prostředků spoužívaných za sekundu. Výpočetní úroveň bez serveru také automaticky pozastaví databáze během neaktivních období, kdy se účtuje pouze úložiště a automaticky obnoví databáze, když se vrátí aktivita.

Jakmile budete postupovali podle níže uvedených kroků, bude databáze migrována do Azure SQL Database Serverless, což vám umožní zpřístupnit databázi ostatním uživatelům nebo aplikacím v cloudu a platit jenom za to, co používáte, s minimálními změnami kódu aplikace.

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure
- Databáze SQLite2 nebo SQLite3, kterou chcete migrovat
- Prostředí Windows
  - Pokud nemáte místní prostředí Windows, můžete pro migraci použít virtuální počítač s Windows v Azure. Přesuňte a zpřístupněte svůj databázový soubor SQLite na virtuálním počítači pomocí Průzkumníka souborů Azure a Průzkumníka úložiště.

## <a name="steps"></a>Kroky

1. Zřídit novou Azure SQL Database v výpočetní vrstvě bez serveru.

    ![Snímek obrazovky portálu Azure s příkladem zřizování pro azure sql databázový server bez](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Ujistěte se, že máte svůj databázový soubor SQLite k dispozici ve vašem prostředí Windows. Nainstalujte ovladač SQLite ODBC, pokud jej ještě nemáte (v Open Source http://www.ch-werner.de/sqliteodbc/)je jich k dispozici mnoho, například .

3. Vytvořte systémové dsn pro databázi. Ujistěte se, že používáte aplikaci Správce zdroje dat, která odpovídá vaší systémové architektuře (32bitová vs 64bitová). Kterou verzi používáte v nastavení systému.

    - Otevřete správce zdroje dat ROZHRANÍ ODBC ve vašem prostředí.
    - Klikněte na kartu Systémové dsn systému a klikněte na tlačítko "Přidat"
    - Vyberte nainstalovaný konektor OdBC SQLite a pojmenujte připojení smysluplným názvem, například sqlitemigrationsource
    - Nastavení názvu databáze na soubor DB
    - Uložit a ukončit

4. Stáhněte a nainstalujte runtime integrace s vlastním hostitelem. Nejjednodušší způsob, jak to udělat, je možnost expresní instalace, jak je podrobně popsáno v dokumentaci. Pokud se rozhodnete pro ruční instalaci, budete muset aplikaci poskytnout ověřovací klíč, který může být umístěn v instanci Data Factory:

    - Spuštění adf (Autor a monitor ze služby na webu Azure Portal)
    - Klikněte na záložku "Autor" (Modrá tužka) vlevo
    - Klikněte na Připojení (vlevo dole), pak integrační runtimes
    - Přidejte nový vlastní hostovaný integrační runtime, pojmenujte jej a vyberte *možnost 2*.

5. Vytvořte novou propojenou službu pro zdrojovou databázi SQLite ve vaší datové továrně.

    ![snímek obrazovky s prázdnou čepelí propojených služeb v Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. V části Připojení klikněte v části Propojená služba na Nový.

7. Vyhledejte a vyberte konektor "ODBC"


    ![snímek obrazovky s logem konektoru ODBC v okně propojených služeb v Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Pojmenujte propojenou službu smysluplným názvem, například "sqlite_odbc". Vyberte dobu runtime integrace z rozevíracího souboru "Připojit prostřednictvím integračního runtime". Do připojovacího řetězce zadejte níže a nahrazujte proměnnou Počáteční katalog pathem souboru pro soubor DB a dsn názvem systémového připojení DSN: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Nastavení typu ověřování na anonymní

10. Otestování připojení

    ![snímek obrazovky s úspěšným připojením v Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Vytvořte další propojenou službu pro cíl SQL bez serveru. Vyberte databázi pomocí průvodce propojenou službou a zadejte pověření ověřování SQL.

    ![snímek obrazovky s azure SQL databází vybranou v Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Extrahujte příkazy CREATE TABLE z databáze SQLite. Můžete to provést spuštěním níže uvedeného skriptu Pythonu v databázovém souboru.

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

13. Vytvořte tabulky podest v cílovém prostředí SQL bez serveru zkopírováním příkazů tabulky CREATE ze souboru CreateTables.sql a spuštěním příkazů SQL v Editoru dotazů na webu Azure Portal.

14. Vraťte se na domovskou obrazovku data factory a klikněte na "Kopírovat data" pro spuštění průvodce vytvořením úlohy.

    ![snímek obrazovky s logem Průvodce kopírováním dat v Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Vyberte všechny tabulky ze zdrojové databáze SQLite pomocí zaškrtávacích políček a namapujte je na cílové tabulky v Azure SQL. Po spuštění úlohy jste úspěšně migrovali data z SQLite do Azure SQL!

## <a name="next-steps"></a>Další kroky

- Další informace najdete v [tématu Úvodní příručka: Vytvoření jedné databáze v Azure SQL Database pomocí webu Azure Portal](sql-database-single-database-get-started.md).
- Omezení prostředků naleznete v [tématu Limity prostředků výpočetní úrovně bez serveru](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
