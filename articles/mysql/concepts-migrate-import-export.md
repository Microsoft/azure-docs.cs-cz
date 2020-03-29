---
title: Import a export – databáze Azure pro MySQL
description: Tento článek vysvětluje běžné způsoby importu a exportu databází v Azure Database for MySQL pomocí nástrojů, jako je MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 83b0a69e063e9427c726216ef873f5a1c97f9582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163722"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrace databáze MySQL pomocí importu a exportu
Tento článek vysvětluje dva běžné přístupy k importu a exportu dat do databáze Azure pro server MySQL pomocí MySQL Workbench. 

## <a name="before-you-begin"></a>Než začnete
Chcete-li projít tento návod, co potřebujete:
- Databáze Azure pro mySQL server podle následujících nastavení [Azure Database for MySQL server using Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- MySQL Workbench [MySQL Workbench Ke stažení](https://dev.mysql.com/downloads/workbench/) nebo jiného nástroje MySQL třetí strany k importu /exportu.

## <a name="use-common-tools"></a>Použití běžných nástrojů
Pomocí běžných nástrojů a nástrojů, jako je MySQL Workbench nebo mysqldump, můžete vzdáleně připojit a importovat nebo exportovat data do Databáze Azure pro MySQL. 

Pomocí těchto nástrojů v klientském počítači s připojením k Internetu se můžete připojit k Azure Database for MySQL. Pro osvědčené postupy zabezpečení použijte připojení šifrované protokolem SSL, jak je popsáno v [článku Konfigurace připojení SSL v Azure Database for MySQL](concepts-ssl-connection-security.md).

Při migraci do Azure Database for MySQL nemusíte přesouvat import a exportovat soubory do žádného zvláštního umístění v cloudu. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Vytvoření databáze na serveru Azure Database for MySQL
Vytvořte prázdnou databázi na serveru Azure Database for MySQL, kam chcete migrovat data. K vytvoření databáze použijte nástroj, jako je MySQL Workbench, Toad nebo Navicat. Databáze může mít stejný název jako databáze, která obsahuje vyřazovaná data, nebo můžete vytvořit databázi s jiným názvem.

Pokud se chcete připojit, vyhledejte informace o připojení v **přehledu** databáze Azure pro MySQL.

![Vyhledání informací o připojení na webu Azure Portal](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Přidejte informace o připojení do pracovní plochy MySQL.

![Připojovací řetězec MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Určení, kdy použít techniky importu a exportu namísto výpisu stavu a obnovení
Pomocí nástrojů MySQL importujte a exportujte databáze do Azure MySQL Database v následujících scénářích. V jiných scénářích můžete mít prospěch z použití [výpisu a obnovení](concepts-migrate-dump-restore.md) přístupu místo. 

- Když potřebujete selektivně vybrat několik tabulek pro import z existující databáze MySQL do databáze Azure MySQL, je nejlepší použít techniku importu a exportu.  Tímto způsobem můžete vynechat všechny nepotřebné tabulky z migrace ušetřit čas a prostředky. Například použijte `--include-tables` nebo `--exclude-tables` přepínač s [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) a `--tables` přepínač s [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Při přesouvání databázových objektů jiných než tabulek explicitně vytvořte tyto objekty. Zahrnout omezení (primární klíč, cizí klíč, indexy), zobrazení, funkce, procedury, aktivační události a všechny ostatní databázové objekty, které chcete migrovat.
- Při migraci dat z jiných externích zdrojů dat, než je databáze MySQL, vytvořte ploché soubory a importujte je pomocí [mysqlimportu](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Ujistěte se, že všechny tabulky v databázi používat modul úložiště InnoDB při načítání dat do databáze Azure pro MySQL. Azure Database for MySQL podporuje jenom modul úložiště InnoDB, takže nepodporuje alternativní moduly úložiště. Pokud vaše tabulky vyžadují alternativní moduly úložiště, nezapomeňte je převést tak, aby používaly formát modulu InnoDB před migrací do databáze Azure pro MySQL. 

Například pokud máte WordPress nebo webovou aplikaci, která používá modul MyISAM, nejprve převeďte tabulky migrací dat do tabulek InnoDB. Pak se obnovte do databáze Azure pro MySQL. Pomocí klauzule `ENGINE=INNODB` nastavte modul pro vytvoření tabulky a potom přenést data do kompatibilní tabulky před migrací. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Doporučení pro sledování výkonu pro import a vývoz
-   Před načtením dat vytvořte seskupené indexy a primární klíče. Načtení dat v pořadí primárního klíče 
-   Zpoždění vytváření sekundárních indexů až po načtení dat. Vytvořte všechny sekundární indexy po načtení. 
-   Před načtením zakažte omezení cizího klíče. Zakázání kontrol y cizího klíče poskytuje významné zvýšení výkonu. Povolte omezení a ověřte data po zatížení, abyste zajistili referenční integritu.
-   Načtení dat paralelně. Vyhněte se příliš mnoho paralelismu, který by způsobil, že byste dosáhli limitu prostředků a monitorování prostředků pomocí metriky dostupné na webu Azure Portal. 
-   V případě potřeby použijte rozdělené tabulky.

## <a name="import-and-export-by-using-mysql-workbench"></a>Import a export pomocí mysql pracovní plochy
Existují dva způsoby, jak exportovat a importovat data v MySQL Workbench. Každý slouží jinému účelu. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Průvodci exportem a importem dat tabulky z kontextové nabídky prohlížeče objektů
![Průvodci mySQL Workbench v kontextové nabídce prohlížeče objektů](./media/concepts-migrate-import-export/p1.png)

Průvodci pro data tabulky podporují operace importu a exportu pomocí souborů CSV a JSON. Zahrnují několik možností konfigurace, jako jsou oddělovače, výběr sloupců a výběr kódování. Každý průvodce můžete provést proti místním nebo vzdáleně připojeným serverům MySQL. Akce importu zahrnuje mapování tabulek, sloupců a typů. 

K těmto průvodcům můžete přistupovat z kontextové nabídky prohlížeče objektů klepnutím pravým tlačítkem myši na tabulku. Potom zvolte **Průvodce exportem dat tabulky** nebo Průvodce **importem dat tabulky**. 

#### <a name="table-data-export-wizard"></a>Průvodce exportem dat tabulky
Následující příklad exportuje tabulku do souboru CSV: 
1. Klikněte pravým tlačítkem myši na tabulku databáze, která má být exportována. 
2. Vyberte **Průvodce exportem dat tabulky**. Vyberte sloupce, které mají být exportovány, odsazení řádku (pokud existuje) a počet (pokud existuje). 
3. Na stránce **Vybrat data pro export** klikněte na **Další**. Vyberte cestu k souboru, csv nebo typ souboru JSON. Vyberte také oddělovač čar, metodu uzavření řetězců a oddělovač polí. 
4. Na stránce **Vybrat umístění výstupního souboru** klepněte na tlačítko **Další**. 
5. Na stránce **Exportovat data** klikněte na **Další**.

#### <a name="table-data-import-wizard"></a>Průvodce importem dat tabulky
Následující příklad importuje tabulku ze souboru CSV:
1. Klikněte pravým tlačítkem myši na tabulku databáze, která má být importována. 
2. Vyhledejte soubor CSV, který chcete importovat, vyberte a klepněte na tlačítko **Další**. 
3. Zaškrtněte cílovou tabulku (novou nebo existující) a zaškrtněte políčko **Zkrátit tabulku před importem** nebo zrušte její zaškrtnutí. Klikněte na **Další**.
4. Vyberte kódování a sloupce, které chcete importovat, a klepněte na tlačítko **Další**. 
5. Na stránce **Importovat data** klikněte na **Další**. Průvodce importuje data odpovídajícím způsobem.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Průvodci exportem a importem dat SQL z podokna Navigátor
Pomocí průvodce exportujte nebo importujte SQL generované z MySQL Workbench nebo generované z příkazu mysqldump. K těmto průvodcům se dostanete z podokna **Navigátor** nebo výběrem **možnosti Server** z hlavní nabídky. Pak vyberte **Import dat** nebo **Import dat**. 

#### <a name="data-export"></a>Export dat
![Export dat mySQL Workbench pomocí podokna Navigátor](./media/concepts-migrate-import-export/p2.png)

K exportu dat MySQL můžete použít kartu **Export dat.** 
1. Vyberte každé schéma, které chcete exportovat, volitelně zvolte konkrétní objekty schématu/tabulky z každého schématu a vygenerujte export. Možnosti konfigurace zahrnují export do složky projektu nebo samostatného souboru SQL, výpis uložených rutin a událostí nebo přeskočení dat tabulky. 
 
   Alternativně použijte **Export sada výsledků** k exportu určité sady výsledků v editoru SQL do jiného formátu, jako je CSV, JSON, HTML a XML. 
3. Vyberte databázové objekty, které chcete exportovat, a nakonfigurujte související možnosti.
4. Chcete-li načíst aktuální objekty, klepněte na **tlačítko Aktualizovat.**
5. Volitelně můžete otevřít kartu **Upřesnit možnosti** a upřesnit operaci exportu. Můžete například přidat zámky tabulek, použít příkazy nahradit místo vložení a identifikátory nabídek se znaky backtick.
6. Chcete-li zahájit proces exportu, klepněte na **tlačítko Spustit export.**


#### <a name="data-import"></a>Import dat
![Import dat mySQL Workbench pomocí navigátoru správy](./media/concepts-migrate-import-export/p3.png)

Kartu Import **dat** můžete použít k importu nebo obnovení exportovaných dat z operace exportu dat nebo z příkazu mysqldump. 
1. Vyberte složku projektu nebo samostatný soubor SQL, zvolte schéma, do kterýchcete importovat, nebo zvolte **Nový** a definujte nové schéma. 
2. Chcete-li zahájit proces importu, klepněte na **tlačítko Spustit import.**

## <a name="next-steps"></a>Další kroky
- Jako další přístup migrace si [přečtěte migrovat databázi MySQL pomocí výpisu a obnovení v Azure Database for MySQL](concepts-migrate-dump-restore.md).
- Další informace o migraci databází do databáze Azure pro MySQL najdete v [průvodci migrací databáze](https://aka.ms/datamigration). 
