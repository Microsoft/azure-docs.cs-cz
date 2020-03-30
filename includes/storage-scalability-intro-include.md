---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75477145"
---
Tento odkaz podrobně popisuje škálovatelnost a výkonnostní cíle pro Azure Storage. Škálovatelnost a výkonnostní cíle uvedené zde jsou high-end cíle, ale jsou dosažitelné. Ve všech případech závisí rychlost požadavků a šířka pásma dosažená vaším účtem úložiště na velikosti uložených objektů, využitých vzorcích přístupu a typu úlohy, kterou vaše aplikace provádí.

Ujistěte se, že otestujete službu a zjistěte, zda její výkon splňuje vaše požadavky. Pokud je to možné, vyhněte se náhlým špičkám v rychlosti provozu a ujistěte se, že provoz je dobře distribuován mezi oddíly.

Když vaše aplikace dosáhne limitu toho, co oddíl zvládne pro vaše úlohy, Azure Storage začne vracet kód chyby 503 (Server Busy) nebo kód chyby 500 (Operace časový limit) odpovědi. Pokud dochází k chybám 503, zvažte úpravu aplikace tak, aby používala exponenciální zásady backoff pro opakované pokusy. Exponenciální backoff umožňuje zatížení oddílu snížit a zmírnit špičky v provozu do tohoto oddílu.
