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
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887756"
---
Následující omezení platí pro Azure Event Grid systémová témata a vlastní témata, *nikoli* domény událostí.

| Prostředek | škálování |
| --- | --- |
| Vlastní témata pro každé předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Míra publikování pro vlastní téma (příchozí) | 5 000 událostí za sekundu na téma |
| Žádosti o publikování | 250 za sekundu |
| Velikost události | Podpora 64 KB v obecné dostupnosti (GA). Podpora 1 MB je aktuálně ve verzi Preview. |

Následující omezení platí pouze pro domény událostí.

| Prostředek | škálování |
| --- | --- |
| Témata na doménu události | 100 000 |
| Odběry událostí na téma v rámci domény | 500 |
| Odběry událostí oboru domény | 50 |
| Frekvence publikování pro doménu události (příchozí) | 5 000 událostí za sekundu |
| Žádosti o publikování | 250 za sekundu |
| Domény událostí na předplatné Azure | 100 |