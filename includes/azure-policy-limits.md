---
title: zahrnout soubor
description: zahrnout soubor
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/25/2020
ms.author: dacoulte
ms.openlocfilehash: 19b43c857058b3af50db3bc1b42ec1ae558ee884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272927"
---
Pro každý typ objektu pro Azure Policy je maximální počet. Položkou _oboru_ se rozumí buď předplatné, nebo [skupina pro správu](../articles/governance/management-groups/overview.md).

| Kde | Co | Maximální počet |
|---|---|---|
| Rozsah | Definice zásad | 500 |
| Rozsah | Definice iniciativ | 100 |
| Tenant | Definice iniciativ | 1 000 |
| Rozsah | Přiřazení zásad nebo iniciativ | 100 |
| Definice zásady | Parametry | 20 |
| Definice iniciativy | Zásady | 100 |
| Definice iniciativy | Parametry | 100 |
| Přiřazení zásad nebo iniciativ | Vyloučení (notScopes) | 400 |
| Pravidlo zásad | Vnořené podmínky | 512 |
| Úkol nápravy | Prostředky | 1000 |
