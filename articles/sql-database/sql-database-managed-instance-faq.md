---
title: SQL Database managed instance – nejčastější dotazy | Dokumentace Microsoftu
description: Spravovaná instance SQL Database – nejčastější dotazy (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/08/2019
ms.openlocfilehash: c3a070eb7e1435055b47b39985cf8cb0b182a514
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798067"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Spravovaná instance SQL Database – nejčastější dotazy (FAQ)

Tento článek obsahuje řadu nejčastější dotazy o [SQL Database managed instance](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>Kde najdete seznam funkcí, které jsou podporované ve spravované instanci?

Seznam podporovaných funkcí ve spravované instanci najdete v tématu [Azure SQL Database a SQL Server](sql-database-features.md).

Rozdíly v syntaxi a chování Azure SQL Database managed instance a místní SQL Server, naleznete v tématu [rozdíly T-SQL z SQL serveru](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Kde najdu technické vlastnosti a omezení prostředků pro spravovanou instanci?

Vlastnosti generace hardwaru k dispozici, najdete v části [technické rozdíly v generacemi hardwaru](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Dostupné úrovně a jejich vlastností najdete v tématu [technické rozdíly mezi úrovněmi služeb](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Kde najdu známé problémy a chyby?

Chyby a známé problémy najdete v tématu [změny chování](sql-database-managed-instance-transact-sql-information.md#Changes) a [známé problémy](sql-database-managed-instance-transact-sql-information.md#Issues).


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Spravovaná instance může mít stejný název jako místní SQL Server?

Spravovaná instance musí mít název, který končí *database.windows.net*. K používání jiné zóny DNS místo výchozího, například **mi jiný název**. contoso.com: 
- Můžete definovat jako alias CliConfig (Tento nástroj je jen sjednocující nastavení registru, takže to také můžete provést pomocí skriptu nebo zásady skupiny).
- Použití *CNAME* s *TrustServerCertificate = true* možnost.


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Jak můžu přesunout databáze z spravovanou instanci zpět na SQL Server nebo Azure SQL Database?

Je možné [export databáze do souboru bacpac](sql-database-export.md) a potom [import souboru bacpac]( sql-database-import.md). Tento přístup se doporučuje, pokud vaše databáze je menší než 100 GB.

Transakční replikace lze použít, pokud primární klíče mají všechny tabulky v databázi.

Nativní `COPY_ONLY` zálohy pořízené ze spravované instance není možné obnovit do systému SQL Server, protože managed instance má vyšší verzi databáze SQL serveru.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Jak můžu migrovat instance databáze do izolované databáze SQL Azure?

Jednou z možností je [export databáze do souboru bacpac](sql-database-export.md) a potom [import souboru bacpac]( sql-database-import.md). 

Toto je doporučený postup, pokud vaše databáze je menší než 100 GB. Transakční replikace lze použít, pokud primární klíče mají všechny tabulky v databázi.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Jak můžu vybrat mezi Gen 4 a 5 obecné generace hardwaru pro spravovanou instanci?

To závisí na velikosti pracovní zátěže jako některé generace hardwaru je lepší pro některé typy úloh než ten druhý. Předmět výkonu je spíše komplexní pro zjednodušení, následující rozdíly mezi generacemi hardwaru, které mají vliv na výkon úloh:
- Gen 4 poskytuje lepší podporu výpočetní prostředky, protože je založena na fyzických procesorů, oproti generace 5, který je založen na vCore procesory. To může být výhodnější pro úlohy náročné na výpočetní výkon.
- Generace 5 podporuje urychlení sítí výsledkem lepší šířku pásma vstupně-výstupní operace do vzdáleného úložiště. To může být výhodné pro úlohy náročné na vstupně-výstupních operací na úrovních služeb pro obecné účely. Generace 5 využívá rychlejší místních disků SSD, která je ve srovnání s Gen 4. To může být výhodné pro úlohy náročné na vstupně-výstupní operace v kritické obchodní úrovních.

Zákazníkům doporučujeme otestovat výkon skutečné úlohy, které jsou určené pro produkční prostředí před přechodem do ostrého provozu k určení hardwaru generování fungují lépe ve vašem případě.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Můžete přepínat Moje spravovanou instanci generace hardwaru Gen 4 až 5 obecné online? 

Automatizované online přepínání mezi generacemi hardwaru je možné, pokud jsou k dispozici obě generace hardwaru ve stejné oblasti, kde je zřízení spravované instance. V takovém případě máte možnost v části cenová úroveň na portálu Azure Portal přepínat mezi generacemi hardwaru.

Toto je dlouho spuštěné operace jako nové spravované instance se zřídí v back-end a databáze automaticky převodu mezi původní a nové instance. Tento proces bude bezproblémové pro zákazníky.

Pokud obě generacemi hardwaru nejsou podporované ve stejné oblasti, změna generace hardwaru je možné, ale musí provádět ručně. To vyžaduje, abyste zřizovat nové instance v oblasti, kde je k dispozici, generování požadovaný hardware a ruční zálohování a obnovení dat mezi původní a nové instance.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Jak můžu Optimalizujte výkon své spravované instance? 

Obecné účely mi používá vzdálené úložiště, kvůli které velikost dat a souborů protokolu záleží na výkonu. Pokud chcete optimalizovat výkon vrstvy služby obecné účely, postupujte podle pokynů v tomto blogovém příspěvku.

Pro úlohy náročné na v/v zvažte použití generace 5 hardwaru, s použitím Gen 4 pro úlohy náročné na výpočetní výkon. Další informace najdete v oddílu Nejčastější dotazy na nutností volby mezi generacemi hardwaru.

Pokud vaše úloha obsahuje velké množství malých transakce, zvažte typ připojení z proxy serveru na režim přesměrování.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Co je maximální velikost úložiště pro spravovanou instanci? 

Velikost úložiště pro spravovanou instanci závisí na úrovni vybrané službě (obecné účely nebo pro důležité obchodní informace). Omezení úložiště o těchto úrovních služeb, naleznete v tématu [charakteristiku úroveň služby](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>Je záložní úložiště odečte od úložiště spravované instance? 

Ne, úložiště záloh se odečte od vašeho prostoru úložiště spravované instance. Záložní úložiště je nezávislá z prostoru instancí úložiště a není omezenou velikost. Úložiště zálohování je omezená doba uchování zálohy databáze instance, konfigurovatelné z 7 pro po dobu 35 dní. Podrobnosti najdete v tématu [automatické zálohování](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Jak můžu nastavit příchozí pravidla skupiny zabezpečení sítě na porty pro správu?

Funkce integrované firewall nakonfiguruje bránu Windows firewall u všech virtuálních počítačů v clusteru a povolí příchozí připojení z rozsahů IP adres přidružený jenom k Microsoft nasazení a správu počítačů a zabezpečených pracovních stanic správců efektivně brání vniknutí prostřednictvím síťové vrstvy.

Zde je, jaké porty se používají pro:

9000 a 9003 porty se používají v infrastruktuře Service Fabric. Primární role Service Fabric je zachovat virtuální cluster v pořádku a zachovávají stav cíle z hlediska počtu replik komponenty.

Porty 1438, 1440 a 1452 bude používat Agent uzlu. Agenta uzlu je aplikace, která běží v rámci clusteru a se používají v rovině řízení provádět příkazy pro správu.

Kromě předdefinovaných brány firewall na síťové vrstvě komunikace je chráněná také pomocí certifikátů.
  
Další informace a postup pro ověření integrované brány firewall najdete v tématu [Azure SQL Database managed instance integrované firewall](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Jak můžete zmírnit rizika sítě? 

Ke zmírnění nebezpečí sítě, zákazníkům se doporučuje použít sadu nastavení zabezpečení a ovládacích prvků:

- Zapněte všechny databáze na transparentní šifrování dat (TDE).
- Zapněte vypnout Common Language Runtime (CLR). Tato možnost se doporučuje na i místně.
- Používejte pouze účty Azure AD.
- Přístup SQL MI s DBA účtu s nízkými oprávněními.
- Konfigurace přístupu jumpbox JiT pro účet správce systému.
- Zapnutí auditování SQL a integrovat ji s mechanismy výstrah.
- Zapnutí detekce hrozeb z ATS sady.


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Kde najdu případy použití a výsledné úspory nákladů, které se spravovaná instance?

Případové studie pro spravovanou instanci:

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Pokud chcete získat lepší pochopení výhod, nákladů a rizik spojených s nasazením spravované instance Azure SQL Database, k dispozici je také studie společnosti Forrester: [Celkový ekonomický dopad služeb MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>Můžete provést aktualizace DNS? 
  
V současné době neposkytujeme funkce Aktualizovat konfiguraci serveru DNS pro spravovanou instanci.

Nakonec se aktualizuje konfiguraci serveru DNS:

- Vypršení platnosti zapůjčení ze serveru DHCP.
- Při upgradu platformy.

Jako alternativní řešení downgradovat spravovanou instanci na 4 vCore a upgradovat později znovu. Tato akce nemá vedlejším účinkem aktualizuje konfiguraci serveru DNS.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Spravovaná instance může mít statickou IP adresu?

V situacích, ale zřídka nezbytné jsme možná bude potřeba provést online migrace do spravované instance do nového virtuálního clusteru. V případě potřeby, tato migrace je z důvodu změn v naší technologiích zaměřených na zvýšení zabezpečení a spolehlivost služby. Migrace na nové virtuální cluster výsledky při změně IP adresu, která se mapuje na název hostitele spravované instance. Služba spravované instance není statická IP adresa podporu deklarací identity a si vyhrazuje právo změnit bez předchozího upozornění v rámci pravidelnou údržbu cykly.

Z tohoto důvodu důrazně Zabraňte spoléhat na neměnnosti IP adresy, protože to může způsobit zbytečné prostoje.


## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Můžete změnit časové pásmo pro existující spravovanou instanci?

Časové pásmo konfigurace můžete nastavit při zřízení spravované instance poprvé. Změna časového pásma existující spravované instance není podporován. Podrobnosti najdete v tématu [omezení časové pásmo](sql-database-managed-instance-timezone.md#limitations).

Alternativní řešení zahrnují vytvoření nové spravované instance pomocí správné časové pásmo a potom buď proveďte ruční zálohování a obnovení, nebo co doporučujeme provést [obnovení bodu v čase mezi instance](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Jak vyřešit problémy s výkonem se Moje spravované instance

Porovnání výkonu mezi spravované instance a SQL Server, je dobrým výchozím bodem [osvědčené postupy pro porovnání výkonu Azure SQL managed instance a SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Načtení dat jsou často pomalejší na spravované instanci než v systému SQL Server z důvodu povinné úplný model obnovení a [omezení](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) na propustnost zápisu protokolu transakcí. V některých případech to může fungovalo kolem načítání přechodné dat do databáze tempdb místo uživatelskou databázi, nebo pomocí clusterovaného indexu columnstore nebo paměťově optimalizovaných tabulkách.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Můžete obnovit šifrované databázi do spravované instance

Ano, nepotřebujete k dešifrování databáze chcete mít možnost obnovit do spravované instance. Je nutné zadat certifikát/klíč použít jako ochrana pomocí klíče šifrování ve zdrojovém systému do spravované instance bude moct číst data z šifrovaný záložní soubor. Existují dva možné způsoby, jak to udělat:

- Nahrajte certifikát protector do spravované instance. To lze provést pouze pomocí prostředí PowerShell. Ukázkový skript popisuje celý proces.
- Ochrana pomocí asymetrického klíče nahrát do Azure Key Vaultu (AKV) a na něj odkažte spravované instance. Tento přístup se podobá případu použití TDE (BYOK) přenést your vlastní key, který také používá integrace se službou AZURE k uložení šifrovacího klíče. Pokud chcete klíč nahraje do službou AZURE k dispozici pro spravovanou instanci pro obnovení šifrovaných databází bez jejího použití klíče jako ochrana pomocí klíče šifrování, postupujte podle pokynů pro nastavení transparentní šifrování dat BYOK a není zaškrtněte políčko zkontrolujte vybraný klíč Výchozí ochranu šifrováním TDE.

Po provedení šifrování ochrany k dispozici pro spravovanou instanci, abyste mohli pokračovat postup obnovení databáze standard.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Jak můžu migrovat z fondu jediné nebo elastické databáze SQL Azure do spravované instance? 

Managed instance nabízí stejnou úrovní výkonu za výpočetní výkon a úložiště velikost jako další možnosti nasazení služby Azure SQL Database. Pokud chcete sloučit data na jednu instanci nebo stačí jednoduše funkce podporované výhradně ve spravované instanci, můžete migrovat data pomocí funkce exportu/importu (BACPAC).
