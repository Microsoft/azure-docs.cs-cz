---
title: Vyhnout se přerušením služeb v úlohách Azure Stream Analytics
description: Tento článek popisuje pokyny k provádění úlohy Stream Analytics upgrade odolný.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: e38f8a923daa210d8aa5b56631e5f8157d4b3f70
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620883"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Záruka spolehlivosti úlohy Stream Analytics během aktualizace služeb

Součástí je plně spravovaná služba je schopnost zavádět nové funkce služby a vylepšení rychlým tempem. Stream Analytics v důsledku toho může mít aktualizace služby nasazení na základě týdenní (nebo vyšší). Bez ohledu na to, jaký objem testování provádí stále existuje riziko, že existující, spuštěné úlohy, mohou přestat fungovat kvůli implementaci chybu. Pokud používáte zásadně důležité úlohy, tato rizika třeba se jim vyhnout. Toto riziko následující Azure můžete snížit **[spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelu. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak spárované oblasti Azure, které řeší tento problém?

Stream Analytics zaručuje, že úlohy ve spárovaných oblastí jsou aktualizovány v samostatné dávky. Mezi aktualizacemi identifikovat možné problémy a opravit je je výsledkem dostatečná časové prodlevy.

_S výjimkou střed Indie_ (jehož spárovaná oblast, Indie – Jih, nemá přítomnost Stream Analytics), nasazení aktualizace do služby Stream Analytics se nevyskytuje v sadě spárované oblasti současně. Nasazení ve více oblastech **ve stejné skupině** může dojít k **ve stejnou dobu**.

V článku věnovaném **[dostupnost a spárovaných oblastí](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** obsahuje nejnovější informace, na kterém jsou spárované oblasti.

Doporučuje se obě spárovaných oblastí nasazení stejné úlohy. Měli byste pak [sledování těchto úloh](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) máme vás upozornit, když se stane něco neočekávaného. Pokud jeden z těchto úloh končí nahoru v [chybového stavu](https://docs.microsoft.com/azure/stream-analytics/job-states) po aktualizaci služby Stream Analytics, můžete kontaktovat zákaznickou podporu vám pomůže identifikovat hlavní příčinu. By měl také převzetí služeb při selhání kterýchkoli příjem dat na výstup úlohy v pořádku.

## <a name="next-steps"></a>Další postup

* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálovat úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k Stream Analytics query language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics správu reference k rozhraní REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
