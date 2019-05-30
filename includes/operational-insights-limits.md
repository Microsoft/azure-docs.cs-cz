---
title: zahrnout soubor
description: zahrnout soubor
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238286"
---
Následující omezení platí pro prostředky Azure Log Analytics na jedno předplatné.

| Resource | Výchozí omezení | Komentáře
| --- | --- | --- |
| Počet bezplatných pracovních prostorů na předplatné | 10 | Toto omezení nejde zvýšit. |
| Počet placených pracovních prostorů na předplatné | neuvedeno | Co vás omezuje počet prostředků v rámci skupiny prostředků a počtem skupin prostředků na předplatné. | 

>[!NOTE]
>Od 2. dubnem 2018, budou automaticky využívat nové pracovní prostory v novém předplatném *Per GB* cenový plán. Pro existující předplatné vytvořené před 2. dubnem nebo předplatné, které se vázalo na existující registraci smlouvy Enterprise můžete stále vybírat ze tří cenových úrovní pro nové pracovní prostory. 
>

Následující omezení platí pro každý pracovní prostor Log Analytics.

|  | Free | Standard | Premium | Standalone | OMS | za GB |
| --- | --- | --- | --- | --- | --- |--- |
| Objem shromážděných dat za den |500 MB<sup>1</sup> |Žádný |Žádný | Žádný | Žádný | Žádný
| Doba uchování dat |7 dní |1 měsíc |12 měsíců | 1 měsíc<sup>2</sup> | 1 měsíc<sup>2</sup>| 1 měsíc<sup>2</sup>|

<sup>1</sup>když zákazník dosáhne svého denního limitu přenosu dat 500 MB, analýza dat se zastaví a bude pokračovat na začátku dalšího dne. Den vychází ze standardu UTC.

<sup>2</sup>období uchovávání dat pro samostatnou, OMS a za GB cenové plány je možné zvýšit prodloužit na 730 dnů.

| Category | Limits | Komentáře
| --- | --- | --- |
| Rozhraní API kolekce dat | Maximální velikost pro jeden příspěvek je 30 MB.<br>Maximální velikost pro hodnoty pole je 32 KB. | Rozdělte větší svazky na více příspěvků.<br>Pole delší než 32 kB se oříznou. |
| Search API | 5000 záznamů vrácených pro neagregovaná data.<br>500 000 záznamů pro agregovaná data. | Agregovaná data znamenají vyhledávání, která zahrnuje `summarize` příkazu.
 
