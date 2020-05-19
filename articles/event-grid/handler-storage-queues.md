---
title: Fronta úložiště jako obslužná rutina události pro Azure Event Grid události
description: Popisuje, jak můžete používat fronty úložiště Azure jako obslužné rutiny událostí pro Azure Event Grid události.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598539"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Fronta úložiště jako obslužná rutina události pro Azure Event Grid události
Obslužná rutina události je místo, kam se událost posílá. Obslužná rutina provede několik dalších akcí zpracování události. Několik služeb Azure se automaticky nakonfiguruje tak, aby zpracovávala události a **Azure Queue Storage** je jednou z nich. 

Pomocí **Queue Storage** můžete přijímat události, které je potřeba načíst. Úložiště Queue můžete použít, když máte dlouho běžící proces, který trvá příliš dlouho, než odpoví. Při odesílání událostí do služby Queue Storage může aplikace získat a zpracovat události podle vlastního plánu.

## <a name="tutorials"></a>Kurzy
V následujícím kurzu najdete příklad použití úložiště Queue jako obslužné rutiny události. 

|Nadpis  |Popis  |
|---------|---------|
| [Rychlý Start: směrování vlastních událostí do Azure Queue Storage pomocí rozhraní příkazového řádku Azure a Event Grid](custom-event-to-queue-storage.md) | Popisuje, jak odesílat vlastní události do úložiště fronty. |

## <a name="next-steps"></a>Další kroky
Seznam podporovaných obslužných rutin událostí naleznete v článku [obslužné rutiny událostí](event-handlers.md) . 
