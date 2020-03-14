---
title: Snížení nákladů na službu pomocí Azure Advisor
description: Využijte Azure Advisor k optimalizaci nákladů na nasazení Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259692"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Snížení nákladů na službu pomocí Azure Advisor

Poradce vám pomůže optimalizovat a snížit celkové výdaje na Azure tím, že identifikuje nečinné a nevyužité prostředky. Cenová doporučení můžete získat na kartě **náklady** na řídicím panelu poradce.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizace útraty virtuálních počítačů změnou velikosti nebo vypnutí nevyužitých instancí 

I když některé scénáře aplikací můžou mít za následek nízké využití podle návrhu, můžete často ušetřit peníze tím, že spravujete velikost a počet virtuálních počítačů. Modely pro pokročilé hodnocení služby Advisor považují virtuální počítače za vypnuté, pokud P95th maximální hodnoty využití procesoru je menší než 3% a využití sítě je méně než 2% po dobu 7 dnů. Virtuální počítače se považují za správnou velikost, pokud je možné přizpůsobit aktuální zatížení menší SKU (v rámci stejné rodiny SKU) nebo menšímu počtu instancí, aby aktuální zatížení nepřesahoval 80% využití, když úlohy, které nepoužívají uživatele, a nepřesahují 40% při uživatelském zatížení. Tady je typ úlohy určený analýzou vlastností využití procesoru úloh.

Doporučené akce mají stav Vypnuto nebo změnit velikost, které jsou specifické pro prostředek doporučený pro. Advisor vám ukáže Odhadované úspory nákladů pro doporučené akce – Změna velikosti nebo vypnutí. Pro doporučené změny velikosti doporučuje poradce poskytovat informace o aktuálním a cílovém SKU. 

Pokud chcete mít více agresivní při identifikaci nevyužitých virtuálních počítačů, můžete upravit pravidlo využití CPU pro každé předplatné.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Snížení nákladů odstraněním nezajištěných okruhů ExpressRoute

Advisor identifikuje okruhy ExpressRoute, které byly ve stavu poskytovatele, *Nezřízeny* po dobu více než jednoho měsíce, a doporučuje odstranit okruh, pokud neplánujete zřídit okruh s poskytovatelem připojení.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Snížení nákladů odstraněním nebo změnou konfigurace nečinných bran virtuální sítě

Poradce identifikuje brány virtuálních sítí, které byly po více než 90 dnech nečinné. Vzhledem k tomu, že se tyto brány účtují po hodinách, měli byste zvážit jejich opětovnou konfiguraci nebo odstranění, pokud je už nechcete používat. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Nákup rezervovaných instancí virtuálních počítačů za účelem úspory peněz nad náklady na průběžné platby

Služba Advisor zkontroluje využití virtuálního počítače za posledních 30 dní a určí, jestli můžete ušetřit peníze tím, že si zakoupíte rezervaci Azure. Advisor vám ukáže oblasti a velikosti, kde máte pravděpodobně nejvíc úspor, a zobrazí vám Odhadované úspory z nákupních rezervací. Pomocí rezervací Azure můžete předem zakoupit základní náklady na virtuální počítače. Slevy budou automaticky platit pro nové nebo existující virtuální počítače, které mají stejnou velikost a oblast jako vaše rezervace. [Přečtěte si další informace o Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor vám taky upozorní na rezervované instance, jejichž platnost vyprší během následujících 30 dnů. Doporučujeme, abyste si nakoupili nové rezervované instance, abyste se vyhnuli platbám za průběžné platby.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Odstraní nepřidružené veřejné IP adresy, aby se ušetřily peníze.

Advisor identifikuje veřejné IP adresy, které nejsou aktuálně přidružené k prostředkům Azure, jako jsou nástroje pro vyrovnávání zatížení nebo virtuální počítače. Tyto veřejné IP adresy se dodávají za nominální poplatek. Pokud je neplánujete použít, může jejich odstranění způsobit úspory nákladů.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Odstranění neúspěšných kanálů Azure Data Factory

Azure Advisor detekuje Azure Data Factory kanály, které opakovaně selžou, a doporučuje, abyste vyřešili problémy nebo odstranili neúspěšné kanály, pokud už je nepotřebujete. Tyto kanály se budou fakturovat i v případě, že vám nejsou zachovány. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Pro Managed Disks použít standardní snímky
Pokud chcete ušetřit 60 % nákladů, doporučujeme uložit snímky ve službě Storage úrovně Standard bez ohledu na typ úložiště nadřazeného disku. Tato možnost je výchozí možností pro Managed Disks snímky. Azure Advisor identifikuje snímky uložené Premium Storage a doporučuje migraci snímku z úrovně Premium na úložiště Standard. [Další informace o cenách spravovaného disku](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Využití správy životního cyklu
Azure Advisor bude využívat inteligentní informace týkající se počtu objektů úložiště objektů BLOB v Azure, celkové velikosti a transakcí, které zjistí, jestli je jeden nebo více účtů úložiště nejvhodnější pro zajištění správy životního cyklu na úrovni dat. Zobrazí se výzva k vytvoření pravidel pro správu životního cyklu, která budou automaticky vrstvit vaše data na studená nebo Archivovaná pro optimalizaci nákladů na úložiště a zachování vašich dat v úložišti objektů BLOB v Azure kvůli kompatibilitě aplikací.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Přístup k cenovým doporučením v Azure Advisor

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyhledejte a vyberte [**Advisor**](https://aka.ms/azureadvisordashboard) na libovolné stránce.

1. Na řídicím panelu **Advisor** vyberte kartu **náklady** .

## <a name="next-steps"></a>Další kroky

Další informace o doporučení Advisoru najdete tady:
* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme](advisor-get-started.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro vysokou dostupnost poradce](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
