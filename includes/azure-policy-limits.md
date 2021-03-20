---
title: zahrnout soubor
description: zahrnout soubor
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91605991"
---
Pro každý typ objektu Azure Policy existuje maximální počet. V případě definic představuje položka _Scope_ [skupinu pro správu](../articles/governance/management-groups/overview.md) nebo předplatné.
V případě přiřazení a výjimek znamená zadání _rozsahu_ [skupinu pro správu](../articles/governance/management-groups/overview.md), předplatné, skupinu prostředků nebo jednotlivý prostředek.

| Kde | Co | Maximální počet |
|---|---|---|
| Obor | Definice zásad | 500 |
| Obor | Definice iniciativ | 200 |
| Tenant | Definice iniciativ | 2,500 |
| Obor | Přiřazení zásad nebo iniciativ | 200 |
| Obor | Stanovených | 1000 |
| Definice zásady | Parametry | 20 |
| Definice iniciativy | Zásady | 1000 |
| Definice iniciativy | Parametry | 100 |
| Přiřazení zásad nebo iniciativ | Vyloučení (notScopes) | 400 |
| Pravidlo zásad | Vnořené podmíněné výrazy | 512 |
| Úloha nápravy | Zdroje informací | 500 |
