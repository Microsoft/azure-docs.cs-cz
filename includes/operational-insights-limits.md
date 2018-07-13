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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755587"
---
Pro prostředky Log Analytics na jedno předplatné platí tato omezení:

| Prostředek | Výchozí omezení | Komentáře
| --- | --- | --- |
| Počet bezplatných pracovních prostorů na předplatné | 10 | Toto omezení nejde zvýšit. |
| Počet placených pracovních prostorů na předplatné | neuvedeno | Jste omezení počtem prostředků v rámci skupiny prostředků a počtem skupin prostředků na předplatné. | 

>[!NOTE]
>Od 2. dubnem 2018, budou automaticky využívat nové pracovní prostory v rámci nového předplatného *Per GB* cenový plán.  Pro existující předplatné vytvořené před 2. dubnem nebo předplatné, které se vázalo na existující registraci smlouvy Enterprise budete pokračovat, výběr mezi třemi cenovými úrovněmi pro nové pracovní prostory. 
>

Na pracovní prostory Log Analytics se vztahují na následující omezení:

|  | Free | Standard | Premium | Standalone | OMS | za GB |
| --- | --- | --- | --- | --- | --- |--- |
| Objem shromážděných dat za den |500 MB<sup>1</sup> |Žádný |Žádný | Žádný | Žádný | Žádný
| Doba uchování dat |7 dní |1 měsíc |12 měsíců | 1 měsíc<sup>2</sup> | 1 měsíc<sup>2</sup>| 1 měsíc<sup>2</sup>|

<sup>1</sup> Když zákazník dosáhne svého denního limitu přenosu dat (500 MB), analýza dat se zastaví a bude pokračovat na začátku dalšího dne. Den vychází ze standardu UTC.

<sup>2</sup> období uchovávání dat pro samostatnou, OMS a za GB cenové plány je možné zvýšit prodloužit na 730 dnů.

| Kategorie | Omezení | Komentáře
| --- | --- | --- |
| Rozhraní API kolekce dat | Maximální velikost pro jeden příspěvek je 30 MB.<br>Maximální velikost pro hodnoty pole je 32 kB. | Rozdělte větší svazky na více příspěvků.<br>Pole delší než 32 kB se oříznou. |
| Search API | 5000 záznamů vrácených pro neagregovaná data<br>500000 záznamů vrácených pro agregovaná data | Agregovaná data znamenají vyhledávání, které zahrnuje příkaz `summarize`.
 
