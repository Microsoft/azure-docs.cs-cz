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
ms.openlocfilehash: bc23f2e5d8304400802c74093d4d78a1fcd8cf22
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219477"
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

## <a name="contribute-to-prebuilt-entity-cultures"></a>Přispívat do předem připravených entit jazykových verzí
Předem připravených entit jsou vyvíjeny v projektu open source nástrojů pro rozpoznávání textu. [Přispívat](https://github.com/Microsoft/Recognizers-Text) do projektu. Tento projekt obsahuje příklady měny na jazykovou verzi. 

GeographyV2 a PersonName nejsou součástí projektu nástrojů pro rozpoznávání textu. Problémy s těmito předem připravených entit, otevřete [žádost o podporu](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Další postup

Další informace o [číslo](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [měny](luis-reference-prebuilt-currency.md) entity. 
