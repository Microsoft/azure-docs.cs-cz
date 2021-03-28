---
title: 'Nasazení Db2 do SQL Server na virtuálních počítačích Azure: Průvodce migrací'
description: Tato příručka vás seznámí s migrací databáze Db2 do SQL Server na virtuálních počítačích Azure pomocí Pomocník s migrací SQL Serveru pro Db2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 003448255a82d0062e9abc3c358a47687cd5ae90
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644196"
---
# <a name="migration-guide-db2-to-sql-server-on-azure-vms"></a>Průvodce migrací: Db2 pro SQL Server na virtuálních počítačích Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Tato příručka k migraci vás seznámí s migrací uživatelských databází z Db2 do SQL Server na virtuálních počítačích Azure pomocí Pomocník s migrací SQL Serveru pro Db2. 

Další příručky k migraci najdete v tématu [migrace databáze](https://docs.microsoft.com/data-migration). 


## <a name="prerequisites"></a>Požadavky

K migraci databáze Db2 na SQL Server potřebujete:

- Ověřte, že [je podporované vaše zdrojové prostředí](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites).
- [Pomocník s migrací SQL serveru (SSMA) pro Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Připojení](../../virtual-machines/windows/ways-to-connect-to-sql.md) mezi zdrojovým prostředím a vaším SQL SERVERm virtuálním počítačem v Azure. 
- Cílový [SQL Server na virtuálním počítači Azure](../../virtual-machines/windows/create-sql-vm-portal.md). 



## <a name="pre-migration"></a>Před migrací

Po splnění požadavků budete připraveni zjistit topologii prostředí a posoudit proveditelnost migrace. 


### <a name="assess"></a>Posouzení 

Pomocí Pomocník s migrací SQL Serveru (SSMA) pro DB2 zkontrolujte objekty databáze a data a vyhodnoťte databáze pro migraci. 

K vytvoření posouzení použijte následující postup:

1. Otevřete [Pomocník s migrací SQL serveru (SSMA) pro Db2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Vyberte **soubor** a pak zvolte **Nový projekt**. 
1. Zadejte název projektu, umístění, kam chcete projekt uložit, a potom z rozevíracího seznamu vyberte cíl migrace SQL Server. Vyberte **OK**:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Zadejte podrobnosti projektu a vyberte OK, které chcete uložit.":::


1. Do dialogového okna **připojit k DB2** zadejte hodnoty pro podrobnosti připojení Db2:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Připojení k instanci Db2":::


1. Klikněte pravým tlačítkem na schéma Db2, které chcete migrovat, a pak zvolte **vytvořit sestavu**. Tím se vygeneruje sestava HTML. Alternativně můžete zvolit **vytvořit sestavu** z navigačního panelu po výběru schématu:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Klikněte pravým tlačítkem na schéma a vyberte vytvořit sestavu.":::

1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Můžete také otevřít sestavu v aplikaci Excel a získat tak inventarizaci objektů Db2 a úsilí potřebné k provedení převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects.

   Příklad: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Zkontrolujte sestavu a Identifikujte případné chyby nebo upozornění.":::


### <a name="validate-data-types"></a>Ověřit datové typy

Ověřte výchozí mapování datových typů a podle potřeby je změňte podle požadavků. To můžete provést pomocí těchto kroků: 

1. V nabídce vyberte **nástroje** . 
1. Vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** :

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Vyberte schéma a pak typ mapování":::

1. Mapování typů pro každou tabulku můžete změnit tak, že vyberete tabulku v **Průzkumníkovi metadat Db2**. 

### <a name="convert-schema"></a>Převést schéma 

K převedení schématu použijte následující postup:

1. Volitelné Přidejte dynamické dotazy a dotazy ad-hoc k příkazům. Pravým tlačítkem myši klikněte na uzel a zvolte příkaz **přidat příkazy**. 
1. Vyberte **připojit k SQL Server**. 
    1. Zadejte podrobnosti připojení, které se připojí ke své SQL Server instanci na VIRTUÁLNÍm počítači Azure. 
    1. Vyberte, že se chcete připojit k existující databázi na cílovém serveru, nebo zadejte nový název pro vytvoření nové databáze na cílovém serveru. 
    1. Zadejte podrobnosti ověřování. 
    1. Vyberte **připojit**:

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Připojení k vašemu SQL Server na VIRTUÁLNÍm počítači Azure":::

1. Klikněte pravým tlačítkem na schéma a pak zvolte **převést schéma**. Alternativně můžete zvolit **převést schéma** z horního navigačního panelu po výběru schématu:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Klikněte pravým tlačítkem na schéma a vyberte převést schéma.":::

1. Po dokončení převodu Porovnejte a zkontrolujte strukturu schématu a Identifikujte potenciální problémy a vyřešte je na základě doporučení: 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Porovnejte a zkontrolujte strukturu schématu a Identifikujte potenciální problémy a vyřešte je na základě doporučení.":::

1. V podokně výstup vyberte možnost **Kontrola výsledků** a zkontrolujte chyby v podokně **Seznam chyb** . 
1. Uložte projekt místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt** . Díky tomu máte možnost vyhodnotit zdrojové a cílové schémat v režimu offline a před publikováním schématu SQL Server na virtuálním počítači Azure provést nápravu.


## <a name="migrate"></a>Migrate

Po dokončení vyhodnocení databází a vyřešení případných rozporů je dalším krokem spuštění procesu migrace.

K publikování schématu a migraci dat použijte následující postup:

1. Publikování schématu: klikněte pravým tlačítkem na databázi z uzlu **databáze** v **SQL Server Průzkumníku metadat** a vyberte **synchronizovat s databází**:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Klikněte pravým tlačítkem na databázi a vyberte synchronizovat s databází.":::

1. Migrace dat: klikněte pravým tlačítkem na databázi nebo objekt, který chcete migrovat v **Průzkumníkovi metadat Db2**, a vyberte **migrovat data**. Alternativně můžete vybrat možnost **migrovat data** z horního navigačního panelu. Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku tabulky a potom zaškrtněte políčko vedle této tabulky. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí políčka:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Klikněte pravým tlačítkem na schéma a vyberte migrovat data.":::

1. Zadejte podrobnosti připojení pro instance Db2 i SQL Server. 
1. Po dokončení migrace si prohlédněte **sestavu migrace dat**:  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Kontrola sestavy migrace dat":::

1.  Připojte se k SQL Server na instanci virtuálního počítače Azure pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a ověřte migraci kontrolou dat a schématu:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Porovnání schématu v SSMS":::

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


## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc najdete v následujících materiálech, které byly vyvinuty v rámci podpory realizace projektu migrace do reálného světa:

|Prostředek  |Description  |
|---------|---------|
|[Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení velkých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy na základě cílové platformy.|
|[Balíček pro zjišťování a vyhodnocení datových assetů Db2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Po spuštění skriptu SQL v databázi můžete výsledky exportovat do souboru v systému souborů. Podporuje se několik formátů souborů, včetně *. csv, takže můžete zachytit výsledky v externích nástrojích, jako jsou tabulky. Tato metoda může být užitečná, pokud chcete výsledky snadno sdílet s týmy, které nemají nainstalovanou aplikaci Workbench.|
|[Skripty a artefakty inventáře IBM Db2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Tento prostředek obsahuje dotaz SQL, který má k dispozici systémové tabulky IBM Db2 LUW verze 11,1 a poskytuje počet objektů podle schématu a typu objektu, hrubý odhad nezpracovaných dat v každém schématu a velikost tabulek v jednotlivých schématech s výsledky uloženými ve formátu CSV.|
|[LUW čistota Db2 v Azure – Průvodce nastavením](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/db2%20PureScale%20on%20Azure.pdf)|Tato příručka slouží jako výchozí bod pro plán implementace Db2. I když se obchodní požadavky budou lišit, použije se stejný základní vzor. Tento model architektury se dá použít i pro aplikace OLAP v Azure.|

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.

## <a name="next-steps"></a>Další kroky

Po migraci si Projděte [příručku ověřování a optimalizace po migraci](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat, a speciálních úlohách najdete v tématu [služby a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

Další příručky k migraci najdete v tématu [migrace databáze](https://datamigration.microsoft.com/). 

Obsah videa najdete v těchto tématech:
- [Přehled cesty migrace](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)