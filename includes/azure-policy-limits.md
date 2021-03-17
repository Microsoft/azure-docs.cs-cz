---
title: zahrnout soubor
description: zahrnout soubor
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91605991"
---
Pro každý typ objektu Azure Policy existuje maximální počet. V případě definic představuje položka _Scope_ [skupinu pro správu](../articles/governance/management-groups/overview.md) nebo předplatné.
V případě přiřazení a výjimek znamená zadání _rozsahu_ [skupinu pro správu](../articles/governance/management-groups/overview.md), předplatné, skupinu prostředků nebo jednotlivý prostředek.

| Kde | Co | Maximální počet |
|---|---|---|
| Rozsah | Definice zásad | 500 |
| Rozsah | Definice iniciativ | 200 |
| Tenant | Definice iniciativ | 2,500 |
| Rozsah | Přiřazení zásad nebo iniciativ | 200 |
| Rozsah | Stanovených | 1000 |
| Definice zásady | Parametry | 20 |
| Definice iniciativy | Zásady | 1000 |
| Definice iniciativy | Parametry | 100 |
| Přiřazení zásad nebo iniciativ | Vyloučení (notScopes) | 400 |
| Pravidlo zásad | Vnořené podmíněné výrazy | 512 |
| Úloha nápravy | Zdroje a prostředky | 500 |
