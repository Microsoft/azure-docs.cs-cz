---
title: Konfigurace zásad řazení událostí pro Azure Stream Analytics
description: Tento článek popisuje, jak přejít na téma Konfigurace nastavení rovnoměrného řazení v Stream Analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: b4e34befbf28de2b985ff49ce17a87a25842015e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87901687"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Konfigurace zásad řazení událostí pro Azure Stream Analytics

Tento článek popisuje, jak nastavit a použít zpožděné a neuspořádané zásady událostí v Azure Stream Analytics. Tyto zásady se použijí jenom v případě, že použijete klauzuli [timestamp by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) v dotazu a použijete ji jenom pro zdroje vstupu v cloudu.

## <a name="event-time-and-arrival-time"></a>Čas události a čas přijetí

Vaše úloha Stream Analytics může zpracovávat události na základě času *události* nebo *doby doručení*. **Událost/čas aplikace** je časové razítko přítomné v datové části události (při generování události). **Doba doručení** je časové razítko, kdy se událost přijala na vstupním zdroji (Event Hubs/IoT Hub/BLOB Storage). 

Ve výchozím nastavení Stream Analytics zpracovává události podle *času doručení*, ale můžete zvolit zpracování událostí podle *času události* pomocí klauzule [timestamp by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) v dotazu. Zásady opožděného doručení a navýšení jsou platné pouze v případě, že zpracováváte události podle času události. Při konfiguraci těchto nastavení berte ohled na požadavky vašeho scénáře z hlediska latence a správnosti. 

## <a name="what-is-late-arrival-policy"></a>Co jsou zásady pozdního přijetí?

Události v některých případech přicházejí pozdě z důvodu různých důvodů. Například událost, která dorazí na 40 sekund pozdě, bude mít čas události = 00:10:00 a čas doručení = 00:10:40. Pokud nastavíte zásadu opožděného doručení na 15 sekund, jakákoli událost, která dorazí později než 15 sekund, se buď vynechá (nezpracovává se Stream Analytics), nebo se upraví jejich čas události. Jak je uvedeno výše, protože událost byla doručena 40 sekund pozdě (víc než sada zásad), její čas události se upraví na maximum v případě opožděné zásady doručení 00:10:25 (hodnota zásady doba doručení – zpožděná). Výchozí zásada opožděného doručení je 5 sekund.

## <a name="what-is-out-of-order-policy"></a>Co jsou zásady mimo pořadí? 

Událost může být doručena i mimo pořadí. Po úpravě času události na základě zásad opožděného doručení můžete také zvolit automatické vyřazení nebo úpravu událostí, které jsou mimo pořadí. Pokud nastavíte tuto zásadu na 8 sekund, všechny události, které dorazí mimo pořadí, ale v rámci 8 sekund se změní pořadí podle času události. Události, které přicházejí později, budou buď vyřazeny nebo upraveny na maximální hodnotu zásad mimo pořadí. Výchozí zásada mimo pořadí je 0 sekund. 

## <a name="adjust-or-drop-events"></a>Úprava nebo vyřazení událostí

Pokud události dorazí pozdě nebo mimo pořadí na základě zásad, které jste nakonfigurovali, můžete tyto události buď vyřadit (nezpracované Stream Analytics), nebo nechat svou událost upravenou.

Podíváme se na příklad těchto zásad v tématu Action (akce).
<br> **Zásady opožděného doručení:** 15 sekund
<br> **Zásada mimo pořadí:** 8 sekund

| Událost – ne. | Čas události | Čas doručení | System.Timestamp | Vysvětlení |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Událost přišla pozdě a mimo úroveň tolerance. Takže čas události se upraví na maximální toleranci doručení.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Událost byla doručena pozdě, ale v úrovni tolerance. Takže se čas události neupraví.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Událost byla doručena včas. Není potřeba žádná úprava.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Událost byla doručena mimo pořadí, ale v rámci tolerance 8 sekund. Takže se čas události neupraví. Pro účely analýzy se tato událost bude považovat za předchozí událost číslo 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Událost byla doručena mimo pořadí a mimo toleranci 8 sekund. Takže čas události se upraví na maximum z tolerance mimo pořadí. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Můžou tato nastavení zpozdit výstup mé úlohy? 

Ano. Ve výchozím nastavení je zásada mimo pořadí nastavená na hodnotu nula (00 minut a 00 sekund). Změníte-li výchozí hodnotu, je první výstup úlohy zpožděn touto hodnotou (nebo vyšší). 

Pokud některý z oddílů vašich vstupů nepřijímá události, měli byste očekávat, že se výstup bude zpozdit hodnotou zásady pozdního doručení. Informace o tom, proč najdete níže v části věnované chybám pro InputPartition. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>V protokolu aktivit se zobrazují zprávy LateInputEvents

Zobrazují se tyto zprávy, které vás informují o tom, že události byly doručeny pozdě a jsou buď vyřazeny nebo upraveny podle vaší konfigurace. Pokud jste správně nakonfigurovali zásady pozdního doručení, můžete tyto zprávy ignorovat. 

Příklad této zprávy: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>V protokolu aktivit se zobrazuje InputPartitionNotProgressing

Váš vstupní zdroj (centrum událostí/IoT Hub) má nejspíš více oddílů. Azure Stream Analytics vytváří výstup pro časové razítko T1 až po tom, co jsou všechny sloučené oddíly alespoň v čase t1. Předpokládejme například, že dotaz čte z oddílu centra událostí, který má dva oddíly. Jeden z oddílů P1 má události do času T1. Druhý oddíl, P2, obsahuje události do času T1 + x. Výstup se pak vytvoří až do času T1. Pokud však existuje explicitní klauzule partition by PartitionId, oba oddíly postupují samostatně. 

Je-li kombinováno více oddílů ze stejného vstupního datového proudu, tolerance pozdního doručení je maximální doba, po kterou každý oddíl čeká na nová data. Pokud je v centru událostí jeden oddíl, nebo pokud IoT Hub neobdrží vstupy, časová osa tohoto oddílu nepokračuje, dokud nedosáhne prahové hodnoty tolerance pro pozdní doručení. Tím se zpozdí výstup prahové hodnoty tolerance pro pozdní doručení. V takových případech se může zobrazit následující zpráva:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Tato zpráva vám informuje o tom, že minimálně jeden oddíl ve vstupu je prázdný a ve výstupu se dokončí prahová hodnota pozdního doručení. Chcete-li to překonat, doporučujeme buď:  
1. Zajistěte, aby všechny oddíly centra událostí/IoT Hub přijímaly vstup. 
2. Použijte v dotazu klauzuli partition by PartitionID. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>Proč se mi zobrazuje zpoždění 5 sekund, i když je zásada zpožděného doručení nastavená na 0?
K tomu dojde, když je vstupní oddíl, který nikdy neobdržel žádný vstup. Pokud chcete toto chování ověřit, můžete ověřit vstupní metriky podle oddílu. 

Pokud oddíl neobsahuje žádná data pro více než nakonfigurovanou prahovou hodnotu pro pozdní doručení, Stream Analytics bude v části požadavky na řazení událostí popsána časová razítka aplikace. To vyžaduje odhadovanou dobu doručení. Pokud oddíl nikdy nemá žádná data, Stream Analytics odhaduje čas doručení v *místním čase – 5 sekund*. Z důvodu těchto oddílů, které nikdy neobsahovaly žádná data, by mohla zobrazit zpoždění meze 5 sekund.  

## <a name="next-steps"></a>Další kroky
* [Důležité informace o práci s časem](stream-analytics-time-handling.md)
* [Metriky dostupné v Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
