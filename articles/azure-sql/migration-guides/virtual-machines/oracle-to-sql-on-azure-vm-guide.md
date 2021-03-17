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
ms.openlocfilehash: f9b6dea216e05bb645daf5fdd041cec692821af8
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564895"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Průvodce migrací: Oracle pro SQL Server na virtuálním počítači Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

V této příručce se naučíte migrovat schémata Oracle pro SQL Server na VIRTUÁLNÍm počítači Azure pomocí Pomocník s migrací SQL Serveru pro Oracle. 

Další scénáře najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Požadavky 

K migraci schématu Oracle pro SQL Server na VIRTUÁLNÍm počítači Azure potřebujete:

- Ověřte, že je podporované vaše zdrojové prostředí.
- Stažení [Pomocník s migrací SQL serveru (SSMA) pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Cílový [virtuální počítač SQL Server](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- [Potřebná oprávnění pro SSMA pro Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) a [poskytovatele](/sql/ssma/oracle/connect-to-oracle-oracletosql).

## <a name="pre-migration"></a>Před migrací

Při přípravě na migraci do cloudu ověřte, že vaše zdrojové prostředí je podporované a že jste vyřešili všechny požadavky. To vám pomůže zajistit efektivní a úspěšnou migraci.

Tato část procesu zahrnuje provádění inventarizace databází, které potřebujete migrovat, jejich vyhodnocování pro potenciální problémy s migrací nebo blokování a pak řešení všech položek, které jste pravděpodobně vystavili. 

### <a name="discover"></a>Zjišťování

Pomocí [mapové](https://go.microsoft.com/fwlink/?LinkID=316883) sady můžete identifikovat stávající zdroje dat a podrobnosti o funkcích používaných vaší firmou a získat tak lepší znalosti a plánování migrace. Tento proces zahrnuje kontrolu sítě a identifikaci všech instancí Oracle vaší organizace spolu s použitou verzí a funkcemi.

Pokud chcete pro kontrolu inventáře použít sadu MAP, postupujte takto: 

1. Otevřete [sadu nástrojů mapa](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Vyberte **vytvořit/vybrat databázi**.
1. Vyberte **vytvořit databázi inventáře**, zadejte název nové databáze inventáře, kterou vytváříte, zadejte krátký popis a pak vyberte **OK**. 
1. Vyberte **shromažďovat data inventáře** a otevřete tak **Průvodce pro inventarizaci a vyhodnocení**. 
1. V **Průvodci inventarizace a hodnocení** zvolte **Oracle** a pak vyberte **Další**. 
1. Zvolte možnost hledání počítače, která nejlépe vyhovuje vašim obchodním potřebám a prostředí, a potom vyberte **Další**: 
1. Buď zadejte přihlašovací údaje, nebo vytvořte nové přihlašovací údaje pro systémy, které chcete prozkoumat, a pak vyberte **Další**.
1. Nastavte pořadí přihlašovacích údajů a pak vyberte **Další**. 
1. Zadejte pověření pro každý počítač, který chcete zjistit. Pro každý počítač/počítač můžete použít jedinečné přihlašovací údaje, nebo můžete použít seznam **přihlašovacích údajů všech počítačů** .  
1. Ověřte souhrn výběru a pak vyberte **Dokončit**.
1. Po dokončení kontroly si prohlédněte sestavu souhrn **shromažďování dat** . Kontrola bude trvat několik minut a závisí na počtu databází. Po dokončení vyberte **Zavřít** . 
1. Vyberte **Možnosti** pro vygenerování sestavy o podrobnostech o vyhodnocení Oracle a databázi. Vyberte obě možnosti (jeden po jedné), aby se sestava vygenerovala.


### <a name="assess"></a>Posouzení

Po identifikaci zdrojů dat použijte [Pomocník s migrací SQL serveru (SSMA) pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) k vyhodnocení instancí Oracle, které se migrují na virtuální počítač SQL Server, abyste porozuměli mezerám mezi nimi. Pomocí Pomocníka s migrací můžete zkontrolovat objekty databáze a data, posoudit databáze pro migraci, migrovat objekty databáze do SQL Server a potom migrovat data do SQL Server.

K vytvoření posouzení použijte následující postup: 

1. Otevřete  [Pomocník s migrací SQL serveru (SSMA) pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Vyberte **soubor** a pak zvolte **Nový projekt**. 
1. Zadejte název projektu, umístění, kam chcete projekt uložit, a potom z rozevíracího seznamu vyberte cíl migrace SQL Server. Vyberte **OK**. 
1. V dialogovém okně **připojit k systému Oracle** zadejte v části hodnoty pro informace o připojení Oracle.
1. V **Průzkumníku metadat Oracle** klikněte pravým tlačítkem na schéma Oracle, které chcete migrovat, a pak zvolte **vytvořit sestavu**. Tím se vygeneruje sestava HTML. Alternativně můžete zvolit možnost **vytvořit sestavu** z navigačního panelu po výběru databáze.

1. V **Průzkumníku metadat Oracle** vyberte schéma Oracle a pak vyberte **vytvořit sestavu** pro vygenerování sestavy HTML s statistikou převodu a chybou nebo upozorněním, pokud existují.
1. Podívejte se na sestavu ve formátu HTML pro statistiku převodu a také chyby a upozornění. Analyzujte je, abyste pochopili problémy s konverzí a jejich řešení.

   K této sestavě se dá také dostat ze složky projekty SSMA, která je vybraná na první obrazovce. Z výše uvedeného příkladu vyhledejte soubor report.xml z: 

   `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`

    a pak ho otevřete v Excelu, abyste získali inventář objektů Oracle a úsilí potřebného k provádění převodů schématu.


### <a name="validate-data-types"></a>Ověřit datové typy

Ověřte výchozí mapování datových typů a podle potřeby je změňte podle požadavků. To můžete provést pomocí těchto kroků: 

1. V nabídce vyberte **nástroje** . 
1. Vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** . 
1. Mapování typů pro každou tabulku můžete změnit tak, že vyberete tabulku v **Průzkumníku metadat Oracle**. 



### <a name="convert-schema"></a>Převést schéma

K převedení schématu použijte následující postup: 

1. Volitelné Chcete-li převést dynamické nebo ad hoc dotazy, klikněte pravým tlačítkem myši na uzel a vyberte **příkaz Přidat příkaz**.
1. V horním navigačním panelu vyberte **připojit k SQL Server** a zadejte podrobnosti o připojení k vašemu SQL Server na virtuálním počítači Azure. Můžete se rozhodnout připojit se k existující databázi nebo zadat nový název. v takovém případě se databáze vytvoří na cílovém serveru.
1. Klikněte pravým tlačítkem na schéma a vyberte **převést schéma**.
1. Po dokončení převodu schématu Porovnejte a zkontrolujte strukturu schématu a Identifikujte potenciální problémy.

   Projekt můžete uložit místně pro práci s offline schématem. To lze provést výběrem možnosti **Uložit projekt** v nabídce **soubor** . Díky tomu máte možnost vyhodnotit zdrojový a cílový schémat v režimu offline a před publikováním schématu pro SQL Server provést nápravu.


## <a name="migrate"></a>Migrate

Po splnění potřebných požadavků a dokončení úkolů přidružených ke fázi **před migrací** jste připraveni provést migraci schématu a dat. Migrace zahrnuje dva kroky – publikování schématu a migrace dat. 


K publikování schématu a migraci dat použijte následující postup: 

1. Klikněte pravým tlačítkem na databázi z **SQL Server Průzkumníku metadat**  a vyberte **synchronizovat s databází**. Tato akce publikuje schéma Oracle pro SQL Server na virtuálním počítači Azure. 
1. V **Průzkumníku metadat Oracle** klikněte pravým tlačítkem na schéma Oracle a vyberte **migrovat data**. Alternativně můžete vybrat možnost migrovat data z navigace na horním řádku.
1. Zadání podrobností o připojení pro Oracle a SQL Server na virtuálním počítači Azure v dialogovém okně.
1. Po dokončení migrace si prohlédněte sestavu migrace dat:
1. Připojte se k vašemu SQL Server na VIRTUÁLNÍm počítači Azure pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) pro kontrolu dat a schématu vaší instance SQL Server. 


Kromě použití SSMA můžete k migraci dat použít taky služba SSIS (SQL Server Integration Services) (SSIS). Další informace najdete v následujících tématech: 
- Článek [Začínáme služba SSIS (SQL Server Integration Services)](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
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
| [Příručka k migraci z Oracle do SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Tento dokument se zaměřuje na úlohy spojené s migrací schématu Oracle na nejnovější verzi SQL ServerBase. Pokud migrace vyžaduje změny funkcí a funkcí, bude možné dopad každé změny v aplikacích používajících databázi pečlivě zvážit.                                                     |

Tyto prostředky byly vyvinuty jako součást programu data SQL expertem, který je financován technickým týmem Azure Data Group. Základní Chartou programu data SQL expertem je odblokování a urychlení komplexní modernizace a konkurenční možnosti migrace datových platforem na datovou platformu Azure od Microsoftu. Pokud si myslíte, že by vaše organizace mohla zajímat účast v programu data SQL expertem, obraťte se prosím na svůj tým a požádejte ho, aby podal jmenování.

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

