---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/16/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0e5d1214a8e1af8299cb40d1a3b31ff6eafc8c5c
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "40184803"
---
Maximální počet každého typu objektů je v Azure Policy omezený. Položka _Obor_ udává buď předplatné, nebo [skupinu pro správu](../articles/azure-resource-manager/management-groups-overview.md).

| Kde | Co | Maximální počet |
|---|---|---|
| Rozsah | Definice zásad | 250 |
| Rozsah | Definice iniciativ | 100 |
| Tenant | Definice iniciativ | 1000 |
| Rozsah | Přiřazení zásad nebo iniciativ | 100 |
| Definice zásady | Parametry | 20 |
| Definice iniciativy | Zásady | 100 |
| Definice iniciativy | Parametry | 100 |
| Přiřazení zásad nebo iniciativ | Vyloučení (notScopes) | 100 |
| Pravidlo zásad | Vnořené podmínky | 512 |
