---
title: 'Pomocného programu SAP pro Azure SQL Database: Průvodce migrací'
description: V této příručce se dozvíte, jak migrovat databáze SAP pomocného programu pro Azure SQL Database pomocí Pomocník s migrací SQL Serveru pro SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 81956a16142f314f54afd9d5a1b9055a559e906c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564922"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Příručka k migraci: pomocnému programu SAP pro Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

V této příručce se dozvíte, jak migrovat databáze SAP pomocného programu pro Azure SQL Database pomocí Pomocník s migrací SQL Serveru pro SAP Adapter Server Enterprise.

Další příručky k migraci najdete v tématu [migrace databáze](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Požadavky 

K migraci databáze SAP SE do Azure SQL Database potřebujete:

- Ověřte, že je podporované vaše zdrojové prostředí. 
- [Pomocník s migrací SQL serveru pro SAP Adaptive Server Enterprise (dříve SAP Sybase POmocného mechanismu řízení)](https://www.microsoft.com/en-us/download/details.aspx?id=54256). 

## <a name="pre-migration"></a>Před migrací

Po splnění požadavků budete připraveni zjistit topologii prostředí a posoudit proveditelnost migrace.

### <a name="assess"></a>Posouzení

Pomocí [Pomocník s migrací SQL serveru (SSMA) pro SAP Adaptive Server Enterprise (formálně SAP Sybase POmocného mechanismu přístupu)](https://www.microsoft.com/en-us/download/details.aspx?id=54256) zkontrolujte objekty databáze a data, vyhodnoťte databáze pro migraci, migrujte databázové objekty Sybase do Azure SQL Database a pak migrujte data do Azure SQL Database. Další informace najdete v tématu [Pomocník s migrací SQL serveru pro Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

K vytvoření posouzení použijte následující postup: 

1. Otevřete **SSMA pro Sybase**. 
1. Vyberte **soubor** a pak zvolte **Nový projekt**. 
1. Zadejte název projektu, umístění, kam chcete projekt uložit, a potom v rozevíracím seznamu vyberte Azure SQL Database jako cíl migrace. Vyberte **OK**.
1. V dialogovém okně **připojit k Sybase** zadejte v části hodnoty pro podrobnosti o připojení SAP. 
1. Klikněte pravým tlačítkem na databázi SAP, kterou chcete migrovat, a pak zvolte **vytvořit sestavu**. Tím se vygeneruje sestava HTML.
1. Projděte si zprávu HTML, abyste pochopili statistiku převodu a případné chyby nebo upozornění. Můžete také otevřít sestavu v aplikaci Excel a získat tak inventarizaci objektů DB2 a úsilí potřebné k provedení převodů schématu. Výchozí umístění sestavy je ve složce sestavy v rámci SSMAProjects.

   Příklad: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 


### <a name="validate-type-mappings"></a>Ověřit mapování typů

Před provedením převodu schématu ověřte výchozí mapování DataType nebo je změňte na základě požadavků. To můžete udělat tak, že přejdete na nabídku **nástroje** a zvolíte **nastavení projektu** , nebo můžete změnit mapování typů pro každou tabulku tak, že vyberete tabulku v **Průzkumníkovi pro pomocného mechanismu SAP**.


### <a name="convert-schema"></a>Převést schéma

K převedení schématu použijte následující postup:

1. Volitelné Chcete-li převést dynamické nebo ad hoc dotazy, klikněte pravým tlačítkem myši na uzel a vyberte **příkaz Přidat příkaz**. 
1. V horním navigačním panelu vyberte **připojit k Azure SQL Database** a zadejte Azure SQL Database podrobnosti. Můžete se rozhodnout připojit se k existující databázi nebo zadat nový název. v takovém případě se databáze vytvoří na cílovém serveru.
1. V **Průzkumníku metadat Sybase** klikněte pravým tlačítkem na schéma SAP pomocného mechanismu řízení a vyberte **převést schéma**. Alternativně můžete vybrat **převést schéma** z navigačního panelu na horním řádku. 
1. Porovnejte a zkontrolujte strukturu schématu a Identifikujte potenciální problémy. 

   Po převodu schématu můžete tento projekt uložit místně pro práci offline schématu pro nápravu. V nabídce **soubor** vyberte **Uložit projekt** . Díky tomu máte možnost vyhodnotit zdrojový a cílový schémat v režimu offline a před publikováním schématu pro Azure SQL Database provést nápravu.

Další informace najdete v tématu [Převod schématu](/sql/ssma/sybase/converting-sybase-ase-database-objects-sybasetosql) .


## <a name="migrate"></a>Migrate 

Po splnění potřebných požadavků a dokončení úkolů přidružených ke fázi **před migrací** jste připraveni provést migraci schématu a dat.

K publikování schématu a migraci dat použijte následující postup: 

1. Klikněte pravým tlačítkem na databázi v **Azure SQL Database Průzkumníku metadat** a vyberte **synchronizovat s databází**.  Tato akce publikuje schéma SAP pomocného mechanismu pro instanci Azure SQL Database.
1. Klikněte pravým tlačítkem na schéma SAP pomocného panelu v **Průzkumníkovi metadat POmocného** seznamu a vyberte **migrovat data**.  Alternativně můžete vybrat možnost **migrovat data** z horního navigačního panelu.  
1. Po dokončení migrace si prohlédněte **sestavu migrace dat**: 
1. Ověřte migraci tak, že zkontrolujete data a schéma Azure SQL Database instance pomocí Azure SQL Database Management Studio (SSMS).


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

> [!NOTE]
> Další podrobnosti o těchto problémech a konkrétních krocích, jak je zmírnit, najdete v [Průvodci pro ověřování po migraci a optimalizaci](/sql/relational-databases/post-migration-validation-and-optimization-guide).


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
