---
title: zahrnout soubor
description: zahrnout soubor
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: e9faea1d5913a19dfdeff662e26992529dc1b22d
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84466884"
---
Pro každý typ objektu Azure Policy existuje maximální počet. Položka _Scope_ znamená buď předplatné, nebo [skupinu pro správu](../articles/governance/management-groups/overview.md).

| Kde | Co | Maximální počet |
|---|---|---|
| Rozsah | Definice zásad | 500 |
| Rozsah | Definice iniciativ | 100 |
| Tenant | Definice iniciativ | 2,500 |
| Rozsah | Přiřazení zásad nebo iniciativ | 100 |
| Definice zásady | Parametry | 20 |
| Definice iniciativy | Zásady | 100 |
| Definice iniciativy | Parametry | 100 |
| Přiřazení zásad nebo iniciativ | Vyloučení (notScopes) | 400 |
| Pravidlo zásad | Vnořené podmíněné výrazy | 512 |
| Úloha nápravy | Zdroje a prostředky | 500 |
