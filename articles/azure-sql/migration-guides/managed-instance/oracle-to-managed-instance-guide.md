---
title: 'Spravovaná instance Oracle do SQL: Průvodce migrací'
description: V této příručce se naučíte migrovat schémata Oracle do spravované instance Azure SQL pomocí Pomocník s migrací SQL Serveru pro Oracle.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: c54ec2cc6e17d9693e25f1471922da8c7c023e36
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602822"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Průvodce migrací: spravovaná instance Oracle do Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

V této příručce se naučíte migrovat schémata Oracle do spravované instance Azure SQL pomocí Pomocník s migrací SQL Serveru pro Oracle. 

Další scénáře najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Předpoklady

K migraci schématu Oracle do spravované instance SQL potřebujete: 

- Ověřte, že je podporované vaše zdrojové prostředí. 
- Stažení [Pomocník s migrací SQL serveru (SSMA) pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
- Cílová [spravovaná instance Azure SQL](../../managed-instance/instance-create-quickstart.md). 
- [Potřebná oprávnění pro SSMA pro Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) a [poskytovatele](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 

## <a name="pre-migration"></a>Před migrací

Po splnění požadavků budete připraveni zjistit topologii prostředí a posoudit proveditelnost migrace. Tato část procesu zahrnuje provádění inventarizace databází, které potřebujete migrovat, jejich vyhodnocování pro potenciální problémy s migrací nebo blokování a pak řešení všech položek, které jste pravděpodobně vystavili.



### <a name="assess"></a>Posouzení 

Pomocí Pomocník s migrací SQL Serveru (SSMA) pro Oracle zkontrolujte objekty databáze a data, vyhodnoťte databáze pro migraci, migrujte objekty databáze do spravované instance Azure SQL a nakonec migrujte data do databáze. 

K vytvoření posouzení použijte následující postup: 


1. Otevřete [Pomocník s migrací SQL serveru pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Vyberte **soubor** a pak zvolte **Nový projekt**. 
1. Zadejte název projektu, umístění pro uložení projektu a potom z rozevíracího seznamu vyberte Azure SQL Managed instance jako cíl migrace. Vyberte **OK**.

   ![Nový projekt](./media/oracle-to-managed-instance-guide/new-project.png)

1. Vyberte **připojit se k Oracle**. V dialogovém okně **připojit k systému Oracle** zadejte v části hodnoty pro informace o připojení Oracle.

   ![Připojení k Oracle](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

   Vyberte schéma Oracle, které chcete migrovat: 

   ![Výběr schématu Oracle](./media/oracle-to-managed-instance-guide/select-schema.png)

1. V **Průzkumníku metadat Oracle** klikněte pravým tlačítkem na schéma Oracle, které chcete migrovat, a pak zvolte **vytvořit sestavu**. Tím se vygeneruje sestava HTML. Alternativně můžete zvolit možnost **vytvořit sestavu** z navigačního panelu po výběru databáze.

   ![Vytvořit sestavu](./media/oracle-to-managed-instance-guide/create-report.png)

1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Sestavu můžete také otevřít v aplikaci Excel a získat tak inventarizaci objektů Oracle a úsilí potřebného k provádění převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects.

   Příklad: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![Sestava posouzení](./media/oracle-to-managed-instance-guide/assessment-report.png)


### <a name="validate-data-types"></a>Ověřit datové typy

Ověřte výchozí mapování datových typů a podle potřeby je změňte podle požadavků. To můžete provést pomocí těchto kroků:

1. V nabídce vyberte **nástroje** . 
1. Vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** . 

   ![Mapování typů](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. Mapování typů pro každou tabulku můžete změnit tak, že vyberete tabulku v **Průzkumníku metadat Oracle**.

### <a name="convert-schema"></a>Převést schéma

K převedení schématu použijte následující postup: 

1. Volitelné Přidejte dynamické dotazy a dotazy ad-hoc k příkazům. Pravým tlačítkem myši klikněte na uzel a zvolte příkaz **přidat příkazy**.
1. Vyberte **připojit ke spravované instanci Azure SQL**. 
    1. Zadejte podrobnosti připojení pro připojení databáze ve spravované instanci Azure SQL.
    1. Z rozevíracího seznamu vyberte cílovou databázi.
    1. Vyberte **Connect** (Připojit).

    ![Připojení ke spravované instanci SQL](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. V **Průzkumníku metadat Oracle** klikněte pravým tlačítkem na schéma Oracle a pak zvolte **převést schéma**. Alternativně můžete po výběru schématu vybrat možnost **převést schéma** z horního navigačního panelu.

   ![Převést schéma](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. Po dokončení převodu Porovnejte a zkontrolujte převedené objekty s původními objekty k identifikaci potenciálních problémů a jejich řešení na základě doporučení.

   ![Porovnat doporučení pro tabulku](./media/oracle-to-managed-instance-guide/table-comparison.png)

   Porovnejte převedený text Transact-SQL s původními uloženými procedurami a Projděte si doporučení: 

   ![Porovnání doporučení k procedurám](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. Uložte projekt místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt** .

## <a name="migrate"></a>Migrate

Po dokončení vyhodnocení databází a vyřešení případných rozporů je dalším krokem spuštění procesu migrace. Migrace zahrnuje dva kroky – publikování schématu a migrace dat. 

K publikování schématu a migraci dat použijte následující postup:

1. Publikování schématu: klikněte pravým tlačítkem na databázi z uzlu **databáze** v **Průzkumníku metadat spravované instance Azure SQL** a vyberte **synchronizovat s databází**.

   ![Synchronizovat s databází](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)

   Zkontrolujte mapování mezi zdrojovým projektem a vaším cílem:

   ![Synchronizace s kontrolou databáze](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. Migrace dat: klikněte pravým tlačítkem na schéma v **Průzkumníku metadat Oracle** a vyberte **migrovat data**. 

   ![Migrace dat](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Zadejte podrobnosti o připojení pro Oracle i pro spravovanou instanci SQL Azure.
1. Zobrazit **sestavu migrace dat**

   ![Sestava migrace dat](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. Připojte se ke spravované instanci SQL Azure pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a ověřte migraci kontrolou dat a schématu.

   ![Ověřit v SSMA](./media/oracle-to-managed-instance-guide/validate-data.png)


Případně můžete k provedení migrace použít taky služba SSIS (SQL Server Integration Services) (SSIS). Další informace najdete v následujících tématech: 

- [Pomocník s migrací SQL Serveru: jak vyhodnotit a migrovat data z datových platforem od jiných společností než Microsoft do SQL Server](https://blogs.msdn.microsoft.com/datamigration/2016/11/16/sql-server-migration-assistant-how-to-assess-and-migrate-databases-from-non-microsoft-data-platforms-to-sql-server/)
- [Začínáme s služba SSIS (SQL Server Integration Services)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services)
- [Služba SSIS (SQL Server Integration Services): SSIS pro Azure a přesun hybridních dat](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

    


## <a name="post-migration"></a>Po migraci 

Po úspěšném dokončení fáze **migrace** je potřeba projít řadu úkolů po migraci, abyste měli jistotu, že všechno funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Výsledkem bude, že v některých případech bude nutné provést změny aplikací.

[Sada nástrojů pro migraci dat](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) je rozšířením pro Visual Studio Code, které umožňuje analyzovat zdrojový kód Java a detekovat volání a dotazy rozhraní API pro přístup k datům. získáte tak přehled o tom, co je potřeba řešit pro podporu nového back-endu databáze. Další informace najdete v blogu [migrace aplikace Java z webu Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) . 

### <a name="perform-tests"></a>Provést testy

Testovací přístup pro migraci databáze se skládá z následujících aktivit:

1.  **Vývoj ověřovacích testů**. K otestování migrace databáze je nutné použít dotazy SQL. Je nutné vytvořit ověřovací dotazy ke spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.

2.  **Nastavte testovací prostředí**. Testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.

3.  **Spusťte ověřovací testy**. Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.

4.  **Spusťte testy výkonu**. Spusťte test výkonnosti proti zdroji a cíli a pak Analyzujte a porovnejte výsledky.

### <a name="optimize"></a>Optimalizace

Fáze po migraci je zásadní pro sjednocení problémů s přesností dat a ověření úplnosti a také řešení potíží s výkonem s úlohou.

> [!NOTE]
> Další podrobnosti o těchto problémech a konkrétních krocích, jak je zmírnit, najdete v [Průvodci pro ověřování po migraci a optimalizaci](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc s dokončením tohoto scénáře migrace najdete v následujících materiálech, které byly vyvinuty v rámci podpory zapojení projektu z reálného světa.

| **Název/odkaz**                                                                                                                                          | **Popis**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které výrazně pomáhají zrychlit hodnocení rozsáhlých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy platforem.                                                          |
| [Artefakty skriptu pro inventář Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Tento prostředek obsahuje dotaz PL/SQL, který narazí na systémové tabulky Oracle a poskytuje počet objektů podle typu schématu, typu objektu a stavu. Poskytuje také hrubý odhad nezpracovaných dat v každém schématu a velikost tabulek v jednotlivých schématech s výsledky uloženými ve formátu CSV.                                                                                                               |
| [Automatizace & konsolidace kolekce vyhodnocení pro SSMA Oracle](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Tato sada prostředků používá soubor. csv jako položku (sources.csv ve složkách projektu) k vytvoření souborů XML, které jsou nutné ke spuštění posouzení SSMA v režimu konzoly. source.csv poskytuje zákazník na základě inventáře existujících instancí Oracle. Výstupní soubory jsou AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml a VariableValueFile.xml.|
| [SSMA pro běžné chyby Oracle a jejich opravy](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | V případě Oracle můžete v klauzuli WHERE přiřadit neskalární podmínku. SQL Server však nepodporuje tento typ podmínky. V důsledku toho Pomocník s migrací SQL Serveru (SSMA) pro Oracle nepřevádí dotazy s neskalární podmínkou v klauzuli WHERE, ale generuje chybu O2SS0001. Tento dokument white paper poskytuje další podrobnosti o problému a způsobech jejich řešení.          |
| [Příručka k migraci z Oracle do SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Tento dokument se zaměřuje na úlohy spojené s migrací schématu Oracle na nejnovější verzi SQL ServerBase. Pokud migrace vyžaduje změny funkcí a funkcí, bude možné dopad každé změny v aplikacích používajících databázi pečlivě zvážit.                                                     |

Tyto prostředky byly vyvinuty jako součást programu data SQL expertem, který je financován technickým týmem Azure Data Group. Základní Chartou programu data SQL expertem je odblokování a urychlení komplexní modernizace a konkurenční možnosti migrace datových platforem na datovou platformu Azure od Microsoftu. Pokud si myslíte, že by vaše organizace mohla zajímat účast v programu data SQL expertem, obraťte se prosím na svůj tým a požádejte ho, aby podal jmenování.

## <a name="next-steps"></a>Další kroky

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v článku [služba a nástroje pro migraci dat](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

- Další informace o spravované instanci Azure SQL najdete tady: 
  - [Přehled spravované instance Azure SQL](../../database/sql-database-paas-overview.md)
  - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) s

- Obsah videa najdete v těchto tématech: 
    - [Přehled cesty migrace a nástroje/služby doporučené pro provádění posouzení a migrace](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)