---
title: Snižte náklady na službu pomocí Azure Advisoru | Dokumentace Microsoftu
description: Použití Azure Advisoru optimalizovat náklady na nasazení v Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: fef7b82e6969de16d1815250d2373c99021b0e86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254725"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Snižte náklady na službu pomocí Azure Advisoru

Advisor vám pomůže optimalizovat a snížit celkové Azure výdajů díky identifikaci nečinnosti a nedostatečně využité prostředky. Můžete získat nákladů doporučení **náklady** karty na řídicí panel služby Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizovat výdaje a prostřednictvím Změna velikosti nebo vypnutí nedostatečně využité instance virtuálního počítače 

I když některé scénáře aplikací může způsobit nízké využití podle návrhu, můžete často ušetřit peníze tím, že spravuje velikosti a počtu virtuálních počítačů. Advisor sleduje využívání virtuálních počítačů po dobu 7 dní a pak identifikuje nízké využití virtuálních počítačů. Virtuální počítače jsou považovány za využití s nízkou při využití výkonu procesoru 5 % nebo méně a jejich využití sítě je menší než 2 %, nebo pokud aktuální zatížení může být obslouženo menší velikost virtuálního počítače.

Advisor zobrazí odhadované náklady na pokračování v používání ve virtuálním počítači, takže je možné ho vypnout, nebo změňte jeho velikost.

Pokud chcete být agresivní více na identifikaci nedostatečně využité virtuální počítače, můžete upravit průměrné využití procesoru pravidlo na bázi předplatného.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Snížení nákladů odstraněním nezajišťované okruhy ExpressRoute

Advisor identifikuje okruhy ExpressRoute, které byly ve stavu poskytovatele *Nezřízeno* pro více než jeden měsíc a doporučuje odstraníte okruh, pokud se nechystáte okruh zřídit s možnostmi připojení Zprostředkovatel.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Snížení nákladů odstraněním nebo překonfigurování nečinných bran virtuální sítě

Advisor identifikuje bran virtuální sítě, které byly nečinné po dobu více než 90 dnů. Protože těchto bran se účtují po hodinách, měli byste zvážit opětovná konfigurace nebo pokud se nechystáte používat už jejich odstranění. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Nákup rezervované instance virtuálních počítačů ušetříte peníze, než byste platili s průběžnými platbami

Advisor bude zkontrolovat využívání virtuálních počítačů za posledních 30 dní a určit, pokud po zakoupení Azure rezervace můžete ušetřit peníze. Služby Advisor se dozvíte, oblasti a velikosti, ve kterém jste potenciálně většina úspory a zobrazí Odhadované úspory z nákupu rezervace. S Azure rezervace, předem pro můžete zakoupit základní náklady na virtuální počítače. Slevy automaticky použije nový nebo existující virtuální počítače, které mají stejné velikosti a oblasti jako vaše rezervace. [Další informace o Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor vám také oznámí rezervované instance, které máte, které vyprší během následujících 30 dní. Vám doporučí, nákupu nového rezervované instance, abyste neplatili ceny s průběžnými platbami.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Odstranit nepřidružené veřejné IP adresy ušetříte peníze

Advisor identifikuje veřejné IP adresy, které nejsou aktuálně přidružené k prostředkům Azure, jako jsou nástroje pro vyrovnávání zatížení nebo virtuální počítače. Tyto veřejné IP adresy, které jsou součástí nominální poplatek. Pokud nechcete používat, jejich odstranění může způsobit úspory nákladů.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Azure Data Factory kanálů, které se nedaří odstranit

Azure Advisor zjistí kanálů Azure Data Factory, které opakovaně selhat a doporučujeme při řešení problémů nebo odstranit kanály selhání, pokud už nepotřebujete. Vám bude účtovat těchto kanálech i v případě, že i když jejich nejsou že vám během se nedaří. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Použít standardní snímky za spravované disky
Pokud chcete uložit 60 % nákladů, doporučujeme ukládat vaše snímky ve standardním úložišti, bez ohledu na typ úložiště nadřazeného disku. Toto je výchozí možnost pro Managed Disks snímky. Azure Advisor bude identifikovat snímky, které jsou uloženy doporučujeme migraci snímek z úrovně Premium na úložiště úrovně Standard a Premium Storage. [Další informace o cenách služby Managed Disks](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Jak získat přístup k doporučení k nákladům v Azure Advisoru

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu služby Advisor, klikněte na tlačítko **náklady** kartu.

## <a name="next-steps"></a>Další postup

Další informace o doporučení Advisoru, naleznete v tématu:
* [Úvod do služby Advisor](advisor-overview.md)
* [Začínáme](advisor-get-started.md)
* [Poradce doporučení k výkonu](advisor-cost-recommendations.md)
* [Poradce doporučení k vysoké dostupnosti](advisor-cost-recommendations.md)
* [Bezpečnostní doporučení advisoru](advisor-cost-recommendations.md)
