---
title: Vyhnout se přerušením služeb v úlohách Azure Stream Analytics
description: Tento článek popisuje pokyny k provádění úlohy Stream Analytics upgrade odolný.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090801"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Záruka spolehlivosti úlohy Stream Analytics během aktualizace služeb

Součástí je plně spravovaná služba je schopnost zavádět nové funkce služby a vylepšení rychlým tempem. Stream Analytics v důsledku toho může mít aktualizace služby nasazení na základě týdenní (nebo vyšší). Bez ohledu na to, jaký objem testování provádí stále existuje riziko, že existující, spuštěné úlohy, mohou přestat fungovat kvůli implementaci chybu. Pro zákazníky, kteří spouštět kritické úlohy streamování zpracování těchto rizik třeba se jim vyhnout. Azure je mechanismus zákazníci mohou používat ke snížení rizika **[spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelu. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak spárované oblasti Azure, které řeší tento problém?

Stream Analytics zaručuje, že úlohy ve spárovaných oblastí jsou aktualizovány v samostatné dávky. Mezi aktualizacemi identifikovat potenciální chyby slov a opravit je v důsledku není dostatečná časové prodlevy.

_S výjimkou střed Indie_ (jehož spárovaná oblast, Indie – Jih, nemá přítomnost Stream Analytics), nasazení aktualizace do služby Stream Analytics se nevyskytuje v sadě spárované oblasti současně. Nasazení ve více oblastech **ve stejné skupině** může dojít k **ve stejnou dobu**.

V článku věnovaném **[dostupnost a spárovaných oblastí](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** obsahuje nejnovější informace, na kterém jsou spárované oblasti.

Zákazníkům doporučujeme obě spárovaných oblastí nasazení stejné úlohy. Kromě Stream Analytics interní možnosti monitorování, Zákazníci také doporučujeme monitorovat úlohy jako **obě** jsou úloh v produkčním prostředí. Pokud se zjistí, že konec být výsledkem aktualizace služby Stream Analytics, odpovídajícím způsobem eskalovat a převzetí služeb při selhání kterýchkoli příjem dat na výstup úlohy v pořádku. Eskalace pro podporu bránící spárované oblasti byly ovlivněny nové nasazení a udržovat tak integritu spárované úloh.

## <a name="next-steps"></a>Další postup

* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálovat úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k Stream Analytics query language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics správu reference k rozhraní REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
