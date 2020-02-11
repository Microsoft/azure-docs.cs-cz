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
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851347"
---
# <a name="how-reservation-recommendations-are-created"></a>Jak se vytvářejí doporučení pro rezervace

Doporučení k nákupu rezervovaných instancí Azure (RI) se generují v rámci spotřeby v Azure pomocí [rozhraní API doporučení pro rezervace](/rest/api/consumption/reservationrecommendations). Doporučení z tohoto rozhraní API také používá [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs). Služba Advisor zobrazuje doporučení na webu Azure Portal.

Pokud máte virtuální počítače běžící v Azure, můžete využít snížené ceny za rezervované instance a předplatit si virtuální počítače. Rozhraní API pro doporučení spotřeby od Microsoftu vyhodnocuje vaše využití za 7, 30 a 60 dní a doporučuje optimální konfigurace pro rezervované instance. Vypočítá náklady, které byste zaplatili v případě, že nemáte k dispozici RI, a porovná je s náklady, které zaplatíte s využitím RI za účelem optimalizace úspor.

Azure Advisor zobrazuje doporučení na základě období 30 dní.

Pro jednoduchost ukazuje následující příklad výpočty týkající se doporučení na 7 dní. Při výpočtu doporučení na 30 nebo 60 dní se používá stejná metoda.

## <a name="calculation-method-example"></a>Příklad metody výpočtu

Předpokládejme, že vaše hodinové využití virtuálních počítačů s Windows pro konkrétní SKU a oblast se po dobu sedmi dní (168 hodin) může měnit. Minimální využití je 65 jednotek a maximální využití je 127 jednotek za 7 dní. V tomto příkladu se v 79. hodině používalo 80 virtuálních počítačů a zakoupili jste 75 rezervovaných instancí.

Pokud si koupíte 75 rezervovaných instancí, zaplatíte za 79. hodinu následující cenu:

- 75 rezervovaných instancí. Cena se platí předem při nákupu RI.
- Rezervované instance zahrnují náklady na hardware běžících virtuálních počítačů, takže zaplatíte cenu pouze 75 hodin použití softwaru.
- Využití pro 79. hodinu je 80, takže zaplatíte pět hodin systému Windows a k tomu měřenou kombinovanou cenu s hardwarem. Kombinovaná cena vychází z vaší smlouvy Enterprise (EA) nebo ze sazby průběžných plateb.

Pokud koupíte 75 rezervovaných instancí, můžete celkové náklady vypočítat tím, že přidáte předchozí hodinové náklady. Aktuální náklady můžete vypočítat také pomocí sazeb. Rozdíl mezi těmito dvěma částkami v tomto příkladu jsou vaše úspory za sedm dní.

Rozhraní API provádí výpočty pro každý konkrétní bod využití. Pak vrátí doporučené množství, které maximalizuje úspory. V následujícím příkladu graf ukazuje maximální úsporu při počtu 68. Následně se úspory snižují, takže API doporučuje 68.

![Diagram znázorňující maximální úspory](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>Další očekávané chování rozhraní API

- Rozhraní API zobrazuje možné úspory se službou [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) pro Windows v případě využití dané výhody. Pokud se výhoda nevyužije, budou doporučení rozhraní API vycházet ze základních cen Windows. Pokud máte Zvýhodněné hybridní využití Azure k dispozici, zvažte jeho použití ke zvýšení úspor.
- Když použijete shrnutí za sedm dní, je možné, že nedostanete doporučení pro případ, kdy se virtuální počítače vypnou na déle než jeden den.

## <a name="next-steps"></a>Další kroky
- Přečtěte si o tom, [Jak se na virtuální počítače uplatňuje sleva za rezervaci Azure](../manage/understand-vm-reservation-charges.md).
