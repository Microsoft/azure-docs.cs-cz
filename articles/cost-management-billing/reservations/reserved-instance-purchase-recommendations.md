---
title: Jak se vytvářejí doporučení pro rezervace Azure
description: Přečtěte si, jak se vytvářejí doporučení pro rezervace Azure pro virtuální počítače.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: 2f7b09c14553fdb5d642080d286ce123176b997f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76991044"
---
# <a name="how-reservation-recommendations-are-created"></a>Jak se vytvářejí doporučení pro rezervace
Doporučení k nákupu rezervovaných instancí (RI) Azure se poskytují prostřednictvím [rozhraní API pro doporučení rezervací](/rest/api/consumption/reservationrecommendations) služby Azure Consumption, [Azure Advisoru](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)a prostředí pro nákup rezervací na webu Azure Portal.

Následující kroky definují, jak se vypočítávají doporučení: 
1. Modul doporučení vyhodnotí hodinové využití vašich prostředků v daném rozsahu za posledních 7, 30 a 60 dnů.
2. Na základě těchto dat o využití modul simuluje vaše náklady s rezervacemi a bez nich.
3. Náklady se simulují pro různá množství a doporučí se množství, které zajišťuje maximální úspory.
4. Pokud se vaše prostředky pravidelně vypínají, simulace nezjistí žádné úspory a neposkytnou se žádná doporučení k nákupu.

## <a name="recommendations-in-azure-advisor"></a>Doporučení od služby Azure Advisor
V Azure Advisoru jsou k dispozici doporučení pro nákup rezervací pro virtuální počítače. Mějte na paměti následující skutečnosti: 
- Advisor poskytuje doporučení jenom v rámci jednotlivých předplatných.
- V Advisoru jsou k dispozici doporučení vypočítaná na základě shrnutí za 30 dnů zpětně.
- Pokud si koupíte rezervaci se sdíleným rozsahem, doporučení pro nákup rezervací Advisoru může trvat až 30 dnů, než zmizí.

## <a name="other-expected-api-behavior"></a>Další očekávané chování rozhraní API
- Když použijete shrnutí za sedm dní, je možné, že nedostanete doporučení pro případ, kdy se virtuální počítače vypnou na déle než jeden den.

## <a name="next-steps"></a>Další kroky
- Přečtěte si o tom, [Jak se na virtuální počítače uplatňuje sleva za rezervaci Azure](../manage/understand-vm-reservation-charges.md).
