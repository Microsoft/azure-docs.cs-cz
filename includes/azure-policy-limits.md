---
title: zahrnout soubor
description: zahrnout soubor
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/22/2020
ms.author: dacoulte
ms.openlocfilehash: 2707523947cae77dd800dd5f560ebe1c47f3893a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905967"
---
Pro každý typ objektu Azure Policy existuje maximální počet. Položka _Scope_ znamená buď předplatné, nebo [skupinu pro správu](../articles/governance/management-groups/overview.md).

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
