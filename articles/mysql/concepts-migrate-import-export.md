---
title: Import a export – Azure Database for MySQL
description: Tento článek vysvětluje běžné způsoby importu a exportu databází v Azure Database for MySQL pomocí nástrojů, jako je MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 721939ee55976f9eb0c4dff24067036a9d0f4164
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591856"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrace databáze MySQL pomocí importu a exportu
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]
Tento článek vysvětluje dva běžné přístupy k importu a exportu dat na server Azure Database for MySQL pomocí MySQL Workbench.

Podrobné a komplexní pokyny k migraci najdete v [příručce k migraci prostředků](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). Další scénáře migrace najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/). 


## <a name="before-you-begin"></a>Než začnete
Pokud chcete projít tento průvodce, budete potřebovat:
- Server Azure Database for MySQL, a to tak, že pomocí [Azure Portal vytvoříte Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md).
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) nebo jiný nástroj MySQL třetí strany pro import/export.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Vytvoření databáze na serveru Azure Database for MySQL
Vytvořte na serveru Azure Database for MySQL prázdnou databázi pomocí MySQL Workbench, TOAD nebo Navicat a vytvořte databázi. Databáze může mít stejný název jako databáze, která obsahuje dumpingová data, nebo můžete vytvořit databázi s jiným názvem.

Pokud se chcete připojit, vyhledejte informace o připojení v **přehledu** Azure Database for MySQL.

:::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Vyhledat informace o připojení v Azure Portal":::

Přidejte informace o připojení do aplikace MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Připojovací řetězec MySQL Workbench":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Určení, kdy použít techniky importu a exportu

> [!TIP]
> U scénářů, ve kterých chcete vypsat a obnovit celou databázi, byste měli použít místo toho přístup k [výpisu a obnovení](concepts-migrate-dump-restore.md) .

Pomocí nástrojů MySQL můžete importovat a exportovat databáze do Azure MySQL Database v následujících scénářích. Další nástroje najdete na stránce 22 [Příručky k migraci databáze MySQL do Azure](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf). 

- Pokud potřebujete selektivně zvolit několik tabulek, které se mají importovat z existující databáze MySQL do databáze MySQL Azure, je nejlepší použít techniku importu a exportu.  Díky tomu můžete z migrace vynechat všechny nepotřebné tabulky a ušetřit tak čas a prostředky. Například použijte `--include-tables` přepínač nebo s parametrem `--exclude-tables` [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) a `--tables` přepínač s [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Pokud přesouváte databázové objekty jiné než tabulky, je třeba tyto objekty explicitně vytvořit. Zahrňte omezení (primární klíč, cizí klíč, indexy), zobrazení, funkce, procedury, aktivační události a všechny další databázové objekty, které chcete migrovat.
- Když migrujete data z jiných zdrojů dat než databáze MySQL, vytvoříte ploché soubory a naimportujete je pomocí [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Jeden server i flexibilní Server podporují **jenom modul úložiště InnoDB**. Zajistěte, aby všechny tabulky v databázi používaly modul úložiště InnoDB při načítání dat do Azure Database for MySQL.
> Pokud zdrojová databáze používá jiný modul úložiště, převeďte prosím na modul InnoDB před migrací databáze. Pokud máte například WordPress nebo webovou aplikaci, která používá modul MyISAM, nejprve převeďte tabulky migrací dat do tabulek InnoDB. Pomocí klauzule `ENGINE=INNODB` Nastavte modul pro vytvoření tabulky a potom přeneste data do kompatibilní tabulky před migrací.

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

> [!NOTE]
> Pokud přidáváte připojení k jednomu nebo flexibilnímu serveru (Preview) na MySQL Workbench, ujistěte se prosím, že:
> - V případě jednoho serveru MySQL musí být uživatelské jméno v tomto formátu: ' username@servername '
> - V případě serveru MySQL Flexible můžete použít možnost "uživatelské jméno", pokud se username@servername k připojení použijete, připojení se nezdaří.

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Průvodce exportem a importem dat v tabulce z kontextové nabídky prohlížeče objektů
:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Průvodci MySQL Workbench v místní nabídce prohlížeče objektů":::

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
3. Vyberte cílovou tabulku (novou nebo existující) a zaškrtněte nebo zrušte zaškrtnutí políčka **zkracování tabulky před importem** . Klikněte na **Next** (Další).
4. Vyberte možnost kódování a sloupce, které chcete importovat, a potom klikněte na tlačítko **Další**.
5. Na stránce **importovat data** klikněte na **Další**. Průvodce importuje data odpovídajícím způsobem.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Průvodce exportem a importem dat SQL z podokna navigátor
Pomocí Průvodce můžete exportovat nebo importovat SQL generovaný z aplikace MySQL Workbench nebo vygenerovanou z příkazu mysqldump. K těmto průvodcům se dostanete z podokna **navigátor** nebo výběrem možnosti **Server** z hlavní nabídky. Pak vyberte **exportovat data** nebo **importovat data**.

#### <a name="data-export"></a>Export dat
:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Export dat MySQL Workbench pomocí podokna navigátor":::

Pomocí karty **exportu dat** můžete exportovat data MySQL.
1. Vyberte každé schéma, které chcete exportovat, volitelně zvolte konkrétní objekty nebo tabulky schématu z každého schématu a vygenerujte export. Mezi možnosti konfigurace patří export do složky projektu nebo samostatného souboru SQL, výpisu uložených rutin a událostí nebo přeskočení dat tabulky.

   Případně můžete pomocí **exportu sady výsledků** exportovat konkrétní sadu výsledků dotazu v editoru SQL do jiného formátu, jako je CSV, JSON, HTML a XML.
3. Vyberte databázové objekty, které chcete exportovat, a nakonfigurujte související možnosti.
4. Kliknutím na **aktualizovat** načtete aktuální objekty.
5. Volitelně otevřete kartu **Pokročilé možnosti** a upřesněte operaci exportu. Například přidejte zámky tabulek, použijte místo příkazů INSERT a nahraďte identifikátory uvozovek znaky.
6. Kliknutím na **Spustit export** zahajte proces exportu.


#### <a name="data-import"></a>Import dat
:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Import dat aplikace MySQL Workbench pomocí navigátoru pro správu":::

Kartu **Import dat** můžete použít k importu nebo obnovení exportovaných dat z operace exportu dat nebo z příkazu mysqldump.
1. Vyberte složku projektu nebo soubor SQL, který jste samostatně obsahovali, vyberte schéma, do kterého chcete importovat, nebo vyberte možnost **nové** a definujte nové schéma.
2. Kliknutím na **Spustit import** zahajte proces importu.

## <a name="next-steps"></a>Další kroky
- Jako další postup migrace si přečtěte téma [migrace databáze MySQL pomocí výpisu a obnovení v Azure Database for MySQL](concepts-migrate-dump-restore.md).
- Další informace o migraci databází do Azure Database for MySQL najdete v [Průvodci migrací databáze](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).