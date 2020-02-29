---
title: Import a export – Azure Database for MySQL
description: Tento článek vysvětluje běžné způsoby importu a exportu databází v Azure Database for MySQL pomocí nástrojů, jako je MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 83b0a69e063e9427c726216ef873f5a1c97f9582
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163722"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrace databáze MySQL pomocí importu a exportu
Tento článek vysvětluje dva běžné přístupy k importu a exportu dat na server Azure Database for MySQL pomocí MySQL Workbench. 

## <a name="before-you-begin"></a>Než začnete
Pokud chcete projít tento průvodce, budete potřebovat:
- Server Azure Database for MySQL, a to tak, že pomocí [Azure Portal vytvoříte Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md).
- Stažení aplikace MySQL Workbench [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) nebo jiného nástroje MySQL třetí strany pro import/export.

## <a name="use-common-tools"></a>Použití běžných nástrojů
Pomocí běžných nástrojů a nástrojů, jako je MySQL Workbench nebo mysqldump, můžete vzdáleně připojit a importovat nebo exportovat data do Azure Database for MySQL. 

Pomocí takových nástrojů na klientském počítači připojte k Azure Database for MySQL připojení k Internetu. Použijte připojení šifrované protokolem SSL pro osvědčené postupy zabezpečení, jak je popsáno v tématu [Konfigurace připojení SSL v Azure Database for MySQL](concepts-ssl-connection-security.md).

Při migraci na Azure Database for MySQL nemusíte soubory importu a exportu přesouvat do jakéhokoli zvláštního umístění v cloudu. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Vytvoření databáze na serveru Azure Database for MySQL
Vytvořte prázdnou databázi na serveru Azure Database for MySQL, na kterém chcete migrovat data. K vytvoření databáze použijte nástroj, jako je například MySQL Workbench, TOAD nebo Navicat. Databáze může mít stejný název jako databáze, která obsahuje dumpingová data, nebo můžete vytvořit databázi s jiným názvem.

Pokud se chcete připojit, vyhledejte informace o připojení v **přehledu** Azure Database for MySQL.

![Vyhledat informace o připojení v Azure Portal](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Přidejte informace o připojení do aplikace MySQL Workbench.

![Připojovací řetězec MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Určení, kdy použít techniky pro import a export místo výpisu a obnovení
Pomocí nástrojů MySQL můžete importovat a exportovat databáze do Azure MySQL Database v následujících scénářích. V jiných scénářích můžete místo toho použít [Výpis paměti a obnovení](concepts-migrate-dump-restore.md) . 

- Pokud potřebujete selektivně zvolit několik tabulek, které se mají importovat z existující databáze MySQL do databáze MySQL Azure, je nejlepší použít techniku importu a exportu.  Díky tomu můžete z migrace vynechat všechny nepotřebné tabulky a ušetřit tak čas a prostředky. Například použijte `--include-tables` nebo `--exclude-tables` přepínač s [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) a přepínač `--tables` s [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Pokud přesouváte databázové objekty jiné než tabulky, je třeba tyto objekty explicitně vytvořit. Zahrňte omezení (primární klíč, cizí klíč, indexy), zobrazení, funkce, procedury, aktivační události a všechny další databázové objekty, které chcete migrovat.
- Když migrujete data z jiných zdrojů dat než databáze MySQL, vytvoříte ploché soubory a naimportujete je pomocí [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Zajistěte, aby všechny tabulky v databázi používaly modul úložiště InnoDB při načítání dat do Azure Database for MySQL. Azure Database for MySQL podporuje jenom modul úložiště InnoDB, takže nepodporuje alternativní úložné moduly. Pokud vaše tabulky vyžadují alternativní úložné moduly, nezapomeňte je převést na použití formátu modulu InnoDB před migrací na Azure Database for MySQL. 

Pokud máte například WordPress nebo webovou aplikaci, která používá modul MyISAM, nejprve převeďte tabulky migrací dat do tabulek InnoDB. Pak proveďte obnovení do Azure Database for MySQL. Pomocí `ENGINE=INNODB` klauzule nastavte modul pro vytvoření tabulky a potom přeneste data do kompatibilní tabulky před migrací. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Doporučení k výkonu pro import a export
-   Vytvoření clusterovaných indexů a primárních klíčů před načtením dat. Načte data v pořadí primárních klíčů. 
-   Zpoždění vytváření sekundárních indexů až po načtení dat Po načtení vytvořit všechny sekundární indexy. 
-   Před načtením zakažte omezení cizího klíče. Zakázání kontrol cizích klíčů přináší výrazné zvýšení výkonu. Povolte omezení a ověřte data po zatížení, abyste zajistili referenční integritu.
-   Načtěte data paralelně. Vyhněte se příliš přílišnému paralelismuům, které by mohly mít za následek omezení prostředků, a monitorujte prostředky pomocí metrik dostupných v Azure Portal. 
-   V případě potřeby použijte dělené tabulky.

## <a name="import-and-export-by-using-mysql-workbench"></a>Import a export pomocí aplikace MySQL Workbench
Existují dva způsoby, jak exportovat a importovat data v aplikaci MySQL Workbench. Každá z nich slouží jako jiný účel. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Průvodce exportem a importem dat v tabulce z kontextové nabídky prohlížeče objektů
![Průvodci MySQL Workbench v místní nabídce prohlížeče objektů](./media/concepts-migrate-import-export/p1.png)

Průvodci pro data tabulky podporují operace importu a exportu pomocí souborů CSV a JSON. Zahrnují několik možností konfigurace, jako jsou oddělovače, výběr sloupců a kódování výběru. Jednotlivé průvodce můžete provádět na místních nebo vzdáleně připojených serverech MySQL. Importovaná akce zahrnuje mapování typu tabulka, sloupec a typ. 

K těmto průvodcům můžete přistupovat z kontextové nabídky prohlížeče objektů kliknutím pravým tlačítkem myši na tabulku. Pak zvolte buď **Průvodce exportem dat tabulky** nebo **Průvodce importem dat tabulky**. 

#### <a name="table-data-export-wizard"></a>Průvodce exportem dat tabulky
Následující příklad exportuje tabulku do souboru CSV: 
1. Klikněte pravým tlačítkem na tabulku databáze, která se má exportovat. 
2. Vyberte **Průvodce exportem dat tabulky**. Vyberte sloupce, které se mají exportovat, posun řádku (pokud existuje) a počet (pokud existuje). 
3. Na stránce **Vybrat data pro export** klikněte na **Další**. Vyberte typ souboru cesta, CSV nebo JSON. Vyberte také oddělovač řádků, metodu ohraničujících řetězců a oddělovač polí. 
4. Na stránce **Vybrat umístění výstupního souboru** klikněte na **Další**. 
5. Na stránce **exportovat data** klikněte na tlačítko **Další**.

#### <a name="table-data-import-wizard"></a>Průvodce importem dat tabulky
Následující příklad importuje tabulku ze souboru CSV:
1. Klikněte pravým tlačítkem na tabulku databáze, která se má importovat. 
2. Vyhledejte a vyberte soubor CSV, který chcete naimportovat, a potom klikněte na **Další**. 
3. Vyberte cílovou tabulku (novou nebo existující) a zaškrtněte nebo zrušte zaškrtnutí políčka **zkracování tabulky před importem** . Klikněte na **Další**.
4. Vyberte možnost kódování a sloupce, které chcete importovat, a potom klikněte na tlačítko **Další**. 
5. Na stránce **importovat data** klikněte na **Další**. Průvodce importuje data odpovídajícím způsobem.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Průvodce exportem a importem dat SQL z podokna navigátor
Pomocí Průvodce můžete exportovat nebo importovat SQL generovaný z aplikace MySQL Workbench nebo vygenerovanou z příkazu mysqldump. K těmto průvodcům se dostanete z podokna **navigátor** nebo výběrem možnosti **Server** z hlavní nabídky. Pak vyberte **exportovat data** nebo **importovat data**. 

#### <a name="data-export"></a>Export dat
![Export dat MySQL Workbench pomocí podokna navigátor](./media/concepts-migrate-import-export/p2.png)

Pomocí karty **exportu dat** můžete exportovat data MySQL. 
1. Vyberte každé schéma, které chcete exportovat, volitelně zvolte konkrétní objekty nebo tabulky schématu z každého schématu a vygenerujte export. Mezi možnosti konfigurace patří export do složky projektu nebo samostatného souboru SQL, výpisu uložených rutin a událostí nebo přeskočení dat tabulky. 
 
   Případně můžete pomocí **exportu sady výsledků** exportovat konkrétní sadu výsledků dotazu v editoru SQL do jiného formátu, jako je CSV, JSON, HTML a XML. 
3. Vyberte databázové objekty, které chcete exportovat, a nakonfigurujte související možnosti.
4. Kliknutím na **aktualizovat** načtete aktuální objekty.
5. Volitelně otevřete kartu **Pokročilé možnosti** a upřesněte operaci exportu. Například přidejte zámky tabulek, použijte místo příkazů INSERT a nahraďte identifikátory uvozovek znaky.
6. Kliknutím na **Spustit export** zahajte proces exportu.


#### <a name="data-import"></a>Import dat
![Import dat aplikace MySQL Workbench pomocí navigátoru pro správu](./media/concepts-migrate-import-export/p3.png)

Kartu **Import dat** můžete použít k importu nebo obnovení exportovaných dat z operace exportu dat nebo z příkazu mysqldump. 
1. Vyberte složku projektu nebo soubor SQL, který jste samostatně obsahovali, vyberte schéma, do kterého chcete importovat, nebo vyberte možnost **nové** a definujte nové schéma. 
2. Kliknutím na **Spustit import** zahajte proces importu.

## <a name="next-steps"></a>Další kroky
- Jako další postup migrace si přečtěte téma [migrace databáze MySQL pomocí výpisu a obnovení v Azure Database for MySQL](concepts-migrate-dump-restore.md).
- Další informace o migraci databází do Azure Database for MySQL najdete v [Průvodci migrací databáze](https://aka.ms/datamigration). 
