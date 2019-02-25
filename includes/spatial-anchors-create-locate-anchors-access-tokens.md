---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752970"
---
### <a name="access-tokens"></a>Přístupové tokeny

Přístupové tokeny jsou robustnější metodu k ověření pomocí Azure prostorových ukotvení. Zejména při přípravě aplikace pro produkční nasazení. Souhrn tento přístup je nastavení, které klientské aplikace můžete bezpečně ověřit s back-end služby. Vaše rozhraní back-end služby v AAD za běhu a službou Azure prostorových kotvy STS požádat o Token k přístupu. Tento token je pak doručit do klientské aplikace a v sadě SDK používá k ověřování pomocí Azure prostorových ukotvení.
