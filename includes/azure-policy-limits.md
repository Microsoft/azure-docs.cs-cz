---
title: zahrnout soubor
description: zahrnout soubor
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: e22594a50a9c0d814ef7d66443f8253f5832cb1d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038504"
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
| Úloha nápravy | Zdroje a prostředky | 1000 |
