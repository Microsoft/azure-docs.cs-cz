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
ms.openlocfilehash: 66cd09df128d454973d008adf4ffc5dd1017a18f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
Pro prostředky Log Analytics na jedno předplatné platí tato omezení:

| Prostředek | Výchozí omezení | Komentáře
| --- | --- | --- |
| Počet bezplatných pracovních prostorů na předplatné | 10 | Toto omezení nejde zvýšit. |
| Počet placených pracovních prostorů na předplatné | neuvedeno | Jste omezení počtem prostředků v rámci skupiny prostředků a počtem skupin prostředků na předplatné. | 

>[!NOTE]
>Od verze 2. dubna 2018, budou automaticky používat nové pracovní prostory v nové předplatné *za GB* ceny plánu.  Pro existující odběry vytvořil před duben 2, nebo předplatné, které bylo vázané na existující EA zápisu můžete pokračovat v výběru mezi tři cenové úrovně pro nové pracovní prostory. 
>

Na pracovní prostory Log Analytics se vztahují na následující omezení:

|  | Free | Standard | Premium | Standalone | OMS | za GB |
| --- | --- | --- | --- | --- | --- |--- |
| Objem shromážděných dat za den |500 MB<sup>1</sup> |Žádný |Žádný | Žádný | Žádný | Žádný
| Doba uchování dat |7 dní |1 měsíc |12 měsíců | 1 měsíc<sup>2</sup> | 1 měsíc<sup>2</sup>| 1 měsíc<sup>2</sup>|

<sup>1</sup> Když zákazník dosáhne svého denního limitu přenosu dat (500 MB), analýza dat se zastaví a bude pokračovat na začátku dalšího dne. Den vychází ze standardu UTC.

<sup>2</sup> období uchovávání dat pro samostatnou, OMS a GB za cenových plánů je možné zvýšit na 730 dnů.

| Kategorie | Omezení | Komentáře
| --- | --- | --- |
| Rozhraní API kolekce dat | Maximální velikost pro jeden příspěvek je 30 MB.<br>Maximální velikost pro hodnoty pole je 32 kB. | Rozdělte větší svazky na více příspěvků.<br>Pole delší než 32 kB se oříznou. |
| Search API | 5000 záznamů vrácených pro neagregovaná data<br>500000 záznamů vrácených pro agregovaná data | Agregovaná data znamenají vyhledávání, které zahrnuje příkaz `summarize`.
 
