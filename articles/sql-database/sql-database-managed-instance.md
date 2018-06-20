---
title: Databáze Azure SQL spravované Instance přehled | Microsoft Docs
description: Toto téma popisuje spravované Instance databáze Azure SQL a vysvětluje, jak to funguje a jak se liší od jedné databáze ve službě Azure SQL Database.
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: bonova
ms.openlocfilehash: f07ce542c176f4038378d54497d7114109ac5bd3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215520"
---
# <a name="what-is-a-managed-instance-preview"></a>Co je Instance spravované (preview)?

SQL databáze spravované Instance Azure (preview) je nová funkce Azure SQL Database, poskytuje téměř 100 % kompatibilitu s systému SQL Server na místní (Enterprise Edition), poskytuje nativní [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md) implementace, které řeší běžné otázky zabezpečení a [modelu business](https://azure.microsoft.com/pricing/details/sql-database/) uspokojivým pro zákazníky, místní systém SQL Server. Spravované Instance umožňuje stávající zákazníky služby SQL Server a navýšení posunutí své místní aplikace do cloudu s minimálními změnami aplikace a databáze. Ve stejnou dobu spravované Instance uchovává všechny možnosti PaaS (Automatické aktualizace použití dílčích oprav a verze, zálohování, vysokou dostupnost), které výrazně snižuje správní režii a celkové náklady na vlastnictví.

> [!IMPORTANT]
> Seznam oblastí, ve kterých je Managed Instance aktuálně k dispozici, najdete v tématu [Migrace databází do plně spravované služby pomocí Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/).
 
Následující diagram popisuje klíčové funkce spravované instance:

![klíčové funkce](./media/sql-database-managed-instance/key-features.png)

Spravované Instance je představovali jako upřednostňovaný platformu pro následující scénáře: 

- Místní SQL Server nebo IaaS zákazníci chtějí migrovat své aplikace na plně spravovaná služby s minimálními návrh změny.
- Nezávislí výrobci softwaru spoléhat na databází SQL, kteří chtějí povolit svým zákazníkům migrovat do cloudu a tím dosáhnout významné konkurencí nebo k dosažení globální trhu. 

Spravované Instance cílem podle obecné dostupnosti, je k poskytování blízko útoku na 100 % kompatibility s nejnovější verzí systému SQL Server na místě prostřednictvím dvoufázové instalace verze plánu. 

Následující tabulka jsou podrobněji popsány dále klíčové rozdíly a představovali scénáře použití mezi SQL IaaS, databáze SQL Azure a spravovat Instance databáze SQL:

| | Scénáře použití | 
| --- | --- | 
|SQL Database Managed Instance |Pro zákazníky chtějí migrovat velký počet aplikací z místní nebo IaaS, samoobslužné vytvořen nebo ISV, které jsou k dispozici s navrhnout jako nízkou migrace úsilí co možná, spravované Instance. Pomocí plně automatického [služby migrace dat (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) v Azure, zákazníků navýšení a posunutí jejich místní SQL Server na spravované Instance, která nabízí kompatibilitu s místním SQL serverem a dokončení izolace instance zákazníků s nativní podporou virtuální sítě.  Pomocí programu Software Assurance, můžete vyměňovat své stávající licence pro zvýhodněné sazby na spravované Instance databáze SQL pomocí [výhody použití hybridní Azure pro systém SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Spravované Instance databáze SQL je nejlepší cíl migrace v cloudu pro instance systému SQL Server, které vyžadují vysoké zabezpečení a bohaté programovatelnosti prostor. |
|Azure SQL Database (jedna nebo fond) |**Elastické fondy**: pro zákazníky vývoj nové víceklientské aplikace SaaS nebo záměrně transformace své stávající místní aplikace do víceklientské aplikace SaaS, navrhnout elastické fondy. Výhody tohoto modelu: <br><ul><li>Převod modelu obchodní prodávané licencí, které chcete prodávané odběry služby (pro ISV)</li></ul><ul><li>Izolace snadný a odrážka ověření klienta</li></ul><ul><li>Zjednodušená zaměřené na databázi programovací model</li></ul><ul><li>Mohlo škálování bez stiskne pevný mezní hodnoty</li></ul>**Jedna databáze**: pro zákazníky vývoji nových aplikací než SaaS více klientů, jejichž zatížení je stabilní a předvídatelné, navrhnout izolované databáze. Výhody tohoto modelu:<ul><li>Zjednodušená zaměřené na databázi programovací model</li></ul>  <ul><li>Předvídatelný výkon pro každou databázi</li></ul>|
|Virtuální počítač SQL IaaS|Pro zákazníky museli přizpůsobení operačního systému nebo na serveru databáze, a také zákazníků, kterým se specifické požadavky z hlediska spouštění aplikací třetích stran stranou s SQL serverem (ve stejném virtuálním počítači), navrhnout virtuálním počítačům systému SQL nebo IaaS jako optimální řešení|
|||

## <a name="how-to-programmatically-identify-a-managed-instance"></a>Jak programově identifikovat a spravovat Instance

Následující tabulka uvádí několik vlastností, přístupný prostřednictvím Transact SQL, můžete použít ke zjištění, zda aplikace pracuje s spravované Instance a načíst důležité vlastnosti.

|Vlastnost|Hodnota|Poznámka|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Tato hodnota je stejný jako v databázi SQL.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Tato hodnota je stejný jako v databázi SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Tato hodnota jednoznačně identifikuje spravované Instance.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Název DNS úplná instance v následujícím formátu:<instanceName>.<dnsPrefix>. Database.Windows.NET, kde <instanceName> je název poskytované zákazníka, zatímco <dnsPrefix> je automaticky generovaný část názvu zaručující globální jedinečnost názvu DNS ("wcus17662feb9ce98", např.)|Příklad: Moje spravované instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>Klíčové funkce a možnosti spravované instance 

> [!IMPORTANT]
> Do spravované Instance, spustí se všechny funkce nejnovější verzi systému SQL Server, včetně online operace, automatické plán opravy a dalších vylepšení výkonu enterprise. 

| **Výhody PaaS** | **Kontinuita podnikových procesů** |
| --- | --- |
|Žádné nákup hardwaru a správy <br>Žádné správu režie pro správu základní infrastruktury <br>Rychlé zřizování a škálování služby <br>Automatizované opravy a verze upgradu <br>Integrace s jinými službami PaaS dat |99,99 % dostupnost SLA  <br>Integrovanou vysokou dostupnost <br>Data chráněná pomocí automatizované zálohování <br>Období konfigurovat uchovávání záloh zákazníků (fixed do 7 dnů ve verzi Public Preview) <br>Uživatel spustil zálohování <br>Obnovení bodu v databázi čas schopností |
|**Zabezpečení a dodržování předpisů** | **Správa**|
|Izolované prostředí (integrace virtuální sítě, jednoho klienta služby, vyhrazeného výpočetního prostředí a úložiště <br>Šifrování dat při přenosu <br>Ověřování Azure AD, podporu přihlašování <br>Dodržuje standardy dodržování předpisů stejné jako databázi Azure SQL <br>Auditování SQL <br>Detekce hrozeb |Azure Resource Manager rozhraní API pro automatizaci služby zřizování a škálování <br>Azure portálu funkce pro ruční služby zřizování a škálování <br>Služba migrace dat 

![Jednotné přihlašování](./media/sql-database-managed-instance/sso.png) 

## <a name="vcore-based-purchasing-model-preview"></a>na základě vCore nákupní model (preview)

Nákupní model (preview) na základě vCore poskytuje flexibilitu, řízení, průhlednost a snadný způsob, jak převede místní požadavků na zatížení do cloudu. Tento model umožňuje škálování výpočetní kapacity, paměť a úložiště podle jejich potřeb pracovního vytížení. Je také vhodné pro až na 30 procent úspor s modelem vCore [výhody použití hybridní Azure pro systém SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Virtuální základní představuje logický procesor nabízí s možností si vybrat mezi generací hardwaru.
- Logické CPU 4. generace jsou založené na procesorech Intel E5-2673 v3 (Haswell) 2,4 GHz.
- Gen 5 logické procesory jsou založené na Intel E5-2673 v4 (Broadwell) 2.3 GHz procesory.

Následující tabulka vám pomůže pochopit postup výběru optimální konfigurace výpočtů, paměť, úložiště a vstupně-výstupních prostředků.

||Gen 4|Gen 5|
|----|------|-----|
|Hardware|Intel E5-2673 v3 (Haswell) 2,4 GHz procesory připojené SSD vCore = 1 PP (fyzických jader)|Intel E5-2673 v4 (Broadwell) 2.3 GHz procesorů, rychlé eNVM SSD, vCore = 1 lineárního programování ÚLOH (hyper vláken)|
|Úrovně výkonu|8, 16, 24 vCores|8, 16, 24, 32, 40 vCores|
|Memory (Paměť)|7GB za vCore|Verzi 5.5GB za vCore|
||||

## <a name="managed-instance-service-tier"></a>Spravované Instance vrstvy služby

Spravované Instance je k dispozici zatím v vrstvu služby jednotného - obecné účely – je určené pro aplikace s typické dostupnosti a běžné požadavky na latenci vstupně-výstupní operace.

Následující seznam popisuje klíčové vlastnosti vrstvy služby s obecné účely: 

- Návrh pro většinu podnikových aplikací s typické výkonu a HA požadavky 
- Vysoce výkonné úložiště Azure Premium (8 TB) 
- 100 databází / instance 

V této vrstvě můžete nezávisle vybrat úložiště a výpočetní kapacitu. 

Následující diagram znázorňuje aktivní výpočetní a redundantních uzlů v této vrstvy služeb.
 
![Obecné účely úroveň služby](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

Následující část popisuje klíčové funkce vrstvy služby s obecné účely:

|Funkce | Popis|
|---|---|
| Počet vCores * | 8, 16, 24 (fin 4)<br>8, 16, 24, 32, 40 (Gen5)|
| Verze systému SQL Server / sestavení | SQL Server nejnovější (k dispozici) |
| Minimální velikost úložiště | 32 GB |
| Maximální velikost úložiště | 8 TB |
| Maximální počet úložiště na databázi | 8 TB |
| Očekávaný úložiště IOPS | 500-7500 IOPS na datový soubor (závisí na datový soubor). V tématu [Storage úrovně Premium](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Počet datových souborů (řádky) na databázi | Několik | 
| Počet souborů protokolu (protokol) na databázi | 1 | 
| Spravované automatizované zálohování | Ano |
| HA | Podle vzdálené úložiště a [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Předdefinované instance a databáze monitorování a metriky | Ano |
| Software automatické opravy | Ano |
| VNet - nasazení Azure Resource Manager | Ano |
| VNet - model nasazení Classic | Ne |
| Podpora portálu | Ano|
|||

\* Virtuální základní představuje logický procesor nabízí s možností si vybrat mezi generací hardwaru. Gen 4 logické procesory jsou založené na Intel E5-2673 v3 (Haswell) procesorů 2,4 GHz a logických procesorů 5 generace jsou založené na Intel E5-2673 v4 (Broadwell) 2.3 GHz procesory. 

## <a name="advanced-security-and-compliance"></a>Pokročilé zabezpečení a dodržování předpisů 

### <a name="managed-instance-security-isolation"></a>Izolace zabezpečení spravované Instance 

Spravované Instance poskytují další bezpečnostní izolaci od ostatních klientů v cloudu Azure. Izolace zabezpečení zahrnuje: 

- [Implementace nativní virtuální sítě](sql-database-managed-instance-vnet-configuration.md) a připojení k prostředí místní pomocí Azure Express trasy nebo brány sítě VPN 
- Koncový bod SQL je k dispozici pouze prostřednictvím privátní IP adresy, umožňuje bezpečné připojení z Azure privátní nebo hybridní sítě
- Jednoho klienta s vyhrazenou základní infrastruktury (výpočetní, úložiště)

Následující diagram popisuje izolace návrhu: 

![Vysoká dostupnost](./media/sql-database-managed-instance/application-deployment-topologies.png)  

### <a name="auditing-for-compliance-and-security"></a>Auditování dodržování předpisů a zabezpečení 

[Spravovat auditování Instance](sql-database-managed-instance-auditing.md) sleduje události databáze a zapisuje je do auditu přihlášení účtu úložiště Azure. Audit může pomoct zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které by mohly být známkou problémů obchodního charakteru nebo by mohly vzbuzovat podezření narušení zabezpečení. 

### <a name="data-encryption-in-motion"></a>Šifrování přenášených dat 

Spravované Instance zabezpečuje vašich dat tím, že poskytuje šifrování dat v pohybu pomocí Transport Layer Security.

Kromě zabezpečení transportní vrstvy, spravované Instance databáze SQL nabízí Ochrana citlivých dat na cestě, v klidovém stavu a během zpracování pomocí dotazů [vždy šifrována](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Funkce Always Encrypted je špičkou v odvětví, která nabízí bezkonkurenční zabezpečení dat před porušením zabezpečení, včetně krádeže důležitých dat. Například se vždycky šifrovaná, čísla platebních karet jsou uloženy v databázi vždy, i v průběhu zašifrovaná dotaz zpracování, která umožňují dešifrování v místě použití oprávnění zaměstnanci nebo aplikace, které potřebují ke zpracování dat. 

### <a name="dynamic-data-masking"></a>Dynamické maskování dat 

Databáze SQL [dynamická data maskování](/sql/relational-databases/security/dynamic-data-masking) omezuje zranitelnost citlivá data pomocí maskování neoprávněným uživatelům. Dynamická data maskování pomáhá zabránit neoprávněnému přístupu k citlivým datům tím, že vám určit, kolik citlivých dat na nich s minimálním dopadem na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění. 

### <a name="row-level-security"></a>Zabezpečení na úrovni řádku 

[Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) řídit přístup k řádky v tabulce databáze na základě vlastností uživatele provádění dotazu (například podle kontextu skupiny členství nebo spuštění). Zabezpečení na úrovni řádku (RLS) zjednodušuje návrh a psaní kódu zabezpečení v aplikaci. RLS umožňuje implementovat omezení přístupu k datovým řádkům. Například pracovníci přístup pouze řádky dat, které jsou relevantní pro jejich oddělení zajistí nebo omezení přístupu k datům pouze relevantní data. 

### <a name="threat-detection"></a>Detekce hrozeb 

[Spravované Instance detekce hrozeb](sql-database-managed-instance-threat-detection.md) doplňuje [spravované Instance auditování](sql-database-managed-instance-auditing.md) tím, že poskytuje další vrstvu zabezpečení intelligence integrovaná služba, která zjistí neobvyklou a potenciálně škodlivé pokusy o přístup nebo zneužití databází. Jsou výstrahy o podezřelých aktivit, potenciální ohrožení zabezpečení a útoky prostřednictvím injektáže SQL, a také nezvyklé databázové přístupové vzorce. Můžete prohlížet výstrah o zjištěných hrozbách [Azure Security Center](https://azure.microsoft.com/services/security-center/) a zadejte podrobnosti podezřelých aktivit a doporučujeme akce o tom, jak prozkoumat a zmírnit riziko.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování 

SQL Database umožňuje centrálně spravovat identity uživatele databáze a dalších služeb Microsoft pomocí [integrace s Azure Active Directory](sql-database-aad-authentication.md). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory podporuje [vícefaktorové ověřování (MFA)](sql-database-ssms-mfa-authentication-configure.md) pro zvýšení zabezpečení dat a aplikací při současné podpoře jednotného přihlašování. 

### <a name="authentication"></a>Authentication 
Ověřování databáze SQL se odkazuje na tom, jak uživatelé prokázání své identity při připojování k databázi. SQL Database podporuje dva typy ověřování:  

- Ověřování SQL, který používá uživatelské jméno a heslo.
- Azure Active Directory, ověřování, která používá identity spravované službou Azure Active Directory a je podporována pro spravované a integrované domén. 

### <a name="authorization"></a>Autorizace

Autorizace odkazuje uživatele, můžete provést v rámci Azure SQL Database a řídí členství role databáze váš uživatelský účet a oprávnění na úrovni objektů. Spravované Instance má stejné možnosti autorizace jako SQL Server 2017. 

## <a name="database-migration"></a>Migrace databáze 

Spravovat instanci cíle uživatelské scénáře s velkokapacitního databáze migrace z místní nebo implementace databáze IaaS. Spravované Instance podporuje několik možností migrace databáze: 

### <a name="data-migration-service"></a>Služba migrace dat

Služba Azure databáze migrace je plně spravovaná služba navržených k povolení bezproblémové migrace z více zdrojů databáze do platformy Azure dat s minimálními výpadky. Tato služba zjednodušuje úkoly vyžadované pro přesun existujících třetích stran a databáze SQL serveru do Azure. Možnosti nasazení zahrnout do virtuálního počítače Azure ve verzi Public Preview služby Azure SQL Database, spravované Instance a SQL Server. V tématu [jak migrovat místní databázi k instanci spravované pomocí DMS](https://aka.ms/migratetoMIusingDMS). 

### <a name="backup-and-restore"></a>Zálohování a obnovení  

Postup migrace využívá zálohování SQL na úložiště objektů blob v Azure. Záloh uložených v objektu blob úložiště Azure můžete přímo obnovena do spravované Instance. Chcete-li obnovit existující databázi SQL do spravované instance, můžete:

- Použití [služba migrace dat (DMS)](../dms/dms-overview.md). Podívejte se kurz [migrací do Instance spravované pomocí služby pro migraci databáze Azure (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) obnovit ze záložního souboru databáze
- Použití [příkaz RESTORE T-SQL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql). 
  - Zobrazuje postup obnovení společnost Wide World Importers - záložní soubor standardní databáze, na adrese [obnovit záložní soubor do Instance spravované](sql-database-managed-instance-restore-from-backup-tutorial.md). Tento kurz ukazuje, že budete muset nahrát záložní soubor do úložiště Azure blog a zabezpečení pomocí klíč sdíleného přístupu podpis (SAS).
  - Informace o obnovení z adresy URL, najdete v článku [nativní obnovit z adresy URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

## <a name="sql-features-supported"></a>Funkce SQL podporované 

Spravované Instance cílem poskytování blízko útoku na 100 % kompatibilitu s místní SQL Server již ve fázích, dokud obecné dostupnosti služby. Pro funkce a porovnání seznamu, najdete v části [společné funkce SQL](sql-database-features.md).
 
Spravované Instance podporuje zpětné kompatibility k databázím SQL 2008. Přímé migrace ze serverů databáze SQL 2005 se podporuje, jsou aktualizovány úroveň kompatibility pro migrované databáze SQL 2005 na SQL 2008. 
 
Následující diagram popisuje útoku kompatibilita ve spravované Instance:  

![Migrace](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Hlavní rozdíly mezi místní SQL Server a spravované Instance 

Spravované Instance výhody nebudou vyřazené vždy až--datum v cloudu, což znamená, že některé funkce v místní SQL Server může být buď zastaralé, nebo mají alternativy. Když je potřeba, aby konkrétní funkce funguje trochu jiným způsobem nebo že není spuštěná v prostředí, které nebudete plně řídit nástroje existují konkrétní případy: 

- Vysoké dostupnosti je součástí a předem nakonfigurované. Funkce vysoké dostupnosti Always On se nezobrazí v stejným způsobem, protože je v SQL IaaS implementace 
- Automatizované zálohování a bod v době obnovení. Můžete zahájit zákazníka `copy-only` zálohování, které nebudou v konfliktu s automatické řetěz záloh. 
- Spravované Instance nepovoluje zadání úplné fyzické cesty k umístění, aby ve všech scénářích odpovídající jinak podporovaná: obnovení databáze nepodporuje WITH MOVE, vytvořit DB neumožňuje fyzické cesty k umístění, BULK INSERT s objekty BLOB Azure funguje pouze atd. 
- Spravované Instance podporuje [ověřování Azure AD](sql-database-aad-authentication.md) jako cloudu alternativu k ověřování systému Windows. 
- Spravované Instance automaticky spravuje XTP souborů a soubory pro databáze, které obsahují objekty OLTP v paměti
 
### <a name="managed-instance-administration-features"></a>Funkce správy spravované Instance  

Spravované Instance povolení správce systému a zaměřit se na čem nejvíc pro firmy. Řada aktivit Správce nebo správce databáze systému nejsou vyžadovány, nebo jsou jednoduché. Například operačního systému / instalace RDBMS a opravy chyb, dynamické instance změny velikosti a konfigurace, zálohování, replikace databáze (včetně systémových databází), konfigurace vysoké dostupnosti a konfigurace dat sledování stavu a výkonu datové proudy. 

> [!IMPORTANT]
> Seznam podporovaných, částečně podporované a nepodporované funkce najdete v tématu [funkce SQL Database](sql-database-features.md). Seznam rozdíly T-SQL ve spravované instance oproti systému SQL Server najdete v tématu [spravované Instance T-SQL rozdíly v systému SQL Server](sql-database-managed-instance-transact-sql-information.md)
 
## <a name="next-steps"></a>Další postup

- Pro funkce a porovnání seznamu, najdete v části [společné funkce SQL](sql-database-features.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě pro Managed Instance](sql-database-managed-instance-vnet-configuration.md).
- Kurz, který vytvoří instanci spravované a obnoví databázi ze záložního souboru, najdete v části [vytvořit instanci spravované](sql-database-managed-instance-create-tutorial-portal.md).
- Kurz migrace pomocí Azure Database Migration Service (DMS) najdete v tématu věnovaném [migraci Managed Instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).
