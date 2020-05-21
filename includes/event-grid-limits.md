---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83721582"
---
Následující omezení platí pro Azure Event Grid systémová témata a vlastní témata, *nikoli* domény událostí.

| Prostředek | Omezení |
| --- | --- |
| Vlastní témata pro každé předplatné Azure | 100 |
| Odběry událostí na téma | 500 |
| Míra publikování pro vlastní téma (příchozí) | 5 000 událostí za sekundu na téma |
| Velikost události | 1 MB. Operace se účtují v 64 KB, ale i když se to bude zvyšovat. Události, které překročí 64 KB, budou účtovat poplatky za operace, jako by se jednalo o více událostí. Například událost, která je 130 KB, by mohla způsobit operace, jako by se jednalo o 3 samostatné události.  |
| Témata na doménu události | 100 000 |
| Odběry událostí na téma v rámci domény | 500 |
| Odběry událostí oboru domény | 50 |
| Frekvence publikování pro doménu události (příchozí) | 5 000 událostí za sekundu |
| Domény událostí na předplatné Azure | 100 |
| Připojení privátního koncového bodu na téma nebo doménu | 64 | 
| Pravidla brány firewall protokolu IP na téma nebo doménu | 16 | 