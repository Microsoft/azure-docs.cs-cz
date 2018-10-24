---
title: Předem připravených entit - LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje seznam předem připravených entit, které jsou zahrnuty v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: d6e628f2131cd5e3bf81cd623b94e02a9837fab7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954534"
---
# <a name="entities-per-culture"></a>Entity pro jednotlivé jazykové verze

Language Understanding (LUIS) poskytuje předem připravených entit. Předem připravených entit je obsažen ve vaší aplikaci, LUIS obsahuje odpovídající entity predikce v odpovědi koncového bodu. Všechny projevy příkladu jsou označeny jako také s entitou. Chování předem připravených entit **nelze** upravit. Pokud není uvedeno jinak, jsou předem připravených entit k dispozici ve všech oblastech aplikace LUIS (jazykové verze). V následující tabulce jsou uvedeny předem připravených entit, které jsou podporovány pro každou jazykovou verzi.

Předem připravených entit   |   Angličtina (Spojené státy)<br>```En-us```   |   Francouzština (Francie)<br>```fr-FR```   |   Italština (Itálie)<br>```it-IT```   |   Španělština (Španělsko)<br>```es-ES```   |   Čínština<br>```zh-CN```   |   Němčina<br>```de-DE```   |   Portugalština (Brazílie)<br>```pt-BR```   |   Japonština (Japonsko)<br>```ja-JP```   |   Korejština (Jižní Korea)<br>```ko-kr```   | Francouzština (Kanada)<br>```fr-CA```   |   Španělština (Mexiko)<br>```es-MX```   |   Nizozemština (Nizozemsko)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Měna](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   |   ✔   |   ✔   |   ✔   |   -   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   |    -   |    -   |    -   |    ✔   |    -   |    -   |    -   |   -   |   -   |   -   |   -   |
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

Pro naleznete v poznámkách [zastaralé předem připravených entit](luis-reference-prebuilt-deprecated.md)

Není k dispozici ve všech subkultury portugalština (Brazílie) – KeyPhrase ```pt-BR```.

<!--
## Examples of prebuilt entities in en-us culture
The following table lists prebuilt entities with example data and the return values.

Prebuilt entity   |   Example utterance   |   JSON
------|------|------|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.currency```     |   ```1000.00 US dollars```   |```{ "type": "builtin.currency", "entity": "1000.00 us dollars" }```
 ```builtin.currency```     |   ```$ 67.5 B```   |```{ "type": "builtin.currency", "entity": "$ 67.5" }```|
 ```builtin.datetimeV2``` | See [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) | See [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) |
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
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Přispívat do předem připravených entit jazykových verzí
Předem připravených entit jsou vyvíjeny v projektu open source nástrojů pro rozpoznávání textu. [Přispívat](https://github.com/Microsoft/Recognizers-Text) do projektu. Tento projekt obsahuje příklady měny na jazykovou verzi. 

## <a name="next-steps"></a>Další postup

Další informace o [číslo](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [měny](luis-reference-prebuilt-currency.md) entity. 
