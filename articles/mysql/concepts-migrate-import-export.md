---
title: Import a export – Azure Database for MySQL
description: Tento článek vysvětluje běžné způsoby importu a exportu databází v Azure Database for MySQL pomocí nástrojů, jako je MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 4d553f6c87d1044f8bde7460a0ea7bf123dd1851
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450068"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrace databáze MySQL pomocí importu a exportu

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Tento článek vysvětluje dva běžné přístupy k importu a exportu dat na server Azure Database for MySQL pomocí MySQL Workbench.

Podrobné a komplexní pokyny k migraci najdete v [příručce k migraci prostředků](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

Další scénáře migrace najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Požadavky

Než začnete s migrací databáze MySQL, musíte:
- Vytvořte [Azure Database for MySQL server pomocí Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- Stáhněte a nainstalujte [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) nebo jiný nástroj MySQL třetí strany pro import a export.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Vytvoření databáze na serveru Azure Database for MySQL
Vytvořte na serveru Azure Database for MySQL prázdnou databázi pomocí MySQL Workbench, TOAD nebo Navicat. Databáze může mít stejný název jako databáze, která obsahuje dumpingová data, nebo můžete vytvořit databázi s jiným názvem.

Připojte se k následujícím akcím:

1. V Azure Portal vyhledejte informace o připojení v podokně **přehled** Azure Database for MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Snímek obrazovky s informacemi o připojení Azure Database for MySQL serveru v Azure Portal.":::

1. Přidejte informace o připojení do aplikace MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Snímek obrazovky připojovacího řetězce MySQL Workbench":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Určení, kdy použít techniky importu a exportu

> [!TIP]
> Pro scénáře, kdy chcete vypsat a obnovit celou databázi, použijte místo toho přístup [výpisu a obnovení](concepts-migrate-dump-restore.md) .

V následujících scénářích použijte nástroje MySQL k importu a exportu databází do databáze MySQL. Další nástroje najdete v části metody migrace (stránka 22) [Průvodce migrací databáze MySQL do Azure](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf). 

- Pokud potřebujete selektivně zvolit několik tabulek, které se mají importovat z existující databáze MySQL do vaší databáze MySQL Azure, je nejlepší použít techniku importu a exportu. Díky tomu můžete z migrace vynechat všechny nepotřebné tabulky a ušetřit tak čas a prostředky. Například použijte `--include-tables` `--exclude-tables` přepínač nebo s [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)a `--tables` přepínač s [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Pokud přesouváte databázové objekty jiné než tabulky, je třeba tyto objekty explicitně vytvořit. Zahrňte omezení (primární klíč, cizí klíč a indexy), zobrazení, funkce, procedury, aktivační události a všechny další databázové objekty, které chcete migrovat.
- Když migrujete data z jiných zdrojů dat než databáze MySQL, vytvoříte ploché soubory a naimportujete je pomocí [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Jeden server i flexibilní Server podporují jenom modul úložiště InnoDB. Zajistěte, aby všechny tabulky v databázi používaly modul úložiště InnoDB při načítání dat do služby Azure Database for MySQL.
>
> Pokud zdrojová databáze používá jiný modul úložiště, před migrací databáze převeďte na modul InnoDB. Pokud máte například WordPress nebo webovou aplikaci, která používá modul MyISAM, nejprve převeďte tabulky migrací dat do tabulek InnoDB. Pomocí klauzule `ENGINE=INNODB` Nastavte modul pro vytvoření tabulky a potom přeneste data do kompatibilní tabulky před migrací.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Doporučení k výkonu pro import a export

Pro optimální výkon importu a exportu dat doporučujeme, abyste provedli následující akce:
-   Vytvoření clusterovaných indexů a primárních klíčů před načtením dat. Načtěte data v pořadí primárního klíče.
-   Odložit vytváření sekundárních indexů až po načtení dat
-   Před načtením dat zakažte omezení cizího klíče. Zakázání kontrol cizích klíčů přináší výrazné zvýšení výkonu. Povolte omezení a ověřte data po zatížení, abyste zajistili referenční integritu.
-   Načtěte data paralelně. Vyhněte se příliš přílišnému paralelismuům, které by mohly mít za následek omezení prostředků, a monitorujte prostředky pomocí metrik dostupných v Azure Portal.
-   V případě potřeby použijte dělené tabulky.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Import a export dat pomocí aplikace MySQL Workbench
Existují dva způsoby, jak exportovat a importovat data v aplikaci MySQL Workbench: v místní nabídce prohlížeče objektů nebo v podokně navigátor. Každá metoda slouží k jinému účelu.

> [!NOTE]
> Pokud přidáváte připojení k jednomu serveru MySQL nebo flexibilnímu serveru (Preview) na MySQL Workbench, udělejte toto:
> - U jednoho serveru MySQL se ujistěte, že je uživatelské jméno ve formátu *\<username@servername>* .
> - Pro MySQL flexibilní Server používejte *\<username>* jenom. Pokud používáte *\<username@servername>* k připojení, připojení selže.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Spusťte Průvodce exportem a importem dat tabulky z kontextové nabídky prohlížeče objektů.

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Snímek obrazovky s příkazy Průvodce exportem a importem MySQL Workbench v místní nabídce prohlížeče objektů":::

Průvodce daty tabulky podporuje operace importu a exportu pomocí souborů CSV a JSON. Průvodci zahrnují několik možností konfigurace, jako jsou oddělovače, výběr sloupců a kódování výběru. Jednotlivé průvodce můžete spustit pro místní nebo vzdáleně připojené servery MySQL. Importovaná akce zahrnuje mapování typu tabulka, sloupec a typ.

Pro přístup k těmto průvodcům z kontextové nabídky prohlížeče objektů klikněte pravým tlačítkem myši na tabulku a pak vyberte Průvodce **exportem dat tabulky** nebo **Průvodce importem dat tabulky**.

#### <a name="the-table-data-export-wizard"></a>Průvodce exportem dat tabulky

Export tabulky do souboru CSV:

1. Klikněte pravým tlačítkem na tabulku databáze, která se má exportovat.
1. Vyberte **Průvodce exportem dat tabulky**. Vyberte sloupce, které se mají exportovat, posun řádku (pokud existuje) a počet (pokud existuje).
1. V podokně **Vybrat data pro export** vyberte **Další**. Vyberte typ souboru cesta, CSV nebo JSON. Vyberte také oddělovač řádků, metodu ohraničujících řetězců a oddělovač polí.
1. V podokně **Vyberte umístění výstupního souboru** vyberte **Další**.
1. V podokně **exportovat data** vyberte **Další**.

#### <a name="the-table-data-import-wizard"></a>Průvodce importem dat tabulky

Import tabulky ze souboru CSV:

1. Klikněte pravým tlačítkem na tabulku databáze, která se má importovat.
1. Vyhledejte a vyberte soubor CSV, který chcete naimportovat, a pak vyberte **Další**.
1. Vyberte cílovou tabulku (nová nebo existující), zaškrtněte nebo zrušte zaškrtnutí políčka **zkrácená tabulka před importem** a pak vyberte **Další**.
1. Vyberte kódování a sloupce, které chcete importovat, a pak vyberte **Další**.
1. V podokně **importovat data** vyberte **Další**. Průvodce importuje data.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Spuštění průvodců pro export a import dat SQL z podokna navigátor
Pomocí Průvodce můžete exportovat nebo importovat data SQL generovaná z MySQL Workbench nebo z příkazu mysqldump. K průvodcům můžete přistupovat z podokna **navigátor** nebo můžete vybrat možnost **Server** z hlavní nabídky.

#### <a name="export-data"></a>Exportovat data

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Snímek obrazovky s použitím podokna Navigátor k zobrazení podokna exportu dat v aplikaci MySQL Workbench.":::

K exportu dat MySQL můžete použít podokno **Export dat** .

1. V aplikaci MySQL Workbench v podokně **navigátor** vyberte **exportovat data**.

1. V podokně **exportu dat** vyberte každé schéma, které chcete exportovat.
 
   Pro každé schéma můžete vybrat konkrétní objekty schématu nebo tabulky k exportu. Mezi možnosti konfigurace patří export do složky projektu nebo samostatného souboru SQL, výpisu uložených rutin a událostí nebo přeskočení dat tabulky.

   Případně můžete pomocí **exportu sady výsledků** exportovat konkrétní sadu výsledků dotazu v editoru SQL do jiného formátu, jako je CSV, JSON, HTML a XML.

1. Vyberte databázové objekty, které chcete exportovat, a nakonfigurujte související možnosti.
1. Vyberte **aktualizovat** a načtěte aktuální objekty.
1. V případě potřeby můžete vybrat možnost **Upřesnit možnosti** v pravém horním rohu a Upřesnit operaci exportu. Například přidejte zámky tabulek, použijte `replace` místo `insert` příkazů a identifikátory uvozovek pomocí znaků typu zaškrtnutí.
1. Pokud chcete zahájit proces exportu, vyberte **Spustit export** .


#### <a name="import-data"></a>Import dat

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Snímek obrazovky s použitím podokna Navigátor k zobrazení podokna import dat v aplikaci MySQL Workbench.":::

Můžete použít podokno **Import dat** k importu nebo obnovení exportovaných dat z operace exportu dat nebo z příkazu mysqldump.

1. V aplikaci MySQL Workbench v podokně **navigátor** vyberte **data export/obnovení**.
1. Vyberte složku projektu nebo soubor SQL, který jste samostatně obsahovali, vyberte schéma, do kterého chcete importovat, nebo vyberte tlačítko **Nový** a definujte nové schéma.
1. Výběrem **Spustit import** zahajte proces importu.

## <a name="next-steps"></a>Další kroky
- Další postup migrace najdete v tématu [migrace databáze MySQL do Azure Database for MySQL pomocí výpisu a obnovení](concepts-migrate-dump-restore.md).
- Další informace o migraci databází do Azure Database for MySQL najdete v [Průvodci migrací databáze](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
