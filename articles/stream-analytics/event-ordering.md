---
title: Konfigurace zásad řazení událostí pro Azure Stream Analytics
description: Tento článek popisuje, jak v Stream Analytics konfigurovat i nastavení řazení.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461187"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Konfigurace zásad řazení událostí pro Azure Stream Analytics

Tento článek popisuje, jak nastavit a používat zásady pozdního doručení a mimo pořadí událostí v Azure Stream Analytics. Tyto zásady jsou použity pouze v případě, že používáte [klauzuli TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) v dotazu.

## <a name="event-time-and-arrival-time"></a>Čas události a čas příjezdu

Vaše úloha Stream Analytics může zpracovávat události na základě *času události* nebo *času příjezdu*. **Čas události/aplikace** je časové razítko přítomné v datové části události (při generování události). **Čas příjezdu** je časové razítko, kdy byla událost přijata ve vstupním zdroji (Event Hubs/IoT Hub/blob storage). 

Ve výchozím nastavení Stream Analytics zpracovává události podle *času příjezdu*, ale můžete se rozhodnout zpracovat události podle *času události* pomocí klauzule [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) v dotazu. Zásady pozdního příjezdu a mimo pořadí platí pouze v případě, že události zpracováváte podle času události. Při konfiguraci těchto nastavení berte ohled na požadavky vašeho scénáře z hlediska latence a správnosti. 

## <a name="what-is-late-arrival-policy"></a>Co jsou zásady pozdního přijetí?

Někdy události dorazí pozdě z různých důvodů. Například událost, která dorazí o 40 sekund později, bude mít čas události = 00:10:00 a čas příjezdu = 00:10:40. Pokud nastavíte zásady pozdního příjezdu na 15 sekund, každá událost, která dorazí později než 15 sekund, bude buď zrušena (není zpracována službou Stream Analytics), nebo bude upraven a čas události upraven. Ve výše uvedeném příkladu, protože událost dorazila o 40 sekund později (více než je nastavena zásada), bude její čas události upraven na maximální dobu pozdního příjezdu 00:10:25 (čas příjezdu - hodnota zásad pozdního příjezdu). Výchozí zásady pozdního příjezdu jsou 5 sekund.

## <a name="what-is-out-of-order-policy"></a>Co je politika mimo pořadí? 

Událost může přijít mimo provoz stejně. Po úpravě času události na základě zásad pozdního příjezdu můžete také zvolit automatické přetažení nebo úpravu událostí, které jsou mimo pořadí. Pokud nastavíte tuto zásadu na 8 sekund, všechny události, které dorazí mimo pořadí, ale v rámci 8 sekund okna jsou uspořádány podle času události. Události, které dorazí později, budou vynechány nebo upraveny na maximální hodnotu zásad mimo pořadí. Výchozí zásady mimo pořadí je 0 sekund. 

## <a name="adjust-or-drop-events"></a>Upravit nebo přetáhnout události

Pokud se události dostaví pozdě nebo mimo pořadí na základě zásad, které jste nakonfigurovali, můžete tyto události (nezpracované službou Stream Analytics) buď vynechat, nebo upravit jejich čas události.

Podívejme se na příklad těchto politik v akci.
<br> **Politika pozdního příjezdu:** 15 sekund
<br> **Zásady mimo pořadí:** 8 sekund

| Číslo události | Čas události | Čas příjezdu | System.Timestamp | Vysvětlení |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Událost dorazila pozdě a mimo úroveň tolerance. Takže čas události se upraví na maximální toleranci pozdního příjezdu.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Událost přišla pozdě, ale v rámci úrovně tolerance. Takže čas události se neupraví.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Událost dorazila včas. Není třeba žádné úpravy.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Událost přišla mimo pořadí, ale s tolerancí 8 sekund. Takže čas události se neupraví. Pro analytické účely bude tato událost považována za předchozí událost číslo 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Událost přišla mimo pořadí a vnější tolerance 8 sekund. Čas události je tedy upraven na maximální toleranci mimo pořadí. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Mohou tato nastavení zpozdit výstup mé úlohy? 

Ano. Ve výchozím nastavení je zásada mimo pořadí nastavena na nulu (00 minut a 00 sekund). Pokud změníte výchozí hodnotu, první výstup úlohy se zpozdí o tuto hodnotu (nebo vyšší). 

Pokud jeden z oddílů vašich vstupů neobdrží události, měli byste očekávat, že výstup bude zpožděn o hodnotu zásad pozdního doručení. Chcete-li se dozvědět proč, přečtěte si část chyby Vstupní oddíl níže. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>V protokolu aktivit se zobrazují zprávy LateInputEvents

Tyto zprávy jsou zobrazeny s informací, že události dorazily pozdě a jsou buď vynechány nebo upraveny podle vaší konfigurace. Tyto zprávy můžete ignorovat, pokud jste správně nakonfigurovali zásady pozdního doručení. 

Příkladem této zprávy je: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>V protokolu aktivit se mi zobrazují položky InputPartitionNotProgressing

Vstupní zdroj (Event Hub/IoT Hub) má pravděpodobně více oddílů. Azure Stream Analytics vytváří výstup pro časové razítko t1 pouze po všechny oddíly, které jsou kombinovány alespoň v čase t1. Předpokládejme například, že dotaz čte z oddílu centra událostí, který má dva oddíly. Jeden z oddílů, P1, má události až do doby t1. Druhý oddíl, P2, má události až do doby t1 + x. Výstup se pak vytváří až do doby t1. Ale pokud existuje explicitní Partition by PartitionId klauzule, oba oddíly postupují nezávisle. 

Při kombinaci více oddílů ze stejného vstupního datového proudu, pozdní doručení tolerance je maximální doba, která každý oddíl čeká na nová data. Pokud je jeden oddíl ve vašem Centru událostí, nebo pokud IoT Hub nepřijímá vstupy, časová osa pro tento oddíl neprobíhá, dokud nedosáhne prahové hodnoty tolerance pozdního příchodu. To zpožďuje výstup o práh tolerance pozdního příjezdu. V takových případech se může zobrazit následující zpráva:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Tato zpráva vás informuje, že alespoň jeden oddíl ve vašem vstupu je prázdný a zpozdí výstup o prahovou hodnotu pozdního příjezdu. Chcete-li to překonat, doporučujeme buď:  
1. Zajistěte, aby všechny oddíly vašeho centra událostí/Centra IoT Hub přijímaly vstup. 
2. V dotazu použijte klauzuli Partition by PartitionID. 

## <a name="next-steps"></a>Další kroky
* [Důležité informace o práci s časem](stream-analytics-time-handling.md)
* [Metriky dostupné v Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
