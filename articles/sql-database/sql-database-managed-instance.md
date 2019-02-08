---
title: Upřesnit přehled zabezpečení dat Azure SQL Database | Dokumentace Microsoftu
description: Toto téma popisuje pokročilé zabezpečení dat ve službě Azure SQL Database a vysvětluje, jak to funguje a jak se liší od jednoho nebo součástí fondu databáze ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, vanto
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: c0067dc96837d758e4c2551bcb29faf63c1a7715
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895037"
---
# <a name="use-sql-database-advanced-data-security-with-virtual-networks-and-near-100-compatibility"></a>Použití rozšířené zabezpečení dat s virtuálními sítěmi a téměř 100 % kompatibility SQL Database

Managed instance je nová možnost nasazení služby Azure SQL Database poskytuje téměř 100 % kompatibilitu s nejnovějšími serveru SQL Server v místním (Enterprise Edition) databázový stroj, poskytuje nativní [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md) implementace, která řeší běžné problémy zabezpečení a [obchodního modelu](https://azure.microsoft.com/pricing/details/sql-database/) uspokojivým pro zákazníky v místním systému SQL Server. Model nasazení spravované instance umožňuje stávající zákazníci systému SQL Server k přenosu svých místních aplikací do cloudu s minimálními změnami aplikace a databáze. Možnost nasazení spravovaných instancí ve stejnou dobu, zachová všechny možnosti modelu PaaS (automatické použití dílčích oprav a verze aktualizace [automatické zálohování](sql-database-automated-backups.md), [vysoké dostupnosti](sql-database-high-availability.md) ), to výrazně snižuje režii na správu a celkové náklady na vlastnictví.

> [!IMPORTANT]
> Seznam oblastí, ve kterých je aktuálně k dispozici možnost nasazení spravované instance najdete v tématu [podporované oblasti](sql-database-managed-instance-resource-limits.md#supported-regions).

Následující diagram popisuje klíčové funkce spravované instance:

![klíčové funkce](./media/sql-database-managed-instance/key-features.png)

Model nasazení spravované instance je určená pro zákazníky v úmyslu migrovat velký počet aplikací z místní nebo IaaS, samoobslužné vytvořen, nebo nezávislých výrobců softwaru ve formě prostředí tak, aby plně spravované cloudové prostředí PaaS s nízkou migrace úsilí co nejvíce. Použití plně automatizované [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) v Azure, zákazníci mohou přenést a shift jejich místního SQL serveru do managed instance, která nabízí kompatibilitu s místním SQL serverem a kompletní izolaci zákaznických instancí s nativní podporou virtuální sítě.  S programem Software Assurance, mohou vyměňovat své stávající licence pro zvýhodněné sazby na spravované instance pomocí [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  Managed instance je nejlepším řešením migrace v cloudu pro instance systému SQL Server, vyžadujících vysoké zabezpečení a s rozsáhlou programovatelností pomocí povrchu.

Poskytuje možnost cílů nasazení spravované instance blízko povrchu 100 % kompatibilitu s nejnovější verzí systému SQL Server v místním prostřednictvím plánu dvoufázové instalace verze.

Rozhodování mezi možnostmi nasazení Azure SQL Database: izolovanou databázi, databázi ve fondu a spravované instance a SQL Server je hostovaný na virtuálním počítači, naleznete v tématu [jak zvolit správnou verzi systému SQL Server v Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Klíčové funkce a možnosti

Managed instance kombinuje nejlepší funkce, které jsou k dispozici v Azure SQL Database i SQL Server Database Engine.

> [!IMPORTANT]
> Spravovaná instance spouští všechny funkce nejnovější verzi SQL serveru, včetně online operace, plán automatické opravy a vylepšení výkonu jiné organizace. Porovnání funkcí, které jsou k dispozici je podrobně [porovnání funkcí: Azure SQL Database a SQL Server](sql-database-features.md).

| **Výhody PaaS** | **Kontinuita podnikových procesů** |
| --- | --- |
|Žádný hardware, nákup a správu <br>Není potřeba Správa režie pro správu základní infrastruktury <br>Rychlé zřizování a škálování služby <br>Automatické použití dílčích oprav a verze upgradu <br>Integrace s dalšími službami PaaS dat |99,99 % smlouva SLA o provozuschopnosti  <br>Součástí [vysoké dostupnosti](sql-database-high-availability.md) <br>Data chráněná [automatické zálohování](sql-database-automated-backups.md) <br>Období zákazník možnost konfigurace uchovávání záloh <br>Uživatelem iniciované [zálohy](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Bod v čase obnovení databáze](sql-database-recovery-using-backups.md#point-in-time-restore) funkce |
|**Zabezpečení a dodržování předpisů** | **Správa**|
|Izolované prostředí ([integrace virtuální sítě](sql-database-managed-instance-connectivity-architecture.md), jednoho tenanta služby, vyhrazené výpočetní prostředky a úložiště) <br>[Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Ověřování Azure AD](sql-database-aad-authentication.md), jednotné přihlašování – podpora <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD přihlášení</a> (**ve verzi public preview**) <br>Dodržuje standardy pro dodržování předpisů stejně jako Azure SQL database <br>[Auditování SQL](sql-database-managed-instance-auditing.md) <br>[Detekce hrozeb](sql-database-managed-instance-threat-detection.md) |Rozhraní API Azure Resource Manageru pro automatizaci služby zřizování a škálování <br>Azure portal funkci pro ruční službu zřizování a škálování <br>Data Migration Service

V následující tabulce jsou uvedeny klíčové funkce spravované instance:

|Funkce | Popis|
|---|---|
| Verze systému SQL Server / build | SQL Server Database Engine (nejnovější stabilní verze) |
| Spravované automatizované zálohování | Ano |
| Integrovanou instanci a databázi sledování a metriky | Ano |
| Software automatické opravy | Ano |
| Nejnovější funkce databázového stroje | Ano |
| Počet datových souborů (řádků) na databázi | Několik |
| Počet souborů protokolu (LOG) na databázi | 1 |
| Připojení typu VNet - nasazení Azure Resource Manageru | Ano |
| Připojení typu VNet - model nasazení Classic | Ne |
| Portál podpory | Ano|
| Vestavěná integrace Service (SSIS) | Ne - služby SSIS je součástí [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Integrované Analysis Service (SSAS) | Ne - SSAS je samostatný [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Integrované služby generování sestav (SSRS) | Ne - použít Power BI nebo služby SSRS IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>Model nákupu na základě virtuálních jader

[Nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) pro spravované instance poskytuje vám flexibilitu, ovládací prvek, transparentnost a jednoduchý způsob převodu místních požadavků na zatížení do cloudu. Tento model umožňuje změnit výpočetních, paměťových a úložiště na základě potřeb vašich úloh. Je také nárok až na 30 procent spoření s modelu virt. jader [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

V modelu virt. jader můžete mezi generacemi hardwaru.

- **Gen4** logické procesory jsou založeny na Intel E5-2673 v3 (Haswell) 2,4 GHz procesory, SSD připojené, fyzických jader, 7 GB paměti RAM na jádro a velikostí výpočetních mezi 8 až 24 virtuálních jader.
- **Gen5** logické procesory jsou založeny na Intel E5-2673 v4 (Broadwell) 2.3 GHz procesorech rychlé NVMe SSD, technologie hyper-threaded logické jádro a výpočet velikosti mezi 80 a 8 jader.

Další informace o rozdílech mezi generacemi hardwaru v [managed instance omezení prostředků](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Spravovaná instanci úrovně služeb

Managed instance je k dispozici ve dvou úrovních služeb:

- **Obecné účely**: Je určená pro aplikace s typické výkon a latenci vstupně-výstupních operací.
- **Pro důležité obchodní informace**: Je určená pro aplikace s nízkou latenci vstupně-výstupní operace a minimální dopad základní údržbových operací u zatížení.

Obě úrovně služeb 99,99 % dostupnost a vám umožní nezávisle na sobě vyberte velikost úložiště a výpočetní kapacitu. Další informace o architektura pro vysokou dostupnost služby Azure SQL Database najdete v tématu [vysokou dostupnost a Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Úrovně služeb pro obecné účely

Následující seznam popisuje úrovni General Purpose služeb klíčovou vlastnost:

- Návrh pro většinu obchodních aplikací s požadavky na typické výkon
- Vysoce výkonné úložiště objektů Blob v Azure (8 TB)
- Integrované [vysoké dostupnosti](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) na základě spolehlivé úložiště objektů Blob v Azure a [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Další informace najdete v tématu [vrstvy úložiště obecně účel úrovně](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) a [úložiště osvědčené postupy z hlediska výkonu a důležité informace pro spravované instance (Obecné)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Další informace o rozdílech mezi úrovní služeb v [managed instance omezení prostředků](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Obchodní vrstvy kritické služby

Obchodní vrstvy kritické služby je sestaven pro aplikace s vysokými požadavky na vstupně-výstupních operací. Nabízí nejvyšší odolnost proti selhání s využitím několika izolovaných replik.

Následující seznam popisuje klíčové vlastnosti pro důležité obchodní informace vrstvy služby:

- Je určená pro obchodní aplikace s nejvyšší výkon a požadavky na vysokou dostupnost
- Se dodává s mimořádně rychlým místní úložiště SSD (až 1 TB na Gen 4 a až 4 TB generace 5)
- Integrované [vysoké dostupnosti](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) na základě [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) a [Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- Integrované Další [repliky jen pro čtení databáze](sql-database-read-scale-out.md) , který lze použít pro vytváření sestav a další úlohy jen pro čtení
- [OLTP v paměti](sql-database-in-memory.md) , který lze použít pro úlohy vysokovýkonného požadavky  

Další informace o rozdílech mezi úrovní služeb v [managed instance omezení prostředků](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="advanced-security-and-compliance"></a>Pokročilé zabezpečení a dodržování předpisů

Možnost nasazení spravované instance kombinuje rozšířené funkce zabezpečení poskytované cloudu Azure a SQL Server Database Engine.

### <a name="managed-instance-security-isolation"></a>Izolace zabezpečení spravované instance

Managed instance poskytuje další bezpečnostní izolaci od ostatních tenantů v cloudu Azure. Izolace zabezpečení zahrnuje:

- [Implementace nativní virtuální sítě](sql-database-managed-instance-connectivity-architecture.md) a připojení k vaší místní prostředí pomocí Azure Express Route nebo VPN Gateway.
- Koncový bod SQL se zobrazují jen prostřednictvím privátní IP adresy, což bezpečné připojení z Azure privátní nebo hybridní sítě.
- Jedním tenantem pomocí vyhrazené základní infrastruktura (výpočetní prostředky, úložiště).

Následující diagram popisuje různé možnosti připojení pro vaše aplikace:

![Vysoká dostupnost](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Další podrobnosti o integrace virtuální sítě a sítě vynucení zásad na úrovni podsítě, najdete v článku [architekturu virtuální sítě pro spravované instance](sql-database-managed-instance-connectivity-architecture.md) a [připojení aplikace k managed instance](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Umístíte více spravované instance ve stejné podsíti, bez ohledu na to, který povoluje vaše požadavky na zabezpečení, jako, který vám přinese další výhody. Tyto instance ve stejné podsíti, se výrazně zjednodušit údržbu síťové infrastruktury a snížit čas, protože doba trvání dlouhé zřizování je spojen s náklady spojené s nasazením první spravované instance v podsíti zřízení instance.

### <a name="azure-sql-database-security-features"></a>Funkcí zabezpečení služby Azure SQL Database

Azure SQL Database poskytuje sadu rozšířené funkce zabezpečení, které je možné chránit vaše data.

- [Managed instance auditování](sql-database-managed-instance-auditing.md) sleduje události databáze a zapisuje je do souboru protokolu auditování umístěny do svého účtu úložiště Azure. Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.
- Šifrování dat při pohybu – spravovaná instance chrání vaše data zajištěním šifrování pro data přenášená data pomocí Transport Layer Security. Kromě zabezpečení transportní vrstvy, možnost nasazení spravovaných instancí nabízí ochranu citlivých dat na cestě v klidovém stavu a průběhu zpracování dotazů pomocí [s funkcí Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Funkce Always Encrypted je špičkou v odvětví, která nabízí bezkonkurenční zabezpečení dat před porušením zabezpečení, včetně krádeže důležitých dat. Například s funkcí Always Encrypted, čísla platebních karet jsou uloženy v zašifrované podobě v databázi vždy, i během dotazu zpracování, která umožňují dešifrování Přejme během použití autorizované zaměstnance nebo aplikace, které potřebují zpracovat data.
- [Detekce hrozeb](sql-database-managed-instance-threat-detection.md) doplňuje [auditování](sql-database-managed-instance-auditing.md) ve poskytuje další úroveň zabezpečení funkce intelligence integrované do služby, který zjistí a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Upozorní vás na podezřelé aktivity, potenciální ohrožení zabezpečení a útoky prostřednictvím injektáže SQL, stejně jako vzory přístupu k databázi neobvyklé. Výstrahy detekce hrozeb můžete zobrazit v [Azure Security Center](https://azure.microsoft.com/services/security-center/) a zadejte podrobnosti o podezřelé aktivitě a doporučení akce k prošetření a zmírnění hrozby.  
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking) omezuje riziko ohrožení citlivých dat pomocí jejich maskování pro neoprávněné uživatele. Dynamické maskování dat pomáhá zabránit neoprávněnému přístupu k citlivým datům tím, že vám určit, kolik citlivých dat zobrazíte s minimálním dopadem na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.
- [Zabezpečení na úrovní řádků](/sql/relational-databases/security/row-level-security) umožňuje řídit přístup k řádkům v databázové tabulce na základě charakteristiky uživatele spouštějícího dotaz (například podle skupiny členství nebo kontext spuštění). Zabezpečení na úrovni řádku (RLS) zjednodušuje návrh a psaní kódu zabezpečení v aplikaci. RLS umožňuje implementovat omezení přístupu k datovým řádkům. Například pro zajištění, že pracovníci mají přístup pouze řádky dat, které se vztahují k jejich oddělení, nebo pro omezení přístupu k datům jenom na relevantní data.
- [Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) šifruje spravovanou instanci datových souborů, označované jako šifruje neaktivní uložená data. Transparentní šifrování dat provádí v reálném čase vstupně-výstupní operace šifrování a dešifrování souborů protokolu a data. Šifrování používá databázi šifrovací klíč (DEK), která je uložena v spouštěcí záznam databáze dostupnosti během obnovení. Můžete chránit všechny vaše databáze spravované instance pomocí transparentního šifrování dat. Transparentní šifrování dat je SQL Server prověřené technologie šifrování v klidovém stavu, který se vyžaduje řada standardů dodržování předpisů pro ochranu před krádeží úložného média.

Azure Database Migration Service (DMS) nebo nativní obnovení se podporuje migrace šifrované databázi do managed instance. Pokud máte v plánu migrovat šifrované databázi pomocí nativní obnovení, se migrace stávajícího transparentní šifrování dat certifikátu ze serveru SQL Server v místním nebo SQL Server na virtuálním počítači do spravované instance o požadovaný krok. Další informace o možnostech migrace najdete v tématu [migrace instance SQL serveru do spravované instance](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integrace Azure Active Directory

Možnost nasazení spravované instance podporuje tradiční přihlášeních SQL server Database engine a přihlašování integrované s Azure Active Directory (AAD). Přihlašovací údaje AAD (**ve verzi public preview**) jsou Azure cloudovou verzi služby místní přihlašovací jména databáze, které používáte ve vašem místním prostředí. Přihlašovací údaje AAD umožňuje určit uživatele a skupiny ze služby Azure Active Directory tenantů jako true rozsahem instance objektů, schopný provádět všechny operace na úrovni instance, včetně mezidatabázové dotazy v rámci stejné spravované instance.

Novou syntaxi se používá k vytvoření přihlášení AAD (**ve verzi public preview**), **z EXTERNÍHO poskytovatele**. Další informace o syntaxi najdete v tématu <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>a zkontrolujte [zřízení správce Azure Active Directory pro spravovanou instanci](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) článku.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

Možnost nasazení spravované instance umožňuje centrálně spravovat identity uživatele databáze a dalším službám Microsoftu s [integrace služby Azure Active Directory](sql-database-aad-authentication.md). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory podporuje [vícefaktorové ověřování (MFA)](sql-database-ssms-mfa-authentication-configure.md) pro zvýšení zabezpečení dat a aplikací při současné podpoře jednotného přihlašování.

### <a name="authentication"></a>Authentication

Ověřování spravované instance odkazuje na tom, jak uživatele prokazovat svoji identitu při připojování k databázi. SQL Database podporuje dva typy ověřování:  

- **Ověřování SQL**:

  Tuto metodu ověřování pomocí uživatelského jména a hesla.
- **Ověřování pomocí Azure Active Directory**:

  Tato metoda ověřování používá identity spravované v Azure Active Directory a je podporované u spravovaných a integrovaných domén. [Kdykoliv to půjde](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), použijte ověřování pomocí Active Directory (integrované zabezpečení).

### <a name="authorization"></a>Autorizace

Autorizace určuje, jak uživatel provádět ve službě Azure SQL Database jsou dané členstvím v databázových rolích a oprávnění na úrovni objektů vašeho účtu. Managed instance má stejné možnosti autorizace jako SQL Server 2017.

## <a name="database-migration"></a>Migrace databáze

Databáze spravované instance nasazení možnost cíle uživatelské scénáře pomocí hromadné databáze migrace z místní nebo IaaS implementace. Spravovaná instance podporuje několik možností pro migraci databáze:

### <a name="back-up-and-restore"></a>Zálohování a obnovení  

Postup migrace využívá zálohování SQL na úložiště objektů Blob v Azure. Zálohy uložené v Azure storage blob do spravované instance pomocí přímo obnovena [příkaz T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Rychlý start ukazuje, jak obnovit Wide World Importers – Standard záložního souboru, naleznete v tématu [obnovit záložní soubor do spravované instance](sql-database-managed-instance-get-started-restore.md). Tento rychlý start ukazuje, jak nahrát soubor zálohy úložištěm objektů BLOB Azure a zabezpečené ho pomocí sdíleného přístupového podpisu (SAS) klíče.
- Informace o obnovení z adresy URL, najdete v části [nativní obnovit z adresy URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Zálohy z managed instance můžete obnovit jen do jiné mi. Nelze obnovit na místní SQL Server nebo do jedné databáze nebo elastického fondu.

### <a name="data-migration-service"></a>Data Migration Service

Azure Database Migration Service je plně spravovaná služba, která umožňují bezproblémovou migraci z několika databázových zdrojů na platformu Azure Data s minimálními výpadky. Tato služba zjednodušuje úlohy potřebné k migraci existujícího třetích stran a databáze systému SQL Server na Azure SQL Database (izolované databáze, databáze ve fondu v elastických fondech a instance databází do spravované instance) a SQL Server na virtuálním počítači Azure. Zobrazit [jak migrovat místní databázi do spravované instance pomocí DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Funkce SQL podporované

Možnost nasazení spravované instance cílem je poskytovat blízko povrchu 100 % kompatibilitu s místní SQL Server už ve fázi až do obecné dostupnosti služby. Pro funkce a seznam porovnání, naleznete v tématu [porovnání funkcí SQL Database](sql-database-features.md)a seznam rozdílů jazyka T-SQL do spravované instance oproti serveru SQL Server najdete v tématu [spravované rozdíly instance T-SQL z SQL serveru](sql-database-managed-instance-transact-sql-information.md).

Možnost nasazení spravované instance podporuje zpětná kompatibilita na databáze SQL 2008. Přímé migrace z databázové servery SQL 2005 je podporovaná, se aktualizují úroveň kompatibility pro migrované databáze SQL 2005 na SQL 2008.
  
Následující diagram popisuje styčné plochy kompatibility v spravovanou instanci:  

![Migrace](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Hlavní rozdíly mezi místním SQL serverem a do spravované instance

Výhody možnost nasazení spravované instance nebudou vždy nahoru – k datu v cloudu, což znamená, že některé funkce v místním SQL serveru může být buď zastaralé, vyřazený nebo mít alternativy. Existují zvláštní případy při nástroje třeba rozpoznat, že konkrétní funkce funguje trochu jinak nebo že služba není spuštěná v prostředí, které nebudete plně řídit:

- Vysoká dostupnost je součástí a předem nakonfigurované pomocí technologie podobný [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatizované zálohování a bodu v čase. Zákazník může iniciovat `copy-only` zálohy, které nejsou v konfliktu s řetězec automatického zálohování.
- Spravovaná instance nepovoluje zadání úplné fyzické cesty, takže všechny odpovídající scénáře a jinak není podporován: OBNOVENÍ databáze nepodporuje se přesun, vytvořit DB neumožňuje fyzické cesty, BULK INSERT funguje s objekty BLOB Azure pouze atd.
- Managed instance podporuje [ověřování Azure AD](sql-database-aad-authentication.md) jako cloudové alternativu k ověřování Windows.
- Spravovaná instance automaticky spravuje skupiny souborů XTP a soubory pro databáze, které obsahují objekty OLTP v paměti
- Podporuje spravovanou instanci SQL Server Integration Services (SSIS) a můžete hostitele katalogu služby SSIS (SSISDB), která ukládá balíčků služby SSIS, ale jsou prováděna na spravované prostředí Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF), najdete v článku [Create Prostředí Azure-SSIS IR ve službě ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Porovnání funkcí služby SSIS ve službě SQL Database, najdete v článku [porovnání Azure SQL Database jedné databáze a elastické fondy a spravované instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Managed instance funkce pro správu

Možnost nasazení spravované instance umožňuje správce systému, abyste strávili méně času na úlohy správy, protože služba SQL Database je provede za vás nebo výrazně zjednodušuje úkoly. Například [operačního systému a relační databázový systém instalace a používání dílčích oprav](sql-database-high-availability.md), [dynamické instance změny velikosti a konfiguraci](sql-database-single-database-scale.md), [zálohy](sql-database-automated-backups.md), [replikace databáze](replication-with-sql-database-managed-instance.md)(včetně systémové databáze), [konfiguraci vysoké dostupnosti](sql-database-high-availability.md)a konfigurace stavu a [performance monitoring pro aplikace](../azure-monitor/insights/azure-sql.md) datové proudy.

> [!IMPORTANT]
> Seznam podporovaných, částečně podporované a nepodporované funkce najdete v tématu [funkcí služby SQL Database](sql-database-features.md). Seznam rozdílů jazyka T-SQL do spravované instance oproti serveru SQL Server najdete v tématu [spravované rozdíly instance T-SQL z SQL serveru](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Jak prostřednictvím kódu programu identifikovat spravovaná instance

V následující tabulce jsou uvedeny několik vlastností, které jsou přístupné prostřednictvím jazyka Transact SQL, můžete použít ke zjištění, že vaše aplikace pracuje s spravovanou instanci a načíst důležité vlastnosti.

|Vlastnost|Hodnota|Poznámka|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Tato hodnota je stejné jako u databáze SQL.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Tato hodnota je stejné jako u databáze SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Tato hodnota jednoznačně identifikuje managed instance.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Název DNS úplná instance v následujícím formátu:`<instanceName>`.`<dnsPrefix>`.Database.Windows.NET, kde `<instanceName>` je poskytnutá výhradně zákazník, zatímco `<dnsPrefix>` je automaticky generované část názvu zaručující globální jedinečnost názvu DNS ("wcus17662feb9ce98", například)|Example: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Další postup

- Zjistěte, jak vytvořit první spravované instanci, najdete v článku [příručky rychlý Start](sql-database-managed-instance-get-started.md).
- Pro funkce a seznam porovnání, naleznete v tématu [běžné funkce SQL](sql-database-features.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [managed instance konfigurace virtuální sítě](sql-database-managed-instance-connectivity-architecture.md).
- Rychlý start, která vytváří managed instance a obnoví databázi ze zálohy, naleznete v tématu [vytvoření managed instance](sql-database-managed-instance-get-started.md).
- Kurz použití Azure Database Migration Service (DMS) pro migraci, najdete v tématu [spravované migrace instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Rozšířené monitorování výkonu databáze spravované instance s integrovanými inteligentními funkcemi pro řešení potíží, najdete v části [monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)
- Informace o cenách najdete v tématu [SQL Database managed instance ceny](https://azure.microsoft.com/pricing/details/sql-database/managed/).
