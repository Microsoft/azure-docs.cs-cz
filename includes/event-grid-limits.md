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
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845968"
---
Následující omezení platí pro Azure Event Grid systémová témata a vlastní témata, *nikoli* domény událostí.

| Prostředek | škálování |
| --- | --- |
| Vlastní témata pro každé předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Míra publikování pro vlastní téma (příchozí) | 5 000 událostí za sekundu na téma |
| Žádosti o publikování | 250 za sekundu |
| Velikost události | 1 MB (účtuje se jako několik událostí 64 – KB) |

Následující omezení platí pouze pro domény událostí.

| Prostředek | škálování |
| --- | --- |
| Témata na doménu události | 100 000 |
| Odběry událostí na téma v rámci domény | 500 |
| Odběry událostí oboru domény | 50 |
| Frekvence publikování pro doménu události (příchozí) | 5 000 událostí za sekundu |
| Žádosti o publikování | 250 za sekundu |
| Domény událostí na předplatné Azure | 100 |