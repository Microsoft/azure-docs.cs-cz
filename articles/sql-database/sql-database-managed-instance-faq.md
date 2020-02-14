---
title: Nejčastější dotazy ke spravované instanci
description: Nejčastější dotazy k SQL Database Managed Instances (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 1c1995b4daf3b76abf7663d8d6c1f4cb7b1d6e2b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201675"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Nejčastější dotazy k SQL Database Managed Instances (FAQ)

Tento článek obsahuje mnoho nejběžnějších otázek týkajících se [SQL Database spravované instance](sql-database-managed-instance.md).

## <a name="supported-features"></a>Podporované funkce

**Kde najdu seznam funkcí, které jsou podporovány ve spravované instanci?**

Seznam podporovaných funkcí ve spravované instanci najdete v tématu [Azure SQL Database versus SQL Server](sql-database-features.md).

Rozdíly v syntaxi a chování mezi Azure SQL Database spravované instance a místním SQL Server naleznete v tématu rozdíly v [jazyce T-SQL od SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Technické specifikace & omezení prostředků
 
**Kde můžu najít technické charakteristiky a omezení prostředků pro spravovanou instanci?**

Dostupné charakteristiky vytváření hardwaru najdete v tématu [technické rozdíly v segenerováních hardwaru](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Dostupné úrovně služeb a jejich charakteristiky najdete v tématu [technické rozdíly mezi úrovněmi služeb](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Známé problémy & chybách

**Kde můžu najít známé problémy a chyby?**

Chyby a známé problémy najdete v tématu [známé problémy](sql-database-managed-instance-transact-sql-information.md#Issues).

## <a name="new-features"></a>Nové funkce

**Kde můžu najít nejnovější funkce a funkce ve verzi Public Preview?**

Nové funkce a verze Preview najdete v [poznámkách k verzi](/azure/sql-database/sql-database-release-notes?tabs=managed-instance).

## <a name="deployment-times"></a>Časy nasazení 

**Jak dlouho trvá vytvoření nebo aktualizace instance nebo obnovení databáze?**

Očekávaná doba pro vytvoření nové spravované instance nebo změna úrovně služby (virtuální jádra, Storage) závisí na několika faktorech. Prohlédněte si [operace správy](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Konvence pojmenování

**Může mít spravovaná instance stejný název jako místní SQL Server?**

Spravovaná instance musí mít název, který končí na *Database.Windows.NET*. Chcete-li použít jinou zónu DNS namísto výchozího, například **mi – jiný název**. contoso.com: 
- Použijte CliConfig k definování aliasu. Tento nástroj je jenom obálkou nastavení registru, takže ho můžete udělat taky pomocí zásad skupiny nebo skriptu.
- Použijte *CNAME* s možností *TrustServerCertificate = true* .

## <a name="move-db-from-mi"></a>Přesunout databázi ze MI 

**Jak můžu přesunout databázi ze spravované instance zpátky do SQL Server nebo Azure SQL Database?**

Můžete [exportovat databázi do BacPac](sql-database-export.md) a pak [importovat soubor BacPac]( sql-database-import.md). To je doporučený postup, pokud je databáze menší než 100 GB.

Transakční replikaci je možné použít, pokud všechny tabulky v databázi mají primární klíče.

Nativní `COPY_ONLY` zálohy pořízené ze spravované instance nelze obnovit do SQL Server, protože spravovaná instance má vyšší verzi databáze v porovnání s SQL Server.

## <a name="migrate-instance-db"></a>Migrace instance DB

**Jak můžu migrovat databázi instance do jediného Azure SQL Database?**

Jednou z možností je [exportovat databázi do BacPac](sql-database-export.md) a pak [importovat soubor BacPac](sql-database-import.md). 

Toto je doporučený postup, pokud je databáze menší než 100 GB. Transakční replikaci je možné použít, pokud všechny tabulky v databázi mají primární klíče.

## <a name="switch-hardware-generation"></a>Přepnout generování hardwaru 

**Můžu přepínat generování hardwaru spravované instance mezi Gen 4 a 1.5 online?**

Automatizované online přepínání mezi generacemi hardwaru je možné, pokud jsou hardwarové generace dostupné v oblasti, kde je spravovaná vaše spravovaná instance zřízena. V takovém případě si můžete prohlédnout [stránku Přehled modelu Vcore](sql-database-service-tiers-vcore.md) vysvětlující, jak přepínat mezi generováním hardwaru.

Tato operace je dlouhotrvající, protože nová spravovaná instance se zřídí na pozadí a databáze automaticky přenesené mezi starou a novou instancí s rychlým převzetím služeb při selhání na konci procesu. 

Pokud se ve stejné oblasti nepodporují hardwarové generace, změna hardwarového generování je možná, ale je nutné ji provést ručně. To vyžaduje zřízení nové instance v oblasti, kde je k dispozici potřebná generace hardwaru, a ruční zálohování a obnovení dat mezi starou a novou instancí.


## <a name="tune-performance"></a>Ladění výkonu

**Návody vyladit výkon mé spravované instance?**

Pro obecné účely spravovaná instance používá vzdálené úložiště, protože se jedná o velikost dat a souborů protokolu, které jsou důležité pro výkon. Další informace najdete v tématu [vliv velikosti souboru protokolu na výkon pro obecné účely spravované instance](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Pokud se vaše zatížení skládá z velkého množství malých transakcí, zvažte možnost přepnout typ připojení ze proxy serveru do režimu přesměrování.

## <a name="maximum-storage-size"></a>Maximální velikost úložiště

**Jaká je maximální velikost úložiště pro spravovanou instanci?**

Velikost úložiště pro spravovanou instanci závisí na vybrané úrovni služby (Pro obecné účely nebo Pro důležité obchodní informace). Omezení úložiště těchto úrovní služeb najdete v tématu věnovaném [vlastnostem úrovně služby](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Náklady na úložiště záloh 

**Je úložiště pro zálohování odečteno od úložiště spravované instance?**

Ne, úložiště zálohování se neodečte z prostoru úložiště spravované instance. Úložiště zálohování je nezávislé na rozsahu úložiště instance a velikost není omezená. Úložiště zálohování je omezené časovým obdobím, aby se zachovalo zálohování databází instancí, a to po dobu 7 až 35 dnů. Podrobnosti najdete v tématu [automatizované zálohování](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Sledovat fakturaci

**Existuje způsob, jak sledovat fakturační náklady pro moji spravovanou instanci?**

Můžete to udělat pomocí [řešení Azure cost management](/azure/cost-management/). V [Azure Portal](https://portal.azure.com) přejděte na **předplatná** a vyberte **Analýza nákladů**. 

Použijte možnost **akumulované náklady** a pak filtrujte podle **typu prostředku** jako `microsoft.sql/managedinstances`. 
  
## <a name="inbound-nsg-rules"></a>Příchozí NSG pravidla

**Jak můžu nastavit příchozí NSG pravidla pro porty pro správu?**

Integrovaná funkce brány firewall nakonfiguruje bránu Windows Firewall na všech virtuálních počítačích v clusteru, aby umožňovala příchozí připojení z rozsahů IP adres přidružených pouze k počítačům Microsoft Management/Deployment a zabezpečeným pracovním stanicím pro správu, které zabraňují vniknutí prostřednictvím síťové vrstvy.

Zde je uvedeno, jaké porty se používají pro:

Porty 9000 a 9003 používá Service Fabric infrastruktura. Service Fabric primární role je zachovat dobrý stav virtuálního clusteru a zachovat stav cíle z hlediska počtu replik komponent.

Agent Node používá porty 1438, 1440 a 1452. Agent uzlu je aplikace, která běží v clusteru a je používána rovinou ovládacího prvku k provádění příkazů pro správu.

Kromě integrované brány firewall na síťové vrstvě je komunikace také chráněná pomocí certifikátů.
  
Další informace a ověření integrované brány firewall najdete v tématu [integrovaná brána firewall Azure SQL Database spravované instance](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-network-risks"></a>Zmírnění rizik sítě  

**Jak můžu zmírnit síťová rizika?**

Pro zmírnění rizik sítě se doporučuje zákazníkům použít sadu nastavení zabezpečení a ovládacích prvků:

- Zapněte [transparentní šifrování dat (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) na všech databázích.
- Vypnout modul CLR (Common Language Runtime). To se doporučuje i v místním prostředí.
- Použijte pouze ověřování Azure Active Directory (AAD).
- Instance přístupu s nízkým privilegovaným účtem DBA
- Nakonfigurujte přístup JiT JumpBox pro účet sysadmin.
- Zapněte [auditování SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)a integrujte je pomocí mechanismů upozorňování.
- Zapněte [detekci hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) ze sady [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Případy použití úspory nákladů

**Kde najdu případy použití a výsledné úspory nákladů se spravovanou instancí?**

Případové studie spravované instance:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Pro lepší porozumění výhodám, nákladům a rizikům spojeným s nasazením Azure SQL Database spravované instance je také studie Forrester: [celkový ekonomický dopad na mi](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Aktualizace DNS 

**Můžu aktualizovat DNS?**

V současné době neposkytujeme funkci pro aktualizaci konfigurace serveru DNS pro spravovanou instanci.

Konfigurace DNS se nakonec aktualizuje:

- Když vyprší platnost zapůjčení DHCP.
- Při upgradu platformy.

Jako alternativní řešení nadowngradujte spravovanou instanci na 4 vCore a potom ji upgradujte znovu. To má vedlejší účinky aktualizace konfigurace DNS.


## <a name="static-ip-address"></a>Statická IP adresa

**Má spravovaná instance statickou IP adresu?**

Ve výjimečných, ale nezbytných situacích může být nutné provést online migraci spravované instance do nového virtuálního clusteru. V případě potřeby se tato migrace provádí kvůli změnám v naší technologické sadě zaměřené na zvýšení zabezpečení a spolehlivosti služby. Výsledkem migrace na nový virtuální cluster je změna IP adresy, která je namapovaná na název hostitele spravované instance. Služba Managed instance nedeklaruje podporu statických IP adres a vyhrazuje právo ji změnit bez upozornění jako součást běžných cyklů údržby.

Z tohoto důvodu se důrazně nedoporučuje spoléhat na neměnnosti IP adresy, protože by mohlo dojít k zbytečnému výpadku.

## <a name="moving-mi"></a>Pohyb MI

**Můžu přesunout spravovanou instanci nebo její virtuální síť do jiné skupiny prostředků?**

Ne, toto je aktuální omezení platformy. Po vytvoření spravované instance se nepodporují přesunutí spravované instance nebo virtuální sítě do jiné skupiny prostředků nebo předplatného.

## <a name="change-time-zone"></a>Změnit časové pásmo

**Můžu změnit časové pásmo existující spravované instance?**

Konfiguraci časového pásma lze nastavit při prvním zřízení spravované instance. Změna časového pásma existující spravované instance se nepodporuje. Podrobnosti najdete v tématu [omezení časového pásma](sql-database-managed-instance-timezone.md#limitations).

Alternativní řešení zahrnují vytvoření nové spravované instance se správným časovým pásmem a následné provedení ručního zálohování a obnovení, nebo to, co doporučujeme, provedení [obnovení mezi instancemi v čase](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Řešení problémů s výkonem

**Návody vyřešit problémy s výkonem pomocí mé spravované instance?**

Pro porovnání výkonu mezi spravovanou instancí a SQL Server je dobrým výchozím bodem [osvědčené postupy pro porovnání výkonu mezi spravovanou instancí Azure SQL a SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) článkem.

Načítání dat je často pomalejší na spravované instanci než v SQL Server z důvodu povinného modelu úplného obnovení a [omezení](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) propustnosti zápisu protokolu transakcí. V některých případech to může být způsobeno tím, že se v databázi tempdb načítají přechodná data namísto uživatelské databáze nebo použije clusterované tabulky columnstore nebo paměťově optimalizované tabulky.


## <a name="restore-encrypted-backup"></a>Obnovit šifrované zálohování

**Je možné obnovit zašifrovanou databázi do spravované instance?**

Ano, nemusíte dešifrovat databázi, abyste ji mohli obnovit do spravované instance. Abyste mohli číst data ze šifrovaného záložního souboru, musíte do spravované instance zadat certifikát nebo klíč, který se používá jako ochrana šifrovacího klíče ve zdrojovém systému. Existují dva možné způsoby, jak to provést:

- *Nahrajte do spravované instance ochranu pomocí certifikátu*. Můžete to udělat jenom pomocí PowerShellu. [Vzorový skript](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) popisuje celý proces.
- *Nahrání asymetrického klíče-ochrany do Azure Key Vault (integrace) a nasměrování na něj na spravované instance*. Tento přístup se podobá BYOKm případu použití TDE, který taky používá integraci integrace k uložení šifrovacího klíče. Pokud nechcete používat klíč jako ochranu šifrovacího klíče a chcete jenom zpřístupnit klíč pro spravovanou instanci pro obnovení šifrovaných databází, postupujte podle pokynů pro [Nastavení BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)a zrušte zaškrtnutí políčka *nastavit vybraný klíč jako výchozí ochranu TDE*.

Jakmile zpřístupníte ochranu šifrování pro spravovanou instanci, můžete pokračovat v postupu standardního obnovení databáze.

## <a name="migrate-from-single-db"></a>Migrace z jedné databáze 

**Jak se dá migrovat z Azure SQL Database jednoho nebo elastického fondu do spravované instance?**

Managed instance nabízí stejné úrovně výkonu na výpočetní úrovni a velikosti úložiště jako jiné možnosti nasazení Azure SQL Database. Pokud chcete konsolidovat data na jednu instanci nebo jednoduše potřebujete funkci podporovanou výhradně ve spravované instanci, můžete data migrovat pomocí funkce Export/Import (BACPAC).
