---
title: Konfigurace událostí řazení zásady pro Azure Stream Analytics
description: Tento článek popisuje, jak přejít o konfiguraci ani nastavení řazení ve službě Stream Analytics
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 970eeb871775e24abb87c8b977e214645e514d3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789472"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Konfigurace událostí řazení zásady pro Azure Stream Analytics

Tento článek popisuje, jak nastavit a používat ho ve službě Azure Stream Analytics události mimo pořadí zásad a opožděné dodání. Tyto zásady se použijí jenom v případě, že používáte [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) klauzule v dotazu.

## <a name="event-time-and-arrival-time"></a>Čas události a čas přijetí

Vaší úlohy Stream Analytics můžete zpracovávat události na základě některé *čas události* nebo *čas doručení*. **Čas události/aplikace** je k dispozici v datové části události časové razítko (Pokud byla událost vygenerována). **Čas doručení** je časové razítko, když událost byla přijata na vstupní zdroj (události rozbočovače a IoT Hub/Blob storage). 

Ve výchozím nastavení, Stream Analytics zpracovává události podle *čas doručení*, ale můžete zpracovat události podle *čas události* pomocí [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) klauzule v dotazu. Pozdní doručení a mimo pořadí zásady platí jenom v případě, že zpracování událostí podle času události. Při konfiguraci těchto nastavení, zvažte požadavky na latenci a správnost pro váš scénář. 

## <a name="what-is-late-arrival-policy"></a>Co je pozdně zásady doručení?

Někdy události dorazí pozdě z různých důvodů. Například událost, která dorazí pozdě 40 sekund bude mít čas události = 10:00:00 a doručení čas = 00:10:40. Pokud ji nastavíte na 15 sekund, událost, která je novější než 15 sekund se buď zahodí (nebyl zpracován služba Stream Analytics) nebo upravili jejich čas události dorazí pozdě zásady doručení. V předchozím příkladu jako události doručené 40 sekund později (více než sada zásad), jeho čas události bude upraveno, aby maximální opožděné přijetí zásad 00:10:25 (čas doručení - pozdní hodnota přijetí zásad). Výchozí zásada pozdní příjezdu je 5 sekund.

## <a name="what-is-out-of-order-policy"></a>Co jsou zásady mimo pořadí? 

Také může do nefungujících událostí. Po času události je upravena podle pozdní přijetí zásad, můžete také automaticky vyřadit nebo upravit události, které jsou mimo pořadí. Pokud tyto zásady nastavíte na 8 sekund, všechny události, které dorazí mimo pořadí, ale v rámci okna 8 druhé přeuspořádají podle času události. Události, které přicházejí později bude vyřazen nebo upravena na hodnotu maximální zásady mimo pořadí. Výchozí zásada mimo pořadí je 0 sekund. 

## <a name="adjust-or-drop-events"></a>Upravit nebo vyřadit události

Pokud události dorazí pozdě nebo mimo pořadí na základě zásad, které jste nakonfigurovali, můžete vyřadit tyto události (nebyl zpracován služba Stream Analytics) nebo mít čas jejich události upravit.

Dejte nám uvedený příklad těchto zásad v akci.
<br> **Pozdní zásady doručení:** 15 sekund
<br> **Mimo pořadí zásad:** 8 sekund

| Číslo události | Čas události | Čas přijetí | System.Timestamp | Vysvětlení |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Události byly přijaty pozdní a mimo přípustnou úroveň. Čas události tak získá upravena maximální tolerance pozdního přijetí.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Události doručené pozdě, ale v rámci úroveň tolerance. Nastaví tak, čas události nelze získat.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Události byly přijaty včas. Není potřeba žádné úpravy.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Události doručené mimo pořadí, ale v toleranci 8 sekund. Ano získat upravit není čas události. Pro účely analýzy bude tato událost pokládán za předchozí číslo události je 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Události byly přijaty tolerance mimo pořadí a mimo 8 sekund. Ano čas události se upraví na maximální tolerance mimo pořadí. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Tato nastavení zpozdit výstup Moje úlohy? 

Ano. Ve výchozím nastavení mimo pořadí zásad je nastaven na nula (00 minut a 00 sekund). Pokud změníte výchozí, první výstup vaší úlohy je zpožděné o tuto hodnotu (nebo vyšší). 

Pokud jeden z oddílů vstupy událostí, měli byste očekávat výstupu, abyste zpozdit pozdní hodnotou zásady doručení. Informace o tom, proč, najdete v části Chyba InputPartition níže. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Zobrazí zprávy LateInputEvents v mé protokolu aktivit

Tyto zprávy se zobrazují vás informovat, že jste tu pozdní a jsou buď vynechané nebo upravené události podle vaší konfigurace. Tyto zprávy můžete ignorovat, pokud jste nakonfigurovali pozdní zásady doručení odpovídajícím způsobem. 

Příklad, tato zpráva je: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Zobrazí InputPartitionNotProgressing v mé protokolu aktivit

Vstupní zdroj (Event Hub/IoT Hub) pravděpodobně obsahuje několik oddílů. Azure Stream Analytics vytvoří výstup pro čas razítko t1 až po všech oddílů, které jsou spojené se minimálně v čase t1. Předpokládejme například, že dotaz načte z oddílu centra událostí, který má dva oddíly. Jeden z oddílů, P1, až do času t1 má události. Druhý oddíl P2, má událostí až do času t1 + x. Výstup se pak vytvářejí až do času t1. Ale pokud je klauzulí PartitionId oddíly průběh explicitní oddílu nezávisle na sobě. 

Kombinaci více oddílů ze stejného vstupního datového proudu tolerance pozdního přijetí je maximální množství času, které každý oddíl čeká na nová data. Pokud byl jeden oddíl v Centru událostí nebo pokud vstupy služby IoT Hub, časovou osu pro tento oddíl není průběh až do dosažení pozdní prahovou hodnotu tolerance pozdního přijetí. Toto zpoždění výstupu pomocí pozdní prahové hodnoty tolerance pozdního přijetí. V takových případech může se zobrazit následující zpráva:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Tuto zprávu informující, že aspoň jeden oddíl v váš vstup, je prázdný a bude zpozdit výstup prahovou hodnotu pozdní doručení. Abyste to vyřešili, je doporučeno buď: 1. Zajistěte, aby že všechny oddíly centra událostí centra/IoT vstup. 2. Oddílu použijte PartitionID klauzule v dotazu. 

## <a name="next-steps"></a>Další postup
* [Důležité informace o zpracování času](stream-analytics-time-handling.md)
* [Metriky, které jsou k dispozici ve službě Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
