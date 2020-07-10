---
title: Nejčastější dotazy
titleSuffix: Azure SQL Managed Instance
description: Nejčastější dotazy k Azure SQL Managed instance (FAQ)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: d2e4b07c97e09fce5cdaa034e2fe67a18ef0d7f1
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171155"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Nejčastější dotazy k Azure SQL Managed instance (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek obsahuje nejčastější dotazy týkající se [spravované instance Azure SQL](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Podporované funkce

**Kde najdu seznam funkcí, které jsou podporovány ve spravované instanci SQL?**

Seznam podporovaných funkcí ve spravované instanci SQL najdete v tématu [funkce spravované instance Azure SQL](../database/features-comparison.md).

Rozdíly v syntaxi a chování mezi spravovanou instancí Azure SQL a SQL Server najdete v tématu [rozdíly v T-SQL od SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Technická specifikace, omezení prostředků a další omezení
 
**Kde můžu najít technické charakteristiky a omezení prostředků pro spravovanou instanci SQL?**

Dostupné charakteristiky vytváření hardwaru najdete v tématu [technické rozdíly v segenerováních hardwaru](resource-limits.md#hardware-generation-characteristics).
Dostupné úrovně služeb a jejich charakteristiky najdete v tématu [technické rozdíly mezi úrovněmi služeb](resource-limits.md#service-tier-characteristics).

**Jakou úroveň služby mám nárok?**

Každý zákazník má nárok na libovolnou úroveň služby. Pokud ale chcete vyměňovat své stávající licence pro zvýhodněné sazby Azure SQL Managed instance pomocí [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), pamatujte na to, že zákazníci SQL Server Enterprise edice s programem Software Assurance mají nárok na úrovně výkonu [pro obecné účely](../database/service-tier-general-purpose.md) nebo [pro důležité obchodní informace](../database/service-tier-business-critical.md) a SQL Server Standard edice pro obecné účely Edition s programem software Assurance mají nárok jenom na úroveň výkonu. Další podrobnosti najdete v tématu [specifická práva pro AHB](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server).

**Jaké typy předplatného jsou podporovány pro spravovanou instanci SQL?**

Seznam podporovaných typů předplatných najdete v tématu [podporované typy předplatného](resource-limits.md#supported-subscription-types). 

**Které oblasti Azure jsou podporované?**

Spravované instance je možné vytvořit ve většině oblastí Azure. viz [podporované oblasti pro spravovanou instanci SQL](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Pokud potřebujete spravovanou instanci v oblasti, která není aktuálně podporovaná, [odešlete žádost o podporu prostřednictvím Azure Portal](../database/quota-increase-request.md).

**Existují nějaká omezení kvóty pro nasazení spravovaných instancí SQL?**

Spravovaná instance má dvě výchozí omezení: omezení počtu podsítí, které můžete použít, a omezení počtu virtuální jádra, které můžete zřídit. Omezení se liší v různých typech a oblastech předplatného. Seznam omezení místních prostředků podle typu předplatného najdete v tématu Tabulka z [omezení regionálního prostředku](resource-limits.md#regional-resource-limitations). Jedná se o tichá omezení, která se dají zvýšit na vyžádání. Pokud potřebujete zřídit ve svých současných oblastech více spravovaných instancí, odešlete žádost o podporu, která zvýší kvótu pomocí Azure Portal. Další informace najdete v tématu [zvýšení kvóty žádostí o Azure SQL Database](../database/quota-increase-request.md).

**Můžu na vyžádání zvýšit počet databází limit (100) na spravované instanci?**

Ne. v současné době nejsou k dispozici žádné potvrzené plány pro zvýšení počtu databází na spravované instanci SQL. 

**Kde můžu provést migraci, když mám více než 8TB dat?**
Můžete uvažovat o migraci na jiné typy Azure, které odpovídají vašim úlohám: [Azure SQL Database škálování](../database/service-tier-hyperscale.md) nebo [SQL Server na Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Kde můžu migrovat konkrétní požadavky na hardware, jako je větší paměť RAM pro vCore poměr nebo více procesorů?**
Můžete zvážit migraci na [SQL Server v Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) nebo [Azure SQL Database](../database/sql-database-paas-overview.md) paměti/procesoru optimalizované pro procesor.

## <a name="known-issues--bugs"></a>Známé problémy & chybách

**Kde můžu najít známé problémy a chyby?**

Chyby a známé problémy najdete v tématu [známé problémy](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Nové funkce

**Kde můžu najít nejnovější funkce a funkce ve verzi Public Preview?**

Nové funkce a verze Preview najdete v [poznámkách k verzi](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Vytvoření, aktualizace, odstranění nebo přesunutí spravované instance SQL

**Jak můžu zřídit spravovanou instanci SQL?**

Instanci můžete zřídit z webu [Azure Portal](instance-create-quickstart.md), [PowerShellu](scripts/create-configure-managed-instance-powershell.md), [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) a [šablon ARM](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates).

**Je možné zřídit spravované instance v existujícím předplatném?**

Ano, pokud toto předplatné patří k [podporovaným typům](resource-limits.md#supported-subscription-types)předplatného, můžete zřídit spravovanou instanci v existujícím předplatném.

**Proč se nám nepovedlo zřídit spravovanou instanci v podsíti, jejíž název začíná číslicí?**

Toto je aktuální omezení základní součásti, která ověřuje název podsítě proti regulárnímu výrazu ^ [a-za-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. Všechny názvy, které přecházejí regulárního výrazu a jsou platné názvy podsítí, jsou aktuálně podporovány.

**Jak můžu škálovat spravovanou instanci?**

Spravovanou instanci můžete škálovat z webu [Azure Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [PowerShellu](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-update) nebo [šablon ARM](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates).

**Můžu svoje spravované instance přesunout z jedné oblasti do druhé?**

Ano, můžete. Pokyny najdete v tématu [Přesunutí prostředků do různých oblastí](../database/move-resources-across-regions.md).

**Jak můžu odstranit spravovanou instanci?**

Spravované instance můžete odstranit prostřednictvím webu Azure Portal, [PowerShellu](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance?view=azps-4.3.0), rozhraní příkazového [řádku azure CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-delete) nebo [Správce prostředků rozhraní REST API](https://docs.microsoft.com/rest/api/sql/managedinstances/delete).

**Jak dlouho trvá vytvoření nebo aktualizace instance nebo obnovení databáze?**

Očekávalo se, že by se vytvořila Nová spravovaná instance nebo aby se změnily úrovně služeb (virtuální jádra, Storage), závisí na několika faktorech. Viz [operace správy](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Zásady vytváření názvů

**Může mít spravovaná instance stejný název jako SQL Server místní instance?**

Změna názvu spravované instance není podporována.

**Můžu změnit předponu zóny DNS?**

Ano, výchozí zóna DNS služby Managed instance *. Database.Windows.NET* lze změnit. 

Chcete-li použít jinou zónu DNS namísto výchozího, například *. contoso.com*: 
- Použijte CliConfig k definování aliasu. Nástroj je jenom Obálka nastavení registru, takže se dá dělat taky pomocí zásad skupiny nebo skriptu.
- Použijte *CNAME* s možností *TrustServerCertificate = true* .

## <a name="move-a-database-from-sql-managed-instance"></a>Přesunutí databáze ze spravované instance SQL 

**Jak můžu přesunout databázi ze spravované instance SQL zpátky do SQL Server nebo Azure SQL Database?**

Můžete [exportovat databázi do BacPac](../database/database-export.md) a pak [importovat soubor BacPac](../database/database-import.md). Toto je doporučený postup, pokud je databáze menší než 100 GB.

Transakční replikaci je možné použít, pokud všechny tabulky v databázi mají primární klíče.

Nativní `COPY_ONLY` zálohy pořízené ze spravované instance SQL nelze obnovit do SQL Server, protože spravovaná instance SQL má vyšší verzi databáze v porovnání s SQL Server.

## <a name="migrate-an-instance-database"></a>Migrace instance databáze

**Jak můžu migrovat databázi instance do Azure SQL Database?**

Jednou z možností je [exportovat databázi do BacPac](../database/database-export.md) a pak [importovat soubor BacPac](../database/database-import.md). 

Toto je doporučený postup, pokud je databáze menší než 100 GB. Transakční replikaci je možné použít, pokud všechny tabulky v databázi mají primární klíče.

## <a name="switch-hardware-generation"></a>Přepnout generování hardwaru 

**Můžu přepnout generování hardwaru spravované instance SQL mezi Gen 4 a gen 5 online?**

Automatizované online přepínání mezi generacemi hardwaru je možné, pokud jsou hardwarové generace dostupné v oblasti, kde je zřízené spravovaná instance SQL. V takovém případě si můžete prohlédnout [stránku Přehled modelu Vcore](../database/service-tiers-vcore.md), která vysvětluje, jak přepínat mezi generováním hardwaru.

Tato operace je dlouhodobá, protože nová spravovaná instance se zřídí na pozadí a databáze automaticky přenesené mezi starými a novými instancemi a rychlé převzetí služeb při selhání na konci procesu. 

**Co když ve stejné oblasti nejsou podporovaná hardwarová generování?**

Pokud se ve stejné oblasti nepodporují hardwarové generace, změna hardwarového generování je možná, ale je nutné ji provést ručně. To vyžaduje zřízení nové instance v oblasti, kde je k dispozici potřebná generace hardwaru, a ruční zálohování a obnovení dat mezi starými a novými instancemi.

**Co dělat, pokud pro provádění operace aktualizace není k dispozici dostatek IP adres?**

V případě, že v podsíti, ve které je zřízena vaše spravovaná instance, není dostatek IP adres, budete muset v rámci ní vytvořit novou podsíť a novou spravovanou instanci. Také doporučujeme, aby se nová podsíť vytvořila s více přidělenými IP adresami, takže budoucí operace aktualizace se vyhnete podobným situacím. (Správnou velikost podsítě najdete v tématu [Určení velikosti podsítě virtuální](vnet-subnet-determine-size.md)sítě.) Po zřízení nové instance můžete ručně zálohovat a obnovovat data mezi starými a novými instancemi nebo provést obnovení mezi instancemi [v rámci časového okamžiku](point-in-time-restore.md?tabs=azure-powershell). 


## <a name="tune-performance"></a>Ladění výkonu

**Návody vyladit výkon spravované instance SQL?**

Spravovaná instance SQL na Pro obecné účely vrstvě používá vzdálené úložiště, takže velikost dat a souborů protokolu jsou důležité pro výkon. Další informace najdete v tématu [vliv velikosti souboru protokolu na pro obecné účely výkonu spravované instance SQL](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Pokud se vaše zatížení skládá z velkého množství malých transakcí, zvažte možnost přepnout typ připojení ze proxy serveru do režimu přesměrování.

## <a name="maximum-storage-size"></a>Maximální velikost úložiště

**Jaká je maximální velikost úložiště pro spravovanou instanci SQL?**

Velikost úložiště pro spravovanou instanci SQL závisí na vybrané úrovni služby (Pro obecné účely nebo Pro důležité obchodní informace). Omezení úložiště těchto úrovní služeb najdete v tématu věnovaném [charakteristikám vrstvy služeb](../database/service-tiers-general-purpose-business-critical.md).

  
## <a name="networking-requirements"></a>Požadavky na síť 

**Jaká jsou aktuální příchozí a odchozí omezení NSG v podsíti spravované instance?**

Požadovaná pravidla NSG a UDR jsou popsána [zde](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)a automaticky nastavena službou.
Mějte na paměti, že tato pravidla jsou jenom ta, která potřebujeme k údržbě služby. Pokud se chcete připojit ke spravované instanci a použít jiné funkce, budete muset nastavit další pravidla specifická pro funkci, která je potřeba udržovat.

**Jak můžu nastavit příchozí NSG pravidla pro porty pro správu?**

Spravovaná instance SQL zodpovídá za nastavení pravidel pro porty pro správu. Toho se dosahuje prostřednictvím funkcí s názvem [Konfigurace podsítě pro službu](connectivity-architecture-overview.md#service-aided-subnet-configuration).
K tomu je potřeba zajistit nepřerušovaný tok provozu správy za účelem splnění smlouvy SLA.

**Můžu získat zdrojové rozsahy IP adres, které se používají pro příchozí provoz správy?**

Ano. Můžete analyzovat provoz přicházející do skupiny zabezpečení sítě [konfigurací protokolů Network Watcher Flow](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group).

**Můžu nastavit NSG pro řízení přístupu ke koncovému bodu dat (port 1433)?**

Ano. Po zřízení spravované instance můžete nastavit NSG, který řídí příchozí přístup k portu 1433. Doporučuje se co nejvíc omezit rozsah IP adres.

**Můžu nastavit síťové virtuální zařízení nebo místní bránu firewall pro filtrování odchozího provozu správy na základě plně kvalifikovaných názvů domén?**

Ne. Tato akce není podporována z několika důvodů:
-   Směrování provozu, který reprezentuje odpověď na příchozí požadavek na správu, by bylo asymetrické a nebylo možné ho použít.
-   Směrování provozu na úložiště by ovlivnilo omezení propustnosti a latenci, takže nebudeme moct poskytovat očekávanou kvalitu a dostupnost služby.
-   Na základě zkušeností jsou tyto konfigurace náchylné k chybám a nepodporují se.

**Můžu pro odchozí provoz bez správy nastavit síťové virtuální zařízení nebo bránu firewall?**

Ano. Nejjednodušší způsob, jak toho dosáhnout, je přidání pravidla 0/0 do UDR přidruženého k podsíti spravované instance ke směrování provozu prostřednictvím síťové virtuální zařízení.
 
**Kolik IP adres potřebuji pro spravovanou instanci?**

Podsíť musí mít dostatečný počet dostupných [IP adres](connectivity-architecture-overview.md#network-requirements). Informace o určení velikosti podsítě virtuální sítě pro spravovanou instanci SQL najdete v tématu [Určení požadované velikosti podsítě a rozsahu pro spravovanou instanci](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet). 

**Co dělat, pokud pro provádění operace aktualizace instance není k dispozici dostatek IP adres?**

V případě, že v podsíti, ve které je zřízena vaše spravovaná instance, není dostatek [IP adres](connectivity-architecture-overview.md#network-requirements) , budete muset v rámci ní vytvořit novou podsíť a novou spravovanou instanci. Také doporučujeme, aby se nová podsíť vytvořila s více přidělenými IP adresami, takže budoucí operace aktualizace se vyhnete podobným situacím. Po zřízení nové instance můžete ručně zálohovat a obnovovat data mezi starými a novými instancemi nebo provést obnovení mezi instancemi [v rámci časového okamžiku](point-in-time-restore.md?tabs=azure-powershell).

**Potřebuji prázdnou podsíť pro vytvoření spravované instance?**

Ne. Můžete použít buď prázdnou podsíť, nebo podsíť, která již obsahuje spravované instance. 

**Můžu změnit rozsah adres podsítě?**

Není v případě, že jsou k dispozici spravované instance. Toto je omezení síťové infrastruktury Azure. Do prázdné podsítě je povoleno [přidávat pouze další adresní prostory](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings). 

**Můžu svoje spravované instance přesunout do jiné podsítě?**

Ne. Toto je aktuální omezení návrhu spravované instance. Můžete ale zřídit novou instanci v jiné podsíti a ručně zálohovat a obnovovat data mezi starou a novou instancí nebo provést [obnovení k určitému časovému okamžiku](point-in-time-restore.md?tabs=azure-powershell)mezi instancemi.

**Potřebuji k vytvoření spravované instance prázdnou virtuální síť?**

Tento krok není povinný. Můžete buď [vytvořit virtuální síť pro spravovanou instanci Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) , nebo [nakonfigurovat stávající virtuální síť pro spravovanou instanci SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet).

**Můžu v podsíti umístit spravovanou instanci na jiné služby?**

Ne. V současné době nepodporujeme umístění spravované instance v podsíti, která už obsahuje jiné typy prostředků.


## <a name="mitigate-data-exfiltration-risks"></a>Zmírnění rizik exfiltrace dat  

**Jak můžu zmírnit rizika exfiltrace dat?**

Zákazníkům se doporučuje použít sadu nastavení zabezpečení a ovládací prvky, aby se zmírnily veškerá rizika exfiltracea dat:

- Zapněte [transparentní šifrování dat (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) na všech databázích.
- Vypnout modul CLR (Common Language Runtime). To se doporučuje i v místním prostředí.
- Použijte pouze ověřování Azure Active Directory (Azure AD).
- Přístup k instanci s nízkým oprávněním pomocí účtu DBA.
- Nakonfigurujte přístup JIT JumpBox pro účet sysadmin.
- Zapněte [auditování SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)a integrujte je pomocí mechanismů upozorňování.
- Zapněte [detekci hrozeb](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) ze sady [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Úspory nákladů – úspora případů použití

**Kde najdu případy použití a výsledkem jsou úspory nákladů pomocí spravované instance SQL?**

Případové studie spravované instance SQL:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Abychom lépe porozuměli výhodám, nákladům a rizikům spojeným s nasazením spravované instance Azure SQL, měli jsme také studii Forrester: [celkový ekonomický dopad Microsoft Azure SQL Database spravované instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns"></a>DNS

**Můžu nakonfigurovat vlastní DNS pro spravovanou instanci SQL?**

Ano. Podívejte [se, jak nakonfigurovat vlastní DNS pro spravovanou instanci SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

**Můžu aktualizovat DNS?**

V současné době neposkytujeme funkci pro aktualizaci konfigurace serveru DNS pro spravovanou instanci SQL.

Konfigurace DNS se nakonec aktualizuje:

- Když vyprší platnost zapůjčení DHCP.
- Při upgradu platformy.

Jako alternativní řešení můžete downgradovat SQL Managed instance na 4 virtuální jádra a později je upgradovat. To má vedlejší účinky aktualizace konfigurace DNS.


## <a name="ip-address"></a>IP address

**Můžu se k spravované instanci SQL připojit pomocí IP adresy?**

Připojení k spravované instanci SQL s použitím IP adresy není podporováno. Název hostitele spravované instance SQL se mapuje na nástroj pro vyrovnávání zatížení před virtuálním clusterem spravované instance SQL. Protože jeden virtuální cluster může hostovat víc spravovaných instancí, připojení se nedají směrovat do správné spravované instance bez explicitního zadání názvu.

Další informace o architektuře virtuálních clusterů spravované instance SQL najdete v tématu [Architektura připojení virtuálních clusterů](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Má spravovaná instance SQL statickou IP adresu?**

Ve výjimečných, ale nezbytných situacích může být nutné provést online migraci spravované instance SQL do nového virtuálního clusteru. V případě potřeby se tato migrace provádí kvůli změnám v naší technologické sadě zaměřené na zvýšení zabezpečení a spolehlivosti služby. Výsledkem migrace na nový virtuální cluster je změna IP adresy, která je namapovaná na název hostitele spravované instance SQL. Služba SQL Managed instance nedeklaruje podporu statických IP adres a vyhrazuje právo ji změnit bez upozornění jako součást běžných cyklů údržby.

Z tohoto důvodu se důrazně nedoporučuje spoléhat na neměnnosti IP adresy, protože by mohlo dojít k zbytečnému výpadku.

## <a name="change-time-zone"></a>Změnit časové pásmo

**Můžu změnit časové pásmo existující spravované instance?**

Konfiguraci časového pásma lze nastavit při prvním zřízení spravované instance. Změna časového pásma existující spravované instance se nepodporuje. Podrobnosti najdete v tématu [omezení časového pásma](timezones-overview.md#limitations).

Alternativní řešení zahrnují vytvoření nové spravované instance se správným časovým pásmem a následné ruční zálohování a obnovení, nebo to, co doporučujeme, aby bylo možné provést [obnovení mezi instancemi v čase](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="security-and-database-encryption"></a>Zabezpečení a šifrování databáze

**Je role serveru sysadmin k dispozici pro spravovanou instanci SQL?**

Ano, zákazníci můžou vytvářet přihlašovací jména, která jsou členem role sysadmin.  Zákazníci, kteří předpokládají oprávnění sysadmin, také předpokládají zodpovědnost za provoz instance, což může mít negativní dopad na závazek smlouvy SLA. Pokud chcete přidat přihlášení do role serveru sysadmin, přečtěte si téma [ověřování Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-aad-security-tutorial#azure-ad-authentication).

**Je pro spravovanou instanci SQL podporovaná transparentní šifrování dat?**

Ano, transparentní šifrování dat je podporována pro spravovanou instanci SQL. Podrobnosti najdete v tématu [transparentní šifrování dat pro spravovanou instanci SQL](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal).

**Můžu využít model "Přineste si vlastní klíč" pro TDE?**

Ano, Azure Key Vault pro scénář BYOK je k dispozici pro spravovanou instanci Azure SQL. Podrobnosti najdete v tématu [transparentní šifrování dat s klíčem spravovaným zákazníkem](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-ver15&tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Můžu migrovat šifrovanou databázi SQL Server?**

Ano, můžete. K migraci šifrované databáze SQL Server je nutné exportovat a importovat stávající certifikáty do spravované instance, potom provést úplnou zálohu databáze a obnovit ji ve spravované instanci. 

K migraci TDE šifrovaných databází můžete použít taky [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) .

**Jak můžu nakonfigurovat rotaci ochrany TDE pro spravovanou instanci SQL?**

TDE Protector pro spravovanou instanci můžete otáčet pomocí Azure Cloud Shell. Pokyny najdete v tématu [transparentní šifrování dat ve spravované instanci SQL pomocí vlastního klíče z Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Je možné obnovit zašifrovanou databázi do spravované instance SQL?**

Ano, databázi nemusíte dešifrovat, aby ji bylo možné obnovit ve spravované instanci SQL. Aby bylo možné číst data z šifrovaného záložního souboru, je třeba zadat certifikát nebo klíč, který se používá jako ochrana šifrovacího klíče ve zdrojovém systému do spravované instance SQL. Existují dva možné způsoby, jak to provést:

- *Nahrání certifikátu – ochrana spravované instance SQL* Můžete to udělat jenom pomocí PowerShellu. [Vzorový skript](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) popisuje celý proces.
- *Nahrání asymetrického klíče-ochrany pro Azure Key Vault a nasměrování spravované instance SQL na ni*. Tento přístup připomíná BYOK případ použití TDE, který také používá integraci Key Vault k uložení šifrovacího klíče. Pokud nechcete používat klíč jako ochranu šifrovacího klíče a chcete ho jenom zpřístupnit pro spravovanou instanci SQL pro obnovení šifrovaných databází, postupujte podle pokynů pro [Nastavení BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)a zrušte zaškrtnutí políčka **nastavit vybraný klíč jako výchozí ochranu TDE**.

Po zpřístupnění ochrany šifrování pro spravovanou instanci SQL můžete postupovat podle standardního postupu obnovení databáze.

## <a name="purchasing-models-and-benefits"></a>Nákup modelů a výhod

**Jaké nákupní modely jsou k dispozici pro spravovanou instanci SQL?**

Spravovaná instance SQL nabízí [nákupní model založený na Vcore](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Jaké cenové výhody jsou k dispozici pro spravovanou instanci SQL?**

Náklady můžete s výhodami Azure SQL ušetřit následujícími způsoby:
-   Maximalizujte stávající investice do místních licencí a pomocí [zvýhodněné hybridní využití Azure](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell)ušetřete až 55 procent. 
-   Potvrďte rezervaci za výpočetní prostředky a Ušetřete až 33 procent s [výhodou rezervované instance](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). V kombinaci s zvýhodněným hybridním využitím Azure získáte úsporu až 82 procent. 
-   Díky [zvýhodnění](https://azure.microsoft.com/pricing/dev-test/) ceny za vývoj a testování pro Azure můžete ušetřit až 55 procent oproti ceníkům, které nabízí zvýhodněné sazby za vaše průběžné vývojové a testovací úlohy.

**Kdo má nárok na zvýhodnění rezervované instance?**

Aby bylo možné využít výhod rezervované instance, váš typ předplatného musí být smlouva Enterprise (čísla nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). Další informace o rezervacích najdete v tématu [výhoda rezervované instance](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). 

**Je možné zrušit, vyměňovat nebo vrátit rezervace?**

Rezervace můžete zrušit, vyměnit nebo vrátit refundaci s určitými omezeními. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Fakturace za spravovanou instanci a úložiště záloh

**Jaké jsou cenové možnosti SQL Managed instance?**

Pokud chcete prozkoumat cenové možnosti spravované instance, podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Jak můžu sledovat náklady na vyúčtování spravované instance?**

Můžete to udělat pomocí [řešení Azure cost management](https://docs.microsoft.com/azure/cost-management-billing/). V [Azure Portal](https://portal.azure.com) přejděte na **předplatná** a vyberte **Analýza nákladů**. 

Použijte možnost **akumulované náklady** a pak filtrujte podle **typu prostředku** jako `microsoft.sql/managedinstances` .

**Kolik jsou náklady na automatizované zálohování?**

Získáte stejnou velikost volného prostoru úložiště pro zálohování jako zakoupeného vyhrazeného místa pro úložiště, bez ohledu na nastavenou dobu uchovávání záloh. Pokud je vaše spotřeba úložiště zálohování v rámci přiděleného prostoru úložiště bezplatného zálohování, k automatickým zálohám ve spravované instanci nebudou účtovány žádné další poplatky, takže budou zadarmo. Překročení použití úložiště zálohování nad rámec volného místa bude mít za následek náklady na přibližně $0,20 – $0,24 za GB/měsíc v oblasti USA nebo si můžete zobrazit podrobnosti o vaší oblasti na stránce s cenami. Další podrobnosti najdete v tématu [vysvětlení spotřeby úložiště zálohování](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Jak můžu monitorovat náklady na vyúčtování spotřeby úložiště záloh?**

Náklady na úložiště zálohování můžete monitorovat prostřednictvím webu Azure Portal. Pokyny najdete v tématu [monitorování nákladů na automatizované zálohování](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs). 

**Jak můžu optimalizovat náklady na úložiště zálohování na spravované instanci?**

Pokud chcete optimalizovat náklady na úložiště zálohování, přečtěte si téma [jemné vyladění záloh na spravované instanci SQL](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

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
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(nahraďte ' test ' s požadovaným přihlašovacím jménem a upravte hodnoty zásady a vypršení platnosti.)

## <a name="azure-feedback-and-support"></a>Názory a podpora Azure

**Kde můžu mi dělat svoje nápady při vylepšeních spravované instance SQL?**

Můžete hlasovat o nové funkci spravované instance nebo na fóru na základě [názoru na spravované instance SQL](https://feedback.azure.com/forums/915676-sql-managed-instance). Tímto způsobem můžete přispět k vývoji produktů a pomůžou nám upřednostnit naše potenciální vylepšení.

**Jak můžu vytvořit žádost o podporu Azure?**

Informace o tom, jak vytvořit žádost o podporu Azure, najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

