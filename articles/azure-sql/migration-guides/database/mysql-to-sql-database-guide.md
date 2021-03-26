---
title: 'MySQL pro Azure SQL Database: Průvodce migrací'
description: Tato příručka vás seznámí s migrací databází MySQL do Azure SQL Database pomocí Pomocník s migrací SQL Serveru pro MySQL (SSMA for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 6b8d3afc214f6b78fcc11b3592cd51dadf37bf96
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564142"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Průvodce migrací: MySQL pro Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

V této příručce se naučíte migrovat databázi MySQL do Azure SQL Database pomocí Pomocník s migrací SQL Serveru pro MySQL (SSMA for MySQL). 

Další příručky k migraci najdete v tématu [migrace databáze](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Požadavky

K migraci databáze MySQL na Azure SQL Database potřebujete:

- Ověřte, že je podporované vaše zdrojové prostředí. V současné době se podporuje MySQL 5,6 a 5,7. 
- [Pomocník s migrací SQL Serveru pro MySQL](https://www.microsoft.com/download/details.aspx?id=54257)
- Připojení a dostatečná oprávnění pro přístup ke zdroji i cíli. 


## <a name="pre-migration"></a>Před migrací 

Po splnění požadavků budete připraveni zjistit topologii prostředí a posoudit proveditelnost migrace.

### <a name="assess"></a>Posouzení 

Pomocí Pomocník s migrací SQL Serveru (SSMA) pro MySQL si můžete prohlédnout databázové objekty a data a posoudit databáze pro migraci. 

Chcete-li vytvořit posouzení, proveďte následující kroky: 

1. Otevřete [Pomocník s migrací SQL serveru pro MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. V nabídce vyberte **soubor** a pak zvolte **Nový projekt**. 
1. Zadejte název projektu, umístění, kam chcete projekt uložit. Jako cíl migrace vyberte **Azure SQL Database** . Vyberte **OK**:

   ![Nový projekt](./media/mysql-to-sql-database-guide/new-project.png)

1. Vyberte **připojit k MySQL** a zadejte podrobnosti o připojení pro připojení serveru MySQL:

   ![Připojení k MySQL](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Klikněte pravým tlačítkem na schéma MySQL v **Průzkumníkovi metadat MySQL** a vyberte **vytvořit sestavu**. Alternativně můžete na horním navigačním panelu vybrat možnost **vytvořit sestavu** :

   ![Vytvořit sestavu](./media/mysql-to-sql-database-guide/create-report.png)

1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Můžete také otevřít sestavu v aplikaci Excel a získat soupis objektů MySQL a úsilí potřebné k provedení převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects.
 
   Příklad: `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Sestava převodu](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>Ověřit datové typy

Ověřte výchozí mapování datových typů a podle potřeby je změňte podle požadavků. To můžete provést pomocí těchto kroků: 

1. V nabídce vyberte **nástroje** . 
1. Vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** :

   ![Mapování typů](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Mapování typů pro každou tabulku můžete změnit tak, že vyberete tabulku v **Průzkumníkovi metadat MySQL**. 

### <a name="convert-schema"></a>Převést schéma 

K převedení schématu použijte následující postup: 

1. Volitelné Chcete-li převést dynamické nebo ad hoc dotazy, klikněte pravým tlačítkem myši na uzel a vyberte **příkaz Přidat příkaz**. 
1. Vyberte **připojit k Azure SQL Database**. 
    1. Zadejte podrobnosti připojení pro připojení databáze v Azure SQL Database.
    1. V rozevíracím seznamu vyberte cílovou SQL Database, nebo zadejte nový název. v takovém případě se na cílovém serveru vytvoří databáze. 
    1. Zadejte podrobnosti ověřování. 
    1. Vyberte **připojit**:

   ![Připojit k SQL](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Klikněte pravým tlačítkem na schéma a vyberte **převést schéma**. Alternativně můžete zvolit **převést schéma** z navigačního panelu horní linky po výběru databáze:

   ![Převést schéma](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Po dokončení převodu Porovnejte a zkontrolujte převedené objekty s původními objekty k identifikaci potenciálních problémů a jejich řešení na základě doporučení:

   ![Převedené objekty se dají porovnat se zdrojem.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Porovnejte převedený text Transact-SQL s původním kódem a přečtěte si doporučení:

   ![Převedené dotazy se dají porovnat se zdrojovým kódem.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. V podokně výstup vyberte možnost **Kontrola výsledků** a zkontrolujte chyby v podokně **Seznam chyb** . 
1. Uložte projekt místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt** . Díky tomu máte možnost vyhodnotit zdrojový a cílový schémat v režimu offline a před publikováním schématu pro SQL Database provést nápravu.



## <a name="migrate"></a>Migrate 

Po dokončení vyhodnocení databází a vyřešení případných rozporů je dalším krokem spuštění procesu migrace. Migrace zahrnuje dva kroky – publikování schématu a migrace dat. 

K publikování schématu a migraci dat použijte následující postup: 

1. Publikování schématu: klikněte pravým tlačítkem na databázi z **Azure SQL Database Průzkumníku metadat** a vyberte **synchronizovat s databází**. Tato akce publikuje schéma MySQL pro Azure SQL Database:

   ![Synchronizovat s databází](./media/mysql-to-sql-database-guide/synchronize-database.png)

   Zkontrolujte mapování mezi zdrojovým projektem a vaším cílem:

   ![Synchronizace s kontrolou databáze](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migrace dat: klikněte pravým tlačítkem na databázi nebo objekt, který chcete migrovat v **Průzkumníkovi metadat MySQL**, a vyberte **migrovat data**. Alternativně můžete vybrat možnost **migrovat data** z horního navigačního panelu. Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku tabulky a potom zaškrtněte políčko vedle této tabulky. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí políčka:

   ![Migrace dat](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Po dokončení migrace si prohlédněte sestavu **migrace dat** : 

   ![Sestava migrace dat](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Připojte se k Azure SQL Database pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a ověřte migraci kontrolou dat a schématu:

    ![Ověřit v SSMA](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



## <a name="post-migration"></a>Po migraci 

Po úspěšném dokončení fáze **migrace** je potřeba projít řadu úkolů po migraci, abyste měli jistotu, že všechno funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Výsledkem bude, že v některých případech bude nutné provést změny aplikací.

### <a name="perform-tests"></a>Provést testy

Testovací přístup pro migraci databáze se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů**. K otestování migrace databáze je nutné použít dotazy SQL. Je nutné vytvořit ověřovací dotazy ke spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.

2. **Nastavte testovací prostředí**. Testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.

3. **Spusťte ověřovací testy**. Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.

4. **Spusťte testy výkonu**. Spusťte test výkonnosti proti zdroji a cíli a pak Analyzujte a porovnejte výsledky.

### <a name="optimize"></a>Optimalizace

Fáze po migraci je zásadní pro sjednocení problémů s přesností dat a ověření úplnosti a také řešení potíží s výkonem s úlohou.

Další podrobnosti o těchto problémech a konkrétních krocích, jak je zmírnit, najdete v [Průvodci pro ověřování po migraci a optimalizaci](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Prostředky migrace

Další pomoc s dokončením tohoto scénáře migrace najdete v následujících materiálech, které byly vyvinuty v rámci podpory zapojení projektu z reálného světa.

| Název/odkaz     | Description    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které výrazně pomáhají zrychlit hodnocení rozsáhlých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy platforem. |

Tyto prostředky byly vyvinuty jako součást programu data SQL expertem, který je financován technickým týmem Azure Data Group. Základní Chartou programu data SQL expertem je odblokování a urychlení komplexní modernizace a konkurenční možnosti migrace datových platforem na datovou platformu Azure od Microsoftu. Pokud si myslíte, že by vaše organizace mohla zajímat účast v programu data SQL expertem, obraťte se prosím na svůj tým a požádejte ho, aby podal jmenování.

## <a name="next-steps"></a>Další kroky 

- Nezapomeňte se podívat na [kalkulačku celkových nákladů na vlastnictví Azure](https://aka.ms/azure-tco) , která vám pomůže odhadnout úspory nákladů, které můžete realizovat migrací do Azure.

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v článku [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další příručky k migraci najdete v tématu [migrace databáze](https://datamigration.microsoft.com/). 

Videa najdete v těchto tématech: 
- [Přehled cesty migrace a nástroje/služby doporučené pro provádění posouzení a migrace](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)