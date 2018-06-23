---
title: Předem sestavené LEOŠ odkaz entity - Azure | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje seznam přednastavených entitami, které jsou zahrnuty v znalosti jazyka (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 7ce50e4c0be605e1700a2c18533cb087384f524c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316880"
---
# <a name="entities-per-culture"></a>Počet entit na jazykové verzi

Principy jazyka (LEOŠ) poskytuje předem entity. Když předem entity je součástí aplikace, zahrnuje LEOŠ odpovídající předpovědi entity v odpovědi koncového bodu. Všechny utterances příkladu jsou označeny také s entitou. Chování entit předem **nelze** upravit. Pokud není uvedeno jinak, předem entity jsou k dispozici ve všech LEOŠ aplikace národních prostředí (jazykové verze). V následující tabulce jsou předem entitami, které jsou podporovány pro každou jazykovou verzi.

Předkompilované entity   |   Angličtina (Spojené státy)<br>```En-us```   |   Francouzština (Francie)<br>```fr-FR```   |   Italština (Itálie)<br>```it-IT```   |   Španělština (Španělsko)<br>```es-ES```   |   Čínština<br>```zh-CN```   |   Němčina<br>```de-DE```   |   Portugalština (Brazílie)<br>```pt-BR```   |   Japonština (Japonsko)<br>```ja-JP```   |   Korejština (Korea)<br>```ko-kr```   | Francouzština (Kanada)<br>```fr-CA```   |   Španělština (Mexiko)<br>```es-MX```   |   Holandština (Nizozemsko)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Měna](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (například: hodnotu ve formátu Měna)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>DateRange<br>time<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>oblasti<br>Váha<br>informace (např: bit/bajtů)<br>Délka (např: měření)<br>rychlost (např: míle za hodinu)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Teplotní](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine<br>delisle<br>c   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

V části poznámky na [zastaralé předem entity](luis-reference-prebuilt-deprecated.md)

## <a name="examples-of-prebuilt-entities-in-en-us-culture"></a>Příklady předem entity v en-us jazykovou verzi
Následující tabulka uvádí předem entity s daty příklad a návratové hodnoty.

Předkompilované entity   |   Příklad utterance   |   JSON
------|------|------|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.currency```     |   ```1000.00 US dollars```   |```{ "type": "builtin.currency", "entity": "1000.00 us dollars" }```
 ```builtin.currency```     |   ```$ 67.5 B```   |```{ "type": "builtin.currency", "entity": "$ 67.5" }```|
 ```builtin.datetimeV2``` | V tématu [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) | V tématu [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) |
 ```builtin.dimension```     |   ```2 miles```   |```{ "type": "builtin.dimension", "entity": "2 miles" }```|
 ```builtin.dimension```     |  ```650 square kilometers```   |```{ "type": "builtin.dimension", "entity": "650 square kilometers" }```|
 ```builtin.email```     |  ```patti.owens@microsoft.com```   |```{ "type": "builtin.email", "entity": "patti.owens@microsoft.com" }```|
 ```builtin.number```     |  ```ten```   |``` { "type": "builtin.number", "entity": "ten" } ```|
 ```builtin.number```     |   ```3.1415```   |```  { "type": "builtin.number", "entity": "3 . 1415" }``` |
 ```builtin.ordinal```     |   ```first```   |```{ "type": "builtin.ordinal", "entity": "first" }``` |
 ```builtin.ordinal```     |   ```10th```   | ```{ "type": "builtin.ordinal", "entity": "10th" }``` |  
 ```builtin.percentage```   |   ```The stock price increase by 7 $ this year```   |```{ "type": "builtin.percentage", "entity": "7 %" }```|
 ```builtin.phonenumber```   |   ```my mobile is 00 44 161 1234567```   |```{ "type": "builtin.phonenumber", "entity": "00 44 161 1234567" }```|
 ```builtin.temperature```     |   ```10 degrees celsius```   | ```{ "type": "builtin.temperature", "entity": "10 degrees celcius" }```|   
 ```builtin.temperature```     |   ```78 F```   |```{ "type": "builtin.temperature", "entity": "78 f" }```|
 ```builtin.url```     |   ```http://www.luis.ai is a great cognitive service```   |```{ "type": "builtin.url", "entity": "http://www.luis.ai" }```|


## <a name="contribute-to-prebuilt-entity-cultures"></a>Můžete přispět k předem entity jazykové verze
Předkompilované entity, které jsou vyvinuté v projektu open source nástroje pro rozpoznávání textu. Prosím [přispívat](https://github.com/Microsoft/Recognizers-Text) do projektu. Tento projekt obsahuje příklady měny na jazykové verzi. 

## <a name="next-steps"></a>Další postup

Další informace o [číslo](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [Měna](luis-reference-prebuilt-currency.md) entity. 