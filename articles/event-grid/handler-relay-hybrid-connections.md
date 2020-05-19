---
title: Přenos hybridního připojení jako obslužné rutiny události pro Azure Event Grid události
description: Popisuje, jak můžete použít Azure Relay Hybrid Connections jako obslužné rutiny událostí pro Azure Event Grid události.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0631724e688a71d7e9685f5f0ad738d81e2a8034
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598427"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Přenos hybridního připojení jako obslužné rutiny události pro Azure Event Grid události
Obslužná rutina události je místo, kam se událost posílá. Obslužná rutina provede několik dalších akcí zpracování události. Několik služeb Azure se automaticky nakonfiguruje tak, aby zpracovával události a **Azure Relay** je jednou z nich. 

Azure **Relay Hybrid Connections** slouží k posílání událostí do aplikací, které jsou v podnikové síti a nemají veřejně přístupný koncový bod.

## <a name="tutorials"></a>Kurzy
V následujícím kurzu najdete příklad použití hybridního připojení Azure Relay jako obslužné rutiny události. 

|Nadpis  |Popis  |
|---------|---------|
| [Kurz: odeslání událostí do hybridního připojení](custom-event-to-hybrid-connection.md) | Odesílá vlastní událost do stávajícího hybridního připojení pro zpracování pomocí aplikace naslouchacího procesu. |

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) . 