---
title: zahrnout soubor
description: zahrnout soubor
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: 9fcaca3f89217f649eb970ec70514a5735222782
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854075"
---
Pro každý typ objektu Azure Policy existuje maximální počet. Položka _Scope_ znamená buď předplatné, nebo [skupinu pro správu](../articles/governance/management-groups/overview.md).

| Kde | Co | Maximální počet |
|---|---|---|
| Obor | Definice zásad | 500 |
| Obor | Definice iniciativ | 200 |
| Tenant | Definice iniciativ | 2,500 |
| Obor | Přiřazení zásad nebo iniciativ | 200 |
| Definice zásady | Parametry | 20 |
| Definice iniciativy | Zásady | 1000 |
| Definice iniciativy | Parametry | 100 |
| Přiřazení zásad nebo iniciativ | Vyloučení (notScopes) | 400 |
| Pravidlo zásad | Vnořené podmíněné výrazy | 512 |
| Úloha nápravy | Zdroje a prostředky | 500 |
