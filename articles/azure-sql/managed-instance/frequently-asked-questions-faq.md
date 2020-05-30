---
title: Nejčastější dotazy (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Nejčastější dotazy týkající se spravované instance Azure SQL SQL
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 518c4b83721e80aeaadfbdf5b03cddc62ae5479f
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2020
ms.locfileid: "84216334"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Nejčastější dotazy k Azure SQL Managed instance (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek obsahuje nejčastější dotazy týkající se [spravované instance Azure SQL](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Podporované funkce

**Kde najdu seznam funkcí, které jsou podporovány ve spravované instanci SQL?**

Seznam podporovaných funkcí ve spravované instanci SQL najdete v tématu [funkce spravované instance Azure SQL](../database/features-comparison.md).

Rozdíly v syntaxi a chování mezi spravovanou instancí Azure SQL a SQL Server najdete v tématu [rozdíly v T-SQL od SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="tech-spec--resource-limits"></a>Technické specifikace & omezení prostředků
 
**Kde můžu najít technické charakteristiky a omezení prostředků pro spravovanou instanci SQL?**

Dostupné charakteristiky vytváření hardwaru najdete v tématu [technické rozdíly v segenerováních hardwaru](resource-limits.md#hardware-generation-characteristics).
Dostupné úrovně služeb a jejich charakteristiky najdete v tématu [technické rozdíly mezi úrovněmi služeb](resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Známé problémy & chybách

**Kde můžu najít známé problémy a chyby?**

Chyby a známé problémy najdete v tématu [známé problémy](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Nové funkce

**Kde můžu najít nejnovější funkce a funkce ve verzi Public Preview?**

Nové funkce a verze Preview najdete v [poznámkách k verzi](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Časy nasazení 

**Jak dlouho trvá vytvoření nebo aktualizace instance nebo obnovení databáze?**

Očekávaná doba vytvoření spravované instance SQL nebo změna úrovně služby (virtuální jádra, Storage) závisí na několika faktorech. Prohlédněte si [operace správy](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Konvence pojmenování

**Může mít spravovaná instance SQL stejný název jako místní instance SQL Server?**

Změna názvu spravované instance SQL se nepodporuje.

Výchozí zóna DNS spravované instance SQL *. Database.Windows.NET* může být změněna. 

Chcete-li použít jinou zónu DNS namísto výchozího, například *. contoso.com*: 
- Použijte CliConfig k definování aliasu. Tento nástroj je jenom obálkou nastavení registru, takže ho můžete udělat taky pomocí zásad skupiny nebo skriptu.
- Použijte *CNAME* s možností *TrustServerCertificate = true* .

## <a name="move-db-from-mi"></a>Přesunout databázi ze MI 

**Jak můžu přesunout databázi ze spravované instance SQL zpátky do SQL Server nebo Azure SQL Database?**

Můžete [exportovat databázi do BacPac](../database/database-export.md) a pak [importovat soubor BacPac]( ../database/database-import.md). Toto je doporučený postup, pokud je databáze menší než 100 GB.

Transakční replikaci je možné použít, pokud všechny tabulky v databázi mají primární klíče.

Nativní `COPY_ONLY` zálohy pořízené ze spravované instance SQL nelze obnovit do SQL Server, protože spravovaná instance SQL má vyšší verzi databáze v porovnání s SQL Server.

## <a name="migrate-instance-db"></a>Migrace instance DB

**Jak můžu migrovat databázi instance do jediného Azure SQL Database?**

Jednou z možností je [exportovat databázi do BacPac](../database/database-export.md) a pak [importovat soubor BacPac](../database/database-import.md). 

Toto je doporučený postup, pokud je databáze menší než 100 GB. Transakční replikaci je možné použít, pokud všechny tabulky v databázi mají primární klíče.

## <a name="switch-hardware-generation"></a>Přepnout generování hardwaru 

**Můžu přepnout generování hardwaru spravované instance SQL mezi Gen 4 a gen 5 online?**

Automatizované online přepínání mezi generacemi hardwaru je možné, pokud jsou hardwarové generace dostupné v oblasti, ve které je vaše spravovaná instance SQL zřízená. V takovém případě si můžete prohlédnout [stránku Přehled modelu Vcore](../database/service-tiers-vcore.md) vysvětlující, jak přepínat mezi generováním hardwaru.

Jedná se o dlouhotrvající operaci, protože nová spravovaná instance SQL se zřídí na pozadí a databáze automaticky přenesené mezi starou a novou instancí na konci procesu. 

**Co když ve stejné oblasti nejsou podporovaná hardwarová generování?**

Pokud se ve stejné oblasti nepodporují hardwarové generace, změna hardwarového generování je možná, ale je nutné ji provést ručně. To vyžaduje zřízení nové instance v oblasti, kde je k dispozici potřebná generace hardwaru, a ruční zálohování a obnovení dat mezi starou a novou instancí.

**Co dělat v případě, že pro provádění operace aktualizace nejsou k dispozici dostatek IP adres?**

V případě, že v podsíti, ve které je zřízena vaše spravovaná instance, není k dispozici dostatek IP adres, budete muset vytvořit novou podsíť a novou spravovanou instanci v ní. Také doporučujeme, aby se nová podsíť vytvořila s více přidělenými IP adresami, aby budoucí operace aktualizace nedocházelo k podobným situacím (pro správnou velikost podsítě, podívejte [se, jak určit velikost podsítě virtuální](vnet-subnet-determine-size.md)sítě. Po zřízení nové instance můžete ručně zálohovat a obnovovat data mezi starou a novou instancí nebo provést obnovení mezi instancemi [v rámci časového okamžiku](point-in-time-restore.md?tabs=azure-powershell). 


## <a name="tune-performance"></a>Ladění výkonu

**Návody vyladit výkon mé spravované instance SQL?**

Pro obecné účely spravovaná instance SQL používá vzdálené úložiště, protože se jedná o velikost dat a souborů protokolu, které jsou důležité pro výkon. Další informace najdete v tématu [vliv velikosti souboru protokolu na pro obecné účely výkonu spravované instance SQL](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Pokud se vaše zatížení skládá z velkého množství malých transakcí, zvažte možnost přepnout typ připojení ze proxy serveru do režimu přesměrování.

## <a name="maximum-storage-size"></a>Maximální velikost úložiště

**Jaká je maximální velikost úložiště pro spravovanou instanci SQL?**

Velikost úložiště pro spravovanou instanci SQL závisí na vybrané úrovni služby (Pro obecné účely nebo Pro důležité obchodní informace). Omezení úložiště těchto úrovní služeb najdete v tématu věnovaném [vlastnostem úrovně služby](../database/service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Náklady na úložiště záloh 

**Je úložiště zálohování odečteno od úložiště spravované instance SQL?**

Ne, úložiště zálohování se neodečte z prostoru úložiště spravované instance SQL. Úložiště zálohování je nezávislé na rozsahu úložiště instance a velikost není omezená. Úložiště zálohování je omezené časovým obdobím, aby se zachovalo zálohování databází instancí, a to po dobu 7 až 35 dnů. Podrobnosti najdete v tématu [automatizované zálohování](../database/automated-backups-overview.md).

## <a name="track-billing"></a>Sledovat fakturaci

**Existuje způsob, jak sledovat náklady na fakturaci spravované instance SQL?**

Můžete to udělat pomocí [řešení Azure cost management](/azure/cost-management/). V [Azure Portal](https://portal.azure.com) přejděte na **předplatná** a vyberte **Analýza nákladů**. 

Použijte možnost **akumulované náklady** a pak filtrujte podle **typu prostředku** jako `microsoft.sql/managedinstances` . 
  
## <a name="inbound-nsg-rules"></a>Příchozí NSG pravidla

**Jak můžu nastavit příchozí NSG pravidla pro porty pro správu?**

Rovina ovládacího prvku spravované instance SQL SQL udržuje pravidla NSG, která chrání porty pro správu.

Zde najdete porty pro správu, které se používají pro:

Porty 9000 a 9003 používá Service Fabric infrastruktura. Service Fabric primární role je zachovat dobrý stav virtuálního clusteru a zachovat stav cíle z hlediska počtu replik komponent.

Agent Node používá porty 1438, 1440 a 1452. Agent uzlu je aplikace, která běží v clusteru a je používána rovinou ovládacího prvku k provádění příkazů pro správu.

Kromě pravidel NSG integrovaná brána firewall chrání instanci v síťové vrstvě. Komunikace s aplikační vrstvou je chráněná pomocí certifikátů.
  
Další informace a jak ověřit vestavěnou bránu firewall najdete v tématu [integrovaná brána firewall Azure SQL Managed instance](management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Zmírnění rizik exfiltrace dat  

**Jak můžu zmírnit rizika exfiltrace dat?**

Zákazníkům se doporučuje použít sadu nastavení zabezpečení a ovládací prvky, aby se zmírnily veškerá rizika exfiltracea dat:

- Zapněte [transparentní šifrování dat (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) na všech databázích.
- Vypnout modul CLR (Common Language Runtime). To se doporučuje i v místním prostředí.
- Použijte pouze ověřování Azure Active Directory (AAD).
- Instance přístupu s nízkým privilegovaným účtem DBA
- Nakonfigurujte přístup JiT JumpBox pro účet sysadmin.
- Zapněte [auditování SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)a integrujte je pomocí mechanismů upozorňování.
- Zapněte [detekci hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) ze sady [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Případy použití úspory nákladů

**Kde najdu případy použití a výsledkem jsou úspory nákladů pomocí spravované instance SQL?**

Případové studie spravované instance SQL:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Pro lepší porozumění výhodám, nákladům a rizikům spojeným s nasazením spravované instance SQL je také studie Forrester: [celkový ekonomický dopad na mi](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Aktualizace DNS 

**Můžu aktualizovat DNS?**

V současné době neposkytujeme funkci pro aktualizaci konfigurace serveru DNS pro spravovanou instanci SQL.

Konfigurace DNS se nakonec aktualizuje:

- Když vyprší platnost zapůjčení DHCP.
- Při upgradu platformy.

Jako alternativní řešení můžete snížit vCore spravované instance SQL na 4 a později je upgradovat. To má vedlejší účinky aktualizace konfigurace DNS.


## <a name="ip-address"></a>IP adresa

**Můžu se k spravované instanci SQL připojit pomocí IP adresy?**

Připojení k spravované instanci SQL pomocí IP adresy není podporováno. Název hostitele spravované instance SQL mapuje na nástroj pro vyrovnávání zatížení před virtuálním clusterem spravované instance SQL. Protože jeden virtuální cluster může hostovat více instancí spravovaného systémem SQL, nelze připojení směrovat do správné spravované instance SQL bez explicitního zadání názvu.

Další informace o architektuře virtuálních clusterů spravované instance SQL najdete v tématu [Architektura připojení virtuálních clusterů](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Může spravovaná instance SQL obsahovat statickou IP adresu?**

Ve výjimečných, ale nezbytných situacích může být nutné provést online migraci spravované instance SQL do nového virtuálního clusteru. V případě potřeby se tato migrace provádí kvůli změnám v naší technologické sadě zaměřené na zvýšení zabezpečení a spolehlivosti služby. Výsledkem migrace na nový virtuální cluster je změna IP adresy, která je namapovaná na název hostitele spravované instance SQL. Služba SQL Managed instance nedeklaruje podporu statických IP adres a vyhrazuje právo ji změnit bez upozornění jako součást běžných cyklů údržby.

Z tohoto důvodu se důrazně nedoporučuje spoléhat na neměnnosti IP adresy, protože by mohlo dojít k zbytečnému výpadku.

## <a name="change-time-zone"></a>Změnit časové pásmo

**Můžu změnit časové pásmo existující spravované instance SQL?**

Konfiguraci časového pásma lze nastavit při prvním zřízení spravované instance SQL. Změna časového pásma existující spravované instance SQL se nepodporuje. Podrobnosti najdete v tématu [omezení časového pásma](timezones-overview.md#limitations).

Alternativní řešení zahrnují vytvoření nové spravované instance SQL se správným časovým pásmem a následné provedení ručního zálohování a obnovení, nebo to, co doporučujeme, provedení [obnovení mezi instancemi v čase](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Řešení problémů s výkonem

**Návody vyřešit problémy s výkonem pomocí spravované instance SQL?**

Pro porovnání výkonu mezi spravovanými instancemi SQL a SQL Server je vhodným výchozím bodem [osvědčené postupy pro porovnání výkonu mezi službou Azure SQL Managed instance a SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) článkem.

Načítání dat je často pomalejší na spravované instanci SQL než v SQL Server z důvodu povinného plného modelu obnovení a [omezení](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) propustnosti zápisu protokolu transakcí. V některých případech to může být způsobeno tím, že se v databázi tempdb načítají přechodná data namísto uživatelské databáze nebo použije clusterované tabulky columnstore nebo paměťově optimalizované tabulky.


## <a name="restore-encrypted-backup"></a>Obnovit šifrované zálohování

**Je možné obnovit zašifrovanou databázi do spravované instance SQL?**

Ano, databázi nemusíte dešifrovat, aby ji bylo možné obnovit ve spravované instanci SQL. Aby bylo možné číst data z šifrovaného záložního souboru, je třeba zadat certifikát nebo klíč, který se používá jako ochrana šifrovacího klíče ve zdrojovém systému na spravovaném serveru SQL. Existují dva možné způsoby, jak to provést:

- *Nahrání certifikátu – ochrana spravované instance SQL* Můžete to udělat jenom pomocí PowerShellu. [Vzorový skript](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) popisuje celý proces.
- *Nahrajte asymetrický klíč-Protector do Azure Key Vault (integrace) a najeďte na něj spravovanou instanci SQL*. Tento přístup se podobá BYOKm případu použití TDE, který taky používá integraci integrace k uložení šifrovacího klíče. Pokud nechcete používat klíč jako ochranu šifrovacího klíče a chcete ho jenom zpřístupnit pro spravovanou instanci SQL pro obnovení šifrovaných databází, postupujte podle pokynů pro [Nastavení BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)a zrušte zaškrtnutí políčka *nastavit vybraný klíč jako výchozí ochranu TDE*.

Po zpřístupnění ochrany šifrování pro spravovanou instanci SQL můžete postupovat podle standardního postupu obnovení databáze.

## <a name="migrate-from-sql-database"></a>Migrace z SQL Database 

**Jak můžu migrovat z Azure SQL Database do spravované instance SQL?**

Služba SQL Managed instance nabízí stejné úrovně výkonu na výpočetní úrovni a velikost úložiště jako Azure SQL Database. Pokud chcete konsolidovat data na jednu instanci nebo jednoduše potřebujete funkci podporovanou výhradně ve spravované instanci SQL, můžete data migrovat pomocí funkce Export/Import (BACPAC).

## <a name="password-policy"></a>Zásady hesel 

**Jaké zásady hesel se používají pro přihlašovací údaje SQL spravované instance SQL?**

Zásady hesel spravované instance SQL pro přihlášení SQL dědí zásady platformy Azure, které se používají u virtuálních počítačů tvořících virtuální cluster, který je držitelem spravované instance. V současné době není možné změnit žádné z těchto nastavení, protože tato nastavení definuje Azure a dědí spravovaná instance.

 > [!IMPORTANT]
 > Platforma Azure může měnit požadavky zásad bez upozorňování služeb, které se na tyto zásady spoléhají.

**Co jsou aktuální zásady platformy Azure?**

Každé přihlášení musí po přihlášení zadat své heslo a po dosažení maximálního stáří změnit jeho heslo.

| **Zásady** | **Nastavení zabezpečení** |
| --- | --- |
| Maximální stáří hesla | 42 dní |
| Minimální stáří hesla | 1 den |
| Minimální délka hesla | 10 znaků |
| Heslo musí splňovat požadavky na složitost. | Povoleno |

**Je možné zakázat složitost a vypršení platnosti hesla ve spravované instanci SQL na úrovni přihlášení?**

Ano, je možné řídit CHECK_POLICY a CHECK_EXPIRATION pole na úrovni přihlášení. Aktuální nastavení můžete kontrolovat spuštěním následujícího příkazu T-SQL:

```sql
SELECT *
FROM sys.sql_logins
```

Potom můžete změnit zadaná nastavení přihlášení spuštěním příkazu:

```sql
ALTER LOGIN test WITH CHECK_POLICY = ON;
ALTER LOGIN test WITH CHECK_EXPIRATION = ON;
```

(nahraďte ' test ' s požadovaným přihlašovacím jménem.)

 > [!Note]
 > Výchozí hodnoty pro CHECK_POLICY a CHECK_EXPIRATION jsou nastaveny na OFF.
