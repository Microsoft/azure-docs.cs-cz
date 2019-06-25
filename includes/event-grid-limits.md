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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66376929"
---
Následující omezení platí pro vlastní témata a témata služby Azure Event Grid systém *není* událostí domény.

| Resource | Omezení |
| --- | --- |
| Vlastní témata na předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Publikování míra vlastního tématu (příchozí) | 5 000 událostí za sekundu na téma |
| Žádostí o publikování | 250 za sekundu |
| Velikost události | Podpora pro 64 KB obecně dostupná (GA). Podpora pro 1 MB je aktuálně ve verzi preview. |

Následující omezení platí pro pouze události domén.

| Resource | Omezení |
| --- | --- |
| Témata události doménu | 1 000 ve verzi public preview |
| Odběry událostí na téma v rámci domény | 50 ve verzi public preview |
| Odběry událostí pro obor domény | 50 ve verzi public preview |
| Publikování frekvence událostí domény (příchozí) | 5 000 událostí za sekundu ve verzi public preview |
| Žádostí o publikování | 250 za sekundu |