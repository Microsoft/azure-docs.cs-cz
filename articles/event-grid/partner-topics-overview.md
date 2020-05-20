---
title: Témata Azure Event Grid partnerů
description: Posílání událostí od partnerů Event Grid SaaS a PaaS od jiných výrobců přímo ke službám Azure pomocí Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691121"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Témata o partnerech v Azure Event Grid (verze Preview)
Témata týkající se partnerů vám umožní propojit zdroje událostí třetích stran přímo s Event Grid. Tato integrace vám umožní přihlásit se k odběru událostí od partnerů stejným způsobem, jakým se přihlašujete k odběru událostí ze služeb Azure. 

## <a name="available-partners"></a>Dostupní partneři
První partner dostupný prostřednictvím témat Event Grid partnerů je Auth0. [Téma Auth0 partner](auth0-overview.md) vám umožní propojit účty Auth0 a Azure. Integrace s cílem reagovat, protokolovat a monitorovat události Auth0 v reálném čase.

[Vyzkoušejte](auth0-how-to.md) si to ještě dnes tím, že se přihlásíte ke svému účtu Auth0 a vytvoříte integraci Event Grid. Po kliknutí na vytvořit v Auth0 se ve vašem účtu Azure zobrazí nedokončené téma Auth0. Klikněte na aktivovat a můžete vytvořit odběry událostí, směrovat, filtrovat a doručovat události stejně jako všechny ostatní zdroje událostí.

## <a name="pricing"></a>Ceny
Tématům partnerů se účtují stejné provozní frekvence jako systémová témata.

## <a name="limits"></a>Omezení
Témata o partnerech jsou v Public Preview. V rámci veřejné verze Preview podléhají témata pro partnery [stejná omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) jako systémová témata a vlastní témata.

## <a name="how-do-i-become-an-event-grid-partner"></a>Návody se stanou Event Gridm partnerem?
Infrastruktura vytvořená za účelem podpory tohoto spuštění umožňuje novým partnerům snadno a rychle integrovat své schopnosti události do Event Grid. Další informace najdete v [dokumentaci k připojování partnerů](partner-onboarding-overview.md) .

## <a name="next-steps"></a>Další kroky

- [Téma Auth0 partner](auth0-overview.md)
- [Jak používat téma Auth0 partner](auth0-how-to.md)
- [Staňte se Event Grid partnerem](partner-onboarding-overview.md)