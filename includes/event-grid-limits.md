---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2425d9455606f5eabba4b89cfa238b7a928be30e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285781"
---
####

Následující omezení platí pro vlastní témata a témata služby Event Grid systém *není* událostí domény.

| Prostředek | škálování |
| --- | --- |
| Vlastní témata na předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Publikování míra vlastního tématu (příchozí) | 5 000 událostí za sekundu na téma |

####

Následující omezení platí pouze k doménám události.

| Prostředek | škálování |
| --- | --- |
| Témata události doménu | 1 000 ve verzi public preview |
| Odběry událostí na téma v rámci domény | 50 ve verzi public preview |
| Obor domény odběry událostí | 50 ve verzi public preview |
| Publikování frekvence událostí domény (příchozí) | 5 000 událostí za sekundu ve verzi public preview |