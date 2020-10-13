---
title: Témata Azure Event Grid partnerů
description: Posílání událostí od partnerů Event Grid SaaS a PaaS od jiných výrobců přímo ke službám Azure pomocí Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e56055c55a3b30c5d13736b9838257f3c0bbec10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87831902"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Témata o partnerech v Azure Event Grid (verze Preview)
Pomocí témat partnerů můžete ke Azure Event Grid připojit zdroje událostí třetích stran přímo. Tato integrace vám umožní přihlásit se k odběru událostí od partnerů stejným způsobem, jakým se přihlašujete k odběru událostí ze služeb Azure. 

## <a name="available-partners"></a>Dostupní partneři
První partner dostupný prostřednictvím témat Event Grid partnerů je Auth0. K připojení účtů Auth0 a Azure můžete použít [téma Auth0 partner](auth0-overview.md) . Integrace umožňuje reagovat na události Auth0 v reálném čase, zaznamenávat je a monitorovat.

Pokud [to chcete vyzkoušet](auth0-how-to.md), přihlaste se ke svému účtu Auth0 a vytvořte integraci Event Grid. Po výběru položky **vytvořit** v Auth0 se ve vašem účtu Azure zobrazí nedokončené téma Auth0. Vyberte **aktivovat**a můžete vytvořit Event Grid odběry k směrování, filtrování a doručování událostí stejně jako všechny ostatní zdroje událostí.

## <a name="pricing"></a>Ceny
Tématům partnerů se účtují stejné provozní frekvence jako systémová témata.

## <a name="limits"></a>Omezení
Témata partnerů jsou ve verzi Public Preview. V rámci veřejné verze Preview podléhají témata pro partnery [stejná omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) jako systémová témata a vlastní témata.

## <a name="how-do-i-become-an-event-grid-partner"></a>Návody se stanou Event Gridm partnerem?
Infrastruktura vytvořená za účelem podpory tohoto spuštění umožňuje novým partnerům snadno a rychle integrovat své schopnosti události do Event Grid. Další informace najdete v dokumentaci k [připojování partnerů](partner-onboarding-overview.md).

## <a name="next-steps"></a>Další kroky

- [Téma Auth0 partner](auth0-overview.md)
- [Jak používat téma Auth0 partner](auth0-how-to.md)
- [Staňte se Event Grid partnerem](partner-onboarding-overview.md)