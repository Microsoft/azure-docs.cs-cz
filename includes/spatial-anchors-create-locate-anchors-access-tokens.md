---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993076"
---
### <a name="access-tokens"></a>Přístupové tokeny

Přístupové tokeny představují robustnější metodu ověřování pomocí prostorových kotev Azure. Obzvláště při přípravě aplikace pro produkční nasazení. Souhrn tohoto přístupu je nastavit back-end službu, se kterou může klientská aplikace bezpečně ověřit. Rozhraní back-end služby s AAD za běhu a službu tokenů prostorových kotev Azure pro vyžádání přístupového tokenu. Tento token se pak doručí do klientské aplikace a použije se v sadě SDK k ověřování pomocí prostorových kotev Azure.
