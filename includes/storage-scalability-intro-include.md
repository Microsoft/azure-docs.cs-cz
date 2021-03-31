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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75477145"
---
Tento odkaz podrobně popisuje škálovatelnost a výkonnostní cíle pro Azure Storage. Zde uvedené cíle škálovatelnosti a výkonu jsou špičkové cíle, ale lze je dosažitelný. Frekvence požadavků a šířka pásma dosažené vaším účtem úložiště závisí ve všech případech na velikosti uložených objektů, využívaných vzorech přístupu a typu zatížení, které vaše aplikace provádí.

Ujistěte se, že jste službu otestovali, abyste zjistili, jestli její výkon vyhovuje vašim požadavkům. Pokud je to možné, vyhněte se náhlým špičkám v oblasti provozu a zajistěte, aby byl provoz v různých oddílech dobře rozložený.

Když vaše aplikace dosáhne limitu toho, co může oddíl zpracovat pro vaše zatížení, Azure Storage začne vracet kód chyby 503 (zaneprázdněný serverem) nebo s kódem chyby 500 (časový limit operace). Pokud dojde k chybám 503, zvažte úpravu aplikace tak, aby pro opakované pokusy používala exponenciální omezení rychlosti zásady. Exponenciální omezení rychlosti umožňuje snížit zatížení oddílu a rozsvítit špičky v provozu do tohoto oddílu.
