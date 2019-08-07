---
title: Nejčastější dotazy týkající se spravované instance SQL Database | Microsoft Docs
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
ms.openlocfilehash: 3637676a330b324d5620885f0cbe50d4aa68ed51
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779049"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Nejčastější dotazy k SQL Database Managed Instances (FAQ)

Tento článek obsahuje mnoho nejběžnějších otázek týkajících se [SQL Database spravované instance](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-supported-on-managed-instance"></a>Kde najdu seznam funkcí, které jsou podporovány ve spravované instanci?

Seznam podporovaných funkcí ve spravované instanci najdete v tématu [Azure SQL Database versus SQL Server](sql-database-features.md).

Rozdíly v syntaxi a chování mezi Azure SQL Database spravované instance a místním SQL Server naleznete v tématu rozdíly v [jazyce T-SQL od SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Kde můžu najít technické charakteristiky a omezení prostředků pro spravovanou instanci?

Dostupné charakteristiky vytváření hardwaru najdete v tématu [technické rozdíly v segenerováních hardwaru](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Dostupné úrovně služeb a jejich charakteristiky najdete v tématu [technické rozdíly mezi úrovněmi služeb](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Kde můžu najít známé problémy a chyby?

Chyby a známé problémy najdete v tématu [změny chování](sql-database-managed-instance-transact-sql-information.md#Changes) a [známé problémy](sql-database-managed-instance-transact-sql-information.md#Issues).


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Může mít spravovaná instance stejný název jako místní SQL Server?

Spravovaná instance musí mít název, který končí na *Database.Windows.NET*. Chcete-li použít jinou zónu DNS namísto výchozího, například **mi – jiný název**. contoso.com: 
- Použijte CliConfig k definování aliasu. Tento nástroj je jenom obálkou nastavení registru, takže ho můžete udělat taky pomocí zásad skupiny nebo skriptu.
- Použijte *CNAME* s možností *TrustServerCertificate = true* .


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Jak můžu přesunout databázi ze spravované instance zpátky do SQL Server nebo Azure SQL Database?

Můžete [exportovat databázi do BacPac](sql-database-export.md) a pak [importovat soubor BacPac]( sql-database-import.md). To je doporučený postup, pokud je databáze menší než 100 GB.

Transakční replikaci je možné použít, pokud všechny tabulky v databázi mají primární klíče.

Nativní `COPY_ONLY` zálohy pořízené ze spravované instance nelze obnovit do SQL Server, protože spravovaná instance má vyšší verzi databáze v porovnání s SQL Server.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Jak můžu migrovat databázi instance do jediného Azure SQL Database?

Jednou z možností je [exportovat databázi do BacPac](sql-database-export.md) a pak [importovat soubor BacPac]( sql-database-import.md). 

Toto je doporučený postup, pokud je databáze menší než 100 GB. Transakční replikaci je možné použít, pokud všechny tabulky v databázi mají primární klíče.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Návody zvolit mezi generováním hardwaru Gen 4 a gen 5 pro spravovanou instanci?

Závisí na vašich úlohách, protože některé generace hardwaru jsou pro určité typy úloh lepší než druhá. I když je předmět výkonu složitý a zjednodušuje se tak tyto rozdíly mezi generacemi hardwaru, které ovlivňují výkon úloh:
- Gen 4 poskytuje lepší výpočetní podporu, protože je založená na fyzických procesorech, vs. Gen 5 založené na vCore procesorech. Může být výhodnější pro úlohy náročné na výpočetní výkon.
- Gen 5 podporuje urychlené síťové služby, což má za následek lepší šířku pásma v/v pro vzdálené úložiště. Může být výhodné pro úlohy náročné na vstupně-výstupní operace na Pro obecné účely úrovní služeb. Gen 5 používá rychlejší místní disky SSD v porovnání s Gen 4. Může být výhodné pro úlohy náročné na vstupně-výstupní operace pro důležité podnikové úrovně služeb.

Důrazně doporučujeme, abyste před živým testováním výkonu skutečných úloh určených pro produkci vyzkoušeli, která generace hardwaru bude v konkrétním případě lépe fungovat.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Můžu přepínat generování hardwaru spravované instance mezi Gen 4 a 1.5 online? 

Automatizované online přepínání mezi generacemi hardwaru je možné, pokud jsou hardwarové generace dostupné v oblasti, kde je spravovaná vaše spravovaná instance zřízena. V tomto případě máte možnost v části s cenovou úrovní Azure Portal pro přepínání mezi generacemi hardwaru.

Tato operace je dlouhotrvající, protože nová spravovaná instance se zřídí na pozadí a databáze automaticky přenesené mezi starou a novou instancí s rychlým převzetím služeb při selhání na konci procesu. 

Pokud se ve stejné oblasti nepodporují hardwarové generace, změna hardwarového generování je možná, ale je nutné ji provést ručně. To vyžaduje zřízení nové instance v oblasti, kde je k dispozici potřebná generace hardwaru, a ruční zálohování a obnovení dat mezi starou a novou instancí.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Návody vyladit výkon mé spravované instance? 

Pro obecné účely spravovaná instance používá vzdálené úložiště, protože se jedná o velikost dat a souborů protokolu, které jsou důležité pro výkon. Další informace najdete v tématu [vliv velikosti souboru protokolu na výkon pro obecné účely spravované instance](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Pro úlohy náročné na vstupně-výstupní operace zvažte použití hardwaru Gen 5 a používejte Gen 4 pro úlohy náročné na výpočetní výkon. Další informace najdete v tématu [návody výběru mezi fin 4 a gen 5](#how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance).

Pokud se vaše zatížení skládá z velkého množství malých transakcí, zvažte možnost přepnout typ připojení ze proxy serveru do režimu přesměrování.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Jaká je maximální velikost úložiště pro spravovanou instanci? 

Velikost úložiště pro spravovanou instanci závisí na vybrané úrovni služby (Pro obecné účely nebo Pro důležité obchodní informace). Omezení úložiště těchto úrovní služeb najdete v tématu věnovaném [vlastnostem úrovně služby](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>Je úložiště pro zálohování odečteno od úložiště spravované instance? 

Ne, úložiště zálohování se neodečte z prostoru úložiště spravované instance. Úložiště zálohování je nezávislé na rozsahu úložiště instance a velikost není omezená. Úložiště zálohování je omezené časovým obdobím, aby se zachovalo zálohování databází instancí, a to po dobu 7 až 35 dnů. Podrobnosti najdete v tématu [automatizované zálohování](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Jak můžu nastavit příchozí NSG pravidla pro porty pro správu?

Integrovaná funkce brány firewall nakonfiguruje bránu Windows Firewall na všech virtuálních počítačích v clusteru, aby umožňovala příchozí připojení z rozsahů IP adres přidružených pouze k počítačům pro správu/nasazení a zabezpečeným pracovním stanicím pro správu, které účinně zabraňují. neoprávněná vniknutí přes síťovou vrstvu.

Zde je uvedeno, jaké porty se používají pro:

Porty 9000 a 9003 používá Service Fabric infrastruktura. Service Fabric primární role je zachovat dobrý stav virtuálního clusteru a zachovat stav cíle z hlediska počtu replik komponent.

Agent Node používá porty 1438, 1440 a 1452. Agent uzlu je aplikace, která běží v clusteru a je používána rovinou ovládacího prvku k provádění příkazů pro správu.

Kromě integrované brány firewall na síťové vrstvě je komunikace také chráněná pomocí certifikátů.
  
Další informace a ověření integrované brány firewall najdete v tématu [integrovaná brána firewall Azure SQL Database spravované instance](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Jak můžu zmírnit síťová rizika? 

Pro zmírnění rizik sítě se doporučuje zákazníkům použít sadu nastavení zabezpečení a ovládacích prvků:

- Zapněte [transparentní šifrování dat (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) na všech databázích.
- Vypnout modul CLR (Common Language Runtime). To se doporučuje i v místním prostředí.
- Použijte pouze ověřování Azure Active Directory (AAD).
- Instance přístupu s nízkým privilegovaným účtem DBA
- Nakonfigurujte přístup JiT JumpBox pro účet sysadmin.
- Zapněte [auditování SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)a integrujte je pomocí mechanismů upozorňování.
- Zapněte [detekci hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) ze sady [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Kde najdu případy použití a výsledné úspory nákladů se spravovanou instancí?

Případové studie spravované instance:

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Pro lepší porozumění výhodám, nákladům a rizikům spojeným s nasazením Azure SQL Database spravované instance je také studie Forrester: [Celkový ekonomický dopad na mi](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>Můžu aktualizovat DNS? 
  
V současné době neposkytujeme funkci pro aktualizaci konfigurace serveru DNS pro spravovanou instanci.

Konfigurace DNS se nakonec aktualizuje:

- Když vyprší platnost zapůjčení DHCP.
- Při upgradu platformy.

Jako alternativní řešení nadowngradujte spravovanou instanci na 4 vCore a potom ji upgradujte znovu. To má vedlejší účinky aktualizace konfigurace DNS.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Má spravovaná instance statickou IP adresu?

Ve výjimečných, ale nezbytných situacích může být nutné provést online migraci spravované instance do nového virtuálního clusteru. V případě potřeby se tato migrace provádí kvůli změnám v naší technologické sadě zaměřené na zvýšení zabezpečení a spolehlivosti služby. Výsledkem migrace na nový virtuální cluster je změna IP adresy, která je namapovaná na název hostitele spravované instance. Služba Managed instance nedeklaruje podporu statických IP adres a vyhrazuje právo ji změnit bez upozornění jako součást běžných cyklů údržby.

Z tohoto důvodu se důrazně nedoporučuje spoléhat na neměnnosti IP adresy, protože by mohlo dojít k zbytečnému výpadku.

## <a name="can-i-move-a-managed-instance-or-its-vnet-to-another-resource-group"></a>Můžu přesunout spravovanou instanci nebo její virtuální síť do jiné skupiny prostředků?

Ne, toto je aktuální omezení platformy. Po vytvoření spravované instance se nepodporují přesunutí spravované instance nebo virtuální sítě do jiné skupiny prostředků nebo předplatného.

## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Můžu změnit časové pásmo existující spravované instance?

Konfiguraci časového pásma lze nastavit při prvním zřízení spravované instance. Změna časového pásma existující spravované instance se nepodporuje. Podrobnosti najdete v tématu [omezení časového pásma](sql-database-managed-instance-timezone.md#limitations).

Alternativní řešení zahrnují vytvoření nové spravované instance se správným časovým pásmem a následné provedení ručního zálohování a obnovení, nebo to, co doporučujeme, provedení [obnovení mezi instancemi v čase](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Návody vyřešit problémy s výkonem pomocí mé spravované instance

Pro porovnání výkonu mezi spravovanou instancí a SQL Server je dobrým výchozím bodem osvědčené [postupy pro porovnání výkonu mezi spravovanou instancí Azure SQL a SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) článkem.

Načítání dat je často pomalejší na spravované instanci než v SQL Server z důvodu povinného modelu úplného obnovení a [omezení](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) propustnosti zápisu protokolu transakcí. V některých případech to může být způsobeno tím, že se v databázi tempdb načítají přechodná data namísto uživatelské databáze nebo použije clusterované tabulky columnstore nebo paměťově optimalizované tabulky.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Je možné obnovit zašifrovanou databázi do spravované instance?

Ano, nemusíte dešifrovat databázi, abyste ji mohli obnovit do spravované instance. Abyste mohli číst data ze šifrovaného záložního souboru, musíte do spravované instance zadat certifikát nebo klíč, který se používá jako ochrana šifrovacího klíče ve zdrojovém systému. Existují dva možné způsoby, jak to provést:

- *Nahrajte do spravované instance ochranu pomocí certifikátu*. Můžete to udělat jenom pomocí PowerShellu. [Vzorový skript](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) popisuje celý proces.
- *Nahrání asymetrického klíče-ochrany do Azure Key Vault (integrace) a nasměrování na něj na spravované instance*. Tento přístup se podobá BYOKm případu použití TDE, který taky používá integraci integrace k uložení šifrovacího klíče. Pokud nechcete používat klíč jako ochranu šifrovacího klíče a chcete jenom zpřístupnit klíč pro spravovanou instanci pro obnovení šifrovaných databází, postupujte podle pokynů pro [Nastavení BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption-in-the-azure-portal)a nezaškrtněte políčko *nastavit vybraný klíč. výchozí ochrana TDE*

Jakmile zpřístupníte ochranu šifrování pro spravovanou instanci, můžete pokračovat v postupu standardního obnovení databáze.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Jak se dá migrovat z Azure SQL Database jednoho nebo elastického fondu do spravované instance? 

Managed instance nabízí stejné úrovně výkonu na výpočetní úrovni a velikosti úložiště jako jiné možnosti nasazení Azure SQL Database. Pokud chcete konsolidovat data na jednu instanci nebo jednoduše potřebujete funkci podporovanou výhradně ve spravované instanci, můžete data migrovat pomocí funkce Export/Import (BACPAC).
