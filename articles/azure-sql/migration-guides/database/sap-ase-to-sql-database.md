---
title: 'Pomocného programu SAP pro Azure SQL Database: Průvodce migrací'
description: V této příručce se dozvíte, jak migrovat databáze SAP pomocného programu do databáze SQL Azure pomocí Pomocník s migrací SQL Serveru pro SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 138a23b610ab96194424bb0f88cf94f516c2d223
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626448"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Příručka k migraci: pomocnému programu SAP pro Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

V této příručce se dozvíte, jak migrovat databáze SAP Adapter Server Enterprise (pomocného programu) do databáze SQL Azure pomocí Pomocník s migrací SQL Serveru pro SAP Adapter Server Enterprise.

Další příručky k migraci najdete v tématu [Průvodce migrací databáze Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Požadavky 

Než začnete migrovat databázi SAP SE do vaší databáze SQL, udělejte toto:

- Ověřte, že je podporované vaše zdrojové prostředí. 
- Stáhněte a nainstalujte [Pomocník s migrací SQL serveru pro SAP Adaptive Server Enterprise (dříve SAP Sybase POmocného mechanismu řízení)](https://www.microsoft.com/en-us/download/details.aspx?id=54256).
- Ujistěte se, že máte připojení a máte dostatečná oprávnění pro přístup ke zdroji i cíli.

## <a name="pre-migration"></a>Před migrací

Až splníte požadavky, budete připraveni zjistit topologii vašeho prostředí a posoudit proveditelnost migrace.

### <a name="assess"></a>Posouzení

Pomocí [Pomocník s migrací SQL serveru (SSMA) pro SAP Adaptive Server Enterprise (formálně SAP Sybase POmocného mechanismu přístupu)](https://www.microsoft.com/en-us/download/details.aspx?id=54256)můžete zkontrolovat objekty databáze a data, posoudit databáze pro migraci, migrovat objekty databáze Sybase do vaší databáze SQL a pak migrovat data do databáze SQL. Další informace najdete v tématu [Pomocník s migrací SQL serveru pro Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Chcete-li vytvořit posouzení, postupujte následovně: 

1. Otevřete SSMA pro Sybase. 
1. Vyberte **soubor** a pak vyberte **Nový projekt**. 
1. V podokně **Nový projekt** zadejte název a umístění projektu a potom v rozevíracím seznamu **migrovat do** vyberte možnost **Azure SQL Database**. 
1. Vyberte **OK**.
1. V podokně **připojit k systému Sybase** zadejte podrobnosti o připojení SAP. 
1. Klikněte pravým tlačítkem na databázi SAP, kterou chcete migrovat, a pak vyberte **vytvořit sestavu**. Tím se vygeneruje sestava HTML. Alternativně můžete vybrat kartu **vytvořit sestavu** v pravém horním rohu.
1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Můžete také otevřít sestavu v aplikaci Excel a získat soupis objektů SAP pomocného programu a úsilí, které je potřeba k provádění převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects. Například:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Ověřit mapování typů

Před provedením převodu schématu ověřte výchozí mapování datových typů nebo je změňte na základě požadavků. Můžete to udělat tak, že **vyberete**  >  **možnost nastavení projektu**, nebo můžete změnit mapování typů pro každou tabulku tak, že vyberete tabulku v **Průzkumníkovi protokolu SAP pomocného mechanismu**.

### <a name="convert-the-schema"></a>Převést schéma

Chcete-li převést schéma, postupujte následovně:

1. Volitelné Chcete-li převést dynamické nebo specializované dotazy, klikněte pravým tlačítkem myši na uzel a pak vyberte **příkaz Přidat příkaz**. 
1. Vyberte kartu **připojit k Azure SQL Database** a potom zadejte podrobnosti pro vaši databázi SQL. Můžete se rozhodnout připojit se k existující databázi nebo zadat nový název. v takovém případě se databáze vytvoří na cílovém serveru.
1. V podokně **Průzkumník metadat Sybase** klikněte pravým tlačítkem na schéma SAP pomocného programu, se kterým pracujete, a pak vyberte **převést schéma**. 
1. Po převedení schématu Porovnejte a zkontrolujte převedenou strukturu do původní struktury a Identifikujte potenciální problémy. 

   Po převodu schématu můžete tento projekt uložit místně pro práci offline schématu pro nápravu. Provedete to tak, že vyberete **soubor**  >  **Uložit projekt**. Získáte tak možnost vyhodnotit zdrojové a cílové schémat v režimu offline a před publikováním schématu do vaší databáze SQL provést nápravu.

1. V podokně **výstup** vyberte **zkontrolovat výsledky** a zkontrolujte všechny chyby v podokně **Seznam chyb** . 
1. Uložte projekt místně pro práci offline schématu pro nápravu. Provedete to tak, že vyberete **soubor**  >  **Uložit projekt**. Získáte tak možnost vyhodnotit zdrojové a cílové schémat v režimu offline a před publikováním schématu do vaší databáze SQL provést nápravu.

## <a name="migrate-the-databases"></a>Migrace databází 

Po splnění potřebných požadavků a dokončení úloh přidružených ke fázi *před migrací* jste připraveni ke spuštění migrace schématu a dat.

Chcete-li publikovat schéma a migrovat data, postupujte následovně: 

1. Publikování schématu. V podokně **Azure SQL Database Průzkumník metadat** klikněte pravým tlačítkem na databázi a pak vyberte **synchronizovat s databází**. Tato akce publikuje schéma SAP pomocného mechanismu pro vaši databázi SQL.

1. Migrujte data. V podokně **Průzkumník metadat POmocného seznamu SAP** klikněte pravým tlačítkem na databázi nebo objekt SAP pomocného mechanismu, který chcete migrovat, a pak vyberte **migrovat data**. Alternativně můžete vybrat kartu **migrovat data** v pravém horním rohu. 

   Chcete-li migrovat data pro celou databázi, zaškrtněte políčko vedle názvu databáze. Chcete-li migrovat data z jednotlivých tabulek, rozbalte databázi, rozbalte položku **tabulky** a potom zaškrtněte políčko vedle této tabulky. Chcete-li vynechat data z jednotlivých tabulek, zrušte zaškrtnutí políčka. 
1. Po dokončení migrace si prohlédněte **sestavu migrace dat**. 
1. Ověřte migraci kontrolou dat a schématu. Provedete to tak, že se připojíte k databázi SQL pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

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


## <a name="next-steps"></a>Další kroky

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o Azure SQL Database najdete v těchto tématech:
   - [Přehled SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/)  

- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete tady:
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a velikosti úloh pro migraci do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Chcete-li vyhodnotit vrstvu přístupu aplikace, přečtěte si část [Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).
