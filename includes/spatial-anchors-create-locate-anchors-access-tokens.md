---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232706"
---
### <a name="access-tokens"></a>Přístupové tokeny

Přístupové tokeny jsou robustnější metodu k ověření pomocí Azure prostorových ukotvení. Zejména při přípravě aplikace pro produkční nasazení. Souhrn tento přístup je nastavení, které klientské aplikace můžete bezpečně ověřit s back-end služby. Vaše rozhraní back-end služby v AAD za běhu a službou Azure prostorových kotvy zabezpečení tokenu požádat o Token k přístupu. Tento token je pak doručit do klientské aplikace a v sadě SDK používá k ověřování pomocí Azure prostorových ukotvení.
