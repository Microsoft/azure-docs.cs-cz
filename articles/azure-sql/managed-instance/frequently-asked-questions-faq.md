---
title: Nejčastější dotazy
titleSuffix: Azure SQL Managed Instance
description: Nejčastější dotazy ke službě Azure SQL Managed Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: 17e97503996b53868f12e74ed5a38dba63285426
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503194"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Nejčastější dotazy ke službě Azure SQL Managed Instance
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

## <a name="known-issues-and-defects"></a>Známé problémy a vady

**Kde můžu najít známé problémy a vady?**

Chyby produktu a známé problémy najdete v tématu [známé problémy](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Nové funkce

**Kde můžu najít nejnovější funkce a funkce ve verzi Public Preview?**

Nové funkce a verze Preview najdete v [poznámkách k verzi](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Vytvoření, aktualizace, odstranění nebo přesunutí spravované instance SQL

**Jak můžu zřídit spravovanou instanci SQL?**

Instanci můžete zřídit z [Azure Portal](instance-create-quickstart.md), [PowerShellu](scripts/create-configure-managed-instance-powershell.md), [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) a [šablon ARM](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates).

**Je možné zřídit spravované instance v existujícím předplatném?**

Ano, pokud toto předplatné patří k [podporovaným typům](resource-limits.md#supported-subscription-types)předplatného, můžete zřídit spravovanou instanci v existujícím předplatném.

**Proč se nám nepovedlo zřídit spravovanou instanci v podsíti, jejíž název začíná číslicí?**

Toto je aktuální omezení základní součásti, která ověřuje název podsítě proti regulárnímu výrazu ^ [a-za-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. Všechny názvy, které přecházejí regulárního výrazu a jsou platné názvy podsítí, jsou aktuálně podporovány.

**Jak můžu škálovat spravovanou instanci?**

Spravovanou instanci můžete škálovat z [Azure Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [PowerShellu](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), [Azure CLI](/cli/azure/sql/mi#az-sql-mi-update) nebo [šablon ARM](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates).

**Můžu svoje spravované instance přesunout z jedné oblasti do druhé?**

Ano, můžete. Pokyny najdete v tématu [Přesunutí prostředků do různých oblastí](../database/move-resources-across-regions.md).

**Jak můžu odstranit spravovanou instanci?**

Spravované instance můžete odstranit prostřednictvím Azure Portal, [PowerShellu](/powershell/module/az.sql/remove-azsqlinstance), rozhraní příkazového [řádku Azure CLI](/cli/azure/sql/mi#az-sql-mi-delete) nebo [Správce prostředků REST API](/rest/api/sql/managedinstances/delete).

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

## <a name="migration-options"></a>Možnosti migrace

**Jak mohu migrovat z Azure SQL Database jednoho nebo elastického fondu do spravované instance SQL?**

Managed instance nabízí stejné úrovně výkonu na výpočetní úrovni a velikosti úložiště jako jiné možnosti nasazení Azure SQL Database. Pokud chcete konsolidovat data na jednu instanci nebo jednoduše potřebujete funkci podporovanou výhradně ve spravované instanci, můžete data migrovat pomocí funkce Export/Import (BACPAC). Tady jsou další způsoby, jak zvážit SQL Database migrace do spravované instance SQL: 
- Použití [externího zdroje dat](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- Použití [SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- Použití [BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Jak můžu migrovat databázi instance do jediného Azure SQL Database?**

Jednou z možností je [exportovat databázi do BacPac](../database/database-export.md) a potom [importovat soubor BacPac](../database/database-import.md). Toto je doporučený postup, pokud je databáze menší než 100 GB.

[Transakční replikaci](replication-two-instances-and-sql-server-configure-tutorial.md?view=sql-server-2017&preserve-view=true) je možné použít, pokud všechny tabulky v databázi mají *primární* klíče a v databázi nejsou žádné objekty OLTP v paměti.

Nativní COPY_ONLY zálohy pořízené ze spravované instance nelze obnovit do SQL Server, protože spravovaná instance má vyšší verzi databáze v porovnání s SQL Server. Další podrobnosti najdete v tématu [zálohování pouze pro kopírování](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15).

**Jak můžu migrovat instanci SQL Server do spravované instance SQL?**

Pokud chcete migrovat instanci SQL Server, přečtěte si téma [migrace instance SQL Server do spravované instance Azure SQL](migrate-to-instance-from-sql-server.md).

**Jak můžu migrovat z jiných platforem do spravované instance SQL?**

Informace o migraci z jiných platforem najdete v tématu [Průvodce migrací databáze Azure](https://datamigration.microsoft.com/).

## <a name="switch-hardware-generation"></a>Přepnout generování hardwaru 

**Můžu přepínat generování hardwaru spravované instance mezi Gen 4 a 1.5 online?**

Automatizované online přepínání z COMPUTE GEN4 – na Gen5 je možné, pokud je k dispozici Gen5 hardware v oblasti, kde je zřízena vaše spravovaná instance. V takovém případě si můžete prohlédnout [stránku Přehled modelu Vcore](../database/service-tiers-vcore.md) vysvětlující, jak přepínat mezi generováním hardwaru.

Tato operace je dlouhotrvající, protože nová spravovaná instance se zřídí na pozadí a databáze automaticky přenesené mezi starou a novou instancí s rychlým převzetím služeb při selhání na konci procesu.

Poznámka: COMPUTE GEN4 – hardware se postupně rozchází a už není k dispozici pro nová nasazení. Všechny nové databáze musí být nasazeny na Gen5 hardwaru. Přepnutí z Gen5 na COMPUTE GEN4 – není k dispozici ani.

## <a name="performance"></a>Výkon 

**Jak můžu porovnat výkon spravované instance s SQL Serverm výkonem?**

Pro porovnání výkonu mezi spravovanou instancí a SQL Server je dobrým výchozím bodem [osvědčené postupy pro porovnání výkonu mezi spravovanou instancí Azure SQL a SQL Server](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210) článkem.

**Co způsobuje rozdíly v výkonu mezi spravovanou instancí a SQL Server?**

Podívejte [se na klíčové příčiny rozdílů výkonu mezi spravovanou instancí SQL a SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Další informace o dopadu velikosti souboru protokolu na Pro obecné účely výkonu spravované instance najdete v tématu [vliv velikosti souboru protokolu na pro obecné účely](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Návody vyladit výkon mé spravované instance?**

Výkon spravované instance můžete optimalizovat pomocí:
- [Automatické ladění](../database/automatic-tuning-overview.md) , které poskytuje špičkový výkon a stabilní úlohy prostřednictvím průběžného ladění výkonu na základě AI a strojového učení.
-   [OLTP v paměti](../in-memory-oltp-overview.md) , který vylepšuje propustnost a latenci při zpracování transakcí a poskytuje rychlejší obchodní přehledy. 

K optimalizaci výkonu ještě více zvažte použití některých z *osvědčených postupů* pro [optimalizaci aplikace a databáze](../database/performance-guidance.md#tune-your-database).
Pokud se vaše zatížení skládá z velkého množství malých transakcí, zvažte možnost [přepnout typ připojení ze serveru proxy na režim přesměrování](connection-types-overview.md#changing-connection-type) pro nižší latenci a vyšší propustnost.

## <a name="monitoring-metrics-and-alerts"></a>Monitorování, metriky a výstrahy

**Jaké jsou možnosti monitorování a upozorňování na moji spravovanou instanci?**

Všechny možné možnosti monitorování a upozorňování na využití a výkon spravované instance SQL najdete v [příspěvku na blogu možnosti monitorování spravované instance Azure SQL](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416). Sledování výkonu pro SQL MI v reálném čase najdete v tématu [monitorování výkonu v reálném čase pro spravovanou instanci Azure SQL DB](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance).

**Můžu použít Profiler SQL pro sledování výkonu?**

Ano, podporuje se SQL Profiler nebo spravovaná instance SQL. Další podrobnosti najdete v tématu [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15).

**Jsou Database Advisor a Query Performance Insight podporovány pro databáze spravované instance?**

Ne, nejsou podporovány. [Zobrazení dynamické správy](../database/monitoring-with-dmvs.md) a [úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) můžete použít společně s [SQL profilerem](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) a [XEvents](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) pro monitorování vašich databází.

**Můžu vytvořit upozornění metriky na spravované instanci SQL?**

Ano. Pokyny najdete v tématu [vytvoření výstrah pro spravovanou instanci SQL](alerts-create.md).

**Můžu vytvářet upozornění na metriky v databázi ve spravované instanci?**

Metriky výstrahy nejsou k dispozici pouze pro spravovanou instanci. Metriky výstrah pro jednotlivé databáze ve spravované instanci nejsou k dispozici.

## <a name="storage-size"></a>Velikost úložiště

**Jaká je maximální velikost úložiště pro spravovanou instanci SQL?**

Velikost úložiště pro spravovanou instanci SQL závisí na vybrané úrovni služby (Pro obecné účely nebo Pro důležité obchodní informace). Omezení úložiště těchto úrovní služeb najdete v tématu věnovaném [charakteristikám vrstvy služeb](../database/service-tiers-general-purpose-business-critical.md).

**Jaká je minimální velikost úložiště, která je k dispozici pro spravovanou instanci?**

Minimální velikost dostupného úložiště v instanci je 32 GB. Úložiště je možné přidat v přírůstcích po 32 GB až do maximální velikosti úložiště. Prvních 32 GB je zdarma.

**Můžu prostor úložiště přiřazený k instanci zvýšit nezávisle na výpočetních prostředcích?**

Ano, v některých případech můžete koupit doplňky úložiště nezávisle na výpočetním prostředí. Viz *Maximum rezervovaného úložiště instance* v [tabulce](resource-limits.md#hardware-generation-characteristics).

**Jak můžu optimalizovat výkon úložiště v Pro obecné účely úrovně služeb?**

Pokud chcete optimalizovat výkon úložiště, přečtěte si téma [osvědčené postupy pro úložiště v pro obecné účely](https://techcommunity.microsoft.com).

## <a name="backup-and-restore"></a>Zálohování a obnovení

**Je úložiště pro zálohování odečteno od úložiště spravované instance?**

Ne, úložiště zálohování se neodečte z prostoru úložiště spravované instance. Úložiště zálohování je nezávislé na rozsahu úložiště instance a velikost není omezená. Úložiště zálohování je omezeno časovým obdobím, aby se zachovalo zálohování databází instancí, a to s možností konfigurace až 35 dní. Podrobnosti najdete v tématu [automatizované zálohování](../database/automated-backups-overview.md).

**Jak zjistím, kdy se v mé spravované instanci provádí automatizované zálohování?**

Pokud chcete sledovat, kdy se na spravované instanci provádělo automatizované zálohování, přečtěte si téma [jak sledovat automatizované zálohování pro spravovanou instanci SQL Azure](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355).

**Je podporováno zálohování na vyžádání?**

Ano, v Blob Storage Azure můžete vytvořit úplnou zálohu, která je jen pro kopírování, ale bude obnovitelné jenom ve spravované instanci. Podrobnosti najdete v tématu [zálohování jen pro kopírování](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15). Zálohování jen pro kopírování ale není možné, pokud je databáze zašifrovaná službou TDE spravované službou, protože certifikát použitý k šifrování je nepřístupný. V takovém případě použijte funkci obnovení k bodu v čase k přesunu databáze na jinou spravovanou instanci SQL nebo přepněte na klíč spravovaný zákazníkem.

**Je nativní obnovení (ze souborů. bak) do podporované spravované instance?**

Ano, podporuje se a je k dispozici pro SQL Server 2005 a verze.  Pokud chcete použít nativní obnovení, nahrajte soubor. bak do Azure Blob Storage a spusťte příkazy T-SQL. Další podrobnosti najdete v tématu [nativní obnovení z adresy URL](./migrate-to-instance-from-sql-server.md#native-restore-from-url).

## <a name="business-continuity"></a>Kontinuita podnikových procesů

**Replikují se moje systémové databáze do sekundární instance ve skupině převzetí služeb při selhání?**

Systémové databáze nejsou replikovány do sekundární instance ve skupině převzetí služeb při selhání. Proto by scénáře závislé na objektech ze systémových databází nemohly být na sekundární instanci možné, pokud nejsou objekty ručně vytvořeny na sekundárním objektu. Alternativní řešení najdete v tématu [Povolení scénářů závislých na objektu ze systémových databází](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases).
 
## <a name="networking-requirements"></a>Požadavky na síť 

**Jaká jsou aktuální příchozí a odchozí omezení NSG v podsíti spravované instance?**

Požadovaná pravidla NSG a UDR jsou popsána [zde](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)a automaticky nastavena službou.
Mějte na paměti, že tato pravidla jsou jenom ta, která potřebujeme k údržbě služby. Pokud se chcete připojit ke spravované instanci a použít jiné funkce, budete muset nastavit další pravidla specifická pro funkci, která je potřeba udržovat.

**Jak můžu nastavit příchozí NSG pravidla pro porty pro správu?**

Spravovaná instance SQL zodpovídá za nastavení pravidel pro porty pro správu. Toho se dosahuje prostřednictvím funkcí s názvem [Konfigurace podsítě pro službu](connectivity-architecture-overview.md#service-aided-subnet-configuration).
K tomu je potřeba zajistit nepřerušovaný tok provozu správy za účelem splnění smlouvy SLA.

**Můžu získat zdrojové rozsahy IP adres, které se používají pro příchozí provoz správy?**

Ano. Můžete analyzovat provoz přicházející do skupiny zabezpečení sítě [konfigurací protokolů Network Watcher Flow](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group).

**Můžu nastavit NSG pro řízení přístupu ke koncovému bodu dat (port 1433)?**

Ano. Po zřízení spravované instance můžete nastavit NSG, který řídí příchozí přístup k portu 1433. Doporučuje se co nejvíc omezit rozsah IP adres.

**Můžu nastavit síťové virtuální zařízení nebo místní bránu firewall pro filtrování odchozího provozu správy na základě plně kvalifikovaných názvů domén?**

No. Tato akce není podporována z několika důvodů:
-   Směrování provozu, který reprezentuje odpověď na příchozí požadavek na správu, by bylo asymetrické a nebylo možné ho použít.
-   Směrování provozu na úložiště by ovlivnilo omezení propustnosti a latenci, takže nebudeme moct poskytovat očekávanou kvalitu a dostupnost služby.
-   Na základě zkušeností jsou tyto konfigurace náchylné k chybám a nepodporují se.

**Můžu pro odchozí provoz bez správy nastavit síťové virtuální zařízení nebo bránu firewall?**

Ano. Nejjednodušší způsob, jak toho dosáhnout, je přidání pravidla 0/0 do UDR přidruženého k podsíti spravované instance ke směrování provozu prostřednictvím síťové virtuální zařízení.
 
**Kolik IP adres potřebuji pro spravovanou instanci?**

Podsíť musí mít dostatečný počet dostupných [IP adres](connectivity-architecture-overview.md#network-requirements). Informace o určení velikosti podsítě virtuální sítě pro spravovanou instanci SQL najdete v tématu [Určení požadované velikosti podsítě a rozsahu pro spravovanou instanci](./vnet-subnet-determine-size.md). 

**Co dělat, pokud pro provádění operace aktualizace instance není k dispozici dostatek IP adres?**

V případě, že v podsíti, ve které je zřízena vaše spravovaná instance, není dostatek [IP adres](connectivity-architecture-overview.md#network-requirements) , budete muset v rámci ní vytvořit novou podsíť a novou spravovanou instanci. Také doporučujeme vytvořit novou podsíť s více přidělenými IP adresami, aby při budoucích operacích aktualizace nedocházelo k podobným situacím. Po zřízení nové instance můžete ručně zálohovat a obnovovat data mezi starými a novými instancemi nebo provést obnovení mezi instancemi [v rámci časového okamžiku](point-in-time-restore.md?tabs=azure-powershell).

**Potřebuji prázdnou podsíť pro vytvoření spravované instance?**

No. Můžete použít buď prázdnou podsíť, nebo podsíť, která již obsahuje spravované instance. 

**Můžu změnit rozsah adres podsítě?**

Není v případě, že jsou k dispozici spravované instance. Toto je omezení síťové infrastruktury Azure. Do prázdné podsítě je povoleno [přidávat pouze další adresní prostory](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings). 

**Můžu svoje spravované instance přesunout do jiné podsítě?**

No. Toto je aktuální omezení návrhu spravované instance. Můžete ale zřídit novou instanci v jiné podsíti a ručně zálohovat a obnovovat data mezi starou a novou instancí nebo provést [obnovení k určitému časovému okamžiku](point-in-time-restore.md?tabs=azure-powershell)mezi instancemi.

**Potřebuji k vytvoření spravované instance prázdnou virtuální síť?**

Tento krok není povinný. Můžete buď [vytvořit virtuální síť pro spravovanou instanci Azure SQL](./virtual-network-subnet-create-arm-template.md) , nebo [nakonfigurovat stávající virtuální síť pro spravovanou instanci SQL Azure](./vnet-existing-add-subnet.md).

**Můžu v podsíti umístit spravovanou instanci na jiné služby?**

No. V současné době nepodporujeme umístění spravované instance v podsíti, která už obsahuje jiné typy prostředků.

## <a name="connectivity"></a>Připojení 

**Můžu se k spravované instanci připojit pomocí IP adresy?**

Ne, tato podpora není podporována. Název hostitele spravované instance se mapuje na nástroj pro vyrovnávání zatížení před virtuálním clusterem spravované instance. Jelikož jeden virtuální cluster může hostovat více spravovaných instancí, nelze připojení směrovat do správné spravované instance bez zadání jeho názvu.
Další informace o architektuře virtuálních clusterů spravované instance SQL najdete v tématu [Architektura připojení virtuálních clusterů](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Má moje spravovaná instance statickou IP adresu?**

To se v tuto chvíli nepodporuje.

Ve výjimečných, ale nezbytných situacích může být nutné provést online migraci spravované instance do nového virtuálního clusteru. V případě potřeby se tato migrace provádí kvůli změnám v naší technologické sadě zaměřené na zvýšení zabezpečení a spolehlivosti služby. Výsledkem migrace na nový virtuální cluster je změna IP adresy, která je namapovaná na název hostitele spravované instance. Služba Managed instance nedeklaruje podporu statických IP adres a vyhrazuje právo ji změnit bez upozornění jako součást běžných cyklů údržby.

Z tohoto důvodu se důrazně nedoporučuje spoléhat na neměnnosti IP adresy, protože by mohlo dojít k zbytečnému výpadku.

**Má spravovaná instance veřejný koncový bod?**

Ano. Spravovaná instance má veřejný koncový bod, který se ve výchozím nastavení používá jenom pro správu služeb, ale zákazník ho může povolit i pro přístup k datům. Další podrobnosti najdete v tématu [použití spravované instance SQL s veřejnými koncovými body](./public-endpoint-overview.md). Pokud chcete nakonfigurovat veřejný koncový bod, použijte ke [konfiguraci veřejného koncového bodu ve spravované instanci SQL](public-endpoint-configure.md).

**Jak řízení spravované instance přistupuje k veřejnému koncovému bodu?**

Spravovaná instance řídí přístup k veřejnému koncovému bodu na úrovni sítě i aplikace.

Služba správy a nasazení se připojí ke spravované instanci pomocí [koncového bodu správy](./connectivity-architecture-overview.md#management-endpoint) , který se mapuje na externí nástroj pro vyrovnávání zatížení. Provoz se směruje na uzly pouze v případě, že se obdrží na předdefinované sadě portů, které používají pouze součásti správy spravované instance. Integrovaná brána firewall na uzlech je nastavená tak, aby povolovala přenosy jenom z rozsahů IP adres Microsoftu. Certifikáty vzájemně ověřují veškerou komunikaci mezi součástmi pro správu a rovinou správy. Další podrobnosti najdete v tématu [Architektura připojení pro spravovanou instanci SQL](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Můžu použít veřejný koncový bod pro přístup k datům v databázích spravované instance?**

Ano. Zákazník bude muset povolit přístup k datům veřejného koncového bodu z [Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShellu](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM a nakonfigurovat NSG tak, aby zamkne přístup k datovému portu (číslo portu 3342). Další informace najdete v tématech [Konfigurace veřejného koncového bodu ve spravované instanci Azure SQL](public-endpoint-configure.md) a [bezpečné použití spravované instance Azure SQL s veřejným koncovým bodem](public-endpoint-overview.md). 

**Můžu zadat vlastní port pro koncové body dat SQL?**

Ne, tato možnost není k dispozici.  Pro koncový bod privátních dat používá spravovaná instance výchozí číslo portu 1433 a pro koncový bod veřejné dat používá spravovaná instance výchozí číslo portu 3342.

**Jaký je doporučený způsob, jak propojit spravované instance umístěné v různých oblastech?**

Způsob, jak to provést, je partnerský vztah okruhu Express Route. Podpora globálního partnerského vztahu virtuálních sítí se podporuje s omezením popsaným v níže uvedené poznámce.  

> [!IMPORTANT]
> [V 9/22/2020 jsme oznámili globální partnerské vztahy virtuálních sítí pro nově vytvořené virtuální clustery](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). To znamená, že globální partnerský vztah virtuálních sítí je podporován pro spravované instance SQL vytvořené v prázdných podsítích po datu oznámení a také pro všechny následné spravované instance vytvořené v těchto podsítích. Pro všechny ostatní podpory partnerského vztahu spravované instance SQL je omezená na sítě ve stejné oblasti v důsledku [omezení globálního partnerského vztahu virtuálních sítí](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Další podrobnosti najdete v článku o nejčastějších dotazech v příslušné části [Azure Virtual Networks](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . 

Pokud není možné partnerské vztahy okruhu Express Route a globální partnerské vztahy virtuálních sítí, stačí, když vytvoříte připojení VPN typu Site-to-Site ([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)a [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)).

## <a name="mitigate-data-exfiltration-risks"></a>Zmírnění rizik exfiltrace dat  

**Jak můžu zmírnit rizika exfiltrace dat?**

Zákazníkům se doporučuje použít sadu nastavení zabezpečení a ovládací prvky, aby se zmírnily veškerá rizika exfiltracea dat:

- Zapněte [transparentní šifrování dat (TDE)](../database/transparent-data-encryption-tde-overview.md) na všech databázích.
- Vypnout modul CLR (Common Language Runtime). To se doporučuje i v místním prostředí.
- Použijte pouze ověřování Azure Active Directory (Azure AD).
- Přístup k instanci s nízkým oprávněním pomocí účtu DBA.
- Nakonfigurujte přístup JIT JumpBox pro účet sysadmin.
- Zapněte [auditování SQL](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)a integrujte je pomocí mechanismů upozorňování.
- Zapněte [detekci hrozeb](../database/threat-detection-configure.md) z [Azure Defenderu pro SQL](../database/azure-defender-for-sql.md) Suite.

## <a name="dns"></a>DNS

**Můžu nakonfigurovat vlastní DNS pro spravovanou instanci SQL?**

Ano. Podívejte [se, jak nakonfigurovat vlastní DNS pro spravovanou instanci SQL Azure](./custom-dns-configure.md).

**Můžu aktualizovat DNS?**

Ano. Viz [synchronizace nastavení serverů DNS virtuální sítě ve virtuálním clusteru spravované instance SQL](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

## <a name="change-time-zone"></a>Změnit časové pásmo

**Můžu změnit časové pásmo existující spravované instance?**

Konfiguraci časového pásma lze nastavit při prvním zřízení spravované instance. Změna časového pásma existující spravované instance se nepodporuje. Podrobnosti najdete v tématu [omezení časového pásma](timezones-overview.md#limitations).

Alternativní řešení zahrnují vytvoření nové spravované instance se správným časovým pásmem a následné ruční zálohování a obnovení, nebo to, co doporučujeme, aby bylo možné provést [obnovení mezi instancemi v čase](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance).


## <a name="security-and-database-encryption"></a>Zabezpečení a šifrování databáze

**Je role serveru sysadmin k dispozici pro spravovanou instanci SQL?**

Ano, zákazníci můžou vytvářet přihlašovací jména, která jsou členem role sysadmin.  Zákazníci, kteří předpokládají oprávnění sysadmin, také předpokládají zodpovědnost za provoz instance, což může mít negativní dopad na závazek smlouvy SLA. Pokud chcete přidat přihlášení do role serveru sysadmin, přečtěte si téma [ověřování Azure AD](./aad-security-configure-tutorial.md#azure-ad-authentication).

**Je pro spravovanou instanci SQL podporovaná transparentní šifrování dat?**

Ano, transparentní šifrování dat je podporována pro spravovanou instanci SQL. Podrobnosti najdete v tématu [transparentní šifrování dat pro spravovanou instanci SQL](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal).

**Můžu využít model "Přineste si vlastní klíč" pro TDE?**

Ano, Azure Key Vault pro scénář BYOK je k dispozici pro spravovanou instanci Azure SQL. Podrobnosti najdete v tématu [transparentní šifrování dat s klíčem spravovaným zákazníkem](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Můžu migrovat šifrovanou databázi SQL Server?**

Ano, můžete. K migraci šifrované databáze SQL Server je nutné exportovat a importovat stávající certifikáty do spravované instance, potom provést úplnou zálohu databáze a obnovit ji ve spravované instanci. 

K migraci TDE šifrovaných databází můžete použít taky [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) .

**Jak můžu nakonfigurovat rotaci ochrany TDE pro spravovanou instanci SQL?**

TDE Protector pro spravovanou instanci můžete otáčet pomocí Azure Cloud Shell. Pokyny najdete v tématu [transparentní šifrování dat ve spravované instanci SQL pomocí vlastního klíče z Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Je možné obnovit zašifrovanou databázi do spravované instance SQL?**

Ano, databázi nemusíte dešifrovat, aby ji bylo možné obnovit ve spravované instanci SQL. Aby bylo možné číst data z šifrovaného záložního souboru, je třeba zadat certifikát nebo klíč, který se používá jako ochrana šifrovacího klíče ve zdrojovém systému do spravované instance SQL. Existují dva možné způsoby, jak to provést:

- *Nahrání certifikátu – ochrana spravované instance SQL* Můžete to udělat jenom pomocí PowerShellu. [Vzorový skript](./tde-certificate-migrate.md) popisuje celý proces.
- *Nahrání asymetrického klíče-ochrany pro Azure Key Vault a nasměrování spravované instance SQL na ni*. Tento přístup připomíná BYOK případ použití TDE, který také používá integraci Key Vault k uložení šifrovacího klíče. Pokud nechcete používat klíč jako ochranu šifrovacího klíče a chcete ho jenom zpřístupnit pro spravovanou instanci SQL pro obnovení šifrovaných databází, postupujte podle pokynů pro [Nastavení BYOK TDE](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)a zrušte zaškrtnutí políčka **nastavit vybraný klíč jako výchozí ochranu TDE**.

Po zpřístupnění ochrany šifrování pro spravovanou instanci SQL můžete postupovat podle standardního postupu obnovení databáze.

## <a name="purchasing-models-and-benefits"></a>Nákup modelů a výhod

**Jaké nákupní modely jsou k dispozici pro spravovanou instanci SQL?**

Spravovaná instance SQL nabízí [nákupní model založený na Vcore](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Jaké cenové výhody jsou k dispozici pro spravovanou instanci SQL?**

Náklady můžete s výhodami Azure SQL ušetřit následujícími způsoby:
-   Maximalizujte stávající investice do místních licencí a pomocí [zvýhodněné hybridní využití Azure](../azure-hybrid-benefit.md?tabs=azure-powershell)ušetřete až 55 procent. 
-   Potvrďte rezervaci za výpočetní prostředky a Ušetřete až 33 procent s [výhodou rezervované instance](../database/reserved-capacity-overview.md). V kombinaci s zvýhodněným hybridním využitím Azure získáte úsporu až 82 procent. 
-   Díky [zvýhodnění](https://azure.microsoft.com/pricing/dev-test/) ceny za vývoj a testování pro Azure můžete ušetřit až 55 procent oproti ceníkům, které nabízí zvýhodněné sazby za vaše průběžné vývojové a testovací úlohy.

**Kdo má nárok na zvýhodnění rezervované instance?**

Aby bylo možné využít výhod rezervované instance, váš typ předplatného musí být smlouva Enterprise (čísla nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo samostatná smlouva s cenami průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P). Další informace o rezervacích najdete v tématu [výhoda rezervované instance](../database/reserved-capacity-overview.md). 

**Je možné zrušit, vyměňovat nebo vrátit rezervace?**

Rezervace můžete zrušit, vyměnit nebo vrátit refundaci s určitými omezeními. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Fakturace za spravovanou instanci a úložiště záloh

**Jaké jsou cenové možnosti SQL Managed instance?**

Pokud chcete prozkoumat cenové možnosti spravované instance, podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Jak můžu sledovat náklady na vyúčtování spravované instance?**

Můžete to udělat pomocí [řešení Azure cost management](../../cost-management-billing/index.yml). V [Azure Portal](https://portal.azure.com) přejděte na **předplatná** a vyberte **Analýza nákladů**. 

Použijte možnost **akumulované náklady** a pak filtrujte podle **typu prostředku** jako `microsoft.sql/managedinstances` .

**Kolik jsou náklady na automatizované zálohování?**

Získáte stejnou velikost volného prostoru úložiště pro zálohování jako zakoupeného vyhrazeného místa pro úložiště, bez ohledu na nastavenou dobu uchovávání záloh. Pokud je vaše spotřeba úložiště zálohování v rámci přiděleného prostoru úložiště bezplatného zálohování, k automatickým zálohám ve spravované instanci nebudou účtovány žádné další poplatky, takže budou zadarmo. Překročení použití úložiště zálohování nad rámec volného místa bude mít za následek náklady na přibližně $0,20 – $0,24 za GB/měsíc v oblasti USA nebo si můžete zobrazit podrobnosti o vaší oblasti na stránce s cenami. Další podrobnosti najdete v tématu [vysvětlení spotřeby úložiště zálohování](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Jak můžu monitorovat náklady na vyúčtování spotřeby úložiště záloh?**

Náklady na úložiště zálohování můžete monitorovat prostřednictvím Azure Portal. Pokyny najdete v tématu [monitorování nákladů na automatizované zálohování](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs). 

**Jak můžu optimalizovat náklady na úložiště zálohování na spravované instanci?**

Pokud chcete optimalizovat náklady na úložiště zálohování, přečtěte si téma [jemné vyladění záloh na spravované instanci SQL](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="cost-saving-use-cases"></a>Úspory nákladů – úspora případů použití

**Kde najdu případy použití a výsledkem jsou úspory nákladů pomocí spravované instance SQL?**

Případové studie spravované instance SQL:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Abychom lépe porozuměli výhodám, nákladům a rizikům spojeným s nasazením spravované instance Azure SQL, měli jsme také studii Forrester: [celkový ekonomický dopad Microsoft Azure SQL Database spravované instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

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


## <a name="service-updates"></a>Aktualizace služeb

**Jakou změnu má kořenová CA pro Azure SQL Database & SQL Managed instance?**

Přečtěte si téma [otočení certifikátu pro Azure SQL Database & spravované instance SQL](../updates/ssl-root-certificate-expiring.md). 

**Jaká je plánovaná událost údržby pro spravovanou instanci SQL?**

Viz [plánování událostí údržby Azure ve spravované instanci SQL](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Názory a podpora Azure

**Kde můžu mi dělat svoje nápady při vylepšeních spravované instance SQL?**

Můžete hlasovat o nové funkci spravované instance nebo na fóru na základě [názoru na spravované instance SQL](https://feedback.azure.com/forums/915676-sql-managed-instance). Tímto způsobem můžete přispět k vývoji produktů a pomůžou nám upřednostnit naše potenciální vylepšení.

**Jak můžu vytvořit žádost o podporu Azure?**

Informace o tom, jak vytvořit žádost o podporu Azure, najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).
