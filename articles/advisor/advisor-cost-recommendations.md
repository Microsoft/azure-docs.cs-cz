---
title: Snížení nákladů na službu pomocí Azure Advisor
description: Využijte Azure Advisor k optimalizaci nákladů na nasazení Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 99dfec669d8981a557b2e8a8d8979292af74616f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658570"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Snížení nákladů na službu pomocí Azure Advisor

Poradce vám pomůže optimalizovat a snížit celkové výdaje na Azure tím, že identifikuje nečinné a nevyužité prostředky.Cenová doporučení můžete získat na kartě **náklady** na řídicím panelu poradce.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizace výdajů za virtuální počítače prostřednictvím změny velikosti nebo vypnutí nevyužitých instancí 

I když některé scénáře aplikací můžou mít za následek nízké využití podle návrhu, můžete často ušetřit peníze tím, že spravujete velikost a počet virtuálních počítačů. Modely pro pokročilé hodnocení služby Advisor považují virtuální počítače za vypnuté, pokud P95th maximální hodnoty využití procesoru je menší než 3% a využití sítě je méně než 2% po dobu 7 dnů. Virtuální počítače se považují za správnou velikost, pokud je možné přizpůsobit aktuální zatížení menší SKU (v rámci stejné rodiny SKU) nebo menšímu počtu instancí, aby aktuální zatížení nepřesahoval 80% využití, když úlohy, které nepoužívají uživatele, a nepřesahují 40% při uživatelském zatížení. Tady je typ úlohy určený analýzou vlastností využití procesoru úloh.

Doporučené akce mají stav Vypnuto nebo změnit velikost, které jsou specifické pro prostředek doporučený pro. Advisor vám ukáže Odhadované úspory nákladů pro doporučené akce – Změna velikosti nebo vypnutí. Pro doporučené změny velikosti doporučuje poradce poskytovat informace o aktuálním a cílovém SKU. 

Pokud chcete mít více agresivní při identifikaci nevyužitých virtuálních počítačů, můžete upravit pravidlo využití CPU pro každé předplatné.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Optimalizace útraty pro servery MariaDB, MySQL a PostgreSQL podle správného určení velikosti 
Poradce analyzuje vaše využití a doporučuje, pokud se prostředky serveru databáze MariaDB/MySQL/PostgreSQL po delší dobu během posledních 7 dnů nevyužily. Nízké využití prostředků vede k nežádoucím výdajům a dá se opravit bez výrazného dopadu na výkon. Pokud chcete snížit náklady a efektivně spravovat své prostředky, doporučujeme snížit velikost výpočetních prostředků (virtuálních jader) o polovinu.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Snížení nákladů odstraněním nezajištěných okruhů ExpressRoute

Advisor identifikuje okruhy ExpressRoute, které byly ve stavu poskytovatele, *Nezřízeny* po dobu více než jednoho měsíce, a doporučuje odstranit okruh, pokud neplánujete zřídit okruh s poskytovatelem připojení.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Snížení nákladů odstraněním nebo překonfigurováním nečinných bran virtuální sítě

Poradce identifikuje brány virtuálních sítí, které byly po více než 90 dnech nečinné. Vzhledem k tomu, že se tyto brány účtují po hodinách, měli byste zvážit jejich opětovnou konfiguraci nebo odstranění, pokud je už nechcete používat. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Nákup rezervovaných instancí virtuálních počítačů a úspora nákladů oproti průběžným platbám

Služba Advisor zkontroluje využití virtuálního počítače za posledních 30 dní a určí, jestli můžete ušetřit peníze tím, že si zakoupíte rezervaci Azure. Advisor vám ukáže oblasti a velikosti, kde máte pravděpodobně nejvíc úspor, a zobrazí vám Odhadované úspory z nákupních rezervací. Pomocí rezervací Azure můžete předem zakoupit základní náklady na virtuální počítače. Slevy budou automaticky platit pro nové nebo existující virtuální počítače, které mají stejnou velikost a oblast jako vaše rezervace. [Přečtěte si další informace o Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor vám taky upozorní na rezervované instance, jejichž platnost vyprší během následujících 30 dnů. Doporučujeme, abyste si nakoupili nové rezervované instance, abyste se vyhnuli platbám za průběžné platby.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Nákup rezervovaných instancí pro několik typů prostředků, které vám umožní ušetřit náklady na průběžné platby

Analyzujeme vzor využití pro následující seznam prostředků, za posledních 30 dní a doporučte si nákup rezervovaných kapacit, který maximalizuje vaše úspory. 
### <a name="cosmos-db-reserved-capacity"></a>Záložní kapacita Cosmos DB
Na základě analýzy vašeho vzorce využití služby Cosmos DB za posledních 30 dnů vám doporučujeme nákup rezervované kapacity za účelem optimalizace nákladů. S rezervovanou kapacitou si můžete předem zakoupit hodinové využití služby Cosmos DB a tím ušetřit oproti průběžným platbám. Rezervovaná kapacita představuje cenové zvýhodnění, které se bude automaticky uplatňovat na nová i stávající nasazení. Odhady úspor se počítají pro jednotlivá předplatná s použitím cen za rezervaci na 3 roky a pozorovaného vzorce využití za posledních 30 dnů. V prostředí pro nákup rezervací jsou k dispozici doporučení pro sdílený rozsah, která můžou úspory ještě zvýšit.

### <a name="sql-paas-reserved-capacity"></a>Vyhrazená kapacita SQL PaaS
Na základě analýzy vzorce využití spravovaných instancí a elastických fondů SQL PaaS za posledních 30 dnů vám doporučujeme nákup rezervované kapacity, která maximalizuje vaše úspory. S rezervovanou kapacitou si můžete předem zakoupit hodinové využití služby SQL Database a tím ušetřit oproti nákladům na výpočetní prostředky SQL. Licence SQL se účtuje samostatně a neplatí pro ni sleva za rezervaci. Rezervovaná kapacita představuje cenové zvýhodnění, které se bude automaticky uplatňovat na nová i stávající nasazení. Odhady úspor se počítají pro jednotlivá předplatná s použitím cen za rezervaci na 3 roky a pozorovaného vzorce využití za posledních 30 dnů. V prostředí pro nákup rezervací jsou k dispozici doporučení pro sdílený rozsah, která můžou úspory ještě zvýšit.

### <a name="app-service-stamp-fee-reserved-capacity"></a>Rezervovaná kapacita poplatku za službu App Service
Analyzujeme vaše App Service používání poplatků za razítko s použitím s použitím izolovaného prostředí za posledních 30 dní a doporučuje nákup rezervovaných kapacit, který maximalizuje vaše úspory. S rezervovanou kapacitou si můžete předem zakoupit hodinové využití poplatku za kolek izolovaného prostředí a tím ušetřit oproti průběžným platbám. Upozorňujeme, že rezervovaná kapacita se vztahuje pouze na poplatek za kolek, a ne na instance služby App Service. Rezervovaná kapacita představuje cenové zvýhodnění, které se bude automaticky uplatňovat na nová i stávající nasazení. Odhady úspor se počítají pro jednotlivá předplatná s použitím cen za rezervaci na 3 roky na základě vzorce využití za posledních 30 dnů.

### <a name="blob-storage-reserved-capacity"></a>Vyhrazená kapacita úložiště objektů BLOB
Na základě analýzy vašeho využití služeb Azure Blob Storage a Data Lake Storage za posledních 30 dnů jsme vypočítali, jak můžete nákupem rezervované kapacity maximalizovat úspory. S rezervovanou kapacitou si můžete předem zakoupit hodinové využití a tím ušetřit oproti aktuálním nákladům na vyžádání. Rezervovaná kapacita služby Blob Storage se vztahuje pouze na data uložená ve službách Azure Blob Storage (GPv2) a Azure Data Lake Storage (Gen2). Rezervovaná kapacita představuje cenové zvýhodnění, které se bude automaticky uplatňovat na nová i stávající nasazení. Odhady úspor se počítají pro jednotlivá předplatná s použitím cen za rezervaci na 3 roky a pozorovaného vzorce využití za posledních 30 dnů. V prostředí pro nákup rezervací jsou k dispozici doporučení pro sdílený rozsah, která můžou úspory ještě zvýšit.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>Vyhrazená kapacita MariaDB, MySQL a PostgreSQL
V průběhu posledních 30 dnů analyzujeme vaše využití Azure Database for MariaDB, MySQL a PostgreSQL a doporučuje nákup rezervovaných kapacit, který maximalizuje vaše úspory. Díky rezervované kapacitě můžete předem zakoupit MariaDB, MySQL a PostgreSQL hodinové využití a ušetřit náklady. Rezervovaná kapacita představuje cenové zvýhodnění, které se bude automaticky uplatňovat na nová i stávající nasazení. Odhady úspor se počítají pro jednotlivá předplatná s použitím cen za rezervaci na 3 roky a vzorce využití za posledních 30 dnů. V prostředí pro nákup rezervací jsou k dispozici doporučení pro sdílený rozsah, která můžou úspory ještě zvýšit.

### <a name="synapse-analytics-formerly-sql-dw-reserved-capacity"></a>Rezervovaná kapacita synapse Analytics (dříve SQL DW)
Váš vzor využití analýzy Azure synapse analyzujeme za posledních 30 dní a doporučuje nákup rezervovaných kapacit, který maximalizuje vaše úspory. S rezervovanou kapacitou si můžete předem zakoupit hodinové využití služby Synapse Analytics a tím ušetřit oproti nákladům na vyžádání. Rezervovaná kapacita představuje cenové zvýhodnění, které se bude automaticky uplatňovat na nová i stávající nasazení. Odhady úspor se počítají pro jednotlivá předplatná s použitím cen za rezervaci na 3 roky a pozorovaného vzorce využití za posledních 30 dnů. V prostředí pro nákup rezervací jsou k dispozici doporučení pro sdílený rozsah, která můžou úspory ještě zvýšit.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Odstraní nepřidružené veřejné IP adresy, aby se ušetřily peníze.

Advisor identifikuje veřejné IP adresy, které nejsou aktuálně přidružené k prostředkům Azure, jako jsou nástroje pro vyrovnávání zatížení nebo virtuální počítače. Tyto veřejné IP adresy se dodávají za nominální poplatek. Pokud je neplánujete použít, může jejich odstranění způsobit úspory nákladů.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Odstranění kanálů Azure Data Factory s chybami

Azure Advisor detekuje Azure Data Factory kanály, které opakovaně selžou, a doporučuje, abyste vyřešili problémy nebo odstranili neúspěšné kanály, pokud už je nepotřebujete. Tyto kanály se budou fakturovat i v případě, že vám nejsou zachovány. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Pro Managed Disks použít standardní snímky
Pokud chcete ušetřit 60 % nákladů, doporučujeme uložit snímky ve službě Storage úrovně Standard bez ohledu na typ úložiště nadřazeného disku. Tato možnost je výchozí možností pro Managed Disks snímky. Azure Advisor identifikuje snímky uložené Premium Storage a doporučuje migraci snímku z úrovně Premium na úložiště Standard. [Další informace o cenách spravovaného disku](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Využití správy životního cyklu
Azure Advisor bude využívat inteligentní informace týkající se počtu objektů úložiště objektů BLOB v Azure, celkové velikosti a transakcí, které zjistí, jestli je jeden nebo více účtů úložiště nejvhodnější pro zajištění správy životního cyklu na úrovni dat. Zobrazí se výzva k vytvoření pravidel pro správu životního cyklu, která budou automaticky vrstvit vaše data na studená nebo Archivovaná pro optimalizaci nákladů na úložiště a zachování vašich dat v úložišti objektů BLOB v Azure kvůli kompatibilitě aplikací.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Doporučení k vytvoření dočasného disku s operačním systémem
S [dočasným diskem s operačním systémem](https://docs.microsoft.com/azure/virtual-machines/windows/ephemeral-os-disks)získají zákazníci tyto výhody: úspory nákladů na úložiště pro disk s operačním systémem. Nižší latence čtení a zápisu na disk s operačním systémem Rychlejší operace obnovení virtuálního počítače z image resetováním operačního systému (a dočasného disku) do původního stavu Je vhodnější použít dočasný disk s operačním systémem pro krátkodobé IaaS virtuální počítače nebo virtuální počítače se stavovým zatížením. Poradce má doporučení pro prostředky, které můžou využívat výhody s dočasným diskem s operačním systémem. 


## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Přístup k cenovým doporučením v Azure Advisor

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Vyhledejte a vyberte [**Advisor**](https://aka.ms/azureadvisordashboard) na libovolné stránce.

1. Na řídicím panelu **Advisor** vyberte kartu **náklady** .

## <a name="next-steps"></a>Další kroky

Další informace o doporučení Advisoru najdete tady:
* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro vysokou dostupnost poradce](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)

