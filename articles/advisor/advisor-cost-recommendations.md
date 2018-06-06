---
title: Azure doporučení služby Advisor náklady | Microsoft Docs
description: Pomocí Azure Advisor optimalizovat náklady na vašich Azure nasazení.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: ade6ef996c00c0c06d5b8e44815520e6e4ab7e9f
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735863"
---
# <a name="advisor-cost-recommendations"></a>Náklady na doporučení služby Advisor

Pomáhá Advisor optimalizovat a snížit vaše celkové Azure tráví určením nečinnosti a nedostatečně prostředky. Můžete získat náklady doporučení z **náklady** na řídicím panelu služby Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizace tráví virtuálního počítače nebo změnou velikosti vypíná nedostatečně instancí 
I když některé scénáře aplikací může být nízkou míru využívání návrhu, můžete často ušetřit peníze pomocí správy velikost a počet virtuálních počítačů. Advisor monitoruje vaše využití virtuálního počítače po dobu 14 dnů a pak identifikuje nízké využití virtuálních počítačů. Virtuální počítače, jejichž využití procesoru je 5 % nebo méně a využití sítě je 7 MB nebo méně čtyři nebo více dní jsou považovány za nízké využití virtuálních počítačů.

Advisor zobrazí odhadované náklady pokračovat ke spuštění virtuálního počítače, takže je možné ho vypnout, nebo jeho velikost.

Pokud chcete být agresivnější zjistit nedostatečně virtuální počítače, můžete upravit průměrná pravidlo využití procesoru na základě za předplatné.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Snížení nákladů odstraněním není zřízený okruhy ExpressRoute
Advisor identifikuje okruhy ExpressRoute, které byly ve stavu zprostředkovatele *není zajišťováno* pro více než jeden měsíc a doporučuje odstraňování okruh, pokud nemáte v plánu poskytnutí okruhu připojení k Zprostředkovatel.

## <a name="buy-virtual-machine-reserved-instances-to-save-money-over-pay-as-you-go-costs"></a>Pokud chcete platit méně, než byste platili s průběžnými platbami, kupte si rezervované instance virtuálních počítačů.
Advisor budou zkontrolujte vaše využití virtuálního počítače za posledních 30 dnů a zjistěte, pokud může ušetřit peníze a nakoupit vyhrazenou instancí. Advisor vám ukáže, oblasti a velikosti, kde jste potenciálně většina úspory a zobrazí Odhadované úspory z nákupu vyhrazenou instancí. 

S vyhrazenou instancí můžete předem zakoupit základní náklady pro virtuální počítače. Slevy automaticky použije nový nebo existující virtuální počítače, které mají stejnou velikost a oblasti jako vaše vyhrazená instance. [Další informace o vyhrazenou instancí virtuálních počítačů Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Jak získat přístup k náklady na doporučení v Azure Advisor

1. Přihlaste se k [portál Azure](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu služby Advisor, klikněte na **náklady** kartě.

## <a name="next-steps"></a>Další postup

Další informace o doporučení služby Advisor najdete v tématu:
* [Úvod do služby Advisor](advisor-overview.md)
* [Začínáme](advisor-get-started.md)
* [Poradce při hodnocení výkonu doporučení](advisor-cost-recommendations.md)
* [Doporučení pro vysokou dostupnost služby Advisor](advisor-cost-recommendations.md)
* [Doporučení zabezpečení Advisor](advisor-cost-recommendations.md)
