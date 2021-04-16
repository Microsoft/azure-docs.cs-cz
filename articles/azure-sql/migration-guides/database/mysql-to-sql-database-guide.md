---
title: 'MySQL pro Azure SQL Database: Průvodce migrací'
description: V této příručce se dozvíte, jak migrovat databáze MySQL do Azure SQL Database pomocí Pomocník s migrací SQL Serveru pro MySQL (SSMA for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: d4510aa5cda61dac88102c89b3e03da231380bd6
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389447"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Průvodce migrací: MySQL pro Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

V této příručce se dozvíte, [jak migrovat](https://azure.microsoft.com/migration/migration-journey) databázi MySQL do služby Azure SQL database pomocí SQL Server Pomocník pro [migraci](https://azure.microsoft.com/en-us/migration/sql-server/) pro MySQL (SSMA for MySQL). 

Další příručky k migraci najdete v tématu [Průvodce migrací databáze Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Požadavky

Než začnete migrovat databázi MySQL do databáze SQL, udělejte toto:

- Ověřte, že je podporované vaše zdrojové prostředí. V současné době jsou podporovány MySQL 5,6 a 5,7. 
- Stáhněte a nainstalujte [Pomocník s migrací SQL serveru pro MySQL](https://www.microsoft.com/download/details.aspx?id=54257).
- Ujistěte se, že máte připojení a máte dostatečná oprávnění pro přístup ke zdroji i cíli.

## <a name="pre-migration"></a>Před migrací 

Až splníte požadavky, budete připraveni zjistit topologii vašeho prostředí a posoudit proveditelnost [migrace do cloudu Azure](https://azure.microsoft.com/migration).

### <a name="assess"></a>Posouzení 

Pomocí Pomocník s migrací SQL Serveru (SSMA) pro MySQL si můžete prohlédnout databázové objekty a data a posoudit databáze pro migraci. 

Chcete-li vytvořit posouzení, postupujte následovně:

1. Otevřete [SSMA pro MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Vyberte **soubor** a pak vyberte **Nový projekt**. 
1. V podokně **Nový projekt** zadejte název a umístění projektu a potom v rozevíracím seznamu **migrovat do** vyberte možnost **Azure SQL Database**. 
1. Vyberte **OK**.

   ![Snímek obrazovky s podoknem nový projekt pro zadání názvu, umístění a cíle projektu migrace](./media/mysql-to-sql-database-guide/new-project.png)

1. Vyberte kartu **připojit k MySQL** a zadejte podrobnosti o připojení serveru MySQL. 

   ![Snímek obrazovky s podoknem připojit k MySQL pro určení připojení ke zdroji](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. V podokně **Průzkumník metadat MySQL** klikněte pravým tlačítkem na schéma MySQL a pak vyberte **vytvořit sestavu**. Alternativně můžete vybrat kartu **vytvořit sestavu** v pravém horním rohu.

   ![Snímek obrazovky s odkazy vytvořit sestavu v SSMA pro MySQL](./media/mysql-to-sql-database-guide/create-report.png)

1. Zkontrolujte sestavu ve formátu HTML, abyste pochopili statistiku převodu, chyby a upozornění. Analyzujte, abyste pochopili problémy s převodem a jejich řešení. 
   Sestavu můžete také otevřít v aplikaci Excel a získat tak inventarizaci objektů MySQL a pochopit úsilí, které je nutné k provádění převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects. Například: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Snímek obrazovky ukázkové sestavy převodu v SSMA](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Ověření datových typů

Ověřte výchozí mapování datových typů a podle potřeby je podle požadavků změňte. Postupujte následovně: 

1. Vyberte **nástroje** a pak vyberte **nastavení projektu**.  
1. Vyberte kartu **mapování typů** . 

   ![Snímek obrazovky s podoknem mapování typů v SSMA pro MySQL](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Mapování typů pro každou tabulku můžete změnit tak, že v podokně **Průzkumník metadat MySQL** vyberete název tabulky. 

### <a name="convert-the-schema"></a>Převést schéma 

Chcete-li převést schéma, postupujte následovně: 

1. Volitelné Chcete-li převést dynamické nebo specializované dotazy, klikněte pravým tlačítkem myši na uzel a pak vyberte **příkaz Přidat příkaz**. 

1. Vyberte kartu **připojit k Azure SQL Database** a proveďte následující kroky:

   a. Zadejte podrobnosti pro připojení k vaší databázi SQL.  
   b. V rozevíracím seznamu vyberte cílovou databázi SQL. Nebo můžete zadat nový název. v takovém případě se na cílovém serveru vytvoří databáze.  
   c. Zadejte podrobnosti ověřování.  
   d. Vyberte **Connect** (Připojit).

   ![Snímek obrazovky s podoknem připojit k Azure SQL Database v SSMA pro MySQL](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Klikněte pravým tlačítkem na schéma, se kterým pracujete, a pak vyberte **převést schéma**. Alternativně můžete vybrat kartu **převést schéma** v pravém horním rohu.

   ![Snímek obrazovky s příkazem převést schéma v podokně "Průzkumník metadat MySQL".](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Po dokončení převodu zkontrolujte a porovnejte převedené objekty s původními objekty a Identifikujte potenciální problémy a vyřešte je na základě doporučení. 

   ![Snímek obrazovky znázorňující porovnání převedených objektů s původními objekty](./media/mysql-to-sql-database-guide/table-comparison.png)

   Porovnejte převedený text Transact-SQL s původním kódem a přečtěte si doporučení.

   ![Snímek obrazovky znázorňující porovnání převedených dotazů se zdrojovým kódem.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. V podokně **výstup** vyberte **zkontrolovat výsledky** a pak zkontrolujte všechny chyby v podokně **Seznam chyb** . 
1. Uložte projekt místně pro práci offline schématu pro nápravu. Provedete to tak, že vyberete **soubor**  >  **Uložit projekt**. Získáte tak možnost vyhodnotit zdrojové a cílové schémat v režimu offline a před publikováním schématu do vaší databáze SQL provést nápravu.

   Porovnejte převedené procedury s původními postupy, jak je znázorněno zde: 

   ![Snímek obrazovky znázorňující porovnání převedených postupů s původními postupy.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Migrace databází 

Po vyhodnocení databází a vyřešení případných rozporů můžete spustit proces migrace. Migrace zahrnuje dva kroky: publikování schématu a migrace dat. 

Chcete-li publikovat schéma a migrovat data, postupujte následovně: 

1. Publikování schématu. V podokně **Azure SQL Database Průzkumník metadat** klikněte pravým tlačítkem na databázi a pak vyberte **synchronizovat s databází**. Tato akce publikuje schéma MySQL do vaší databáze SQL.

   ![Snímek obrazovky s podoknem synchronizace v databázi pro kontrolu mapování databáze](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migrujte data. V podokně **Průzkumník metadat MySQL** klikněte pravým tlačítkem na schéma MySQL, které chcete migrovat, a pak vyberte **migrovat data**. Alternativně můžete vybrat kartu **migrovat data** v pravém horním rohu.

   Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku **tabulky** a potom zaškrtněte políčko vedle této tabulky. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí políčka.

   ![Snímek obrazovky s příkazem "migrovat data" v podokně "Průzkumník metadat MySQL".](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Po dokončení migrace si prohlédněte **sestavu migrace dat**.
   
   ![Snímek obrazovky sestavy migrace dat](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Připojte se k databázi SQL pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a ověřte migraci kontrolou dat a schématu.

   ![Snímek obrazovky SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Po migraci 

Po úspěšném dokončení fáze *migrace* je potřeba dokončit sérii úloh po migraci, abyste měli jistotu, že všechno funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Výsledkem bude, že v některých případech bude nutné provést změny aplikací.

### <a name="perform-tests"></a>Provést testy

Testovací přístup k migraci databáze se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů**: k otestování migrace databáze je nutné použít dotazy SQL. Je nutné vytvořit ověřovací dotazy ke spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.

1. **Nastavení testovacího prostředí**: testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.

1. **Spustit ověřovací testy**: Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.

1. **Spustit testy výkonu**: spustit testy výkonu proti zdroji a cíli a pak výsledky analyzovat a porovnat.

### <a name="optimize"></a>Optimalizace

Fáze po migraci je zásadní pro sjednocení problémů s přesností dat, ověřování úplnosti a řešení problémů s výkonem s úlohou.

Další informace o těchto problémech a krocích pro jejich zmírnění najdete v [Průvodci pro ověřování po migraci a optimalizaci](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Prostředky migrace

Další pomoc s dokončením tohoto scénáře migrace najdete v následujícím prostředku. Bylo vyvinuto v rámci podpory realizace projektu migrace do reálného světa.

| Nadpis | Popis |
| --- | --- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Nabízí navrhované cílové platformy, připravenost do cloudu a úrovně nápravy aplikací a databází pro zadané úlohy. Nabízí jednoduché sestavování s jedním kliknutím a generování sestav, které pomáhá zrychlit vyhodnocení velkých nemovitostí poskytnutím automatizovaného procesu pro rozhodování zaměřeného na cílovou platformu. |

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.

## <a name="next-steps"></a>Další kroky 

- Pokud chcete odhadnout úspory nákladů, které můžete realizovat migrací úloh do Azure, přečtěte si [Kalkulačka celkové náklady na vlastnictví Azure](https://aka.ms/azure-tco).

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další příručky k migraci najdete v tématu [Průvodce migrací databáze Azure](https://datamigration.microsoft.com/). 

- Videa k migraci najdete v tématu [Přehled cesty migrace a doporučené služby a nástroje pro migraci a hodnocení](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).

- Další [zdroje migrace](https://azure.microsoft.com/migration/resources/)do cloudu najdete v tématu [řešení migrace do cloudu](https://azure.microsoft.com/migration).

