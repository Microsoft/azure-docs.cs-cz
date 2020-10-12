---
title: Vyhněte se přerušením služeb v Azure Stream Analytics úlohách
description: Tento článek popisuje pokyny k tomu, jak Stream Analytics úlohy upgradu odolné.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 8d62f87a6bf33bbbd85b68c9d2e0c6f4a93adf16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86044426"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Záruka Stream Analytics spolehlivost úloh během aktualizací služby

Součástí plně spravované služby je schopnost zavádět nové funkce a vylepšení služby při rychlém tempu. V důsledku toho Stream Analytics možné mít aktualizaci Service Update na týden (nebo častější) bázi. Bez ohledu na to, kolik je testování provedeno, je stále riziko, že existující spuštěná úloha může být přerušena z důvodu zavedení chyby. Pokud spouštíte důležité úlohy, je nutné jim zabránit v těchto rizicích. Toto riziko můžete snížit následujícím modelem **[spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak se tyto informace týkají spárovaných oblastí Azure?

Stream Analytics garantuje, že se úlohy v spárovaných oblastech aktualizují v samostatných dávkách. Výsledkem je dostatečné časové rozmezí mezi aktualizacemi k identifikaci potenciálních problémů a jejich napravení.

_S výjimkou střed Indie_ (jejichž spárovaná oblast, Jižní Indie, nemá Stream Analytics přítomnost), nasazení aktualizace Stream Analytics se v sadě spárovaných oblastí neprojeví současně. Nasazení ve více oblastech **ve stejné skupině** se může vyskytovat **ve stejnou dobu**.

Článek o **[dostupnosti a spárovaných oblastech](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** obsahuje nejaktuálnější informace o párování oblastí.

Doporučuje se nasazovat stejné úlohy do obou spárovaných oblastí. [Tyto úlohy](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) byste pak měli sledovat, abyste byli informováni, když dojde k neočekávanému problému. Pokud dojde k [selhání](https://docs.microsoft.com/azure/stream-analytics/job-states) jedné z těchto úloh po aktualizaci služby Stream Analytics, obraťte se na zákaznickou podporu, která vám může pomoci identifikovat hlavní příčinu. Měli byste také převzít služby při selhání na výstup úlohy v pořádku.

## <a name="next-steps"></a>Další kroky

* [Úvod do Stream Analytics](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování úloh Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční dokumentace jazyka Stream Analytics dotazů](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční informace o REST API správy Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
