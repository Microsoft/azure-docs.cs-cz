---
title: Import a export ve službě Azure Database for MySQL
description: Tento článek popisuje běžné způsoby, jak importovat a exportovat databáze ve službě Azure Database for MySQL, pomocí nástrojů, jako jsou aplikace MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: fa72037c8f54271f5651667765c5d5e2e9c03619
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545514"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrace databáze MySQL s použitím import a export
Tento článek vysvětluje dvě běžné přístupy k importu a exportu dat na serveru Azure Database for MySQL pomocí aplikace MySQL Workbench. 

## <a name="before-you-begin"></a>Před zahájením
Pro jednotlivé kroky v této příručce s postupy, musíte:
- Azure Database for MySQL serveru pomocí následujících [vytvoření Azure Database for MySQL pomocí webu Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- Aplikace MySQL Workbench [stáhli](https://dev.mysql.com/downloads/workbench/), nebo jiný nástroj MySQL pro import a export.

## <a name="use-common-tools"></a>Použít běžné nástroje
Pomocí běžných nástrojů, jako jsou aplikace MySQL Workbench, Toad nebo Navicat vzdáleně připojit a importovat nebo exportovat data do služby Azure Database for MySQL. 

Připojení ke službě Azure Database for MySQL pomocí těchto nástrojů v klientském počítači s připojením k Internetu. Použít připojení zašifrované protokolem SSL pro osvědčené postupy zabezpečení, jak je popsáno v [konfigurace připojení SSL ve službě Azure Database for MySQL](concepts-ssl-connection-security.md).

Není potřeba přesunout import a export souborů do jakéhokoli umístění speciální cloudových při migraci do služby Azure Database for MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Vytvoření databáze v serveru Azure Database for MySQL
Vytvoření prázdné databáze v Azure Database for MySQL serveru, ve které chcete migrovat data. Pomocí nástroje, jako je aplikace MySQL Workbench, Toad nebo Navicat k vytvoření databáze. Databáze může mít stejný název jako databázi obsahující vypsaná bitová kopie řidicího dat nebo můžete vytvořit databázi s jiným názvem.

Se připojí, vyhledejte informace o připojení v **přehled** z Azure Database for MySQL.

![Najít informace o připojení webu Azure Portal](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Přidejte informace o připojení k MySQL Workbench.

![Aplikace MySQL Workbench připojovací řetězec](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Určuje, kdy použít import a export techniky místo výpis a obnovení
Pomocí nástroje MySQL pro import a export databáze do služby Azure Database for MySQL v následujících scénářích. V jiných scénářích může přinést použití [výpisu a obnovení](concepts-migrate-dump-restore.md) místo toho dosáhnout. 

- Když je potřeba si zvolit několik tabulek pro import z existující databáze MySQL do služby Azure Database for MySQL, je vhodné použít import a export technika.  Díky tomu můžete vynechat všechny nepotřebné tabulky z migrace jak šetřit čas i prostředky. Například použít `--include-tables` nebo `--exclude-tables` přepínači s [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) a `--tables` přepínači s [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Když přesouváte i jiné než tabulkové databázových objektů, explicitně vytvořte tyto objekty. Zahrnují omezení (primární klíč, cizí klíče, indexy), zobrazení, funkce, procedury, triggery a ostatních databázových objektů, které chcete migrovat.
- Když migrujete data z externích zdrojů dat než databázi MySQL, vytvoříte plochých souborů a importovat je pomocí [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Ujistěte se, že všechny tabulky v databázi použít modul InnoDB úložiště při načítání dat do služby Azure Database for MySQL. Azure Database for MySQL podporuje pouze úložiště modul InnoDB, proto nepodporuje moduly alternativní úložiště. Pokud vaše tabulky vyžadují moduly alternativní úložiště, je potřeba převést tak, aby použijte formát modul InnoDB před migrací do služby Azure Database for MySQL. 

Například pokud máte WordPress nebo do webové aplikace, která využívá modul MyISAM, nejprve převeďte tabulky a migrujte data do tabulek InnoDB. Po obnovení do služby Azure Database for MySQL. Použití klauzule `ENGINE=INNODB` nastavit modul pro vytváření tabulek a pak přenést data do tabulky kompatibilní před migrací. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Doporučení k výkonu pro import a export
-   Před načtením dat vytvořte Clusterované indexy a primárního klíče. Načtení dat do primární pořadí klíčů. 
-   Zpoždění vytváření sekundárních indexů až po data se načtou. Po načtení vytvořte všechny sekundární indexy. 
-   Zákaz omezení cizího klíče před načtením. Zakázání cizího klíče kontroly poskytuje významného zvýšení výkonu. Povolte omezení a ověřit data po načtení k zajištění referenční integrity.
-   Načtení dat paralelně. Vyhněte se příliš mnoho paralelismu, který by vést k dosažení limitu prostředků a monitorovat prostředky s využitím metrik, které jsou k dispozici na webu Azure Portal. 
-   Dělené tabulky v případě potřeby použijte.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importovat a exportovat pomocí aplikace MySQL Workbench
Existují dva způsoby, jak exportovat a importovat data v MySQL Workbench. Každý slouží k jinému účelu. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Data v tabulce exportu a importu průvodců z místní nabídky prohlížeče objektů
![Aplikace MySQL Workbench průvodců v místní nabídce prohlížeče objektů](./media/concepts-migrate-import-export/p1.png)

Průvodcích tabulkových dat podporují importu a exportu přes soubory CSV a JSON. Patří mezi ně několik možností konfigurace, jako je například oddělovače, výběr sloupců a výběru kódování. Můžete provádět jednotlivých průvodců proti místní nebo vzdálené připojené servery MySQL. Import akce obsahuje tabulky, sloupce a mapování typu. 

Tito průvodci z místní nabídky prohlížeče objektů můžete přistupovat kliknutím pravým tlačítkem myši tabulku. Klikněte na tlačítko buď **Průvodce exportem dat tabulky** nebo **Průvodce importem dat tabulky**. 

#### <a name="table-data-export-wizard"></a>Průvodce exportem dat tabulky
Následující příklad exportuje do souboru CSV v tabulce: 
1. Klikněte pravým tlačítkem na tabulku databáze, kterou chcete exportovat. 
2. Vyberte **tabulky Průvodce exportem dat**. Vyberte sloupce, které chcete exportovat, řádek posun (pokud existuje) a počet (pokud existuje). 
3. Na **vyberte data pro export** klikněte na **Další**. Vyberte cestu k souboru, typ souboru CSV nebo JSON. Také vyberte oddělovač řádků, způsob uzavření řetězců a oddělovač polí. 
4. Na **umístění souboru vyberte výstupu** klikněte na **Další**. 
5. Na **exportovat data** klikněte na **Další**.

#### <a name="table-data-import-wizard"></a>Průvodce importem tabulky dat
Následující příklad importuje ze souboru CSV:
1. Klikněte pravým tlačítkem na tabulku databáze, kterou chcete importovat. 
2. Vyhledejte a vyberte soubor sdíleného svazku clusteru naimportují, a potom klikněte na **Další**. 
3. Vyberte cílové tabulky (nový nebo existující) a zaškrtněte nebo zrušte zaškrtnutí **Truncate table před importem** zaškrtávací políčko. Klikněte na tlačítko **Další**.
4. Vyberte možnost kódování a sloupce, které chcete importovat a potom klikněte na tlačítko **Další**. 
5. Na **importovat data** klikněte na **Další**. Průvodce importuje data odpovídajícím způsobem.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SQL data export a import průvodců z navigační podokno
Průvodce použijte k exportu nebo importu SQL generován z aplikace MySQL Workbench nebo nevygeneruje příkaz mysqldump. Přístup k těmto průvodcům **Navigátor** podokně nebo tak, že vyberete **Server** z hlavní nabídky. Potom vyberte **Export dat** nebo **Import dat**. 

#### <a name="data-export"></a>Export dat
![Export dat aplikace MySQL Workbench pomocí navigační podokno](./media/concepts-migrate-import-export/p2.png)

Můžete použít **Export dat** kartu exportování dat MySQL. 
1. Vyberte každou schéma, které chcete exportovat, volitelně vybrat konkrétní schématu objekty/tabulky z každé schéma a generovat exportu. Možnosti konfigurace zahrnují export do složky projektu nebo samostatný soubor SQL, vypsat uložené procedury a události nebo přeskočit data v tabulce. 
 
   Můžete taky použít **exportovat sady výsledků** exportovat konkrétní sadu výsledků, v editoru jazyka SQL do jiného formátu, jako je například CSV, JSON, HTML a XML. 
3. Vyberte databázové objekty pro export a související možnosti konfigurace.
4. Klikněte na tlačítko **aktualizovat** načíst aktuální objekty.
5. Volitelně můžete otevřít **rozšířené možnosti** kartu Upřesnit operace exportu. Například přidáte zámky pro tabulky, použijte nahradit místo příkazů insert a identifikátory nabídku s prvními znaků.
6. Klikněte na tlačítko **spusťte Export** zahájíte proces exportu.


#### <a name="data-import"></a>Import dat
![Import dat MySQL Workbench pomocí správy Navigátor](./media/concepts-migrate-import-export/p3.png)

Můžete použít **importování dat** kartu importujte nebo obnovte exportovat data z operace exportu dat nebo z příkazu mysqldump. 
1. Zvolte složku projektu nebo samostatný soubor SQL, zvolte možnost schématu a importovat do, nebo zvolte **nový** definovat nové schéma. 
2. Klikněte na tlačítko **spusťte Import** zahajte proces importu.

## <a name="next-steps"></a>Další postup
- Jako jiný přístup k migraci, přečtěte si [migrace vaší databáze MySQL pomocí výpisu a obnovení ve službě Azure Database for MySQL](concepts-migrate-dump-restore.md).
- Další informace o migraci databází do služby Azure Database for MySQL, najdete v článku [Průvodce migrací databází](https://aka.ms/datamigration). 
