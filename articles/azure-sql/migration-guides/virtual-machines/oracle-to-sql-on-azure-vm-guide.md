---
title: 'SQL Server Oracle na virtuálním počítači Azure: Průvodce migrací'
description: Tato příručka vás seznámí s migrací schémat Oracle pro SQL Server na virtuálních počítačích Azure pomocí Pomocník s migrací SQL Serveru pro Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 8f034492568a7525f8f75f5f2add1a732c3ad896
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644283"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Průvodce migrací: Oracle pro SQL Server na virtuálním počítači Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

V této příručce se naučíte migrovat schémata Oracle pro SQL Server na VIRTUÁLNÍm počítači Azure pomocí Pomocník s migrací SQL Serveru pro Oracle. 

Další příručky k migraci najdete v tématu [migrace databáze](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Požadavky 

K migraci schématu Oracle pro SQL Server na VIRTUÁLNÍm počítači Azure potřebujete:

- Ověřte, že je podporované vaše zdrojové prostředí.
- Stažení [Pomocník s migrací SQL serveru (SSMA) pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Cílový [virtuální počítač SQL Server](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- [Potřebná oprávnění pro SSMA pro Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) a [poskytovatele](/sql/ssma/oracle/connect-to-oracle-oracletosql).
- Připojení a dostatečná oprávnění pro přístup ke zdroji i cíli. 


## <a name="pre-migration"></a>Před migrací

Při přípravě na migraci do cloudu ověřte, že vaše zdrojové prostředí je podporované a že jste vyřešili všechny požadavky. To vám pomůže zajistit efektivní a úspěšnou migraci.

Tato část procesu zahrnuje provádění inventarizace databází, které potřebujete migrovat, jejich vyhodnocování pro potenciální problémy s migrací nebo blokování a pak řešení všech položek, které jste pravděpodobně vystavili. 

### <a name="discover"></a>Zjišťování

Pomocí [mapové](https://go.microsoft.com/fwlink/?LinkID=316883) sady můžete identifikovat stávající zdroje dat a podrobnosti o funkcích používaných vaší firmou a získat tak lepší znalosti a plánování migrace. Tento proces zahrnuje kontrolu sítě a identifikaci všech instancí Oracle vaší organizace spolu s použitou verzí a funkcemi.

Pokud chcete pro kontrolu inventáře použít sadu MAP, postupujte takto: 

1. Otevřete [sadu nástrojů mapa](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Vyberte **vytvořit/vybrat databázi**:

   ![Výběr databáze](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Vyberte **vytvořit databázi inventáře**, zadejte název nové databáze inventáře, kterou vytváříte, zadejte krátký popis a pak vyberte **OK**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Vytvoření databáze inventáře":::

1. Vyberte **shromažďovat data inventáře** a otevřete tak **Průvodce pro inventarizaci a vyhodnocení**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Shromažďování inventárních dat":::

1. V **Průvodci inventarizace a hodnocení** zvolte **Oracle** a potom vyberte **Další**:

   ![Zvolit Oracle](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Zvolte možnost hledání počítače, která nejlépe vyhovuje vašim obchodním potřebám a prostředí, a potom vyberte **Další**: 

   ![Vyberte si možnost hledání počítače, která nejlépe vyhovuje vašim obchodním potřebám.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Buď zadejte přihlašovací údaje, nebo vytvořte nové přihlašovací údaje pro systémy, které chcete prozkoumat, a pak vyberte **Další**:

    ![Zadat přihlašovací údaje](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)

1. Nastavte pořadí přihlašovacích údajů a potom vyberte **Další**:

   ![Nastavit pořadí přihlašovacích údajů](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  

1. Zadejte pověření pro každý počítač, který chcete zjistit. Pro každý počítač/počítač můžete použít jedinečné přihlašovací údaje, nebo můžete použít seznam **přihlašovacích údajů všech počítačů** :


   ![Zadejte přihlašovací údaje pro každý počítač, který chcete zjistit.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Ověřte souhrn výběru a pak vyberte **Dokončit**:

   ![Souhrn kontroly](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)

1. Po dokončení kontroly si prohlédněte sestavu souhrn **shromažďování dat** . Kontrola může trvat několik minut a závisí na počtu databází. Po dokončení vyberte **Zavřít** :

   ![Souhrnná sestava kolekce](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Vyberte **Možnosti** pro vygenerování sestavy o podrobnostech o vyhodnocení Oracle a databázi. Vyberte obě možnosti (jeden po jedné), aby se sestava vygenerovala.


### <a name="assess"></a>Posouzení

Po identifikaci zdrojů dat použijte [Pomocník s migrací SQL serveru (SSMA) pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) k vyhodnocení instancí Oracle, které se migrují na virtuální počítač SQL Server, abyste porozuměli mezerám mezi nimi. Pomocí Pomocníka s migrací můžete zkontrolovat objekty databáze a data, posoudit databáze pro migraci, migrovat objekty databáze do SQL Server a potom migrovat data do SQL Server.

K vytvoření posouzení použijte následující postup: 

1. Otevřete  [Pomocník s migrací SQL serveru (SSMA) pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Vyberte **soubor** a pak zvolte **Nový projekt**. 
1. Zadejte název projektu, umístění, kam chcete projekt uložit, a potom z rozevíracího seznamu vyberte cíl migrace SQL Server. Vyberte **OK**:

   ![Nový projekt](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)

1. Vyberte **připojit se k Oracle**. V dialogovém okně **připojit k systému Oracle** zadejte v části hodnoty pro informace o připojení Oracle:

   ![Připojení k Oracle](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Vyberte schéma Oracle, které chcete migrovat: 

   ![Výběr schématu Oracle](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)

1. V **Průzkumníku metadat Oracle** klikněte pravým tlačítkem na schéma Oracle, které chcete migrovat, a pak zvolte **vytvořit sestavu**. Tím se vygeneruje sestava HTML. Alternativně můžete zvolit **vytvořit sestavu** z navigačního panelu po výběru databáze:

   ![Vytvořit sestavu](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. V **Průzkumníku metadat Oracle** vyberte schéma Oracle a pak výběrem **vytvořit sestavu** vygenerujte sestavu HTML s statistikou převodu a chybou nebo upozorněními, pokud existují.
1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Sestavu můžete také otevřít v aplikaci Excel a získat tak inventarizaci objektů Oracle a úsilí potřebného k provádění převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects. 

   Příklad: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`
    
   ![Sestava převodu](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)

### <a name="validate-data-types"></a>Ověřit datové typy

Ověřte výchozí mapování datových typů a podle potřeby je změňte podle požadavků. To můžete provést pomocí těchto kroků: 

1. V nabídce vyberte **nástroje** . 
1. Vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** :

   ![Mapování typů](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Mapování typů pro každou tabulku můžete změnit tak, že vyberete tabulku v **Průzkumníku metadat Oracle**. 

### <a name="convert-schema"></a>Převést schéma

K převedení schématu použijte následující postup: 

1. Volitelné Chcete-li převést dynamické nebo ad hoc dotazy, klikněte pravým tlačítkem myši na uzel a vyberte **příkaz Přidat příkaz**.
1. V horním navigačním panelu vyberte **připojit k SQL Server** . 
     1. Zadejte podrobnosti o připojení pro váš SQL Server na VIRTUÁLNÍm počítači Azure. 
     1. V rozevíracím seznamu vyberte cílovou databázi, nebo zadejte nový název. v takovém případě se na cílovém serveru vytvoří databáze. 
     1. Zadejte podrobnosti ověřování. 
     1. Vyberte **Connect** (Připojit). 

   ![Připojit k SQL](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. V **Průzkumníku metadat Oracle** klikněte pravým tlačítkem na schéma Oracle a vyberte **převést schéma**. Alternativně můžete vybrat **převést schéma** z horního navigačního panelu:

   ![Převést schéma](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)

1. Po dokončení převodu Porovnejte a zkontrolujte převedené objekty s původními objekty k identifikaci potenciálních problémů a jejich řešení na základě doporučení:

   ![Zkontrolovat doporučení](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Porovnejte převedený text Transact-SQL s původními uloženými procedurami a Projděte si doporučení: 

   ![Kontrola kódu doporučení](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Projekt můžete uložit místně pro práci s offline schématem. To lze provést výběrem možnosti **Uložit projekt** v nabídce **soubor** . Díky tomu máte možnost vyhodnotit zdrojový a cílový schémat v režimu offline a před publikováním schématu pro SQL Server provést nápravu.

1. V podokně výstup vyberte možnost **Kontrola výsledků** a zkontrolujte chyby v podokně **Seznam chyb** . 
1. Uložte projekt místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt** . Díky tomu máte možnost vyhodnotit zdrojové a cílové schémat v režimu offline a před publikováním schématu SQL Server na virtuálním počítači Azure provést nápravu.


## <a name="migrate"></a>Migrate

Po splnění potřebných požadavků a dokončení úkolů přidružených ke fázi **před migrací** jste připraveni provést migraci schématu a dat. Migrace zahrnuje dva kroky – publikování schématu a migrace dat. 


K publikování schématu a migraci dat použijte následující postup: 

1. Publikování schématu: klikněte pravým tlačítkem na databázi z **SQL Server Průzkumníku metadat**  a vyberte **synchronizovat s databází**. Tato akce publikuje schéma Oracle pro SQL Server na virtuálním počítači Azure:

   ![Synchronizovat s databází](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Zkontrolujte mapování mezi zdrojovým projektem a vaším cílem:

   ![Kontrola stavu synchronizace](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)


1. Migrace dat: klikněte pravým tlačítkem na databázi nebo objekt, který chcete migrovat v **Průzkumníkovi metadat Oracle**, a vyberte **migrovat data**. Alternativně můžete vybrat možnost **migrovat data** z horního navigačního panelu. Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku tabulky a potom zaškrtněte políčko vedle této tabulky. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí políčka:

   ![Migrace dat](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Zadání podrobností o připojení pro Oracle a SQL Server na virtuálním počítači Azure v dialogovém okně.
1. Po dokončení migrace si prohlédněte **sestavu migrace dat**:  

    ![Sestava migrace dat](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Připojte se k SQL Server na instanci virtuálního počítače Azure pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a ověřte migraci kontrolou dat a schématu:

   ![Ověřit v SSMA](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)


Kromě použití SSMA můžete k migraci dat použít taky služba SSIS (SQL Server Integration Services) (SSIS). Další informace najdete v následujících tématech: 
- Článek [Začínáme služba SSIS (SQL Server Integration Services)](//sql/integration-services/sql-server-integration-services).
- Dokument White Paper [služba SSIS (SQL Server Integration Services): SSIS pro Azure a přesun hybridních dat](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).



## <a name="post-migration"></a>Po migraci 

Po úspěšném dokončení fáze **migrace** je potřeba projít řadu úkolů po migraci, abyste měli jistotu, že všechno funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Výsledkem bude, že v některých případech bude nutné provést změny aplikací.

[Sada nástrojů pro migraci dat](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) je rozšířením pro Visual Studio Code, které umožňuje analyzovat zdrojový kód Java a detekovat volání a dotazy rozhraní API pro přístup k datům. získáte tak přehled o tom, co je potřeba řešit pro podporu nového back-endu databáze. Další informace najdete v blogu [migrace aplikace Java z webu Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) . 

### <a name="perform-tests"></a>Provést testy

Testovací přístup pro migraci databáze se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů**. K otestování migrace databáze je nutné použít dotazy SQL. Je nutné vytvořit ověřovací dotazy ke spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.

2. **Nastavte testovací prostředí**. Testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.

3. **Spusťte ověřovací testy**. Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.

4. **Spusťte testy výkonu**. Spusťte test výkonnosti proti zdroji a cíli a pak Analyzujte a porovnejte výsledky.

### <a name="optimize"></a>Optimalizace

Fáze po migraci je zásadní pro sjednocení problémů s přesností dat a ověření úplnosti a také řešení potíží s výkonem s úlohou.

> [!Note]
> Další podrobnosti o těchto problémech a konkrétních krocích, jak je zmírnit, najdete v [Průvodci pro ověřování po migraci a optimalizaci](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc s dokončením tohoto scénáře migrace najdete v následujících materiálech, které byly vyvinuty v rámci podpory zapojení projektu z reálného světa.

| **Název/odkaz**                                                                                                                                          | **Popis**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které výrazně pomáhají zrychlit hodnocení rozsáhlých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy platforem.                                                          |
| [Artefakty skriptu pro inventář Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Tento prostředek obsahuje dotaz PL/SQL, který narazí na systémové tabulky Oracle a poskytuje počet objektů podle typu schématu, typu objektu a stavu. Poskytuje také hrubý odhad nezpracovaných dat v každém schématu a velikost tabulek v jednotlivých schématech s výsledky uloženými ve formátu CSV.                                                                                                               |
| [Automatizace & konsolidace kolekce vyhodnocení pro SSMA Oracle](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Tato sada prostředků používá soubor. csv jako položku (sources.csv ve složkách projektu) k vytvoření souborů XML, které jsou nutné ke spuštění posouzení SSMA v režimu konzoly. source.csv poskytuje zákazník na základě inventáře existujících instancí Oracle. Výstupní soubory jsou AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml a VariableValueFile.xml.|
| [SSMA pro běžné chyby Oracle a jejich opravy](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | V případě Oracle můžete v klauzuli WHERE přiřadit neskalární podmínku. SQL Server však nepodporuje tento typ podmínky. V důsledku toho Pomocník s migrací SQL Serveru (SSMA) pro Oracle nepřevádí dotazy s neskalární podmínkou v klauzuli WHERE, ale generuje chybu O2SS0001. Tento dokument white paper poskytuje další podrobnosti o problému a způsobech jejich řešení.          |
| [Příručka k migraci z Oracle do SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Tento dokument se zaměřuje na úlohy spojené s migrací schématu Oracle na nejnovější verzi SQL Server. Pokud migrace vyžaduje změny funkcí a funkcí, bude možné dopad každé změny v aplikacích používajících databázi pečlivě zvážit.                                                     |

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.

## <a name="next-steps"></a>Další kroky

- Postup kontroly dostupnosti služeb použitelných pro SQL Server najdete v tématu [Azure Global Infrastructure Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database) .

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v článku [služba a nástroje pro migraci dat.](../../../dms/dms-tools-matrix.md)

- Další informace o Azure SQL najdete tady:
   - [Možnosti nasazení](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server na virtuálních počítačích Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Informace o licencování najdete v tématu.
   - [Využijte vlastní licenci s Zvýhodněné hybridní využití Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Získejte bezplatnou rozšířenou podporu pro SQL Server 2008 a SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Postup vyhodnocení vrstvy přístupu k aplikaci najdete v tématu [sada Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) .
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).