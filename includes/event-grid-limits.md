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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845968"
---
Následující omezení platí pro témata systému Azure Event Grid a vlastní témata, *ne* domény událostí.

| Prostředek | Omezení |
| --- | --- |
| Vlastní témata podle předplatného Azure | 100 |
| Odběry událostí podle témat | 500 |
| Rychlost publikování pro vlastní téma (příchozí přenos dat) | 5 000 událostí za sekundu na téma |
| Publikovat žádosti | 250 za sekundu |
| Velikost události | 1 MB (nabitá jako více událostí 64 KB) |

Následující omezení platí pouze pro domény událostí.

| Prostředek | Omezení |
| --- | --- |
| Témata podle domény události | 100 000 |
| Odběry událostí podle témat v rámci domény | 500 |
| Odběry událostí oboru domény | 50 |
| Rychlost publikování pro doménu události (příchozí přenos dat) | 5 000 událostí za sekundu |
| Publikovat žádosti | 250 za sekundu |
| Domény událostí na předplatné Azure | 100 |