---
title: Import a export ve službě Azure Database pro databázi MySQL
description: Tento článek vysvětluje běžné způsoby import a export databáze v databázi Azure pro databázi MySQL, pomocí nástrojů, jako je například MySQL Workbench.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 3f4c813f7c107f769f32cdd6ab66790f937f1c9b
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736989"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migraci databáze MySQL pomocí import a export
Tento článek vysvětluje dvě běžné přístupy k importu a exportu dat do Azure databáze MySQL serveru pomocí MySQL Workbench. 

## <a name="before-you-begin"></a>Než začnete
Chcete-li krok tímto průvodcem postupy, je třeba:
- Databázi Azure pro server databáze MySQL, pomocí následujících [vytvoření databáze Azure pro server databáze MySQL pomocí portálu Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- MySQL Workbench [Stáhnout](https://dev.mysql.com/downloads/workbench/), nebo jiný nástroj MySQL k importu a exportu.

## <a name="use-common-tools"></a>Běžné nástroje pro použití
Použijte běžné nástroje, například MySQL Workbench, Toad nebo Navicat vzdáleně připojit a importovat nebo exportovat data do Azure databáze pro databázi MySQL. 

Pomocí těchto nástrojů na klientský počítač s připojením k Internetu pro připojení k databázi Azure pro databázi MySQL. Použít připojení zašifrované protokolem SSL pro osvědčené postupy zabezpečení, jak je popsáno v [připojení konfigurace protokolu SSL ve službě Azure Database pro databázi MySQL](concepts-ssl-connection-security.md).

Není nutné přesunout import a export souborů do libovolného umístění, speciální cloudu při migraci na Azure Database pro databázi MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Vytvoření databáze na databázi Azure pro server databáze MySQL
Vytvořte prázdnou databázi na databázi Azure pro server databáze MySQL, ve které chcete migrovat data. Pomocí nástroje, jako je MySQL Workbench, Toad nebo Navicat k vytvoření databáze. Databáze může mít stejný název jako databáze, která obsahuje dumpingových data, nebo můžete vytvořit databázi s jiným názvem.

Se připojí, vyhledejte informace o připojení v **přehled** Azure databáze pro databázi MySQL.

![Vyhledat informace o připojení na portálu Azure](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Přidáte informace o připojení do databáze MySQL Workbench.

![MySQL Workbench připojovací řetězec](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Kdy použít import a export techniky místo výpis a obnovení
Pomocí nástrojů MySQL pro import a export databáze do databáze MySQL Azure v následujících scénářích. V jiných scénářích může být vhodné použití [dump a obnovení](concepts-migrate-dump-restore.md) přístupu místo. 

- Když potřebujete selektivně vybrat několik tabulek pro import z existující databáze MySQL do databáze MySQL Azure, je vhodné použít import a export techniku.  Díky tomu je možné vynechat všechny nepotřebné tabulky z migrace ušetříte čas a prostředky. Například použít `--include-tables` nebo `--exclude-tables` přepínač s [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) a `--tables` přepínač s [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Když přesouváte databázi objekty než tabulky, vytvořte explicitně těchto objektů. Zahrnují omezení (primární klíč, cizí klíč, indexy), zobrazení, funkce, postupy, aktivační události a všechny ostatní databázové objekty, které chcete migrovat.
- Když se migraci dat z externích zdrojů dat. než databázi MySQL, vytvořit plochých souborů a importovat pomocí [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Ujistěte se, že všechny tabulky v databázi použít modul InnoDB úložiště při načítání dat do Azure databáze pro databázi MySQL. Azure databáze pro databázi MySQL podporuje pouze InnoDB modul úložiště, tak nepodporuje moduly alternativní úložiště. Pokud vaše tabulky vyžadují moduly alternativní úložiště, je nutné převést tak, aby používala formát modul InnoDB před migrací do Azure Database pro databázi MySQL. 

Například pokud máte WordPress nebo webovou aplikaci, která používá modul MyISAM, nejprve převeďte tabulky migraci dat do InnoDB tabulek. Po obnovení databáze Azure pro databázi MySQL. Použijte klauzuli `ENGINE=INNODB` nastavit modul pro vytvoření tabulky, a potom přenést data do tabulky kompatibilní před migrací. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Výkon doporučení pro import a export
-   Před načtením dat vytvořte Clusterované indexy a primární klíče. Načtení dat do primární klíče pořadí. 
-   Zpoždění vytváření sekundárních indexů až po dat je načten. Všechny sekundární indexy vytvořte po načtení. 
-   Zakažte omezení cizích klíčů před načtením. Zakázání cizího klíče kontroly poskytuje výrazné zvýšení výkonu. Povolte omezení a ověřit data po zatížení zajistit referenční integrity.
-   Načtení dat paralelně. Vyhněte se příliš mnoho paralelismus, který by vést k dosáhl limitu prostředků a sledování prostředků pomocí metriky, které jsou k dispozici na portálu Azure. 
-   Dělené tabulky v případě nutnosti použijte.

## <a name="import-and-export-by-using-mysql-workbench"></a>Import a export pomocí MySQL Workbench
Existují dva způsoby, jak exportovat a importovat data v MySQL Workbench. Každý slouží k jinému účelu. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Data tabulky exportovat a importovat průvodců z kontextové nabídky Prohlížeč objektů
![MySQL Workbench průvodců v místní nabídce Prohlížeč objektů](./media/concepts-migrate-import-export/p1.png)

Průvodců pro data tabulky podporují import a export operace pomocí souborů sdílených svazků clusteru a JSON. Obsahují několik možností konfigurace, například oddělovačů, výběr sloupců a výběru kódování. Každý průvodce pro místní nebo vzdálené připojení MySQL servery, můžete provést. Import akce obsahuje tabulky, sloupce a mapování typu. 

Tito průvodci dostanete z kontextové nabídky Prohlížeč objektů kliknutím pravým tlačítkem na tabulku. Potom vyberte buď **Průvodce exportem dat tabulky** nebo **Průvodce importem tabulky dat**. 

#### <a name="table-data-export-wizard"></a>Průvodce exportem dat tabulky
Následující příklad exportuje v tabulce do souboru CSV: 
1. Klikněte pravým tlačítkem v tabulce databáze pro export. 
2. Vyberte **tabulky Průvodce exportem dat**. Vyberte sloupce, které chcete exportovat, řádek posun (pokud existuje) a počet (pokud existuje). 
3. Na **vyberte data pro export** klikněte na tlačítko **Další**. Vyberte cestu k souboru, CSV nebo JSON typ souboru. Také vyberte oddělovač řádků, způsob uzavření řetězce a pole oddělovače. 
4. Na **vyberte výstupní soubor umístění** klikněte na tlačítko **Další**. 
5. Na **Export dat** klikněte na tlačítko **Další**.

#### <a name="table-data-import-wizard"></a>Průvodce importem tabulky dat
Následující příklad importuje tabulky ze souboru CSV:
1. Klikněte pravým tlačítkem v tabulce databáze určených k importu. 
2. Vyhledejte a vyberte soubor CSV importovat, a pak klikněte na **Další**. 
3. Vyberte cílové tabulky (nový nebo existující) a zaškrtněte nebo zrušte **Truncate table před importem** zaškrtávací políčko. Klikněte na **Další**.
4. Vyberte kódování a sloupce, které chcete importovat a potom klikněte na **Další**. 
5. Na **importovat data** klikněte na tlačítko **Další**. Průvodce importuje data odpovídajícím způsobem.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SQL data exportovat a importovat průvodců z podokna Navigátor
Průvodce použijte k exportu nebo importu SQL z databáze MySQL Workbench generovány nebo vygenerovat z mysqldump příkazu. Přístup z těchto průvodců **Navigátor** podokně nebo výběrem **Server** z hlavní nabídky. Potom vyberte **exportu dat** nebo **Import dat**. 

#### <a name="data-export"></a>Export dat
![Export dat MySQL Workbench z panelu Navigátor](./media/concepts-migrate-import-export/p2.png)

Můžete použít **exportu dat** a exportu dat MySQL. 
1. Vyberte každý schéma, které chcete exportovat, volitelně vybrat konkrétní schématu objekty nebo tabulky z každé schéma a generovat exportu. Možnosti konfigurace zahrnují export do složky projektu nebo samostatný soubor SQL, dump uložené rutiny a události nebo přeskočit dat v tabulce. 
 
   Můžete taky použít **exportovat sady výsledků** export konkrétní sady výsledků v editoru jazyka SQL do jiného formátu, například CSV, JSON, HTML a XML. 
3. Vyberte databázi objekty pro export a související možnosti konfigurace.
4. Klikněte na tlačítko **aktualizovat** načíst aktuální objekty.
5. Volitelně můžete otevřít **pokročilé možnosti** kartě Upřesnit operace exportu. Například umožňuje přidáte zámky tabulky, použijte nahradit místo příkazech insert a identifikátory uvozovky s backtick znaky.
6. Klikněte na tlačítko **spusťte Export** k zahájení procesu exportu.


#### <a name="data-import"></a>Import dat
![Import dat MySQL Workbench pomocí správy Navigátor](./media/concepts-migrate-import-export/p3.png)

Můžete použít **importování dat** a importujte nebo obnovte exportovat data z operace exportu dat nebo z mysqldump příkazu. 
1. Zvolte samostatný SQL soubor nebo složku projekt a potom vyberte schéma tak, aby importovat do, nebo zvolte **nový** definovat nové schéma. 
2. Klikněte na tlačítko **spusťte Import** zahájíte proces importu.

## <a name="next-steps"></a>Další postup
- Jako další migrace přístup pro čtení [migrací pomocí databáze MySQL dump a obnovení ve službě Azure Database pro databázi MySQL](concepts-migrate-dump-restore.md).
- Další informace o migraci databáze pro databázi MySQL, Azure najdete [příručka k migraci databáze](http://aka.ms/datamigration). 
