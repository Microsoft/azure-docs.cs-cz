---
title: zahrnout soubor
description: zahrnout soubor
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 06/10/2019
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: c5fedc59c80c68fc222693a67664ef60ddd210a9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133276"
---
Následující omezení platí pro každý pracovní prostor Log Analytics v aktuální založenou na skutečné spotřebě cenové úrovni zavedené v dubnu 2018:

|     | Za GB 2018 |
| --- | --- | 
| Objem shromážděných dat za den | Žádný |
| Doba uchování dat | 30 do 730 dnů<sup>1</sup> |

Následující omezení platí pro každý pracovní prostor Log Analytics nejnovější starší verze cenové úrovně:

|  | Free | Samostatná (za GB) | Za uzel (OMS) |
| --- | --- | --- | --- | --- | --- |--- |
| Objem shromážděných dat za den |500 MB<sup>2</sup> |Žádný |Žádný |
| Doba uchování dat |7 dní | 30 do 730 dnů<sup>1</sup> | 30 do 730 dnů<sup>1</sup> |

Následující omezení platí pro každý pracovní prostor Log Analytics nejstarší starší verze cenové úrovně:

|  | Standard | Premium | 
| --- | --- | --- | --- | --- | --- |--- |
| Objem shromážděných dat za den | Žádný | Žádný | 
| Doba uchování dat |30 dní | 365 dnů |

<sup>1</sup>uchovávání dat nad rámec 31 dní je k dispozici pro další poplatky. Další informace o [ceny Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

<sup>2</sup>když váš pracovní prostor dosáhne 500 MB denního limitu přenosu dat, analýza dat se zastaví a bude pokračovat na začátku dalšího dne. Den vychází ze standardu UTC.

>[!NOTE]
>V závislosti na tom, jak dlouho používáte Log Analytics můžete mít přístup k cenové úrovně starší verze. Další informace o [starší verze Log Analytics cenové úrovně](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
>

Následující omezení platí pro prostředky Azure Log Analytics (pracovní prostory) na jedno předplatné.

| Cenová úroveň    | Počet pracovních prostorů na předplatné | Komentáře
| --- | --- | --- |
| Úroveň Free  | 10 | Toto omezení nejde zvýšit. |
| Všechny úrovně než Free | neuvedeno | Co vás omezuje počet prostředků v rámci skupiny prostředků a počtem skupin prostředků na předplatné. | 

Na rozhraní API služby Log Analytics se vztahují následující omezení:

| Category | Limits | Komentáře
| --- | --- | --- |
| Rozhraní API kolekce dat | Maximální velikost pro jeden příspěvek je 30 MB.<br>Maximální velikost pro hodnoty pole je 32 KB. | Rozdělte větší svazky na více příspěvků.<br>Pole delší než 32 kB se oříznou. |
| Search API | 5000 záznamů vrácených pro neagregovaná data.<br>500 000 záznamů pro agregovaná data. | Agregovaná data znamenají vyhledávání, která zahrnuje `summarize` příkazu.
 
