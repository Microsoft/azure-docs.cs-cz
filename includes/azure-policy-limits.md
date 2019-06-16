---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133781"
---
Je maximální počet pro každý typ objektu pro Azure Policy. Položka _Obor_ udává buď předplatné, nebo [skupinu pro správu](../articles/governance/management-groups/overview.md).

| Kde | Co | Maximální počet |
|---|---|---|
| Scope | Definice zásad | 500 |
| Scope | Definice iniciativ | 100 |
| Tenant | Definice iniciativ | 1 000 |
| Scope | Přiřazení zásady nebo iniciativa | 100 |
| Definice zásady | Parametry | 20 |
| Definice iniciativy | Zásady | 100 |
| Definice iniciativy | Parametry | 100 |
| Přiřazení zásady nebo iniciativa | Vyloučení (notScopes) | 400 |
| Pravidlo zásad | Vnořené podmínky | 512 |
