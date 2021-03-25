---
title: 'Přístup k Azure SQL Database: Průvodce migrací'
description: V této příručce se dozvíte, jak migrovat databáze aplikace Microsoft Access, aby Azure SQL Database používaly Pomocník s migrací SQL Serveru pro přístup (SSMA for Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 99e0fd3665a0269710a9b0994a1340745f77236f
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027308"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Průvodce migrací: přístup k Azure SQL Database

Tato příručka k migraci vás seznámí s migrací databází z aplikace Microsoft Access do Azure SQL Database pomocí Pomocník s migrací SQL Serveru pro přístup.

Další příručky k migraci najdete v tématu [migrace databáze](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Požadavky

K migraci databáze Access do Azure SQL Database potřebujete:

- Ověřte, že je podporované vaše zdrojové prostředí. 
- [Pomocník s migrací SQL serveru pro přístup](https://www.microsoft.com/download/details.aspx?id=54255) 
- Připojení a dostatečná oprávnění pro přístup ke zdroji i cíli. 


## <a name="pre-migration"></a>Před migrací

Po splnění požadavků budete připraveni zjistit topologii prostředí a posoudit proveditelnost migrace.


### <a name="assess"></a>Posouzení 

Pro přístup ke kontrole databázových objektů a dat a vyhodnocení databází pro migraci použijte Pomocník s migrací SQL Serveru (SSMA). 

K vytvoření posouzení použijte následující postup: 

1. Otevřete [Pomocník s migrací SQL serveru pro přístup](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Vyberte **soubor** a pak zvolte **Nový projekt**. 
1. Zadejte název projektu, umístění, kam chcete projekt uložit, a potom v rozevíracím seznamu vyberte Azure SQL Database jako cíl migrace. Vyberte **OK**:

   ![Zvolit nový projekt](./media/access-to-sql-database-guide/new-project.png)

1. Vyberte **přidat databáze** a vyberte databáze, které chcete přidat do nového projektu:

   ![Zvolit možnost přidat databáze](./media/access-to-sql-database-guide/add-databases.png)

1. V **aplikaci Access metadata Explorer** klikněte pravým tlačítkem na databázi a pak zvolte **vytvořit sestavu**. Alternativně můžete zvolit **vytvořit sestavu** z navigačního panelu po výběru schématu:

   ![Klikněte pravým tlačítkem na databázi a vyberte vytvořit sestavu.](./media/access-to-sql-database-guide/create-report.png)

1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Můžete také otevřít sestavu v aplikaci Excel a získat tak inventarizaci objektů přístupu a úsilí potřebného k provedení převodů schématu. Výchozí umístění sestavy je ve složce sestavy v SSMAProjects

   Příklad: `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Kontrola ukázkové sestavy vyhodnocení](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-data-types"></a>Ověřit datové typy

Ověřte výchozí mapování datových typů a podle potřeby je změňte podle požadavků. To můžete provést pomocí těchto kroků:

1. V nabídce vyberte **nástroje** . 
1. Vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** :

   ![Mapování typů](./media/access-to-sql-database-guide/type-mappings.png)

1. Mapování typů pro každou tabulku můžete změnit tak, že ji vyberete v **Průzkumníku přístupových metadat**.


### <a name="convert-schema"></a>Převést schéma

K převodu databázových objektů použijte následující postup: 

1. Vyberte **připojit k Azure SQL Database**. 
    1. Zadejte podrobnosti připojení pro připojení databáze v Azure SQL Database.
    1. V rozevíracím seznamu vyberte cílovou SQL Database, nebo zadejte nový název. v takovém případě se na cílovém serveru vytvoří databáze. 
    1. Zadejte podrobnosti ověřování. 
    1. Vyberte **připojit**:

   ![Připojení k Azure SQL Database](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Klikněte pravým tlačítkem na databázi v **aplikaci Access metadata Explorer** a vyberte **převést schéma**. Alternativně můžete zvolit **převést schéma** z horního navigačního panelu po výběru databáze:

   ![Klikněte pravým tlačítkem na databázi a vyberte převést schéma.](./media/access-to-sql-database-guide/convert-schema.png)
   

1. Po dokončení převodu Porovnejte a zkontrolujte převedené objekty s původními objekty k identifikaci potenciálních problémů a jejich řešení na základě doporučení:

   ![Převedené objekty se dají porovnat se zdrojem.](./media/access-to-sql-database-guide/table-comparison.png)

   Porovnejte převedený text Transact-SQL s původním kódem a přečtěte si doporučení:

   ![Převedené dotazy se dají porovnat se zdrojovým kódem.](./media/access-to-sql-database-guide/query-comparison.png)

1. Volitelné Chcete-li převést jednotlivý objekt, klikněte pravým tlačítkem myši na objekt a vyberte příkaz **převést schéma**. Převedené objekty vypadají tučně v **Průzkumníkovi přístupových metadat**: 

   ![Byly převedeny tučné objekty v Průzkumníkovi metadat.](./media/access-to-sql-database-guide/converted-items.png)
 
1. V podokně výstup vyberte možnost **Kontrola výsledků** a zkontrolujte chyby v podokně **Seznam chyb** . 
1. Uložte projekt místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt** . Díky tomu máte možnost vyhodnotit zdrojový a cílový schémat v režimu offline a před publikováním schématu pro SQL Database provést nápravu.


## <a name="migrate"></a>Migrate

Po dokončení vyhodnocení databází a vyřešení případných rozporů je dalším krokem spuštění procesu migrace. Migrace dat je operace hromadného načtení, která přesouvá řádky dat do Azure SQL Database v transakcích. Počet řádků, které mají být načteny do Azure SQL Database v každé transakci, je konfigurován v nastavení projektu.

K publikování schématu a migraci dat pomocí SSMA pro přístup použijte následující postup: 

1. Pokud jste to ještě neudělali, vyberte **připojit k Azure SQL Database** a zadejte podrobnosti o připojení. 
1. Publikování schématu: klikněte pravým tlačítkem na databázi z **Azure SQL Database Průzkumníku metadat** a vyberte **synchronizovat s databází**. Tato akce publikuje schéma MySQL pro Azure SQL Database:

   ![Synchronizovat s databází](./media/access-to-sql-database-guide/synchronize-with-database.png)

   Zkontrolujte mapování mezi zdrojovým projektem a vaším cílem:

   ![Kontrola synchronizace s databází](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Migrace dat: klikněte pravým tlačítkem na databázi nebo objekt, který chcete migrovat v **Průzkumníku metadat aplikace Access** a vyberte **migrovat data**. Alternativně můžete vybrat možnost **migrovat data** z horního navigačního panelu. Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku tabulky a potom zaškrtněte políčko vedle této tabulky. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí políčka:

    ![Migrace dat](./media/access-to-sql-database-guide/migrate-data.png)

1. Po dokončení migrace si prohlédněte **sestavu migrace dat**:  

    ![Migrace revize dat](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Připojte se k Azure SQL Database pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a ověřte migraci kontrolou dat a schématu:

   ![Ověřit v SSMA](./media/access-to-sql-database-guide/validate-data.png)



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

| **Název/odkaz**                                                                                                                                          | **Popis**                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které výrazně pomáhají zrychlit hodnocení rozsáhlých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy platforem. |


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

