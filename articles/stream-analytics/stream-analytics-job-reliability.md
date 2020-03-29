---
title: Vyhněte se přerušení min. služby v azure stream analytics úlohy
description: Tento článek popisuje pokyny k tomu, aby vaše úlohy Služby Stream Analytics byly odolné.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425991"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Zaručit spolehlivost úloh Stream Analytics během aktualizací služeb

Součástí plně spravované služby je schopnost rychle zavádět nové funkce a vylepšení služeb. V důsledku toho stream analytics může mít aktualizace služby nasadit na týdenní (nebo častější) základě. Bez ohledu na to, kolik testování se provádí, stále existuje riziko, že existující spuštěná úloha může přerušit v důsledku zavedení chyby. Pokud provozujete kritické úlohy, je třeba se těmto rizikům vyhnout. Toto riziko můžete snížit podle modelu **[spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak spárované oblasti Azure řeší tento problém?

Stream Analytics zaručuje, že úlohy ve spárovaných oblastech jsou aktualizovány v samostatných dávkách. V důsledku toho existuje dostatečná časová prodleva mezi aktualizacemi pro identifikaci potenciálních problémů a jejich nápravu.

_S výjimkou střední Indie_ (jejíž spárovaná oblast, Jižní Indie, nemá přítomnost služby Stream Analytics), by k nasazení aktualizace do Služby Stream Analytics nedošlo současně v sadě spárovaných oblastí. Nasazení ve více oblastech **ve stejné skupině** může dojít **současně**.

Článek o **[dostupnosti a spárovaných oblastech](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** obsahuje nejaktuálnější informace o tom, které oblasti jsou spárovány.

Doporučuje se nasadit identické úlohy do obou spárovaných oblastí. Potom byste měli [sledovat tyto úlohy,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) abyste byli upozorněni, když se stane něco neočekávaného. Pokud jedna z těchto úloh skončí po aktualizaci služby Stream Analytics ve [stavu Selhání,](https://docs.microsoft.com/azure/stream-analytics/job-states) můžete se obrátit na zákaznickou podporu a zjistit hlavní příčinu. Měli byste také převzetí služeb při selhání všechny následné spotřebitele na výstupu úlohy v pořádku.

## <a name="next-steps"></a>Další kroky

* [Úvod do streamové analýzy](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování úloh Stream Analytics](stream-analytics-scale-jobs.md)
* [Odkaz na dotaz ového jazyka Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Odkaz na rozhraní REST API správy Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
