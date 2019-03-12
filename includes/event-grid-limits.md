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
ms.openlocfilehash: 443db1b4609e62fb7c57de417e42a2b4d0737ada
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553426"
---
Následující omezení platí pro vlastní témata a témata služby Azure Event Grid systém *není* událostí domény.

| Prostředek | Omezení |
| --- | --- |
| Vlastní témata na předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Publikování míra vlastního tématu (příchozí) | 5 000 událostí za sekundu na téma |

Následující omezení platí pro pouze události domén.

| Prostředek | Omezení |
| --- | --- |
| Témata události doménu | 1 000 ve verzi public preview |
| Odběry událostí na téma v rámci domény | 50 ve verzi public preview |
| Odběry událostí pro obor domény | 50 ve verzi public preview |
| Publikování frekvence událostí domény (příchozí) | 5 000 událostí za sekundu ve verzi public preview |