---
title: Nejčastější dotazy ke spravované instanci
description: Nejčastější dotazy ke spravované instanci SQL Database (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364160"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Nejčastější dotazy ke spravované instanci SQL Database (FAQ)

Tento článek obsahuje mnoho nejčastějších otázek týkajících se [instance spravované databází SQL](sql-database-managed-instance.md).

## <a name="supported-features"></a>Podporované funkce

**Kde najdu seznam funkcí podporovaných ve spravované instanci?**

Seznam podporovaných funkcí ve spravované instanci najdete v [tématu Azure SQL Database versus SQL Server](sql-database-features.md).

Rozdíly v syntaxi a chování mezi spravovanou instancí Azure SQL Database a místním SQL Serverem najdete v [tématu Rozdíly T-SQL od SQL Serveru](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Technické specifikace & omezení zdrojů
 
**Kde najdu technické charakteristiky a omezení prostředků pro spravovanou instanci?**

Dostupné charakteristiky generování hardwaru naleznete [v tématu technické rozdíly v generacích hardwaru](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Dostupné úrovně služeb a jejich charakteristiky naleznete v [technických rozdílech mezi úrovněmi služeb](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Známé problémy & chyby

**Kde najdu známé problémy a chyby?**

Chyby a známé problémy viz [známé problémy](sql-database-release-notes.md#known-issues).

## <a name="new-features"></a>Nové funkce

**Kde najdu nejnovější funkce a funkce ve verzi Public Preview?**

Nové funkce a funkce náhledu viz [poznámky k verzi](sql-database-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Časy nasazení 

**Kolik času trvá vytvoření nebo aktualizace instance nebo obnovení databáze?**

Očekávaná doba vytvoření nové spravované instance nebo změna úrovně služby (virtuální jádra, úložiště) závisí na několika faktorech. Podívejte se na [operace řízení](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Konvence

**Může mít spravovaná instance stejný název jako místní SQL Server?**

Změna názvu spravované instance není podporována.

Výchozí zóna DNS spravované instance *.database.windows.net* by mohla být změněna. 

Chcete-li místo výchozí zóny DNS použít jinou zónu DNS, například *.contoso.com*: 
- K definování aliasu použijte CliConfig. Nástroj je pouze obálka nastavení registru, takže by to mohlo být provedeno pomocí zásad skupiny nebo skriptu.
- Použijte *CNAME* s možností *TrustServerCertificate=true.*

## <a name="move-db-from-mi"></a>Přesunout DB z MI 

**Jak můžu přesunout databázi ze spravované instance zpět na SQL Server nebo Azure SQL Database?**

Databázi můžete [exportovat do bacpacu](sql-database-export.md) a potom [importovat soubor BACPAC]( sql-database-import.md). Toto je doporučený přístup, pokud je databáze menší než 100 GB.

Transakční replikace lze použít, pokud všechny tabulky v databázi mají primární klíče.

Nativní `COPY_ONLY` zálohy převzaté ze spravované instance nelze obnovit na SQL Server, protože spravovaná instance má vyšší verzi databáze ve srovnání se serverem SQL Server.

## <a name="migrate-instance-db"></a>Migrace instance DB

**Jak můžu migrovat databázi instancí do jedné azure sql databáze?**

Jednou z možností je [exportovat databázi do BACPAC](sql-database-export.md) a potom [importovat soubor BACPAC](sql-database-import.md). 

Toto je doporučený přístup, pokud je databáze menší než 100 GB. Transakční replikace lze použít, pokud všechny tabulky v databázi mají primární klíče.

## <a name="switch-hardware-generation"></a>Přepnutí generování hardwaru 

**Mohu přepnout generaci hardwaru spravované instance mezi Gen 4 a Gen 5 online?**

Automatické přepínání online mezi generacemi hardwaru je možné, pokud jsou obě generace hardwaru k dispozici v oblasti, kde je zřízena vaše spravovaná instance. V takovém případě můžete zkontrolovat [stránku přehledu modelu virtuálních jader](sql-database-service-tiers-vcore.md) s vysvětlením, jak přepínat mezi generacemi hardwaru.

Jedná se o dlouhotrvající operaci, protože nová spravovaná instance bude zřízena na pozadí a databáze se automaticky přenesou mezi starou a novou instancí s rychlým převzetím služeb při selhání na konci procesu. 

Pokud obě generace hardwaru nejsou podporovány ve stejné oblasti, změna generování hardwaru je možné, ale musí být provedeno ručně. To vyžaduje, abyste zřídit novou instanci v oblasti, kde je k dispozici požadované generování hardwaru a ručně zálohovat a obnovit data mezi staré a nové instance.


## <a name="tune-performance"></a>Ladění výkonu

**Jak naladím výkon spravované instance?**

Spravovaná instance pro obecné účely používá vzdálené úložiště kvůli velikosti dat a souborů protokolu, které jsou důležité pro výkon. Další informace naleznete [v tématu Dopad velikosti souboru protokolu na výkon spravované instance pro obecné účely](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Pokud se vaše úloha skládá z velké části malých transakcí, zvažte přepnutí typu připojení z proxy serveru do režimu přesměrování.

## <a name="maximum-storage-size"></a>Maximální velikost úložiště

**Jaká je maximální velikost úložiště pro spravovanou instanci?**

Velikost úložiště pro spravovanou instanci závisí na vybrané úrovni služby (obecné účely nebo důležité pro podnikání). Omezení úložiště těchto úrovní služeb naleznete v tématu [Charakteristika úrovně služby](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Zálohování nákladů na úložiště 

**Odečte se úložiště záloh z úložiště spravovaných instancí?**

Ne, úložiště záloh se neodečítá z úložného prostoru spravované instance. Úložiště záloh je nezávislé na úložném prostoru instance a není omezeno na velikost. Úložiště záloh je omezeno časovým obdobím pro zachování zálohy databází instancí, které lze konfigurovat od 7 do 35 dnů. Podrobnosti naleznete [v tématu Automatické zálohování](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Sledování fakturace

**Existuje způsob, jak sledovat náklady na fakturaci pro spravovanou instanci?**

Můžete tak učinit pomocí [řešení Azure Cost Management](/azure/cost-management/). Přejděte na **předplatná** na [webu Azure portal](https://portal.azure.com) a vyberte Analýza **nákladů**. 

Použijte možnost **Akumulované náklady** a potom `microsoft.sql/managedinstances`filtrujte podle typu **Zdroj** jako . 
  
## <a name="inbound-nsg-rules"></a>Příchozí pravidla nsg

**Jak lze nastavit příchozí pravidla nsg pro porty správy?**

Rovina řízení spravované instance udržuje pravidla nsg, která chrání porty pro správu.

Zde je to, co správa porty se používají pro:

Porty 9000 a 9003 jsou používány infrastrukturou Service Fabric. Primární role Service Fabric je zachovat virtuální cluster v pořádku a zachovat stav cíle z hlediska počtu replik komponent.

Porty 1438, 1440 a 1452 jsou používány agentem uzlu. Agent uzlu je aplikace, která běží uvnitř clusteru a je používána rovinou ovládacího prvku ke spuštění příkazů pro správu.

Kromě pravidel skupiny zabezpečení sítě chrání integrovaná brána firewall instanci v síťové vrstvě. Na aplikační vrstvě je komunikace chráněna certifikáty.
  
Další informace a způsob ověření integrované brány firewall najdete v tématu [Integrovaná brána firewall spravované službou Azure SQL Database](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Zmírnění rizik a rizik a exfiltrace dat  

**Jak mohu zmírnit rizika exfiltrace dat?**

Chcete-li zmírnit rizika exfiltrace dat, doporučujeme zákazníkům použít sadu nastavení zabezpečení a ovládacích prvků:

- Zapněte [transparentní šifrování dat (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) ve všech databázích.
- Vypněte čas CLR (Common Language Runtime). Doporučujeme to i v místním prostředí.
- Používejte jenom ověřování azure active directory (AAD).
- Přístup k instanci s nízkými privilegovanými účtem DBA.
- Konfigurace JiT jumpbox přístup pro účet sysadmin.
- Zapněte [auditování SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)a integrujte ho s mechanismy výstrah.
- Zapněte [detekci hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) ze sady [Advanced Data Security (ADS).](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="cost-saving-use-cases"></a>Případy použití úspory nákladů

**Kde najdu případy použití a výsledné úspory nákladů se spravovanou instancí?**

Případové studie spravovaných instancí:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Všechny skripty](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Chcete-li získat lepší přehled o výhodách, nákladech a rizicích spojených s nasazením spravované instance Azure SQL Database, je tu také studie společnosti Forrester: [Celkový ekonomický dopad MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Aktualizace DNS 

**Mohu provést aktualizaci DNS?**

V současné době neposkytujeme funkci pro aktualizaci konfigurace serveru DNS pro spravovanou instanci.

Konfigurace DNS je nakonec aktualizována:

- Po vypršení platnosti zapůjčení služby DHCP.
- Na platformě upgrade.

Jako řešení downgrade spravované instance 4 virtuální jádro a upgrade znovu později. To má vedlejší účinek aktualizace konfigurace DNS.


## <a name="ip-address"></a>IP adresa

**Mohu se ke spravované instanci připojit pomocí IP adresy?**

Připojení ke spravované instanci pomocí adresy IP není podporováno. Názvy hostitelů spravované instance se mapují na nástroj pro vyrovnávání zatížení před virtuálním clusterem spravované instance. Vzhledem k tomu, že jeden virtuální cluster může hostovat více spravovaných instancí připojení nelze směrovat na vlastní spravovanou instanci bez zadání jeho názvu.

Další informace o architektuře virtuálního clusteru spravované instance naleznete v [tématu Architektura připojení virtuálního clusteru](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture).

**Může mít spravovaná instance statickou IP adresu?**

Ve výjimečných, ale nezbytných situacích možná budeme muset provést online migraci spravované instance do nového virtuálního clusteru. V případě potřeby je tato migrace z důvodu změn v našem zásobníku technologií, jejichž cílem je zlepšit zabezpečení a spolehlivost služby. Migrace do nového virtuálního clusteru má za následek změnu adresy IP, která je mapována na název hostitele spravované instance. Služba spravované instance si nenárokuje podporu statické IP adresy a vyhrazuje si právo ji změnit bez předchozího upozornění jako součást pravidelných cyklů údržby.

Z tohoto důvodu důrazně nedoporučujeme spoléhat na neměnnost ip adresy, protože by to mohlo způsobit zbytečné prostoje.

## <a name="change-time-zone"></a>Změna časového pásma

**Je možné změnit časové pásmo pro existující spravovanou instanci?**

Konfiguraci časového pásma lze nastavit při prvním zřízení spravované instance. Změna časového pásma existující spravované instance není podporována. Podrobnosti naleznete v tématu [omezení časového pásma](sql-database-managed-instance-timezone.md#limitations).

Mezi zástupná řešení patří vytvoření nové spravované instance se správným časovým pásmem a potom provést ruční zálohování a obnovení nebo to, co doporučujeme, provést [obnovení bodu v čase mezi instancemi](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Řešení problémů s výkonem

**Jak vyřeším problémy s výkonem s pravosanou instancí?**

Pro porovnání výkonu mezi spravovanou instancí a SQL Server, dobrým výchozím bodem je [doporučené postupy pro porovnání výkonu mezi azure SQL spravované instance a SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) článku.

Načítání dat je často pomalejší na spravované instanci než v SQL Server z důvodu povinné úplné model obnovení a [omezení](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) propustnosti zápisu transakční protokol. V některých proto lze pracovat kolem načtením přechodná data do databáze uživatele namísto databáze uživatele nebo pomocí clusterované columnstore nebo tabulky optimalizované pro paměť.


## <a name="restore-encrypted-backup"></a>Obnovení šifrované zálohy

**Je možné obnovit šifrovanou databázi do spravované instance?**

Ano, není nutné dešifrovat databázi, abyste ji mohli obnovit do spravované instance. Je třeba zadat certifikát nebo klíč používaný jako ochrana šifrovacího klíče ve zdrojovém systému spravované instanci, abyste mohli číst data ze šifrovaného záložního souboru. Existují dva možné způsoby, jak to udělat:

- *Nahrajte ochranu certifikátu do spravované instance*. To lze provést pouze pomocí prostředí PowerShell. [Ukázkový skript](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) popisuje celý proces.
- *Nahrajte asymetrickou ochranu klíčů do trezoru klíčů Azure (AKV) a nakontou instanci spravovanou*bodem . Tento přístup se podobá bring-your-own-key (BYOK) TDE případ použití, který také používá integraci AKV k uložení šifrovacího klíče. Pokud nechcete použít klíč jako ochranu šifrovacího klíče a chcete pouze zpřístupnit klíč pro spravovanou instanci k obnovení šifrované databáze, postupujte podle pokynů pro [nastavení BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)a nezaškrtávejte políčko: *Zadejte Nastavit požadovaný klíč jako výchozí ochranu TDE*.

Jakmile zpřístupníte ochranu šifrování spravované instanci, můžete pokračovat standardním postupem obnovení databáze.

## <a name="migrate-from-single-db"></a>Migrace z jednoho DB 

**Jak můžu migrovat z jednoho nebo elastického fondu Azure SQL Database do spravované instance?**

Spravovaná instance nabízí stejné úrovně výkonu na výpočetní prostředky a velikost úložiště jako ostatní možnosti nasazení azure SQL database. Pokud chcete konsolidovat data na jednu instanci, nebo stačí funkce podporované výhradně ve spravované instanci, můžete migrovat data pomocí funkce exportu nebo importu (BACPAC).
