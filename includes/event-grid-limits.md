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
ms.openlocfilehash: ac08a9ab6975039402df44e6a043ee81cf5b222e
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56988070"
---
Následující omezení platí pro vlastní témata a témata služby Event Grid systém *není* událostí domény.

| Prostředek | Omezení |
| --- | --- |
| Vlastní témata na předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Publikování míra vlastního tématu (příchozí) | 5 000 událostí za sekundu na téma |

Následující omezení platí pouze k doménám události.

| Prostředek | Omezení |
| --- | --- |
| Témata události doménu | 1 000 ve verzi public preview |
| Odběry událostí na téma v rámci domény | 50 ve verzi public preview |
| Obor domény odběry událostí | 50 ve verzi public preview |
| Publikování frekvence událostí domény (příchozí) | 5 000 událostí za sekundu ve verzi public preview |