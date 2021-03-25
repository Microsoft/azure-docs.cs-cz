---
title: 'Spravovaná instance Db2 do Azure SQL: Průvodce migrací'
description: V této příručce se naučíte migrovat databáze Db2 do spravované instance Azure SQL pomocí Pomocník s migrací SQL Serveru pro Db2.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 7ac3518e0d27be6b6a18790b9fcbdbce0f6f8fef
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025046"
---
# <a name="migration-guide-db2-to-azure-sql-managed-instance"></a>Průvodce migrací: spravovaná instance Db2 do Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

V této příručce se naučíte migrovat databáze Db2 do spravované instance Azure SQL pomocí Pomocník s migrací SQL Serveru pro Db2. 

Další příručky k migraci najdete v tématu [migrace databáze](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Požadavky 

K migraci databáze Db2 do spravované instance SQL potřebujete:

- Ověřte, že [je podporované vaše zdrojové prostředí](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites).
- pro stažení [Pomocník s migrací SQL serveru (SSMA) pro Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- Cílová [spravovaná instance Azure SQL](../../managed-instance/instance-create-quickstart.md).
- Připojení a dostatečná oprávnění pro přístup ke zdroji i cíli. 



## <a name="pre-migration"></a>Před migrací

Po splnění požadavků budete připraveni zjistit topologii prostředí a posoudit proveditelnost migrace. 

### <a name="assess-and-convert"></a>Vyhodnotit a převést



Vytvořte posouzení pomocí Pomocník s migrací SQL Serveru (SSMA). 

K vytvoření posouzení použijte následující postup:

1. Otevřete Pomocník s migrací SQL Serveru (SSMA) pro Db2. 
1. Vyberte **soubor** a pak zvolte **Nový projekt**. 
1. Zadejte název projektu, umístění pro uložení projektu a potom z rozevíracího seznamu vyberte Azure SQL Managed instance jako cíl migrace. Vyberte **OK**:

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Zadejte podrobnosti projektu a vyberte OK, které chcete uložit.":::


1. Do dialogového okna **připojit k DB2** zadejte hodnoty pro podrobnosti připojení Db2:

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Připojení k instanci Db2":::


1. Klikněte pravým tlačítkem na schéma Db2, které chcete migrovat, a pak zvolte **vytvořit sestavu**. Tím se vygeneruje sestava HTML. Alternativně můžete zvolit **vytvořit sestavu** z navigačního panelu po výběru schématu:

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Klikněte pravým tlačítkem na schéma a vyberte vytvořit sestavu.":::

1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Můžete také otevřít sestavu v aplikaci Excel a získat tak inventarizaci objektů Db2 a úsilí potřebné k provedení převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects.

   Příklad: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Zkontrolujte sestavu a Identifikujte případné chyby nebo upozornění.":::


### <a name="validate-data-types"></a>Ověřit datové typy

Ověřte výchozí mapování datových typů a podle potřeby je změňte podle požadavků. To můžete provést pomocí těchto kroků: 

1. V nabídce vyberte **nástroje** . 
1. Vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** :

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Vyberte schéma a pak typ mapování":::

1. Mapování typů pro každou tabulku můžete změnit tak, že vyberete tabulku v **Průzkumníkovi metadat Db2**. 

### <a name="schema-conversion"></a>Převod schématu 

K převedení schématu použijte následující postup:

1. Volitelné Přidejte dynamické dotazy a dotazy ad-hoc k příkazům. Pravým tlačítkem myši klikněte na uzel a zvolte příkaz **přidat příkazy**. 
1. Vyberte **připojit ke spravované instanci Azure SQL**. 
    1. Zadejte podrobnosti připojení pro připojení ke spravované instanci Azure SQL. 
    1. V rozevíracím seznamu vyberte cílovou databázi, nebo zadejte nový název. v takovém případě se na cílovém serveru vytvoří databáze. 
    1. Zadejte podrobnosti ověřování. 
    1. Vyberte **připojit**:

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Zadejte podrobnosti pro připojení k SQL Server":::


1. Klikněte pravým tlačítkem na schéma a pak zvolte **převést schéma**. Alternativně můžete zvolit **převést schéma** z horního navigačního panelu po výběru schématu:

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Klikněte pravým tlačítkem na schéma a vyberte převést schéma.":::

1. Po dokončení převodu Porovnejte a zkontrolujte strukturu schématu a Identifikujte potenciální problémy a vyřešte je na základě doporučení:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Porovnejte a zkontrolujte strukturu schématu a Identifikujte potenciální problémy a vyřešte je na základě doporučení.":::

1. V podokně výstup vyberte možnost **Kontrola výsledků** a zkontrolujte chyby v podokně **Seznam chyb** . 
1. Uložte projekt místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt** . Díky tomu máte možnost vyhodnotit zdrojové a cílové schémat v režimu offline a před publikováním schématu do spravované instance SQL provést nápravu.


## <a name="migrate"></a>Migrate

Po dokončení vyhodnocení databází a vyřešení případných rozporů je dalším krokem spuštění procesu migrace.

K publikování schématu a migraci dat použijte následující postup:

1. Publikování schématu: klikněte pravým tlačítkem na databázi z uzlu **databáze** v **Průzkumníku METADAT spravované instance SQL Azure** a vyberte **synchronizovat s databází**:

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Klikněte pravým tlačítkem na databázi a vyberte synchronizovat s databází.":::

1. Migrace dat: klikněte pravým tlačítkem na databázi nebo objekt, který chcete migrovat v **Průzkumníkovi metadat Db2**, a vyberte **migrovat data**. Alternativně můžete vybrat možnost **migrovat data** z horního navigačního panelu. Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku tabulky a potom zaškrtněte políčko vedle této tabulky. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí políčka:

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Klikněte pravým tlačítkem na schéma a vyberte migrovat data.":::

1. Zadejte podrobnosti o připojení pro spravovanou instanci Db2 i SQL. 
1. Po dokončení migrace si prohlédněte **sestavu migrace dat**:  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Kontrola sestavy migrace dat":::

1. Připojte se ke spravované instanci SQL Azure pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a ověřte migraci kontrolou dat a schématu:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Porovnání schématu v SSMS":::

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


## <a name="leverage-advanced-features"></a>Využití pokročilých funkcí 

Nezapomeňte využít výhod pokročilých cloudových funkcí nabízených službou Azure SQL Managed instance, jako je [integrovaná vysoká dostupnost](../../database/high-availability-sla.md), [detekce hrozeb](../../database/azure-defender-for-sql.md)a [monitorování a optimalizace vašich úloh](../../database/monitor-tune-overview.md). 


Některé funkce SQL Server jsou dostupné až po změně [úrovně kompatibility databáze](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) na nejnovější úroveň kompatibility (150). 

## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc najdete v následujících materiálech, které byly vyvinuty v rámci podpory realizace projektu migrace do reálného světa:

|Prostředek  |Description  |
|---------|---------|
|[Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení velkých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy na základě cílové platformy.|
|[Balíček pro zjišťování a vyhodnocení datových assetů Db2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Po spuštění skriptu SQL v databázi můžete výsledky exportovat do souboru v systému souborů. Podporuje se několik formátů souborů, včetně *. csv, takže můžete zachytit výsledky v externích nástrojích, jako jsou tabulky. Tato metoda může být užitečná, pokud chcete výsledky snadno sdílet s týmy, které nemají nainstalovanou aplikaci Workbench.|
|[Skripty a artefakty inventáře IBM Db2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Tento prostředek obsahuje dotaz SQL, který má k dispozici systémové tabulky IBM Db2 LUW verze 11,1 a poskytuje počet objektů podle schématu a typu objektu, hrubý odhad nezpracovaných dat v každém schématu a velikost tabulek v jednotlivých schématech s výsledky uloženými ve formátu CSV.|
|[LUW čistota Db2 v Azure – Průvodce nastavením](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Db2%20PureScale%20on%20Azure.pdf)|Tato příručka slouží jako výchozí bod pro plán implementace Db2. I když se obchodní požadavky budou lišit, použije se stejný základní vzor. Tento model architektury se dá použít i pro aplikace OLAP v Azure.|

Tyto prostředky byly vyvinuty jako součást programu data SQL expertem, který je financován technickým týmem Azure Data Group. Základní Chartou programu data SQL expertem je odblokování a urychlení komplexní modernizace a konkurenční možnosti migrace datových platforem na datovou platformu Azure od Microsoftu. Pokud si myslíte, že by vaše organizace mohla zajímat účast v programu data SQL expertem, obraťte se prosím na svůj tým a požádejte ho, aby podal jmenování.


## <a name="next-steps"></a>Další kroky

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat i v speciálních úlohách, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o spravované instanci Azure SQL najdete tady:
   - [Přehled spravované instance SQL](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Postup vyhodnocení vrstvy přístupu k aplikaci najdete v tématu [sada Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) .
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).