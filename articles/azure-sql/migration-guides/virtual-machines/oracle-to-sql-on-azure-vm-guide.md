---
title: 'Oracle pro SQL Server v Azure Virtual Machines: Průvodce migrací'
description: V této příručce se naučíte migrovat schémata Oracle pro SQL Server v Azure Virtual Machines pomocí Pomocník s migrací SQL Serveru pro Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: d4fb33e8e904d12e242f7eeaf9c2dc50a02eff4d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961247"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>Průvodce migrací: Oracle pro SQL Server v Azure Virtual Machines
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

V této příručce se naučíte migrovat schémata Oracle pro SQL Server v Azure Virtual Machines pomocí Pomocník s migrací SQL Serveru pro Oracle. 

Další příručky k migraci najdete v tématu [migrace databáze](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Požadavky 

K migraci schématu Oracle pro SQL Server v Azure Virtual Machines potřebujete:

- Podporované zdrojové prostředí.
- [Pomocník s migrací SQL serveru (SSMA) pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Cílový [virtuální počítač SQL Server](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- [Potřebná oprávnění pro SSMA pro Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) a [poskytovatele](/sql/ssma/oracle/connect-to-oracle-oracletosql).
- Připojení a dostatečná oprávnění pro přístup ke zdroji a cíli. 


## <a name="pre-migration"></a>Před migrací

Pokud chcete připravit migraci na Cloud, ověřte, že je vaše zdrojové prostředí podporované a že jste vyřešili nějaké požadavky. To vám pomůže zajistit efektivní a úspěšnou migraci.

Tato část procesu zahrnuje: 
- Provádění inventáře databází, které potřebujete migrovat.
- Vyhodnocování těchto databází pro potenciální problémy s migrací nebo blokování. 
- Řešení všech problémů, které zjistíte. 

### <a name="discover"></a>Zjišťování

Pomocí [map toolkitu](https://go.microsoft.com/fwlink/?LinkID=316883) můžete identifikovat stávající zdroje dat a podrobnosti o funkcích, které vaše firma používá. Tím zajistíte lepší porozumění migraci a pomůže vám to naplánovat. Tento proces zahrnuje kontrolu sítě a identifikaci instancí Oracle vaší organizace a verzí a funkcí, které používáte.

Pokud chcete k prověřování inventáře použít sadu MAP, postupujte takto: 


1. Otevřete [sadu nástrojů mapa](https://go.microsoft.com/fwlink/?LinkID=316883).


1. Vyberte **vytvořit/vybrat databázi**:

   ![Snímek obrazovky, který ukazuje možnost vytvořit/vybrat databázi.](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Vyberte **vytvořit databázi inventáře**. Zadejte název nové databáze inventáře, kterou vytváříte, zadejte krátký popis a pak vyberte **OK**: 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Snímek obrazovky, který zobrazuje rozhraní pro vytvoření databáze inventáře.":::

1. Vyberte **shromažďovat data inventáře** a otevřete tak **Průvodce pro inventarizaci a vyhodnocení**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Snímek obrazovky, který zobrazuje odkaz na data shromáždění inventáře.":::


1. V **Průvodci inventarizace a vyhodnocení** vyberte **Oracle** a potom vyberte **Další**:

   ![Snímek obrazovky zobrazující stránku scénáře inventarizace v průvodci inventarizací a hodnocením.](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Vyberte možnost hledání počítače, která nejlépe vyhovuje vašim obchodním potřebám a prostředí, a potom vyberte **Další**: 

   ![Snímek obrazovky zobrazující stránku metody zjišťování v průvodci inventarizací a hodnocením.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Buď zadejte přihlašovací údaje, nebo vytvořte nové přihlašovací údaje pro systémy, které chcete prozkoumat, a pak vyberte **Další**:

    ![Snímek obrazovky zobrazující stránku přihlašovací údaje všech počítačů v průvodci inventarizací a hodnocením.](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. Nastavte pořadí přihlašovacích údajů a potom vyberte **Další**: 

   ![Snímek obrazovky zobrazující stránku pořadí přihlašovacích údajů v průvodci inventarizací a hodnocením.](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. Zadejte přihlašovací údaje pro každý počítač, který chcete zjistit. Pro každý počítač/počítač můžete použít jedinečné přihlašovací údaje, nebo můžete použít seznam přihlašovacích údajů všech počítačů.  

   ![Snímek obrazovky zobrazující stránku zadat počítače a přihlašovací údaje v průvodci inventarizací a hodnocením](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Ověřte výběr a pak vyberte **Dokončit**:

   ![Snímek obrazovky zobrazující stránku Souhrn v průvodci inventarizací a hodnocením.](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. Po dokončení kontroly si prohlédněte souhrn **shromažďování dat** . Kontrola může trvat několik minut v závislosti na počtu databází. Po skončení vyberte **Zavřít** : 

   ![Snímek obrazovky zobrazující souhrn shromažďování dat](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Vyberte **Možnosti** pro vygenerování sestavy o podrobnostech o vyhodnocení Oracle a databázi. K vygenerování sestavy vyberte obě možnosti, jednu po druhé.


### <a name="assess"></a>Posouzení

Po identifikaci zdrojů dat použijte [Pomocník s migrací SQL serveru pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) k vyhodnocení instancí Oracle, které migrují na virtuální počítač SQL Server. Pomocník vám pomůže pochopit mezery mezi zdrojovou a cílovou databází. Můžete zkontrolovat objekty databáze a data, posoudit databáze pro migraci, migrovat objekty databáze do SQL Server a potom migrovat data do SQL Server.

K vytvoření posouzení použijte následující postup: 


1. Otevřete [Pomocník s migrací SQL serveru pro Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. V nabídce **soubor** vyberte **Nový projekt**. 
1. Zadejte název projektu a umístění pro váš projekt a potom v seznamu vyberte cíl migrace SQL Server. Vyberte **OK**: 

   ![Snímek obrazovky, který zobrazuje dialogové okno Nový projekt.](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. Vyberte **připojit se k Oracle**. Do dialogového okna **připojit k systému Oracle** zadejte hodnoty pro připojení Oracle:

   ![Snímek obrazovky, který zobrazuje dialogové okno připojit k systému Oracle.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Vyberte schémata Oracle, která chcete migrovat: 

   ![Snímek obrazovky zobrazující seznam schémat Oracle, která se dají migrovat](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. V **Průzkumníku metadat Oracle** klikněte pravým tlačítkem na schéma Oracle, které chcete migrovat, a pak vyberte **vytvořit sestavu**. Tím se vygeneruje sestava HTML. Alternativně můžete vybrat databázi a potom v horní nabídce vybrat **vytvořit sestavu** .

   ![Snímek obrazovky, který ukazuje, jak vytvořit sestavu.](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Podívejte se na zprávu HTML pro statistiku převodu, chyby a upozornění. Analyzujte je, abyste pochopili problémy s konverzí a jejich řešení.

    Sestavu můžete také otevřít v aplikaci Excel a získat tak inventarizaci objektů Oracle a úsilí potřebného k dokončení převodů schématu. Výchozím umístěním pro sestavu je složka sestavy v SSMAProjects. 

   Příklad: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![Snímek obrazovky, který zobrazuje sestavu pro převod.](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>Ověřit datové typy

Ověřte výchozí mapování datových typů a podle potřeby je podle požadavků změňte. To můžete provést pomocí těchto kroků: 


1. V nabídce **nástroje** vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** . 

   ![Snímek obrazovky zobrazující kartu mapování typů](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Mapování typů pro každou tabulku můžete změnit tak, že ji vyberete v **Průzkumníku metadat Oracle**. 

### <a name="convert-the-schema"></a>Převést schéma

K převedení schématu použijte následující postup: 

1. Volitelné Chcete-li převést dynamické nebo ad hoc dotazy, klikněte pravým tlačítkem myši na uzel a vyberte **příkaz Přidat příkaz**.

1. V horní nabídce vyberte **připojit k SQL Server** . 
     1. Zadejte podrobnosti o připojení pro váš SQL Server na VIRTUÁLNÍm počítači Azure. 
     1. V seznamu vyberte cílovou databázi nebo zadejte nový název. Pokud zadáte nový název, vytvoří se na cílovém serveru databáze. 
     1. Zadejte podrobnosti ověřování. 
     1. Vyberte **Connect** (Připojit). 


   ![Snímek obrazovky, který ukazuje, jak se připojit k SQL Server](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. V **Průzkumníku metadat Oracle** klikněte pravým tlačítkem na schéma Oracle a vyberte **převést schéma**. Alternativně můžete vybrat **převést schéma** v horní nabídce:

   ![Snímek obrazovky, který ukazuje, jak převést schéma.](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. Po dokončení převodu schématu zkontrolujte převedené objekty a porovnejte je s původními objekty a Identifikujte možné problémy. K řešení jakýchkoli problémů použijte doporučení:

   ![Snímek obrazovky, který zobrazuje porovnání dvou schémat.](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Porovnejte převedený text Transact-SQL s původními uloženými procedurami a Projděte si doporučení: 

   ![Snímek obrazovky, který ukazuje Transact-SQL, uložené procedury a upozornění.](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Projekt můžete uložit místně pro práci s offline schématem. Provedete to tak, že v nabídce **soubor** vyberete **Uložit projekt** . Místní uložení projektu umožňuje vyhodnotit zdrojový a cílový schémat offline a provést nápravu před publikováním schématu do SQL Server.

1. V podokně **výstup** vyberte možnost **Kontrola výsledků** a v podokně **Seznam chyb** Zkontrolujte chyby. 
1. Uložte projekt místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt** . Díky tomu máte možnost vyhodnotit zdrojové a cílové schémat v režimu offline a před publikováním schématu SQL Server v Azure Virtual Machines provést nápravu.


## <a name="migrate"></a>Migrate

Po splnění potřebných požadavků a dokončení úkolů přidružených ke fázi před migrací jste připraveni zahájit migraci schématu a dat. Migrace zahrnuje dva kroky: publikování schématu a migrace dat. 


K publikování schématu a migraci dat použijte následující postup: 

1. Publikování schématu: klikněte pravým tlačítkem na databázi v **SQL Server Průzkumníku metadat** a vyberte **synchronizovat s databází**. Provedete to tak, že publikujete schéma Oracle pro SQL Server v Azure Virtual Machines. 

   ![Snímek obrazovky zobrazující příkaz synchronizovat s databází](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Zkontrolujte mapování mezi zdrojovým projektem a vaším cílem:

   ![Snímek obrazovky, který zobrazuje stav synchronizace.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. Migrace dat: klikněte pravým tlačítkem na databázi nebo objekt, který chcete migrovat v **Průzkumníkovi metadat Oracle** , a vyberte **migrovat data**. Alternativně můžete vybrat možnost **migrovat data** v horní nabídce.

   Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku **tabulky** a potom zaškrtněte políčko vedle této tabulky. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí příslušných políček.

   ![Snímek obrazovky zobrazující příkaz migrace dat](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Zadejte podrobnosti o připojení pro Oracle a SQL Server v Azure Virtual Machines v dialogovém okně.
1. Po dokončení migrace si prohlédněte **sestavu migrace dat**:

    ![Snímek obrazovky zobrazující sestavu migrace dat](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)se připojte ke svému SQL Server na Azure Virtual Machines instanci. Ověřte migraci tím, že zkontrolujete data a schéma:


   ![Snímek obrazovky zobrazující instanci SQL Server v SSMA](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

Místo použití SSMA můžete k migraci dat použít služba SSIS (SQL Server Integration Services) (SSIS). Další informace najdete v následujících tématech: 
- Článek [služba SSIS (SQL Server Integration Services)](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- Dokument White Paper [SSIS pro Azure a přesun hybridních dat](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)


## <a name="post-migration"></a>Po migraci 

Po dokončení fáze migrace je potřeba dokončit sérii úloh po migraci, abyste měli jistotu, že všechno běží co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Provádění těchto změn může vyžadovat změny aplikací.

[Sada nástrojů pro migraci Data Access](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) je rozšířením pro Visual Studio Code. Umožňuje analyzovat zdrojový kód Java a detekovat volání a dotazy rozhraní API pro přístup k datům. Sada nástrojů poskytuje přehled o tom, co je potřeba řešit pro podporu nového back-endu databáze. Další informace najdete v tématu [migrace aplikace v jazyce Java z Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 

### <a name="perform-tests"></a>Provést testy

K otestování migrace databáze dokončete tyto činnosti:

1. **Vývoj ověřovacích testů**. K otestování migrace databáze je nutné použít dotazy SQL. Vytvořte dotazy ověřování pro spuštění na zdrojové i cílové databázi. Dotazy na ověřování by měly pokrývat obor, který jste definovali.

2. **Nastavte testovací prostředí**. Testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.

3. **Spusťte ověřovací testy**. Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.

4. **Spusťte testy výkonu**. Spusťte test výkonnosti proti zdroji a cíli a pak Analyzujte a porovnejte výsledky.

### <a name="optimize"></a>Optimalizace

Fáze po migraci je zásadní pro sjednocení všech problémů s přesností dat a ověření úplnosti. Je také důležité pro řešení problémů s výkonem s úlohou.

> [!Note]
> Další informace o těchto problémech a konkrétních krocích, jak je zmírnit, najdete v [Průvodci pro ověřování po migraci a optimalizaci](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-resources"></a>Prostředky migrace 

Další nápovědu k dokončení tohoto scénáře migrace najdete v následujících zdrojích, které byly vyvinuty za účelem podpory reálného projektu migrace.

| **Název/odkaz**                                                                                                                                          | **Popis**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Tento nástroj poskytuje navrhované nejlepší cílení platformy, připravenost na Cloud a aplikace/databáze úrovní nápravy pro danou úlohu. Nabízí jednoduché výpočty s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení velkých nemovitostí tím, že poskytuje automatizovaný a jednotný proces rozhodování na platformě.                                                          |
| [Artefakty skriptu pro inventář Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Tento prostředek obsahuje dotaz PL/SQL, který cílí na systémové tabulky Oracle a poskytuje počet objektů podle typu schématu, typu objektu a stavu. Poskytuje také hrubý odhad nezpracovaných dat v každém schématu a velikost tabulek v jednotlivých schématech s výsledky uloženými ve formátu CSV.                                                                                                               |
| [Automatizace & konsolidace kolekce vyhodnocení pro SSMA Oracle](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Tato sada prostředků používá soubor. csv jako položku (sources.csv ve složkách projektu) k vytvoření souborů XML, které potřebujete ke spuštění posouzení SSMA v režimu konzoly. Soubor source.csv zadáte tak, že převezmete inventář stávajících instancí Oracle. Výstupní soubory jsou AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml a VariableValueFile.xml.|
| [SSMA problémy a možné nápravy při migraci databází Oracle](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | V případě Oracle můžete v klauzuli WHERE přiřadit neskalární podmínku. SQL Server nepodporuje tento typ podmínky. Proto SSMA pro Oracle nepřevádí dotazy, které mají neskalární podmínku v klauzuli WHERE. Místo toho generuje chybu: O2SS0001. Tento dokument white paper poskytuje podrobné informace o problémech a způsobech jejich řešení.          |
| [Příručka k migraci z Oracle do SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Tento dokument se zaměřuje na úlohy spojené s migrací schématu Oracle na nejnovější verzi SQL Server. Pokud migrace vyžaduje změny funkcí nebo funkcí, je nutné pečlivě zvážit možný účinek každé změny v aplikacích, které databázi používají.                                                     |


Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na Microsoft Azure datovou platformu.


## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit dostupnost služeb, které platí pro SQL Server, přečtěte si téma [Azure Global Infrastructure Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici pro různé scénáře migrace databáze a dat a specializované úkoly, najdete v tématu [služby a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o Azure SQL najdete tady:
   - [Možnosti nasazení](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server na Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/)


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete tady:
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro náklady a velikost úloh migrovaných do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Informace o licencování najdete v tématech:
   - [Využijte vlastní licenci s Zvýhodněné hybridní využití Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Získejte bezplatnou rozšířenou podporu pro SQL Server 2008 a SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- K vyhodnocení vrstvy přístupu k aplikacím použijte [verzi Preview sady Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Podrobnosti o tom, jak provést testování vrstvy přístupu k datům a/B, najdete v tématu [přehled Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).


