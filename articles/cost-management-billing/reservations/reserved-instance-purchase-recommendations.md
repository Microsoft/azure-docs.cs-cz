---
title: Jak se vytvářejí doporučení pro rezervace Azure
description: Přečtěte si, jak se vytvářejí doporučení pro rezervace Azure pro virtuální počítače.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 90967e740b87c2f93bd46bfb78684af96f36193a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508475"
---
# <a name="how-reservation-recommendations-are-created"></a>Jak se vytvářejí doporučení pro rezervace

Doporučení k nákupu rezervovaných instancí (RI) Azure se poskytují prostřednictvím [rozhraní API pro doporučení rezervací](/rest/api/consumption/reservationrecommendations) služby Azure Consumption, [Azure Advisoru](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)a prostředí pro nákup rezervací na webu Azure Portal.

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
