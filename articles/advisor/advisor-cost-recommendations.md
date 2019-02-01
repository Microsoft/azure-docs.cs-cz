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
ms.openlocfilehash: c76c7bdb398184cc297831c9395063e7bf0f6bdc
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492534"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Snižte náklady na službu pomocí Azure Advisoru

Advisor vám pomůže optimalizovat a snížit celkové Azure výdajů díky identifikaci nečinnosti a nedostatečně využité prostředky. Můžete získat nákladů doporučení **náklady** karty na řídicí panel služby Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizovat výdaje a prostřednictvím Změna velikosti nebo vypnutí nedostatečně využité instance virtuálního počítače 

I když některé scénáře aplikací může způsobit nízké využití podle návrhu, můžete často ušetřit peníze tím, že spravuje velikosti a počtu virtuálních počítačů. Advisor sleduje využívání virtuálních počítačů po dobu 14 dnů a pak identifikuje nízké využití virtuálních počítačů. Virtuální počítače, jejichž využití procesoru je 5 % nebo méně a využití sítě je 7 MB nebo méně čtyři nebo více dny jsou považovány za nízké využití virtuálních počítačů.

Advisor zobrazí odhadované náklady na pokračování v používání ve virtuálním počítači, takže je možné ho vypnout, nebo změňte jeho velikost.

Pokud chcete být agresivní více na identifikaci nedostatečně využité virtuální počítače, můžete upravit průměrné využití procesoru pravidlo na bázi předplatného.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Snížení nákladů odstraněním nezajišťované okruhy ExpressRoute

Advisor identifikuje okruhy ExpressRoute, které byly ve stavu poskytovatele *Nezřízeno* pro více než jeden měsíc a doporučuje odstraníte okruh, pokud se nechystáte okruh zřídit s možnostmi připojení Zprostředkovatel.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Snížení nákladů odstraněním nebo překonfigurování nečinných bran virtuální sítě

Advisor identifikuje bran virtuální sítě, které byly nečinné po dobu více než 90 dnů. Protože těchto bran se účtují po hodinách, měli byste zvážit opětovná konfigurace nebo pokud se nechystáte používat už jejich odstranění. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Nákup rezervované instance virtuálních počítačů ušetříte peníze, než byste platili s průběžnými platbami

Advisor bude zkontrolovat využívání virtuálních počítačů za posledních 30 dní a určit, pokud po zakoupení Azure rezervace můžete ušetřit peníze. Služby Advisor se dozvíte, oblasti a velikosti, ve kterém jste potenciálně většina úspory a zobrazí Odhadované úspory z nákupu rezervace. 

S Azure rezervace, předem pro můžete zakoupit základní náklady na virtuální počítače. Slevy automaticky použije nový nebo existující virtuální počítače, které mají stejné velikosti a oblasti jako vaše rezervace. [Další informace o Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

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
