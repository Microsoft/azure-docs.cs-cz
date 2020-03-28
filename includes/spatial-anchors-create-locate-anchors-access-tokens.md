---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67174702"
---
### <a name="access-tokens"></a>Přístupové tokeny

Přístupové tokeny jsou robustnější metodou ověřování pomocí prostorových ukotvení Azure. Zejména při přípravě aplikace pro produkční nasazení. Souhrn tohoto přístupu je nastavení back-endové služby, kterou může klientská aplikace bezpečně ověřit. Vaše back-endové služby rozhraní s AAD za běhu a azure prostorové kotvy zabezpečené tokeny služby požádat o přístupový token. Tento token se pak doručuje do klientské aplikace a používá se v sadě SDK k ověření pomocí prostorových kotvy Azure.
