---
title: Zabránilo přerušení služeb v úlohy Azure Stream Analytics
description: Tento článek popisuje pokyny pro provádění vaše úlohy Stream Analytics upgrade odolný.
services: stream-analytics
author: jseb225
manager: kfile
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 47ccfe99d2ee6576dbb70324eb383f52d2a1b2e7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Zajistit spolehlivost úlohy Stream Analytics během aktualizace služby

Součástí je plně spravovaná služba, je schopnost zavádět nové funkce služby a vylepšení rychlé tempem. Stream Analytics v důsledku toho může mít aktualizaci služby nasazení na základě týdenní (nebo vyšší). Bez ohledu na to, kolik testování se provádí stále existuje riziko, že existující, spuštěné úlohy je možné ukončit z důvodu zavedení chyby. Pro zákazníky, kteří spustí kritické úlohy streamování zpracování těchto rizik muset se vyhnout. Azure je mechanismus zákazníci mohou používat pro toto riziko snížit **[spárované oblast](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelu. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Jak oblastí Azure spárované tuto situaci řešit?

Stream Analytics zaručuje, že úlohy v oblasti spárované se aktualizují odděleně. Mezi aktualizace opravit, a identifikovat potenciální chyby ukončování řádků je v důsledku dostatečná časová mezera.

_S výjimkou střed_ (jehož spárované oblast – Jih, Indie, nemá přítomnosti Stream Analytics), nasazení aktualizace do služby Stream Analytics se nevyskytuje ve stejnou dobu v sadě spárované oblasti. Nasazení v několika oblastech **ve stejné skupině** může dojít, **ve stejnou dobu**.

V článku na **[dostupnosti a oblastí, spárované](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** má nejaktuálnější informace, na kterém jsou spárovat oblasti.

Zákazníci by měli nasadit stejné úlohy do obou spárované oblasti. Kromě Stream Analytics interní možnosti monitorování, Zákazníci by měli také ke sledování úloh jako **i** provozní úlohy. Pokud zalomení identifikuje jako výsledek aktualizace služby Stream Analytics, eskalovat správně a převzít všechny podřízené příjemcům výstup úlohy v pořádku. Eskalace pro podporu budou zabránění oblasti spárované ovlivnění nové nasazení a zachovat integritu spárované úloh.
