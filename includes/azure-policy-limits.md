---
title: zahrnout soubor
description: zahrnout soubor
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: 08183cc2cbe2fce83bc7347da22308931b99dcc1
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010983"
---
Pro každý typ objektu Azure Policy existuje maximální počet. Položka _Scope_ znamená buď předplatné, nebo [skupinu pro správu](../articles/governance/management-groups/overview.md).

| Kde | Co | Maximální počet |
|---|---|---|
| Rozsah | Definice zásad | 500 |
| Rozsah | Definice iniciativ | 200 |
| Tenant | Definice iniciativ | 2,500 |
| Rozsah | Přiřazení zásad nebo iniciativ | 200 |
| Definice zásady | Parametry | 20 |
| Definice iniciativy | Zásady | 1000 |
| Definice iniciativy | Parametry | 100 |
| Přiřazení zásad nebo iniciativ | Vyloučení (notScopes) | 400 |
| Pravidlo zásad | Vnořené podmíněné výrazy | 512 |
| Úloha nápravy | Zdroje a prostředky | 500 |
