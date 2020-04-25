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
ms.openlocfilehash: 5992726893b722b0aa46c976a0167793f5ee6bb4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131600"
---
Následující omezení platí pro Azure Event Grid systémová témata a vlastní témata, *nikoli* domény událostí.

| Prostředek | Omezení |
| --- | --- |
| Vlastní témata pro každé předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Míra publikování pro vlastní téma (příchozí) | 5 000 událostí za sekundu na téma |
| Žádosti o publikování | 250 za sekundu |
| Velikost události | 1 MB. Operace se účtují v 64 KB, ale i když se to bude zvyšovat. Události, které překročí 64 KB, budou účtovat poplatky za operace, jako by se jednalo o více událostí. Například událost, která je 130 KB, by mohla způsobit operace, jako by se jednalo o 3 samostatné události.  |

Následující omezení platí pouze pro domény událostí.

| Prostředek | Omezení |
| --- | --- |
| Témata na doménu události | 100 000 |
| Odběry událostí na téma v rámci domény | 500 |
| Odběry událostí oboru domény | 50 |
| Frekvence publikování pro doménu události (příchozí) | 5 000 událostí za sekundu |
| Žádosti o publikování | 250 za sekundu |
| Domény událostí na předplatné Azure | 100 |