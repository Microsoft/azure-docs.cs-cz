---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 57cec39bde460c6079091490acf541761c61e003
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553430"
---
Je maximální počet pro každý typ objektu pro Azure Policy. Položka _Obor_ udává buď předplatné, nebo [skupinu pro správu](../articles/governance/management-groups/overview.md).

| Kde | Co | Maximální počet |
|---|---|---|
| Rozsah | Definice zásad | 250 |
| Rozsah | Definice iniciativ | 100 |
| Tenant | Definice iniciativ | 1 000 |
| Rozsah | Přiřazení zásady nebo iniciativa | 100 |
| Definice zásady | Parametry | 20 |
| Definice iniciativy | Zásady | 100 |
| Definice iniciativy | Parametry | 100 |
| Přiřazení zásady nebo iniciativa | Vyloučení (notScopes) | 250 |
| Pravidlo zásad | Vnořené podmínky | 512 |
