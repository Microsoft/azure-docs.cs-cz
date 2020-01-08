---
title: Vyhnout se přerušením služeb v úlohách Azure Stream Analytics
description: Tento článek popisuje pokyny k provádění úlohy Stream Analytics upgrade odolný.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425991"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Záruka spolehlivosti úlohy Stream Analytics během aktualizace služeb

Součástí je plně spravovaná služba je schopnost zavádět nové funkce služby a vylepšení rychlým tempem. Stream Analytics v důsledku toho může mít aktualizace služby nasazení na základě týdenní (nebo vyšší). Bez ohledu na to, jaký objem testování provádí stále existuje riziko, že existující, spuštěné úlohy, mohou přestat fungovat kvůli implementaci chybu. Pokud spouštíte důležité úlohy, je nutné jim zabránit v těchto rizicích. Toto riziko můžete snížit následujícím modelem **[spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak spárované oblasti Azure, které řeší tento problém?

Stream Analytics zaručuje, že úlohy ve spárovaných oblastí jsou aktualizovány v samostatné dávky. Výsledkem je dostatečné časové rozmezí mezi aktualizacemi k identifikaci potenciálních problémů a jejich napravení.

_S výjimkou střed Indie_ (jehož spárovaná oblast, Indie – Jih, nemá přítomnost Stream Analytics), nasazení aktualizace do služby Stream Analytics se nevyskytuje v sadě spárované oblasti současně. Nasazení ve více oblastech **ve stejné skupině** může dojít k **ve stejnou dobu**.

V článku věnovaném **[dostupnost a spárovaných oblastí](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** obsahuje nejnovější informace, na kterém jsou spárované oblasti.

Doporučuje se nasazovat stejné úlohy do obou spárovaných oblastí. [Tyto úlohy](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) byste pak měli sledovat, abyste byli informováni, když dojde k neočekávanému problému. Pokud dojde k [selhání](https://docs.microsoft.com/azure/stream-analytics/job-states) jedné z těchto úloh po aktualizaci služby Stream Analytics, obraťte se na zákaznickou podporu, která vám může pomoci identifikovat hlavní příčinu. Měli byste také převzít služby při selhání na výstup úlohy v pořádku.

## <a name="next-steps"></a>Další kroky

* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálovat úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k Stream Analytics query language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics správu reference k rozhraní REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
