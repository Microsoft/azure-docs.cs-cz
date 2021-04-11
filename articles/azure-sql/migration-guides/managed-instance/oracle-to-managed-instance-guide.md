---
title: 'Spravovaná instance Oracle do Azure SQL: Průvodce migrací'
description: V této příručce se dozvíte, jak migrovat schémata Oracle do spravované instance Azure SQL pomocí Pomocník s migrací SQL Serveru pro Oracle.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2bb019a692178c5b44c3589d401d3b2b34c3dccb
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553902"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Průvodce migrací: spravovaná instance Oracle do Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

V této příručce se dozvíte, jak migrovat schémata Oracle do spravované instance Azure SQL pomocí Pomocník s migrací SQL Serveru pro Oracle (SSMA for Oracle).

Další příručky k migraci najdete v tématu [Příručky k Azure Database Migration](https://docs.microsoft.com/data-migration).

## <a name="prerequisites"></a>Požadavky

Než začnete migrovat schéma Oracle do spravované instance SQL:

- Ověřte, že je podporované vaše zdrojové prostředí.
- Stáhněte si [SSMA pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Mít cíl [spravované instance SQL](../../managed-instance/instance-create-quickstart.md) .
- Získejte [potřebná oprávnění pro SSMA pro Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) a [poskytovatele](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 
## <a name="pre-migration"></a>Před migrací

Až splníte požadavky, budete připraveni zjistit topologii vašeho prostředí a posoudit proveditelnost migrace. Tato část procesu zahrnuje provádění inventarizace databází, které potřebujete migrovat, jejich vyhodnocování pro potenciální problémy s migrací nebo blokování a pak řešení všech položek, které jste pravděpodobně vystavili.

### <a name="assess"></a>Posouzení

Pomocí SSMA pro Oracle můžete zkontrolovat objekty databáze a data, posoudit databáze pro migraci, migrovat objekty databáze do spravované instance SQL a nakonec migrovat data do databáze.

Postup vytvoření posouzení:

1. Otevřete [SSMA pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
1. Vyberte **soubor** a pak vyberte **Nový projekt**.
1. Zadejte název projektu a umístění, kam chcete projekt uložit. Pak v rozevíracím seznamu vyberte **Azure SQL Managed instance** jako cíl migrace a vyberte **OK**.

   ![Snímek obrazovky, který zobrazuje nový projekt.](./media/oracle-to-managed-instance-guide/new-project.png)

1. Vyberte **připojit se k Oracle**. V dialogovém okně **připojit k Oracle** zadejte hodnoty pro podrobnosti o připojení Oracle.

   ![Snímek obrazovky, který ukazuje připojení k Oracle](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

1. Vyberte schémata Oracle, která chcete migrovat.

   ![Snímek obrazovky zobrazující výběr schématu Oracle](./media/oracle-to-managed-instance-guide/select-schema.png)

1. V **Průzkumníku metadat Oracle** klikněte pravým tlačítkem na schéma Oracle, které chcete migrovat, a pak vyberte **vytvořit sestavu** pro vygenerování sestavy HTML. Případně můžete vybrat databázi a pak vybrat kartu **vytvořit sestavu** .

   ![Snímek obrazovky, který ukazuje vytvoření sestavy.](./media/oracle-to-managed-instance-guide/create-report.png)

1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Sestavu můžete také otevřít v aplikaci Excel a získat tak inventarizaci objektů Oracle a úsilí potřebného k provádění převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects.

   Podívejte se například na `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Snímek obrazovky zobrazující sestavu posouzení](./media/oracle-to-managed-instance-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Ověření datových typů

Ověřte výchozí mapování datových typů a podle potřeby je změňte podle požadavků. To můžete provést pomocí těchto kroků:

1. V SSMA pro Oracle vyberte **nástroje** a pak vyberte **nastavení projektu**.
1. Vyberte kartu **mapování typů** .

   ![Snímek obrazovky, který zobrazuje mapování typů](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. Mapování typů pro každou tabulku můžete změnit tak, že ji vyberete v **Průzkumníku metadat Oracle**.

### <a name="convert-the-schema"></a>Převést schéma

Postup při převodu schématu:

1. Volitelné Přidejte dynamické dotazy a dotazy ad-hoc k příkazům. Pravým tlačítkem myši klikněte na uzel a pak vyberte **přidat příkazy**.
1. Vyberte kartu **připojit k Azure SQL Managed instance** .
    1. Zadejte podrobnosti připojení pro připojení databáze ve **SQL Database Managed instance**.
    1. V rozevíracím seznamu vyberte cílovou databázi, nebo zadejte nový název. v takovém případě se databáze vytvoří na cílovém serveru.
    1. Zadejte podrobnosti ověřování a vyberte **připojit**.

    ![Snímek obrazovky, který ukazuje připojení ke spravované instanci Azure SQL](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. V **Průzkumníku metadat Oracle** klikněte pravým tlačítkem na schéma Oracle a pak vyberte **převést schéma**. Případně můžete vybrat schéma a pak vybrat kartu **převést schéma** .

   ![Snímek obrazovky, který ukazuje převod schématu.](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. Po dokončení převodu Porovnejte a zkontrolujte převedené objekty s původními objekty a Identifikujte potenciální problémy a vyřešte je na základě doporučení.

   ![Snímek obrazovky, který ukazuje porovnávání doporučení tabulek.](./media/oracle-to-managed-instance-guide/table-comparison.png)

1. Porovnejte převedený text Transact-SQL s původním kódem a přečtěte si doporučení.

   ![Snímek obrazovky, který ukazuje porovnání doporučení k procedurám.](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. V podokně výstup vyberte **zkontrolovat výsledky** a zkontrolujte chyby v podokně **Seznam chyb** .
1. Uložte projekt místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt**. Tento krok vám poskytne možnost vyhodnotit zdrojový a cílový schémat offline a provést nápravu před publikováním schématu do spravované instance SQL.

## <a name="migrate"></a>Migrate

Po dokončení vyhodnocení databází a vyřešení případných rozporů je dalším krokem spuštění procesu migrace. Migrace zahrnuje dva kroky: publikování schématu a migrace dat.

Publikování schématu a migrace dat:
1. Publikujte schéma tak, že kliknete pravým tlačítkem na databázi z uzlu **databáze** v **Průzkumníku metadat spravované instance Azure SQL** a vyberete **synchronizovat s databází**.

   ![Snímek obrazovky, který zobrazuje synchronizaci s databází.](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)
   

1. Zkontrolujte mapování mezi zdrojovým projektem a vaším cílem.

   ![Snímek obrazovky, který zobrazuje synchronizaci s kontrolou databáze.](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. Migrujte data tak, že kliknete pravým tlačítkem na schéma nebo objekt, který chcete migrovat v **Průzkumníkovi metadat Oracle** , a vyberete **migrovat data**. Případně můžete vybrat kartu **migrovat data** . Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku **tabulky** a zaškrtněte políčka vedle tabulek. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí políček.

   ![Snímek obrazovky, který ukazuje migraci dat.](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Zadejte podrobnosti o připojení pro spravovanou instanci Oracle i SQL.
1. Po dokončení migrace si prohlédněte **sestavu migrace dat**.

   ![Snímek obrazovky zobrazující sestavu migrace dat](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. Připojte se k vaší instanci spravované instance SQL pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)a ověřte migraci kontrolou dat a schématu.

   ![Snímek obrazovky, který zobrazuje ověření v SSMA pro Oracle.](./media/oracle-to-managed-instance-guide/validate-data.png)

K provedení migrace taky můžete použít služba SSIS (SQL Server Integration Services). Další informace najdete v následujících tématech:

- [Začínáme s služba SSIS (SQL Server Integration Services)](/sql/integration-services/sql-server-integration-services)
- [služba SSIS (SQL Server Integration Services) pro Azure a přesun hybridních dat](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Po migraci

Po úspěšném dokončení fáze *migrace* je potřeba dokončit sérii úloh po migraci, abyste měli jistotu, že všechno funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Splnění tohoto kroku vyžaduje v některých případech změny v aplikacích.

[Sada nástrojů pro migraci Data Access](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) je rozšířením pro Visual Studio Code, které umožňuje analyzovat zdrojový kód Java a detekovat volání a dotazy rozhraní API pro přístup k datům. Sada nástrojů poskytuje přehled o tom, co je potřeba řešit pro podporu nového back-endu databáze. Další informace najdete v blogovém příspěvku věnovaném [migraci naší aplikace v jazyce Java z Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) .

### <a name="perform-tests"></a>Provést testy

Testovací přístup k migraci databáze se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů**: k otestování migrace databáze je nutné použít dotazy SQL. Je nutné vytvořit ověřovací dotazy ke spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.
2. **Nastavení testovacího prostředí**: testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.
3. **Spustit ověřovací testy**: Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.
4. **Spustit testy výkonu**: spustit testy výkonu proti zdroji a cíli a pak výsledky analyzovat a porovnat.

### <a name="optimize"></a>Optimalizace

Fáze po migraci je zásadní pro sjednocení problémů s přesností dat, ověřování úplnosti a řešení problémů s výkonem s úlohou.

> [!NOTE]
> Další informace o těchto problémech a krocích pro jejich zmírnění najdete v [Průvodci pro ověřování po migraci a optimalizaci](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Prostředky migrace

Další pomoc s dokončením tohoto scénáře migrace najdete v následujících zdrojích informací. Byly vyvinuty v rámci podpory realizace projektu migrace do reálného světa.

| **Název/odkaz**                                                                                                                                          | **Popis**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení velkých majetku poskytnutím automatizovaného a sjednoceného rozhodovacího procesu platformy.                                                          |
| [Artefakty skriptu pro inventář Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Tento prostředek obsahuje dotaz PL/SQL, který narazí na systémové tabulky Oracle a poskytuje počet objektů podle typu schématu, typu objektu a stavu. Poskytuje také hrubý odhad nezpracovaných dat v každém schématu a velikost tabulek v jednotlivých schématech s výsledky uloženými ve formátu CSV.                                                                                                               |
| [Automatizace & konsolidace kolekce vyhodnocení pro SSMA Oracle](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Tato sada prostředků používá soubor. csv jako položku (sources.csv ve složkách projektu) k vytvoření souborů XML, které jsou potřebné ke spuštění posouzení SSMA v režimu konzoly. source.csv poskytuje zákazník na základě inventáře existujících instancí Oracle. Výstupní soubory jsou AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml a VariableValueFile.xml.|
| [SSMA pro běžné chyby Oracle a jejich opravy](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | V případě Oracle můžete v klauzuli WHERE přiřadit neskalární podmínku. SQL Server však nepodporuje tento typ podmínky. Výsledkem je, že SSMA pro Oracle nepřevádí dotazy s neskalární podmínkou v klauzuli WHERE. Místo toho generuje chybu O2SS0001. Tento dokument white paper poskytuje další podrobnosti o problému a způsobech jejich řešení.          |
| [Příručka k migraci z Oracle do SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Tento dokument se zaměřuje na úlohy spojené s migrací schématu Oracle na nejnovější verzi SQL Server databáze. Pokud migrace vyžaduje změny funkcí nebo funkcí, je třeba pečlivě zvážit možné dopady každé změny v aplikacích, které používají databázi.                                                     |

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.

## <a name="next-steps"></a>Další kroky

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v tématu [služby a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o spravované instanci SQL najdete v těchto tématech:
  - [Přehled spravované instance Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/en-us/pricing/tco/calculator/)

- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete tady:
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a velikosti úloh pro migraci do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Obsah videa najdete v těchto tématech:
    - [Přehled cesty migrace a nástrojů a služeb doporučených pro provádění posouzení a migrace](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
