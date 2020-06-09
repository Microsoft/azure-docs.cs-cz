---
title: Témata Azure Event Grid partnerů
description: Posílání událostí od partnerů Event Grid SaaS a PaaS od jiných výrobců přímo ke službám Azure pomocí Azure Event Grid.
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: femila
ms.openlocfilehash: 5327efea8af734c723ba76d1a00b72c08f5c88b9
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560274"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Témata o partnerech v Azure Event Grid (verze Preview)
Pomocí témat partnerů můžete ke Azure Event Grid připojit zdroje událostí třetích stran přímo. Tato integrace vám umožní přihlásit se k odběru událostí od partnerů stejným způsobem, jakým se přihlašujete k odběru událostí ze služeb Azure. 

## <a name="available-partners"></a>Dostupní partneři
První partner dostupný prostřednictvím témat Event Grid partnerů je Auth0. K připojení účtů Auth0 a Azure můžete použít [téma Auth0 partner](auth0-overview.md) . Integrace umožňuje reagovat na události Auth0 v reálném čase, zaznamenávat je a monitorovat.

Pokud [to chcete vyzkoušet](auth0-how-to.md), přihlaste se ke svému účtu Auth0 a vytvořte integraci Event Grid. Po výběru položky **vytvořit** v Auth0 se ve vašem účtu Azure zobrazí nedokončené téma Auth0. Vyberte **aktivovat**a můžete vytvořit Event Grid odběry k směrování, filtrování a doručování událostí stejně jako všechny ostatní zdroje událostí.

## <a name="pricing"></a>Ceny
Tématům partnerů se účtují stejné provozní frekvence jako systémová témata.

## <a name="limits"></a>Omezení
Témata partnerů jsou ve verzi Public Preview. V rámci veřejné verze Preview podléhají témata pro partnery [stejná omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) jako systémová témata a vlastní témata.

## <a name="how-do-i-become-an-event-grid-partner"></a>Návody se stanou Event Gridm partnerem?
Infrastruktura vytvořená za účelem podpory tohoto spuštění umožňuje novým partnerům snadno a rychle integrovat své schopnosti události do Event Grid. Další informace najdete v dokumentaci k [připojování partnerů](partner-onboarding-overview.md).

## <a name="next-steps"></a>Další kroky

- [Téma Auth0 partner](auth0-overview.md)
- [Jak používat téma Auth0 partner](auth0-how-to.md)
- [Staňte se Event Grid partnerem](partner-onboarding-overview.md)