---
title: Zabránilo přerušení služeb s úlohy Azure Stream Analytics | Microsoft Docs
description: Pokyny k provedení Stream Analytics úlohy upgradu odolný.
services: stream-analytics
documentationCenter: ''
author: jseb225
manager: ryanw
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 9f5306e4cbcfc2b554a688e2d502cc4b40845d60
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Zajistit spolehlivost úlohy Stream Analytics během aktualizace služby

Součástí je plně spravovaná služba, je schopnost zavádět nové funkce služby a vylepšení rychlé tempem. Stream Analytics v důsledku toho může mít aktualizaci služby nasazení na základě týdenní (nebo vyšší). Bez ohledu na to, kolik testování se provádí stále existuje riziko, že existující, spuštěné úlohy je možné ukončit z důvodu zavedení chyby. Pro zákazníky, kteří spustí kritické úlohy streamování zpracování těchto rizik muset se vyhnout. Azure je mechanismus zákazníci mohou používat pro toto riziko snížit **[spárované oblast](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelu. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak oblastí Azure spárované tuto situaci řešit?

Stream Analytics zaručuje, že úlohy v oblasti spárované se aktualizují odděleně. Mezi aktualizace opravit, a identifikovat potenciální chyby ukončování řádků je v důsledku dostatečná časová mezera.

_S výjimkou střed_ (jehož spárované oblast – Jih, Indie, nemá přítomnosti Stream Analytics), nasazení aktualizace do služby Stream Analytics se nevyskytuje ve stejnou dobu v sadě spárované oblasti. Nasazení v několika oblastech **ve stejné skupině** může dojít, **ve stejnou dobu**.

V článku na **[dostupnosti a oblastí, spárované](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** má nejaktuálnější informace, na kterém jsou spárovat oblasti.

Zákazníci by měli nasadit stejné úlohy do obou spárované oblasti. Kromě Stream Analytics interní možnosti monitorování, Zákazníci by měli také ke sledování úloh jako **i** provozní úlohy. Pokud zalomení identifikuje jako výsledek aktualizace služby Stream Analytics, eskalovat správně a převzít všechny podřízené příjemcům výstup úlohy v pořádku. Eskalace pro podporu budou zabránění oblasti spárované ovlivnění nové nasazení a zachovat integritu spárované úloh.
