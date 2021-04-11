---
title: 'Db2 do Azure SQL Database: Průvodce migrací'
description: V této příručce se naučíte migrovat databáze IMB Db2 do Azure SQL Database pomocí Pomocník s migrací SQL Serveru pro Db2 (SSMA pro Db2).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f20af8c61bbfbbbbc20c29470648c3df6a272396
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285492"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-database"></a>Průvodce migrací: IBM Db2 pro Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

V této příručce se dozvíte, [jak migrovat](https://azure.microsoft.com/migration/migration-journey) databáze IBM Db2 na Azure SQL Database pomocí SQL Server pomocníka s [migrací](https://azure.microsoft.com/en-us/migration/sql-server/) pro Db2. 

Další příručky k migraci najdete v tématu [Příručky k Azure Database Migration](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Požadavky 

K migraci databáze Db2 na SQL Database potřebujete:

- Ověřte, jestli [je vaše zdrojové prostředí podporované](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites).
- Pro stažení [Pomocník s migrací SQL serveru (SSMA) pro Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- Cílová databáze v [Azure SQL Database](../../database/single-database-create-quickstart.md).
- Připojení a dostatečná oprávnění pro přístup ke zdroji i cíli. 

## <a name="pre-migration"></a>Před migrací

Po splnění požadovaných požadavků jste připraveni zjistit topologii prostředí a posoudit proveditelnost [migrace do cloudu Azure](https://azure.microsoft.com/migration).

### <a name="assess-and-convert"></a>Vyhodnotit a převést

Pomocí SSMA pro DB2 zkontrolujte databázové objekty a data a vyhodnoťte databáze pro migraci. 

K vytvoření posouzení použijte následující postup:

1. Otevřete [SSMA pro Db2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Vyberte **soubor**  >  **Nový projekt**. 
1. Zadejte název projektu a umístění, kam chcete projekt uložit. Pak v rozevíracím seznamu vyberte jako cíl migrace Azure SQL Database a vyberte **OK**.

   :::image type="content" source="media/db2-to-sql-database-guide/new-project.png" alt-text="Snímek obrazovky zobrazující podrobnosti projektu, které se mají zadat":::


1. V části **připojení k DB2** zadejte hodnoty pro podrobnosti připojení Db2. 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-db2.png" alt-text="Snímek obrazovky, který zobrazuje možnosti připojení k instanci Db2.":::


1. Klikněte pravým tlačítkem na schéma Db2, které chcete migrovat, a pak zvolte **vytvořit sestavu**. Tím se vygeneruje sestava HTML. Alternativně můžete zvolit **vytvořit sestavu** z navigačního panelu po výběru schématu.

   :::image type="content" source="media/db2-to-sql-database-guide/create-report.png" alt-text="Snímek obrazovky, který ukazuje, jak vytvořit sestavu.":::

1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Můžete také otevřít sestavu v aplikaci Excel a získat tak inventarizaci objektů Db2 a úsilí potřebné k provedení převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci *SSMAProjects*.

   Příklad: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-database-guide/report.png" alt-text="Snímek obrazovky s touto sestavou, abyste identifikovali případné chyby nebo upozornění.":::


### <a name="validate-data-types"></a>Ověřit datové typy

Ověřte výchozí mapování datových typů a podle potřeby je změňte podle požadavků. To můžete provést pomocí těchto kroků: 

1. V nabídce vyberte **nástroje** . 
1. Vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** .

   :::image type="content" source="media/db2-to-sql-database-guide/type-mapping.png" alt-text="Snímek obrazovky zobrazující výběr schématu a mapování typů":::

1. Mapování typů pro každou tabulku můžete změnit tak, že vyberete tabulku v **Průzkumníkovi metadat Db2**. 

### <a name="convert-schema"></a>Převést schéma

K převedení schématu použijte následující postup:

1. Volitelné Přidejte dynamické dotazy a dotazy ad-hoc k příkazům. Pravým tlačítkem myši klikněte na uzel a zvolte příkaz **přidat příkazy**. 
1. Vyberte **připojit k Azure SQL Database**. 
    1. Zadejte podrobnosti připojení pro připojení databáze v Azure SQL Database.
    1. V rozevíracím seznamu vyberte cílovou SQL Database, nebo zadejte nový název. v takovém případě se na cílovém serveru vytvoří databáze. 
    1. Zadejte podrobnosti ověřování. 
    1. Vyberte **Connect** (Připojit).

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-sql-database.png" alt-text="Snímek obrazovky, který zobrazuje podrobnosti potřebné pro připojení k logickému serveru v Azure.":::


1. Klikněte pravým tlačítkem na schéma a pak zvolte **převést schéma**. Alternativně můžete po výběru schématu vybrat možnost **převést schéma** z horního navigačního panelu.

   :::image type="content" source="media/db2-to-sql-database-guide/convert-schema.png" alt-text="Snímek obrazovky zobrazující výběr schématu a jeho převod.":::

1. Po dokončení převodu Porovnejte a zkontrolujte strukturu schématu a Identifikujte možné problémy. Vyřešte problémy podle doporučení.

   :::image type="content" source="media/db2-to-sql-database-guide/compare-review-schema-structure.png" alt-text="Snímek obrazovky, který ukazuje porovnání a kontrolu struktury schématu za účelem identifikace potenciálních problémů.":::

1. V podokně **výstup** vyberte **zkontrolovat výsledky**. V podokně **Seznam chyb** Zkontrolujte chyby. 
1. Uložte projekt místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt**. Získáte tak možnost vyhodnotit zdrojové a cílové schémat v režimu offline a před publikováním schématu pro SQL Database provést nápravu.

## <a name="migrate"></a>Migrate

Po dokončení vyhodnocení databází a vyřešení případných rozporů je dalším krokem spuštění procesu migrace.

K publikování schématu a migraci dat použijte následující postup:

1. Publikování schématu. V **Azure SQL Database Průzkumníku metadat** klikněte v uzlu **databáze** pravým tlačítkem na databázi. Pak vyberte **synchronizovat s databází**.

   :::image type="content" source="media/db2-to-sql-database-guide/synchronize-with-database.png" alt-text="Snímek obrazovky, který ukazuje možnost synchronizace s databází.":::

1. Migrujte data. Klikněte pravým tlačítkem na databázi nebo objekt, který chcete migrovat v **Průzkumníkovi metadat Db2** a vyberte **migrovat data**. Alternativně můžete vybrat možnost **migrovat data** z navigačního panelu. Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku **tabulky** a potom zaškrtněte políčko vedle této tabulky. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí políčka.

   :::image type="content" source="media/db2-to-sql-database-guide/migrate-data.png" alt-text="Snímek obrazovky zobrazující výběr schématu a volba migrace dat":::

1. Zadejte podrobnosti připojení pro Db2 i Azure SQL Database. 
1. Po dokončení migrace si prohlédněte **sestavu migrace dat**.  

   :::image type="content" source="media/db2-to-sql-database-guide/data-migration-report.png" alt-text="Snímek obrazovky, který ukazuje, kde se má zkontrolovat sestava migrace dat":::

1. Připojte se k databázi v Azure SQL Database pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Ověřte migraci kontrolou dat a schématu.

   :::image type="content" source="media/db2-to-sql-database-guide/compare-schema-in-ssms.png" alt-text="Snímek obrazovky, který ukazuje porovnávání schématu v SQL Server Management Studio.":::

## <a name="post-migration"></a>Po migraci 

Po dokončení migrace je potřeba projít řadu úkolů po migraci, abyste měli jistotu, že všechno funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace 

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Výsledkem bude, že v některých případech bude nutné provést změny aplikací.

### <a name="perform-tests"></a>Provést testy

Testování se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů**: k otestování migrace databáze je nutné použít dotazy SQL. Je nutné vytvořit ověřovací dotazy ke spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.
1. **Nastavení testovacího prostředí**: testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.
1. **Spustit ověřovací testy**: Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.
1. **Spustit testy výkonu**: spustit testy výkonu proti zdroji a cíli a pak výsledky analyzovat a porovnat.

## <a name="advanced-features"></a>Pokročilé funkce 

Nezapomeňte využít výhod pokročilých cloudových funkcí, které nabízí SQL Database, jako je [integrovaná vysoká dostupnost](../../database/high-availability-sla.md), [detekce hrozeb](../../database/azure-defender-for-sql.md)a [monitorování a optimalizace vašich úloh](../../database/monitor-tune-overview.md). 

Některé funkce SQL Server jsou dostupné, jenom když se [úroveň kompatibility databáze](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) změní na nejnovější úroveň kompatibility. 

## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc najdete v následujících materiálech, které byly vyvinuty v rámci podpory realizace projektu migrace do reálného světa:

|Prostředek  |Description  |
|---------|---------|
|[Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení velkých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy na základě cílové platformy.|
|[Balíček pro zjišťování a vyhodnocení datových assetů Db2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Po spuštění skriptu SQL v databázi můžete výsledky exportovat do souboru v systému souborů. Podporuje se několik formátů souborů, včetně *. csv, takže můžete zachytit výsledky v externích nástrojích, jako jsou tabulky. Tato metoda může být užitečná, pokud chcete výsledky snadno sdílet s týmy, které nemají nainstalovanou aplikaci Workbench.|
|[Skripty a artefakty inventáře IBM Db2 LUW](https://github.com/microsoft/DataMigrationTeam/blob/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Tento prostředek obsahuje dotaz SQL, který má k dispozici systémové tabulky IBM Db2 LUW verze 11,1 a poskytuje počet objektů podle schématu a typu objektu, hrubý odhad "nezpracovaných dat" v každém schématu a velikost tabulek v jednotlivých schématech s výsledky uloženými ve formátu CSV.|
|[LUW čistota Db2 v Azure – Průvodce nastavením](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Tato příručka slouží jako výchozí bod pro plán implementace Db2. I když se obchodní požadavky budou lišit, použije se stejný základní vzor. Tento model architektury se dá také použít pro aplikace OLAP v Azure.|

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.



## <a name="next-steps"></a>Další kroky

- Služby a nástroje Microsoftu a třetích stran, které vám pomůžou s různými scénáři migrace databáze a dat, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o Azure SQL Database najdete v těchto tématech:
   - [Přehled SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete tady:
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro určování nákladů a velikostí úloh migrovaných do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Prostředky Migrace do cloudu](https://azure.microsoft.com/migration/resources) 

- Pokud chcete vyhodnotit vrstvu přístupu aplikace, přečtěte si téma [sada Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům a/B, najdete v tématu [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).
