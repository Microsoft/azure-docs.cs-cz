---
title: Co je spravovaná instance Azure SQL?
description: Přečtěte si, jak Azure Managed instance poskytuje téměř 100% kompatibilitu s nejnovějším databázovým strojem SQL Server (Enterprise Edition).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: bonova
ms.author: bonova
ms.reviewer: sstein, vanto
ms.date: 08/14/2020
ms.openlocfilehash: c98e377ec216bea6c1d4a96b15b3741aa52672e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618126"
---
# <a name="what-is-azure-sql-managed-instance"></a>Co je spravovaná instance Azure SQL?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL je inteligentní, Škálovatelná cloudová databázová služba, která kombinuje nejširší SQL Server kompatibility databázového stroje se všemi výhodami plně spravované a doručoval platformy jako služby. SQL Managed instance má téměř 100% kompatibilitu s nejnovějším databázovým strojem SQL Server (Enterprise Edition), zajišťuje implementaci nativní [virtuální sítě (VNET)](../../virtual-network/virtual-networks-overview.md) , která řeší běžné bezpečnostní obavy, a [obchodní model](https://azure.microsoft.com/pricing/details/sql-database/) , který je pro stávající zákazníky SQL Server příznivý. Spravovaná instance SQL umožňuje stávajícím zákazníkům SQL Server překládat a přesouvat místní aplikace do cloudu s minimálními změnami aplikace a databáze. Ve stejnou dobu zachovává spravovaná instance SQL všechny možnosti PaaS (automatické opravy a aktualizace verzí, [automatizované zálohování](../database/automated-backups-overview.md)a [vysokou dostupnost](../database/high-availability-sla.md)), které výrazně snižují režijní náklady na správu a celkové náklady na vlastnictví.

Pokud se službou Azure SQL Managed instance začínáte, podívejte se na video o *spravované instanci Azure SQL* z naší podrobné sady [videí SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)Azure:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Managed-Instance-Overview-6-of-61/player]

> [!IMPORTANT]
> Seznam oblastí, ve kterých je aktuálně dostupná spravovaná instance SQL, najdete v tématu [podporované oblasti](resource-limits.md#supported-regions).

Následující diagram popisuje klíčové funkce spravované instance SQL:

![Klíčové funkce](./media/sql-managed-instance-paas-overview/key-features.png)

Spravovaná instance Azure SQL je navržená pro zákazníky, kteří chtějí migrovat velký počet aplikací z místního prostředí nebo prostředí IaaS, samostatného nebo nezávislého výrobce softwaru (ISV) do plně spravovaného cloudového prostředí PaaS s co nejmenším možným úsilím migrovat. Díky plně automatizované [službě Azure Data Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)můžou zákazníci vyzvednutím a posunutím stávající instance SQL Server do spravované instance SQL, což nabízí kompatibilitu s SQL Server a úplnou izolaci zákaznických instancí s nativní podporou virtuální sítě.  Se Software Assurance vám umožní vyměňovat si stávající licence pro zvýhodněné sazby SQL spravované instance pomocí [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Spravovaná instance SQL je nejlepší cíl migrace v cloudu pro SQL Server instance, které vyžadují vysoké zabezpečení a bohatou programovatelné plochu.

## <a name="key-features-and-capabilities"></a>Klíčové funkce a možnosti

Spravovaná instance SQL kombinuje nejlepší funkce, které jsou k dispozici v Azure SQL Database i databázovém stroji SQL Server.

> [!IMPORTANT]
> Spravovaná instance SQL se spouští se všemi funkcemi nejnovější verze SQL Server, včetně online operací, automatických oprav plánů a dalších vylepšení výkonu podniku. Porovnání funkcí, které jsou k dispozici, je vysvětleno v tématu [porovnání funkcí: Azure SQL Managed instance versus SQL Server](../database/features-comparison.md).

| **Výhody PaaS** | **Kontinuita podnikových procesů** |
| --- | --- |
|Bez nákupu a správy hardwaru <br>Žádná režie správy pro správu základní infrastruktury <br>Rychlé zřizování a škálování služby <br>Automatizované opravy a upgrade verze <br>Integrace s jinými datovými službami PaaS |Smlouva SLA o 99,99% provozu  <br>Integrovaná [Vysoká dostupnost](../database/high-availability-sla.md) <br>Data chráněná pomocí [automatizovaných záloh](../database/automated-backups-overview.md) <br>Uživatelsky konfigurovatelné období uchovávání záloh pro zákazníky <br>[Zálohy](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current&preserve-view=true) iniciované uživatelem <br>Možnost [obnovení databáze v daném časovém okamžiku](../database/recovery-using-backups.md#point-in-time-restore) |
|**Zabezpečení a dodržování předpisů** | **správy**|
|Izolované prostředí ([Integrace virtuální](connectivity-architecture-overview.md)sítě, samostatná služba tenanta, vyhrazený výpočetní výkon a úložiště) <br>[Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Ověřování Azure Active Directory (Azure AD)](../database/authentication-aad-overview.md), podpora jednotného přihlašování <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Objekty zabezpečení serveru Azure AD (přihlášení)</a>  <br>Dodržuje standardy dodržování předpisů stejné jako Azure SQL Database <br>[Auditování SQL](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |Rozhraní Azure Resource Manager API pro automatizaci zřizování a škálování služby <br>Azure Portal funkce ručního zřizování a škálování služeb <br>Database Migration Service

> [!IMPORTANT]
> Spravovaná instance Azure SQL byla certifikována pro určitý počet standardů dodržování předpisů. Další informace najdete v tématu [nabídky dodržování předpisů Microsoft Azure](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), kde můžete najít nejaktuálnější seznam certifikátů dodržování předpisů spravovaných instancí SQL, které jsou uvedené v části **SQL Database**.

Klíčové funkce spravované instance SQL jsou uvedené v následující tabulce:

|Funkce | Popis|
|---|---|
| Verze SQL Server/Build | Databázový stroj SQL Server (nejnovější stabilní) |
| Spravované automatizované zálohy | Yes |
| Integrovaná instance a monitorování databáze a metriky | Yes |
| Automatické opravy softwaru | Yes |
| Nejnovější funkce databázového stroje | Yes |
| Počet datových souborů (řádků) na databázi | Několik |
| Počet souborů protokolu (protokol) na databázi | 1 |
| Nasazení VNet-Azure Resource Manager | Yes |
| Model nasazení sítě VNet – klasický | No |
| Podpora portálu | Yes|
| Integrovaná integrační služba (SSIS) | No-SSIS je součástí [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Integrovaná služba Analysis Service (SSAS) | No-SSAS je samostatný [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Integrovaná služba vytváření sestav (SSRS) | Nepoužívejte místo toho [Power BI stránkované sestavy](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) nebo Hostujte službu SSRS na virtuálním počítači Azure. I když spravovaná instance SQL nemůže službu SSRS spustit jako službu, může hostovat [databáze katalogu SSRS](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) pro server sestav nainstalovaný na virtuálním počítači Azure pomocí SQL Server ověřování. |
|||

## <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

[Nákupní model založený na Vcore](../database/service-tiers-vcore.md) pro SQL Managed instance poskytuje flexibilitu, kontrolu, transparentnost a snadný způsob, jak přeložit požadavky na místní úlohy do cloudu. Tento model umožňuje změnit výpočetní prostředky, paměť a úložiště na základě potřeb vašich úloh. VCore model má taky nárok na úspory až 55 procent a [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server.

V modelu vCore si můžete vybrat mezi generacemi hardwaru.

- Logické procesory **COMPUTE GEN4 –** jsou založené na procesorech Intel® E5-2673 V3 (Haswell) 2,4 GHz, připojené SSD, fyzické jádra, 7 GB RAM na jádro a výpočetních velikostí mezi 8 a 24 virtuální jádra.
- **Gen5** logické procesory jsou založené na Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) a Intel® 8272CL (Cascade Lake) 2,5 GHz, rychlé NVMe SSD, logické jádro a výpočetní velikosti mezi 4 a 80 jádry.

Přečtěte si další informace o rozdílech mezi generacemi hardwaru v [omezeních prostředků spravované instance SQL](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Úrovně služby

Spravovaná instance SQL je k dispozici ve dvou úrovních služeb:

- **Obecné účely**: navržené pro aplikace s typickými požadavky na výkon a I/O latence.
- **Kritické pro podnikání**: slouží pro aplikace s požadavky na latenci v/v a minimálním dopadem na základní operace údržby na zatížení.

Obě úrovně služeb zaručují 99,99% dostupnost a umožňují nezávisle vybrat velikost úložiště a výpočetní kapacitu. Další informace o architektuře vysoké dostupnosti spravované instance Azure SQL najdete v tématu [Vysoká dostupnost a Azure SQL Managed instance](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Úroveň služby Pro obecné účely

Následující seznam popisuje klíčové charakteristiky Pro obecné účely úrovně služeb:

- Navrženo pro většinu obchodních aplikací s typickými požadavky na výkon
- Vysoce výkonné úložiště Azure Blob Storage (8 TB)
- Integrovaná [Vysoká dostupnost](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) založená na spolehlivých úložištích Azure Blob a [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)

Další informace najdete v tématu [vrstva úložiště v pro obecné účely úrovni](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) a [osvědčené postupy pro výkon úložiště a požadavky pro spravovanou instanci SQL (pro obecné účely)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Přečtěte si další informace o rozdílu mezi úrovněmi služeb v [omezeních prostředků spravované instance SQL](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Úroveň služby Pro důležité obchodní informace

Úroveň služby Pro důležité obchodní informace je sestavená pro aplikace s vysokými požadavky na vstupně-výstupní operace. Nabízí nejvyšší odolnost proti chybám při použití několika izolovaných replik.

Následující seznam popisuje klíčové charakteristiky Pro důležité obchodní informace úrovně služeb:

- Navrženo pro obchodní aplikace s nejvyšším výkonem a požadavky na HA
- Obsahuje vysoce rychlé místní úložiště SSD (až do 1 TB v COMPUTE GEN4 – a až 4 TB v Gen5).
- Integrovaná [Vysoká dostupnost](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability) na základě [skupin dostupnosti Always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) a [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)
- Integrovaná další [replika databáze jen pro čtení](../database/read-scale-out.md) , která se dá použít pro vytváření sestav a další úlohy jen pro čtení
- [OLTP v paměti](../in-memory-oltp-overview.md) , které se dají použít pro úlohy s vysokými nároky na výkon  

Přečtěte si další informace o rozdílech mezi úrovněmi služeb v [omezeních prostředků spravované instance SQL](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Operace správy

Spravovaná instance Azure SQL poskytuje operace správy, které můžete použít k automatickému nasazení nových spravovaných instancí, aktualizaci vlastností instance a odstranění instancí, pokud už nepotřebujete. Podrobné vysvětlení operací správy najdete na stránce s [přehledem operací správy spravované instance](management-operations-overview.md) .

## <a name="advanced-security-and-compliance"></a>Pokročilé zabezpečení a dodržování předpisů

Služba SQL Managed instance přináší pokročilé funkce zabezpečení poskytované platformou Azure a databázovým strojem SQL Server.

### <a name="security-isolation"></a>Bezpečnostní izolace

Spravovaná instance SQL poskytuje další izolaci zabezpečení od ostatních tenantů na platformě Azure. Izolace zabezpečení zahrnuje:

- [Implementace nativní virtuální sítě](connectivity-architecture-overview.md) a připojení k místnímu prostředí pomocí Azure ExpressRoute nebo VPN Gateway.
- Ve výchozím nasazení se koncový bod SQL zveřejňuje jenom přes soukromou IP adresu, což umožňuje bezpečné připojení z privátních a hybridních sítí Azure.
- Jeden tenant s vyhrazenou základní infrastrukturou (COMPUTE, úložiště).

Následující diagram popisuje různé možnosti připojení pro vaše aplikace:

![Vysoká dostupnost](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Další informace o integraci virtuální sítě a vynucování zásad sítě na úrovni podsítě najdete v tématu [Architektura virtuální sítě pro spravované instance](connectivity-architecture-overview.md) a [připojení aplikace ke spravované instanci](connect-application-instance.md).

> [!IMPORTANT]
> Ve stejné podsíti můžete umístit několik spravovaných instancí, ať už je to povolené v rámci požadavků na zabezpečení, jako by vám to přineslo další výhody. Společné umísťování instancí ve stejné podsíti významně zjednodušuje údržbu síťové infrastruktury a sníží čas zřízení instance, protože doba trvání dlouhého zřízení je spojená s náklady na nasazení první spravované instance v podsíti.

### <a name="security-features"></a>Funkce zabezpečení

Azure SQL Managed instance poskytuje sadu pokročilých funkcí zabezpečení, které se dají použít k ochraně vašich dat.

- [Auditování spravované instance SQL](auditing-configure.md) sleduje události databáze a zapisuje je do souboru protokolu auditu umístěného v účtu úložiště Azure. Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.
- Šifrování dat v rámci SQL spravované instance zabezpečuje vaše data tím, že zajišťuje šifrování dat při pohybu pomocí Transport Layer Security. Kromě Transport Layer Security nabízí spravovaná instance SQL i ochranu citlivých dat v letu, v klidovém umístění a při zpracování dotazů pomocí [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted nabízí zabezpečení dat proti narušením, která se týkají krádeže důležitých dat. Například u Always Encrypted se čísla kreditních karet ukládají v databázi vždycky, i když se zpracovávají dotazy a umožňují dešifrování v bodě použití autorizovanými pracovníky nebo aplikacemi, které potřebují zpracovávat tato data.
- [Rozšířená ochrana před internetovými útoky](threat-detection-configure.md) doplňuje [auditování](auditing-configure.md) tím, že poskytuje další vrstvu zabezpečení, která je součástí služby, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Jste upozorňováni na podezřelé aktivity, potenciální ohrožení zabezpečení a útoky prostřednictvím injektáže SQL a také na neobvyklé vzory přístupu k databázi. Výstrahy rozšířené ochrany před internetovými útoky si můžete prohlédnout z [Azure Security Center](https://azure.microsoft.com/services/security-center/). Poskytují podrobné informace o podezřelých aktivitách a doporučuje akci, jak tuto hrozbu prozkoumat a zmírnit.  
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking) omezuje vystavení citlivých dat jejich maskováním na uživatele bez oprávnění. Dynamické maskování dat pomáhá zabránit neoprávněnému přístupu k citlivým datům tím, že umožňuje určit, kolik citlivých dat se má zobrazit s minimálním dopadem na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.
- [Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) (RLS) umožňuje řídit přístup k řádkům v databázové tabulce na základě charakteristik uživatele, který spouští dotaz (například členství ve skupině nebo kontext spuštění). RLS zjednodušuje návrh a kódování zabezpečení ve vaší aplikaci. RLS umožňuje implementovat omezení přístupu k datovým řádkům. Například zajistěte, aby měli zaměstnanci přístup pouze k datovým řádkům, které jsou relevantní pro jejich oddělení, nebo omezit přístup k datům pouze na relevantní data.
- [Transparentní šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) šifruje datové soubory spravované instance SQL, označované jako šifrování dat v klidovém formátu. TDE provádí šifrování v/v v reálném čase a dešifruje data a soubory protokolů. Šifrování používá šifrovací klíč databáze (klíč DEK), který je uložený ve spouštěcím záznamu databáze pro dostupnost během obnovování. Pomocí transparentního šifrování dat můžete chránit všechny své databáze ve spravované instanci. TDE je prověřená technologie šifrování v klidovém SQL Server, kterou vyžaduje mnoho standardů dodržování předpisů pro ochranu před odcizením úložného média.

Migrace zašifrované databáze do spravované instance SQL je podporována prostřednictvím Azure Database Migration Service nebo nativního obnovení. Pokud plánujete migrovat šifrovanou databázi pomocí nativního obnovení, je nutný krok migrace stávajícího certifikátu TDE z instance SQL Server do spravované instance SQL. Další informace o možnostech migrace najdete v tématu [SQL Server migrace do spravované instance SQL](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Integrace Azure Active Directory

Spravovaná instance SQL podporuje tradiční přihlášení SQL Server databázového stroje a přihlašování do Azure AD. Objekty zabezpečení serveru Azure AD (přihlášení) (**Public Preview**) jsou cloudová verze místních přihlášení Azure, která používáte v místním prostředí. Objekty zabezpečení serveru Azure AD (přihlášení) umožňují zadat uživatele a skupiny z vašeho tenanta Azure AD jako pravdivé objekty v rozsahu instance, které mohou provádět operace na úrovni instance, včetně databázových dotazů v rámci stejné spravované instance.

Zavádí se nová syntaxe pro vytváření objektů zabezpečení serveru Azure AD (přihlášení) **od externího poskytovatele**. Další informace o syntaxi najdete v tématu <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Vytvoření přihlášení</a>a přečtěte si článek [zřízení Azure Active Directory správce pro spravovanou instanci SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

Služba SQL Managed instance umožňuje centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu pomocí [Azure Active Directory Integration](../database/authentication-aad-overview.md). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory podporuje službu [Multi-Factor Authentication](../database/authentication-mfa-ssms-configure.md) pro zvýšení zabezpečení dat a aplikací při podpoře jednotného přihlašování.

### <a name="authentication"></a>Authentication

Ověřování spravované instance SQL odkazuje na to, jak uživatelé při připojování k databázi prokáže jejich identitu. Spravovaná instance SQL podporuje dva typy ověřování:  

- **Ověřování SQL**:

  Tato metoda ověřování používá uživatelské jméno a heslo.
- **Ověřování Azure Active Directory**:

  Tato metoda ověřování používá identity spravované pomocí Azure Active Directory a je podporovaná pro spravované a integrované domény. [Kdykoliv to půjde](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), použijte ověřování pomocí Active Directory (integrované zabezpečení).

### <a name="authorization"></a>Autorizace

Autorizace odkazuje na to, co může uživatel dělat v rámci databáze ve spravované instanci Azure SQL, a řídí se členstvím v databázových rolích vašeho uživatelského účtu a oprávněními na úrovni objektů. SQL Managed instance má stejné možnosti autorizace jako SQL Server 2017.

## <a name="database-migration"></a>Migrace databáze

Spravovaná instance SQL cílí na scénáře uživatelů s migrací do databáze z místních nebo IaaSch databázových implementací. Spravovaná instance SQL podporuje několik možností migrace databáze:

### <a name="backup-and-restore"></a>Zálohování a obnovení  

Přístup k migraci využívá zálohy SQL do úložiště objektů BLOB v Azure. Zálohy uložené v objektu BLOB služby Azure Storage je možné přímo obnovit do spravované instance pomocí [příkazu T-SQL Restore](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

- Základní informace o tom, jak obnovit World World Importers – soubor zálohy Standard, najdete v tématu [obnovení záložního souboru do spravované instance](restore-sample-database-quickstart.md). V tomto rychlém startu se dozvíte, že musíte nahrát záložní soubor do úložiště objektů BLOB v Azure a zabezpečit ho pomocí klíče sdíleného přístupového podpisu (SAS).
- Informace o obnovení z adresy URL najdete v tématu [NATIVNÍ obnovení z adresy URL](migrate-to-instance-from-sql-server.md#native-restore-from-url).

> [!IMPORTANT]
> Zálohy ze spravované instance lze obnovit pouze do jiné spravované instance. Nelze je obnovit do SQL Server instance nebo Azure SQL Database.

### <a name="database-migration-service"></a>Database Migration Service

Azure Database Migration Service je plně spravovaná služba navržená tak, aby umožňovala bezproblémové migrace z více databázových zdrojů do datových platforem Azure s minimálními výpadky. Tato služba zjednodušuje úlohy potřebné k přesunutí stávajících databází třetích stran a SQL Server do Azure SQL Database, spravované instance Azure SQL a SQL Server na virtuálním počítači Azure. Přečtěte si [článek jak migrovat místní databázi do spravované instance SQL pomocí Database Migration Service](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Podporované funkce SQL

Spravovaná instance SQL se zaměřuje na zajištění téměř 100% kompatibility Surface Surface s nejnovější verzí SQL Server prostřednictvím připraveného plánu vydání. Seznam funkcí a porovnání najdete v tématu [porovnání funkcí spravované instance SQL](../database/features-comparison.md)a seznam rozdílů t-SQL ve spravované instanci SQL a v tématu SQL Server, viz [rozdíly t-SQL spravované instance SQL z SQL Server](transact-sql-tsql-differences-sql-server.md).

Spravovaná instance SQL podporuje zpětnou kompatibilitu pro databáze SQL Server 2008. Je podporována přímá migrace z databázových serverů SQL Server 2005 a úroveň kompatibility migrovaných databází SQL Server 2005 je aktualizována na SQL Server 2008.
  
Následující diagram popisuje kompatibilitu oblasti Surface ve spravované instanci SQL:  

![Kompatibilita oblasti Surface](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>Klíčové rozdíly mezi SQL Server místní a SQL Managed instance

Výhody spravované instance SQL nemusejí být v cloudu vždycky aktuální, což znamená, že některé funkce v SQL Server můžou být zastaralé, mají být vyřazené nebo mají alternativy. Existují určité případy, kdy nástroje potřebují rozpoznat, že konkrétní funkce funguje trochu jiným způsobem nebo že služba běží v prostředí, které neovládáte úplně.

Některé klíčové rozdíly:

- Vysoká dostupnost je integrovaná a předem nakonfigurovaná s využitím technologie podobně jako [skupiny dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- K dispozici jsou pouze automatizované zálohy a obnovení k bodu v čase. Zákazníci můžou zahájit `copy-only` zálohování, které nekoliduje s automatickým řetězcem zálohování.
- Zadání úplných fyzických cest není podporováno, takže všechny odpovídající scénáře musí být podporovány jinak: příkaz Restore DB nepodporuje operaci MOVE. možnost CREATE DB neumožňuje použití fyzických cest, BULK INSERT pracuje pouze s objekty blob Azure atd.
- Spravovaná instance SQL podporuje [ověřování Azure AD](../database/authentication-aad-overview.md) jako cloudovou alternativu k ověřování Windows.
- Spravovaná instance SQL automaticky spravuje skupiny souborů XTP a soubory pro databáze obsahující In-Memory objekty OLTP.
- Spravovaná instance SQL podporuje služba SSIS (SQL Server Integration Services) (SSIS) a může hostovat katalog SSIS (SSISDB), který ukládá balíčky SSIS, ale spouští se na spravovaném Azure-SSIS Integration Runtime (IR) v Azure Data Factory. Viz téma [Create Azure-SSIS IR in Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Pokud chcete porovnat funkce SSIS, přečtěte si téma [porovnání SQL Database s SQL Managed instance](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Funkce pro správu

Spravovaná instance SQL umožňuje správcům systémů strávit méně času na úlohy správy, protože služba je buď vykonává za vás, nebo významně tyto úlohy zjednodušuje. Například [operační systém/RDBMS instalace a opravy](../database/high-availability-sla.md), [Změna velikosti a konfigurace dynamické instance](../database/single-database-scale.md), [zálohování](../database/automated-backups-overview.md), [replikace databáze](replication-between-two-instances-configure-tutorial.md) (včetně systémových databází), konfigurace s [vysokou dostupností](../database/high-availability-sla.md)a konfigurace datových proudů monitorování stavu a [výkonu](../../azure-monitor/insights/azure-sql.md) .

Další informace najdete v [seznamu podporovaných a nepodporovaných funkcí SQL spravované instance](../database/features-comparison.md)a [rozdíly v T-SQL mezi spravovanou instancí SQL a SQL Server](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Programové určení spravované instance

Následující tabulka ukazuje několik vlastností, které jsou přístupné prostřednictvím jazyka Transact-SQL, které můžete použít k detekci, že aplikace pracuje se službou SQL Managed instance a načítá důležité vlastnosti.

|Vlastnost|Hodnota|Komentář|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) – 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Tato hodnota je stejná jako v SQL Database. To neznamená, **že** modul SQL verze 12 (SQL Server 2014). Spravovaná instance SQL vždy spouští nejnovější stabilní verzi stroje SQL, která je stejná nebo vyšší než nejnovější dostupná verze RTM SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Tato hodnota je stejná jako v SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Tato hodnota jednoznačně identifikuje spravovanou instanci.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Úplný název DNS instance v následujícím formátu: `<instanceName>` . `<dnsPrefix>` . database.windows.net, kde `<instanceName>` je název poskytovaný zákazníkem, zatímco `<dnsPrefix>` se automaticky vygenerovala část názvu, která zaručuje jedinečnost globálních názvů DNS (například "wcus17662feb9ce98").|Příklad: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak vytvořit první spravovanou instanci, najdete v tématu [Průvodce rychlým startem](instance-create-quickstart.md).
- Seznam funkcí a porovnání najdete v tématu věnovaném [běžným funkcím SQL](../database/features-comparison.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě spravované instance SQL](connectivity-architecture-overview.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Kurz týkající se použití Azure Database Migration Service k migraci najdete v tématu [migrace spravované instance SQL pomocí Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Informace o pokročilém monitorování výkonu databáze spravované instance SQL s integrovanými funkcemi pro řešení potíží najdete v tématu [monitorování spravované instance Azure SQL pomocí Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Informace o cenách najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/managed/).
