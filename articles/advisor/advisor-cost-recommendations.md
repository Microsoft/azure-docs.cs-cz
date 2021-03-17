---
title: Snížení nákladů na službu pomocí Azure Advisor
description: Využijte Azure Advisor k optimalizaci nákladů na nasazení Azure.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 346b790c6970abc8670661e1ec180662957af47d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503370"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Snížení nákladů na službu pomocí Azure Advisor

Azure Advisor vám pomůže optimalizovat a snížit celkové výdaje na Azure tím, že se identifikují nečinné a nevyužité prostředky. Cenová doporučení můžete získat na kartě **náklady** na řídicím panelu poradce.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizace výdajů za virtuální počítače prostřednictvím změny velikosti nebo vypnutí nevyužitých instancí 

I když některé scénáře aplikací můžou mít za následek nízké využití podle návrhu, můžete často ušetřit peníze tím, že spravujete velikost a počet virtuálních počítačů. 

Doporučené akce jsou vypnuté nebo změny velikosti specifické pro vyhodnocování prostředku.

Model pokročilého vyhodnocení v Advisoru se považuje za vypínání virtuálních počítačů, pokud jsou splněné všechny tyto příkazy: 
- P95th maximální hodnoty využití procesoru je menší než 3%. 
- Využití sítě je méně než 2% po dobu sedmi dnů.
- Tlak paměti je nižší než mezní hodnoty.

Poradce bere v úvahu změnu velikosti virtuálních počítačů, pokud je možné přizpůsobit aktuální zatížení menší SKU (ve stejné rodině SKU) nebo menšímu počtu instancí:
- Aktuální zatížení nepřekračuje 80% využití pro úlohy, které na uživatele nečelí. 
- Zatížení nepřekračuje 40% pro úlohy pro uživatele. 

Služba Advisor v tomto případě určuje typ úlohy analýzou vlastností využití procesoru úloh.

Advisor zobrazuje odhadované náklady na doporučenou akci: Změna velikosti nebo vypnutí. Pro změnu velikosti poskytuje poradce informace o aktuálním a cílovém SKU.

Pokud chcete mít více agresivní informace o identifikaci nevyužitých virtuálních počítačů, můžete upravit pravidlo využití CPU na základě jednotlivých předplatných.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Optimalizace útraty pro servery MariaDB, MySQL a PostgreSQL podle správného určení velikosti 
Advisor analyzuje vaše využití a vyhodnotí, jestli se prostředky serveru databáze MariaDB, MySQL nebo PostgreSQL v posledních sedmi dnech nevyužily po delší dobu. Nedostatečné využití prostředků má za následek nechtěné výdaje, které můžete opravit bez významného dopadu na výkon. Pokud chcete snížit náklady a efektivně spravovat prostředky, doporučujeme, abyste snížili velikost výpočetní kapacity (virtuální jádra) o polovinu.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Snížení nákladů odstraněním nezajištěných okruhů ExpressRoute

Advisor identifikuje okruhy Azure ExpressRoute, které se ve stavu poskytovatele **nezřídily** po dobu více než jednoho měsíce. Doporučuje se odstranit okruh, pokud neplánujete zřídit okruh s vaším poskytovatelem připojení.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Snížení nákladů odstraněním nebo překonfigurováním nečinných bran virtuální sítě

Poradce identifikuje brány virtuální sítě, které byly nečinné déle než 90 dní. Vzhledem k tomu, že se tyto brány účtují po hodinách, měli byste zvážit jejich opětovnou konfiguraci nebo odstranění, pokud je už nechcete používat. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Nákup rezervovaných instancí virtuálních počítačů a úspora nákladů oproti průběžným platbám

Služba Advisor zkontroluje využití virtuálních počítačů během posledních 30 dnů a určí, jestli můžete ušetřit peníze tím, že si zakoupíte rezervaci Azure. Advisor vám ukáže oblasti a velikosti, kde je potenciální úspora nejvyšší a Odhadované úspory z nákupních rezervací. Pomocí rezervací Azure můžete předem zakoupit základní náklady na virtuální počítače. Slevy se automaticky použijí na nové nebo existující virtuální počítače, které mají stejnou velikost a oblast jako vaše rezervace. [Přečtěte si další informace o Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor také vás upozorní na rezervované instance, jejichž platnost vyprší během následujících 30 dnů. Doporučuje se zakoupit nové rezervované instance, abyste se vyhnuli průběžným platbám za průběžné platby.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Nákup rezervovaných instancí pro několik typů prostředků, které vám umožní ušetřit náklady na průběžné platby

Advisor analyzuje vzorce využití za posledních 30 dní pro následující prostředky a doporučuje nákup rezervovaných kapacit, který optimalizuje náklady.

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB rezervovanou kapacitu
Advisor analyzuje vzorce používání Azure Cosmos DB za posledních 30 dnů a doporučuje nákup rezervovaných kapacit pro optimalizaci nákladů. Pomocí rezervované kapacity si můžete předběžně koupit Azure Cosmos DB hodinové využití a ušetřit náklady za průběžné platby. Rezervovaná kapacita je fakturační zvýhodnění, které se automaticky vztahuje na nová i existující nasazení. Advisor vypočítá Odhadované úspory pro jednotlivá předplatná pomocí ročních cen rezervací a extrapolací vzorců využití zjištěných za posledních 30 dnů. Doporučení ke sdílenému rozsahu jsou k dispozici pro rezervované nákupy kapacity a můžou zvýšit úspory.

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>SQL Database a Rezervovaná kapacita spravované instance SQL
Advisor analyzuje SQL Database a vzory využití spravované instance SQL za posledních 30 dní. Pak doporučí rezervované nákupy kapacity, které optimalizují náklady. Pomocí rezervované kapacity můžete předem zakoupit SQL DB po hodinách využití a ušetřit náklady na výpočetní výkon SQL. Vaše licence SQL se účtuje samostatně a rezervace se nesleví. Rezervovaná kapacita je fakturační zvýhodnění, které se automaticky vztahuje na nová i existující nasazení. Advisor vypočítá Odhadované úspory pro jednotlivá předplatná pomocí ročních cen rezervací a extrapolací vzorců využití zjištěných za posledních 30 dnů. Doporučení ke sdílenému rozsahu jsou k dispozici pro rezervované nákupy kapacity a můžou zvýšit úspory. Podrobnosti najdete v tématu [Azure SQL Database & rezervované kapacity spravované instance SQL](../azure-sql/database/reserved-capacity-overview.md).

### <a name="app-service-stamp-fee-reserved-capacity"></a>App Service vyhrazenou kapacitu pro poplatky za razítko
Advisor v posledních 30 dnech analyzuje vzor použití poplatku za Azure App Service izolované prostředí a doporučuje rezervované nákupy kapacity, které optimalizují náklady. Pomocí rezervované kapacity si můžete předem koupit hodinové využití pro poplatek za izolované prostředí a ušetřit náklady za průběžné platby. Rezervovaná kapacita se vztahuje pouze na poplatky za razítko a nikoli na App Service instance. Rezervovaná kapacita je fakturační zvýhodnění, které se automaticky vztahuje na nová i existující nasazení. Služba Advisor počítá s uložením odhadů pro jednotlivá předplatná za použití ceny za 3 roky na základě vzorců využití za posledních 30 dní.

### <a name="blob-storage-reserved-capacity"></a>Vyhrazená kapacita úložiště objektů BLOB
Služba Advisor analyzuje úložiště objektů BLOB v Azure a využití úložiště Azure Data Lake za posledních 30 dní. Pak vypočítá rezervované nákupy kapacity, které optimalizují náklady. Díky rezervované kapacitě můžete předem nakoupit hodinové využití a ušetřit je na základě aktuálních nákladů na vyžádání. Rezervovaná kapacita úložiště objektů BLOB se vztahuje jenom na data uložená ve službě Azure Blob pro obecné účely v2 a účty Azure Data Lake Storage Gen2. Rezervovaná kapacita je fakturační zvýhodnění, které se automaticky vztahuje na nová i existující nasazení. Advisor vypočítá Odhadované úspory pro jednotlivá předplatná pomocí cen za 3 roky a vzorců využití zjištěných za posledních 30 dní. Doporučení ke sdílenému rozsahu jsou k dispozici pro rezervované nákupy kapacity a můžou zvýšit úspory.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>Vyhrazená kapacita MariaDB, MySQL a PostgreSQL
Advisor analyzuje vaše vzory využití pro Azure Database for MariaDB, Azure Database for MySQL a Azure Database for PostgreSQL za posledních 30 dnů. Pak doporučí rezervované nákupy kapacity, které optimalizují náklady. Pomocí rezervované kapacity můžete předem zakoupit MariaDB, MySQL a PostgreSQL hodinové využití a ušetřit přitom své aktuální náklady. Rezervovaná kapacita je fakturační zvýhodnění, které se automaticky vztahuje na nová i existující nasazení. Advisor vypočítá Odhadované úspory pro jednotlivá předplatná pomocí cen za 3 roky a vzorců využití zjištěných za posledních 30 dní. Doporučení ke sdílenému rozsahu jsou k dispozici pro rezervované nákupy kapacity a můžou zvýšit úspory.

### <a name="azure-synapse-analytics-reserved-capacity"></a>Rezervovaná kapacita Azure synapse Analytics
Advisor během posledních 30 dnů analyzuje vaše vzory využití Azure synapse Analytics a doporučuje rezervované nákupy kapacity, které optimalizují náklady. Pomocí rezervované kapacity můžete předem zakoupit synapse analýzu po hodinách a ušetřit náklady na vyžádání. Rezervovaná kapacita je fakturační zvýhodnění, které se automaticky vztahuje na nová i existující nasazení. Advisor vypočítá Odhadované úspory pro jednotlivá předplatná pomocí cen za 3 roky a vzorců využití zjištěných za posledních 30 dní. Doporučení ke sdílenému rozsahu jsou k dispozici pro rezervované nákupy kapacity a můžou zvýšit úspory.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Odstraní nepřidružené veřejné IP adresy, aby se ušetřily peníze.

Advisor identifikuje veřejné IP adresy, které nejsou přidružené k prostředkům Azure, jako jsou nástroje pro vyrovnávání zatížení a virtuální počítače. K těmto veřejným IP adresám se přidruží nominální poplatek. Pokud je neplánujete použít, můžete peníze ušetřit jejich odstraněním.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Odstranění kanálů Azure Data Factory s chybami

Poradce detekuje Azure Data Factory kanály, které opakovaně selžou. Doporučuje, abyste vyřešili problémy nebo odstranili kanály, pokud je nepotřebujete. Tyto kanály se fakturují i v případě, že vám nejsou zachovány.

## <a name="use-standard-snapshots-for-managed-disks"></a>Použít standardní snímky pro spravované disky
Pokud chcete ušetřit 60% nákladů, doporučujeme ukládat snímky ve službě Storage úrovně Standard bez ohledu na typ úložiště nadřazeného disku. Tato možnost je výchozí možností pro snímky spravovaných disků. Poradce identifikuje snímky uložené v Premium Storage a doporučuje migraci z úrovně Premium na úložiště Standard. [Přečtěte si další informace o cenách spravovaného disku.](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>Použití správy životního cyklu
Pomocí inteligentních informací o počtu, celkové velikosti a transakcích služby Azure Blob Storage zjistí služba Advisor, jestli byste měli povolit správu životního cyklu na úrovni dat na jednom nebo několika účtech úložiště. Vyzve vás k vytvoření pravidel pro správu životního cyklu, která budou automaticky vrstvit vaše data na studená nebo archivní úložiště pro optimalizaci nákladů na úložiště a zachování vašich dat v úložišti objektů BLOB v Azure kvůli kompatibilitě aplikací.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Doporučení k vytvoření dočasného disku s operačním systémem
[Dočasný disk s operačním systémem](../virtual-machines/ephemeral-os-disks.md) vám umožní: 
- Ušetřete náklady na úložiště pro disky s operačním systémem. 
- Získejte nižší latenci čtení a zápisu na disky s operačním systémem. 
- Získejte rychlejší operace obnovení imagí virtuálních počítačů obnovením operačního systému (a dočasného disku) do původního stavu.

Je vhodnější použít dočasný disk s operačním systémem pro krátkodobé IaaS virtuální počítače nebo virtuální počítače se stavovým zatížením. Advisor poskytuje doporučení pro prostředky, které můžou využívat dočasný disk s operačním systémem.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>Zmenšení mezipaměti Azure Průzkumník dat Table – perioda (zásady) pro optimalizaci nákladů na cluster (Preview)
Poradce identifikuje prostředky, ve kterých se omezuje zásada mezipaměti tabulky, uvolní uzly clusteru Azure Průzkumník dat s nízkým využitím procesoru, pamětí a konfigurací vysoké velikosti mezipaměti.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Přístup k cenovým doporučením v Azure Advisor

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyhledejte a vyberte [**Advisor**](https://aka.ms/azureadvisordashboard) na libovolné stránce.

1. Na řídicím panelu **Advisor** vyberte kartu **náklady** .

## <a name="next-steps"></a>Další kroky

Další informace o doporučení Advisoru najdete tady:
* [Úvod do Advisoru](advisor-overview.md)
* [Skóre Advisoru](azure-advisor-score.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro vysokou dostupnost poradce](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
