---
title: 'Přístup k Azure SQL Database: Průvodce migrací'
description: V této příručce se dozvíte, jak migrovat databáze Microsoft Access do databáze SQL Azure pomocí Pomocník s migrací SQL Serveru pro přístup (SSMA for Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 73ee4d4be16284880b10df4a52b422a08c04c6a0
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284183"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Průvodce migrací: přístup k Azure SQL Database

V této příručce se dozvíte, [jak migrovat](https://azure.microsoft.com/migration/migration-journey) databázi Microsoft Access do Azure SQL database pomocí SQL Server Pomocník pro [migraci](https://azure.microsoft.com/en-us/migration/sql-server/) pro přístup (SSMA for Access).

Další příručky k migraci najdete v tématu [Průvodce migrací databáze Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Požadavky

Než začnete migrovat databázi Access do databáze SQL, udělejte toto:

- Ověřte, že je podporované vaše zdrojové prostředí. 
- Stáhněte a nainstalujte [Pomocník s migrací SQL serveru pro přístup](https://www.microsoft.com/download/details.aspx?id=54255).
- Ujistěte se, že máte připojení a máte dostatečná oprávnění pro přístup ke zdroji i cíli.

## <a name="pre-migration"></a>Před migrací

Až splníte požadavky, budete připraveni zjistit topologii vašeho prostředí a posoudit proveditelnost [migrace do cloudu Azure](https://azure.microsoft.com/migration).


### <a name="assess"></a>Posouzení 

Použijte SSMA pro přístup ke kontrole databázových objektů a dat a vyhodnocení databází pro migraci. 

Chcete-li vytvořit posouzení, postupujte následovně: 

1. Otevřete [SSMA pro přístup](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Vyberte **soubor** a pak vyberte **Nový projekt**. 
1. Zadejte název projektu a umístění pro váš projekt a potom v rozevíracím seznamu vyberte jako cíl migrace možnost **Azure SQL Database** . 
1. Vyberte **OK**. 

   ![Snímek obrazovky s podoknem nový projekt pro zadání názvu a umístění projektu migrace](./media/access-to-sql-database-guide/new-project.png)

1. Vyberte **přidat databáze** a pak vyberte databáze, které chcete přidat do nového projektu. 

   ![Snímek obrazovky s kartou "přidat databáze" v SSMA pro přístup](./media/access-to-sql-database-guide/add-databases.png)

1. V podokně **Průzkumník přístupových metadat** klikněte pravým tlačítkem na databázi a potom vyberte **vytvořit sestavu**. Alternativně můžete vybrat kartu **vytvořit sestavu** v pravém horním rohu.

   ![Snímek obrazovky s příkazem Vytvořit sestavu v aplikaci Access metadata Explorer](./media/access-to-sql-database-guide/create-report.png)

1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Můžete také otevřít sestavu v aplikaci Excel a získat tak inventář objektů přístupu a pochopit úsilí potřebné k provádění převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects. Například:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Snímek obrazovky ukázkového vyhodnocení sestavy databáze v SSMA](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Ověření datových typů

Ověřte výchozí mapování datových typů a podle potřeby je podle vašich požadavků změňte. Postupujte následovně:

1. V SSMA pro přístup vyberte **nástroje** a pak vyberte **nastavení projektu**. 
1. Vyberte kartu **mapování typů** . 

   ![Snímek obrazovky s podoknem mapování typů v SSMA pro přístup](./media/access-to-sql-database-guide/type-mappings.png)

1. Mapování typů pro každou tabulku můžete změnit tak, že vyberete název tabulky v podokně **Průzkumník přístupových metadat** .


### <a name="convert-the-schema"></a>Převést schéma

Chcete-li převést databázové objekty, postupujte následovně: 

1. Vyberte kartu **připojit k Azure SQL Database** a proveďte následující kroky:

   a. Zadejte podrobnosti pro připojení k vaší databázi SQL.  
   b. V rozevíracím seznamu vyberte cílovou databázi SQL. Případně můžete zadat nový název. v takovém případě bude databáze vytvořena na cílovém serveru.  
   c. Zadejte podrobnosti ověřování.   
   d. Vyberte **Connect** (Připojit).

   ![Snímek obrazovky s podoknem připojit k Azure SQL Database pro zadání podrobností o připojení](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. V podokně **Průzkumník přístupových metadat** klikněte pravým tlačítkem na databázi a pak vyberte **převést schéma**. Případně můžete vybrat databázi a pak vybrat kartu **převést schéma** .

   ![Snímek obrazovky s příkazem "převést schéma" v podokně "Access metadata Explorer".](./media/access-to-sql-database-guide/convert-schema.png)

1. Po dokončení převodu Porovnejte převedené objekty s původními objekty a Identifikujte možné problémy a vyřešte problémy na základě doporučení.

   ![Snímek obrazovky znázorňující porovnání převedených objektů se zdrojovými objekty.](./media/access-to-sql-database-guide/table-comparison.png)

    Porovnejte převedený text Transact-SQL s původním kódem a přečtěte si doporučení.

   ![Snímek obrazovky znázorňující porovnání převedených dotazů se zdrojovým kódem.](./media/access-to-sql-database-guide/query-comparison.png) 

1. Volitelné Chcete-li převést jednotlivý objekt, klikněte pravým tlačítkem myši na objekt a poté vyberte možnost **převést schéma**. Převedené objekty se v **aplikaci Access metadata Explorer** zobrazují tučně v textu: 

   ![Snímek obrazovky zobrazující, že jsou převedeny objekty v aplikaci Access metadata Explorer](./media/access-to-sql-database-guide/converted-items.png)
 
1. V podokně **výstup** vyberte ikonu **Kontrola výsledků** a zkontrolujte chyby v podokně **Seznam chyb** . 
1. Uložte projekt místně pro práci offline schématu pro nápravu. Provedete to tak, že vyberete **soubor**  >  **Uložit projekt**. Získáte tak možnost vyhodnotit zdrojové a cílové schémat v režimu offline a před jejich publikováním do databáze SQL provést nápravu.

## <a name="migrate-the-databases"></a>Migrace databází

Po vyhodnocení databází a vyřešení případných rozporů můžete spustit proces migrace. Migrace dat je operace hromadného načtení, která přesouvá řádky dat do databáze SQL Azure v transakcích. Počet řádků, které mají být načteny do databáze SQL v každé transakci, je konfigurován v nastavení projektu.

Pokud chcete publikovat schéma a migrovat data pomocí SSMA pro přístup, udělejte toto: 

1. Pokud jste to ještě neudělali, vyberte **připojit k Azure SQL Database** a zadejte podrobnosti o připojení. 

1. Publikování schématu. V podokně **Azure SQL Database Průzkumník metadat** klikněte pravým tlačítkem na databázi, se kterou právě pracujete, a pak vyberte **synchronizovat s databází**. Tato akce publikuje schéma MySQL do databáze SQL.

1. V podokně **synchronizovat s databází** zkontrolujte mapování mezi zdrojovým projektem a vaším cílem:

   ![Snímek obrazovky s podoknem synchronizace s databází pro kontrolu synchronizace s databází](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. V podokně **přístupová metadata v prohlížeči** zaškrtněte políčka vedle položek, které chcete migrovat. Chcete-li migrovat celou databázi, zaškrtněte políčko vedle databáze. 

1. Migrujte data. Klikněte pravým tlačítkem na databázi nebo objekt, který chcete migrovat, a pak vyberte **migrovat data**. Alternativně můžete vybrat kartu **migrovat data** v pravém horním rohu.  

   Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku **tabulky** a potom zaškrtněte políčko vedle této tabulky. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí políčka.

    ![Snímek obrazovky s příkazem "migrovat data" v podokně "Access metadata Explorer".](./media/access-to-sql-database-guide/migrate-data.png)

1. Po dokončení migrace si prohlédněte **sestavu migrace dat**.  

    ![Snímek obrazovky s podoknem "migrace dat sestavy" zobrazující ukázkovou sestavu pro kontrolu.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Připojte se k databázi SQL Azure pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)a ověřte migraci kontrolou dat a schématu.

   ![Snímek obrazovky SQL Server Management Studio Průzkumník objektů pro ověření migrace v SSMA.](./media/access-to-sql-database-guide/validate-data.png)

## <a name="post-migration"></a>Po migraci 

Po úspěšném dokončení fáze *migrace* je potřeba dokončit sérii úloh po migraci, abyste měli jistotu, že všechno funguje co nejrychleji a efektivně.

### <a name="remediate-applications"></a>Opravit aplikace

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Výsledkem bude, že v některých případech bude nutné provést změny aplikací.

### <a name="perform-tests"></a>Provést testy

Testovací přístup k migraci databáze se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů**: k otestování migrace databáze je nutné použít dotazy SQL. Je nutné vytvořit ověřovací dotazy ke spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.

1. **Nastavení testovacího prostředí**: testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.

1. **Spustit ověřovací testy**: Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.

1. **Spustit testy výkonu**: spustit testy výkonu proti zdroji a cíli a pak výsledky analyzovat a porovnat.


### <a name="optimize"></a>Optimalizace

Fáze po migraci je zásadní pro sjednocení problémů s přesností dat, ověřování úplnosti a řešení problémů s výkonem s úlohou.

Další informace o těchto problémech a krocích pro jejich zmírnění najdete v [Průvodci pro ověřování po migraci a optimalizaci](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Prostředky migrace 

Další pomoc s dokončením tohoto scénáře migrace najdete v následujícím prostředku. Bylo vyvinuto v rámci podpory realizace projektu migrace do reálného světa.

| Nadpis | Popis |
| --- | --- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Nabízí navrhované cílové platformy, připravenost do cloudu a úrovně nápravy aplikací a databází pro zadané úlohy. Nabízí jednoduché sestavování s jedním kliknutím a generování sestav, které pomáhá zrychlit vyhodnocení velkých nemovitostí poskytnutím automatizovaného procesu pro rozhodování zaměřeného na cílovou platformu. |

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.

## <a name="next-steps"></a>Další kroky

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o Azure SQL Database najdete v těchto tématech:
   - [Přehled SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete tady:
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a velikosti úloh pro migraci do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Prostředky Migrace do cloudu](https://azure.microsoft.com/migration/resources)


- Chcete-li vyhodnotit vrstvu přístupu aplikace, přečtěte si část [Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Informace o tom, jak provádět testování vrstvy přístupu k datům a/B, najdete v tématu [přehled Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).
