---
title: Snižte náklady na služby pomocí Azure Advisoru
description: Pomocí Azure Advisoru optimalizujte náklady na nasazení Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259692"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Snižte náklady na služby pomocí Azure Advisoru

Poradce vám pomůže optimalizovat a snížit celkové výdaje Azure tím, že identifikuje nečinné a nevyužité prostředky.Doporučení nákladů můžete získat na kartě **Náklady** na řídicím panelu Poradce.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizace výdajů za virtuální počítače prostřednictvím změny velikosti nebo vypnutí nevyužitých instancí 

I když některé scénáře aplikace může mít za následek nízké využití podle návrhu, můžete často ušetřit peníze tím, že spravuje velikost a počet virtuálních počítačů. Pokročilé modely hodnocení Advisor považuje virtuální počítače pro vypnutí, když P95th z maximální hodnoty maximální využití procesoru je menší než 3 % a využití sítě je menší než 2 % za 7 denní období. Virtuální počítače jsou považovány za správnou velikost, pokud je možné přizpůsobit aktuální zatížení v menší skladové jednotce (v rámci stejné řady skladových míst) nebo menší počet # instance tak, že aktuální zatížení nepřesahuje 80 % využití při neuživatelské pracovní zatížení a ne nad 40 % při zatížení uživatele. Zde je typ pracovního vytížení určen analýzou charakteristik využití procesoru úlohy.

Doporučené akce jsou vypnuty nebo změnit velikost, specifické pro prostředek, který je doporučen pro. Poradce vám zobrazí odhadované úspory nákladů pro doporučené akce – změnit velikost nebo vypnout. Pro doporučenou akci pro změny velikosti poskytuje poradce aktuální a cílové informace skladové položky. 

Pokud chcete být agresivnější při identifikaci nedostatečně využitých virtuálních počítačů, můžete upravit pravidlo využití procesoru na základě předplatného.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Snížení nákladů odstraněním nezřízených okruhů ExpressRoute

Advisor identifikuje okruhy ExpressRoute, které byly ve stavu *zprostředkovatele Nezřízeno* po dobu delší než jeden měsíc, a doporučuje odstranění okruhu, pokud neplánujete zřídit okruh u vašeho poskytovatele připojení.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Snížení nákladů odstraněním nebo překonfigurováním nečinných bran virtuální sítě

Poradce identifikuje brány virtuální sítě, které byly nečinné více než 90 dní. Vzhledem k tomu, že tyto brány se účtují každou hodinu, měli byste zvážit jejich změnu konfigurace nebo odstranění, pokud je již nechcete používat. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Nákup rezervovaných instancí virtuálních počítačů a úspora nákladů oproti průběžným platbám

Poradce zkontroluje využití vašeho virtuálního počítače za posledních 30 dní a zjistí, jestli byste mohli ušetřit peníze zakoupením rezervace Azure. Poradce vám ukáže regiony a velikosti, kde máte potenciálně nejvíce úspor, a ukáže vám odhadované úspory z nákupu rezervací. Pomocí rezervací Azure můžete předem zakoupit základní náklady na vaše virtuální počítače. Slevy se automaticky použijí na nové nebo stávající virtuální počítače, které mají stejnou velikost a oblast jako vaše rezervace. [Další informace o rezervovaných instancích virtuálních počítačích Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Poradce vás také upozorní na rezervované instance, které vyprší v příštích 30 dnech. Doporučuje, abyste si zakoupili nové rezervované instance, abyste se vyhnuli placení průběžných plateb.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Odstranění nepřidružených veřejných IP adres pro úsporu peněz

Advisor identifikuje veřejné IP adresy, které nejsou aktuálně přidružené k prostředkům Azure, jako jsou vyrovnávání zatížení nebo virtuální počítače. Tyto veřejné IP adresy jsou dodávány s nominálním poplatkem. Pokud je neplánujete používat, jejich odstranění může vést k úspoře nákladů.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Odstranění kanálů Azure Data Factory s chybami

Azure Advisor zjišťuje kanály Azure Data Factory, které opakovaně selžou, a doporučuje vyřešit problémy nebo odstranit neúspěšné kanály, pokud už nejsou potřeba. Tyto kanály vám budou účtovány, i když vám neslouží, když selhávají. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Použití standardních snímků pro spravované disky
Pokud chcete ušetřit 60 % nákladů, doporučujeme uložit snímky ve službě Storage úrovně Standard bez ohledu na typ úložiště nadřazeného disku. Tato možnost je výchozí možností pro snímky spravovaných disků. Azure Advisor identifikuje snímky, které jsou uložené úložiště Premium a doporučuje migraci snímku z Premium do standardního úložiště. [Další informace o cenách spravovaného disku](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Využití správy životního cyklu
Azure Advisor bude využívat inteligenci týkající se počtu objektů úložiště objektů blob Azure, celkové velikosti a transakcí ke zjištění, jestli by jeden nebo více účtů úložiště bylo nejvhodnější pro povolení správy životního cyklu pro vrstvová data. Vyzve vás k vytvoření pravidel správy životního cyklu, která automaticky navrství vaše data do cool nebo archivu, abyste optimalizovali náklady na úložiště při zachování dat v úložišti objektů blob Azure pro kompatibilitu aplikací.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Jak získat přístup k doporučením nákladů v Azure Advisoru

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Vyhledejte a vyberte [**poradce**](https://aka.ms/azureadvisordashboard) z libovolné stránky.

1. Na řídicím panelu **Poradce** vyberte kartu **Náklady.**

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních poradců najdete v tématu:
* [Úvod do poradce](advisor-overview.md)
* [Začínáme](advisor-get-started.md)
* [Doporučení pro výkon poradce](advisor-performance-recommendations.md)
* [Doporučení poradce pro vysokou dostupnost](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení poradce](advisor-security-recommendations.md)
* [Doporučení poradce pro provozní excelenci](advisor-operational-excellence-recommendations.md)
