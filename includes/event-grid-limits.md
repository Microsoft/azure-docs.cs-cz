---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "66376929"
---
Následující omezení platí pro Azure Event Grid systémová témata a vlastní témata, *nikoli* domény událostí.

| Resource | Omezení |
| --- | --- |
| Vlastní témata pro každé předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Míra publikování pro vlastní téma (příchozí) | 5 000 událostí za sekundu na téma |
| Žádosti o publikování | 250 za sekundu |
| Velikost události | Podpora 64 KB v obecné dostupnosti (GA). Podpora 1 MB je aktuálně ve verzi Preview. |

Následující omezení platí pouze pro domény událostí.

| Resource | Omezení |
| --- | --- |
| Témata na doménu události | 1 000 během veřejné verze Preview |
| Odběry událostí na téma v rámci domény | 50 během veřejné verze Preview |
| Odběry událostí oboru domény | 50 během veřejné verze Preview |
| Frekvence publikování pro doménu události (příchozí) | 5 000 událostí za sekundu během veřejné verze Preview |
| Žádosti o publikování | 250 za sekundu |