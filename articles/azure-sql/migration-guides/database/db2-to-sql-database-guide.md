---
title: Průvodce migrací z DB2 do SQL Database
description: Podle tohoto průvodce migrujte databáze DB2 do Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 369f17a39b9d7b2f85fffb8b72a293558d16416e
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325158"
---
# <a name="migration-guide-db2-to-sql-database"></a>Průvodce migrací: DB2 do SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Tato příručka vás seznámí s migrací databází DB2 do Azure SQL Database pomocí Pomocník s migrací SQL Serveru pro DB2. 

Další scénáře najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Požadavky 

K migraci databáze DB2 na SQL Database potřebujete:

- Ověřte, že je podporované vaše zdrojové prostředí.
- pro stažení [Pomocník s migrací SQL serveru (SSMA) pro DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- cílový [Azure SQL Database](../../database/single-database-create-quickstart.md).


## <a name="pre-migration"></a>Před migrací

Po splnění požadavků budete připraveni zjistit topologii prostředí a posoudit proveditelnost migrace. 

### <a name="assess-and-convert"></a>Vyhodnotit a převést

Vytvořte posouzení pomocí Pomocník s migrací SQL Serveru (SSMA). 

K vytvoření posouzení použijte následující postup:

1. Otevřete Pomocník s migrací SQL Serveru (SSMA) pro DB2. 
1. Vyberte **soubor** a pak zvolte **Nový projekt**. 
1. Zadejte název projektu, umístění, kam chcete projekt uložit, a potom v rozevíracím seznamu vyberte Azure SQL Database jako cíl migrace. Vyberte **OK**.  

   :::image type="content" source="media/db2-to-sql-database-guide/new-project.png" alt-text="Zadejte podrobnosti projektu a vyberte OK, které chcete uložit.":::


1. V dialogovém okně **připojit k DB2** zadejte hodnoty pro podrobnosti připojení DB2. 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-db2.png" alt-text="Připojení k instanci DB2":::


1. Klikněte pravým tlačítkem na schéma DB2, které chcete migrovat, a pak zvolte **vytvořit sestavu**. Tím se vygeneruje sestava HTML. Alternativně můžete zvolit **vytvořit sestavu** z navigačního panelu po výběru schématu. 

   :::image type="content" source="media/db2-to-sql-database-guide/create-report.png" alt-text="Klikněte pravým tlačítkem na schéma a vyberte vytvořit sestavu.":::

1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Můžete také otevřít sestavu v aplikaci Excel a získat tak inventarizaci objektů DB2 a úsilí potřebné k provedení převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects.

   Příklad: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-database-guide/report.png" alt-text="Zkontrolujte sestavu a Identifikujte případné chyby nebo upozornění.":::


### <a name="validate-data-types"></a>Ověřit datové typy

Ověřte výchozí mapování datových typů a podle potřeby je změňte podle požadavků. To můžete provést pomocí těchto kroků: 

1. V nabídce vyberte **nástroje** . 
1. Vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** . 

   :::image type="content" source="media/db2-to-sql-database-guide/type-mapping.png" alt-text="Vyberte schéma a pak typ mapování":::

1. Mapování typů pro každou tabulku můžete změnit tak, že vyberete tabulku v **Průzkumníkovi metadat DB2**. 

### <a name="schema-conversion"></a>Převod schématu 

K převedení schématu použijte následující postup:

1. Volitelné Přidejte dynamické dotazy a dotazy ad-hoc k příkazům. Pravým tlačítkem myši klikněte na uzel a zvolte příkaz **přidat příkazy**. 
1. Vyberte **připojit k Azure SQL Database**. 
    1. Zadejte podrobnosti připojení pro připojení databáze v Azure SQL Database. 
    1. Z rozevíracího seznamu vyberte cílovou SQL Database. 
    1. Vyberte **Connect** (Připojit). 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-sql-database.png" alt-text="Pokud se chcete připojit k logickému serveru v Azure, vyplňte podrobnosti.":::


1. Klikněte pravým tlačítkem na schéma a pak zvolte **převést schéma**. Alternativně můžete po výběru schématu vybrat možnost **převést schéma** z horního navigačního panelu. 

   :::image type="content" source="media/db2-to-sql-database-guide/convert-schema.png" alt-text="Klikněte pravým tlačítkem na schéma a vyberte převést schéma.":::

1. Po dokončení převodu Porovnejte a zkontrolujte strukturu schématu a Identifikujte potenciální problémy a vyřešte je na základě doporučení. 

   :::image type="content" source="media/db2-to-sql-database-guide/compare-review-schema-structure.png" alt-text="Porovnejte a zkontrolujte strukturu schématu a Identifikujte potenciální problémy a vyřešte je na základě doporučení.":::

1. Uložte projekt místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt** . 


## <a name="migrate"></a>Migrate

Po dokončení vyhodnocení databází a vyřešení případných rozporů je dalším krokem spuštění procesu migrace.

K publikování schématu a migraci dat použijte následující postup:

1. Publikování schématu: klikněte pravým tlačítkem na databázi v uzlu **databáze** v **Azure SQL Database Průzkumníku metadat** a vyberte **synchronizovat s databází**.

   :::image type="content" source="media/db2-to-sql-database-guide/synchronize-with-database.png" alt-text="Klikněte pravým tlačítkem na databázi a vyberte synchronizovat s databází.":::

1. Migrace dat: klikněte pravým tlačítkem na schéma v **Průzkumníku metadat DB2** a vyberte **migrovat data**. 

   :::image type="content" source="media/db2-to-sql-database-guide/migrate-data.png" alt-text="Klikněte pravým tlačítkem na schéma a vyberte migrovat data.":::

1. Zadejte podrobnosti připojení pro DB2 i Azure SQL Database. 
1. Zobrazit **sestavu migrace dat** 

   :::image type="content" source="media/db2-to-sql-database-guide/data-migration-report.png" alt-text="Kontrola sestavy migrace dat":::

1. Připojte se k Azure SQL Database pomocí SQL Server Management Studio a ověřte migraci kontrolou dat a schématu. 

   :::image type="content" source="media/db2-to-sql-database-guide/compare-schema-in-ssms.png" alt-text="Porovnání schématu v SSMS":::

## <a name="post-migration"></a>Po migraci 

Po úspěšném dokončení fáze migrace je potřeba projít řadu úkolů po migraci, abyste měli jistotu, že všechno funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace 

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Výsledkem bude, že v některých případech bude nutné provést změny aplikací.


### <a name="perform-tests"></a>Provést testy

Testovací přístup pro migraci databáze se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů**: k otestování migrace databáze je nutné použít dotazy SQL. Je nutné vytvořit ověřovací dotazy ke spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.
1. **Nastavení testovacího prostředí**: testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.
1. **Spustit ověřovací testy**: Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.
1. **Spustit testy výkonu**: spustit test výkonnosti proti zdroji a cíli a pak analyzovat a porovnat výsledky.

   > [!NOTE]
   > Pro pomoc s vývojem a prováděním ověřovacích testů po migraci zvažte řešení kvality dat dostupné od partnera [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 


## <a name="leverage-advanced-features"></a>Využití pokročilých funkcí 

Nezapomeňte využít výhod pokročilých cloudových funkcí, které nabízí SQL Database, jako je [integrovaná vysoká dostupnost](../../database/high-availability-sla.md), [detekce hrozeb](../../database/azure-defender-for-sql.md)a [monitorování a optimalizace vašich úloh](../../database/monitor-tune-overview.md). 


Některé funkce SQL Server jsou dostupné až po změně [úrovně kompatibility databáze](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) na nejnovější úroveň kompatibility (150). 

## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc najdete v následujících materiálech, které byly vyvinuty v rámci podpory realizace projektu migrace do reálného světa:

|Prostředek  |Popis  |
|---------|---------|
|[Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení velkých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy na základě cílové platformy.|
|[Balíček pro zjišťování a vyhodnocení datových assetů DB2 zOS](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Po spuštění skriptu SQL v databázi můžete výsledky exportovat do souboru v systému souborů. Podporuje se několik formátů souborů, včetně *. csv, takže můžete zachytit výsledky v externích nástrojích, jako jsou tabulky. Tato metoda může být užitečná, pokud chcete výsledky snadno sdílet s týmy, které nemají nainstalovanou aplikaci Workbench.|
|[Skripty a artefakty inventáře IBM DB2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Tento prostředek obsahuje dotaz SQL, který má k dispozici systémové tabulky IBM DB2 LUW verze 11,1 a poskytuje počet objektů podle schématu a typu objektu, hrubý odhad nezpracovaných dat v každém schématu a velikost tabulek v jednotlivých schématech s výsledky uloženými ve formátu CSV.|
|[LUW čistota DB2 v Azure – Průvodce nastavením](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Tato příručka slouží jako výchozí bod pro plán implementace DB2. I když se obchodní požadavky budou lišit, použije se stejný základní vzor. Tento model architektury se dá použít i pro aplikace OLAP v Azure.|

Tyto prostředky byly vyvinuty jako součást programu data SQL expertem, který je financován technickým týmem Azure Data Group. Základní Chartou programu data SQL expertem je odblokování a urychlení komplexní modernizace a konkurenční možnosti migrace datových platforem na datovou platformu Azure od Microsoftu. Pokud si myslíte, že by vaše organizace mohla zajímat účast v programu data SQL expertem, obraťte se prosím na svůj tým a požádejte ho, aby podal jmenování.



## <a name="next-steps"></a>Další kroky

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat i v speciálních úlohách, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o Azure SQL Database najdete v těchto tématech:
   - [Přehled SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Postup vyhodnocení vrstvy přístupu k aplikaci najdete v tématu [sada Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) .
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).