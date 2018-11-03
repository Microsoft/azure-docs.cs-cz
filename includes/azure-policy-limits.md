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
ms.openlocfilehash: 0a54dfdb810ea578c1e7c8fcc7ca0343e72164ae
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50964558"
---
Maximální počet každého typu objektů je v Azure Policy omezený. Položka _Obor_ udává buď předplatné, nebo [skupinu pro správu](../articles/governance/management-groups/overview.md).

| Kde | Co | Maximální počet |
|---|---|---|
| Rozsah | Definice zásad | 250 |
| Rozsah | Definice iniciativ | 100 |
| Tenant | Definice iniciativ | 1000 |
| Rozsah | Přiřazení zásad nebo iniciativ | 100 |
| Definice zásady | Parametry | 20 |
| Definice iniciativy | Zásady | 100 |
| Definice iniciativy | Parametry | 100 |
| Přiřazení zásad nebo iniciativ | Vyloučení (notScopes) | 250 |
| Pravidlo zásad | Vnořené podmínky | 512 |
