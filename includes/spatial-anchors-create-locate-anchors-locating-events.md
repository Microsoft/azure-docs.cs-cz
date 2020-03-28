---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "71180041"
---
Po vytvoření sledovacího `AnchorLocated` procesu se událost vypálí pro každou požadovanou kotvu. Tato událost je vyvolána, když je umístěna kotva nebo pokud kotva nemůže být umístěna. Pokud k této situaci dojde, bude ve stavu uveden důvod. Poté, co jsou zpracovány všechny kotvy pro sledovací `LocateAnchorsCompleted` proces, nalezeny nebo nebyly nalezeny, bude událost vystřelit. Existuje limit 35 identifikátorů na sledovací proces. 
