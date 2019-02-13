---
title: Všechny předem připravených entit
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje seznam předem připravených entit, které jsou zahrnuty v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/12/2019
ms.author: diberry
ms.openlocfilehash: 880c825ab8923dbdf17277a51378b6f4b624e091
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179892"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Počet entit na jazykové verzi v modelu LUIS

Language Understanding (LUIS) poskytuje předem připravených entit. Předem připravených entit je obsažen ve vaší aplikaci, LUIS obsahuje odpovídající entity predikce v odpovědi koncového bodu. Všechny projevy příkladu jsou označeny jako také s entitou. Chování předem připravených entit **nelze** upravit. Pokud není uvedeno jinak, jsou předem připravených entit k dispozici ve všech oblastech aplikace LUIS (jazykové verze). V následující tabulce jsou uvedeny předem připravených entit, které jsou podporovány pro každou jazykovou verzi.

|Jazyková verze|Subkultury|
|--|--|
|Čínština|[zh-CN](#chinese-entity-support)|
|Holandština|[NL-NL](#dutch-entity-support)|
|Angličtina|[en US (American)](#english-american-entity-support)|
|Francouzština|[fr-CA (Kanada)](#french-canadian-entity-support), [fr-FR (Francie)](#french-france-entity-support), |
|Němčina|[de-DE](#german-entity-support)|
|italština|[IT-IT](#italian-entity-support)|
|Japonština|[ja-JP](#japanese-entity-support)|
|Korejština|[ko-KR](#korean-entity-support)|
|Portugalština|[pt-BR (Brazílie)](#portuguese-brazil-entity-support)|
|Španělština|[ES-ES (Španělsko)](#spanish-spain-entity-support), [es-MX (Mexiko)](#spanish-mexico-entity-support)|
|turečtina|[Turečtina](#turkish-entity-support)|

## <a name="chinese-entity-support"></a>Podpora čínských entity

Podporují se následující entity:

|Předem připravených entit|```zh-CN``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Podpora Nizozemská entity

Podporují se následující entity:

|Předem připravených entit|```nl-NL``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    -   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Angličtina (American) entity podpory

Podporují se následující entity:

|Předem připravených entit|```en-US``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Podpora entity francouzština (Francie)

Podporují se následující entity:

|Předem připravených entit|```fr-FR``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Francouzština (Kanada) entity podpory

Podporují se následující entity:

|Předem připravených entit|```fr-CA``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Podpora německé entity

Podporují se následující entity:

|Předem připravených entit|```de-DE``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Podpora italské entity

Podporují se následující entity:

|Předem připravených entit|```it-IT``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    -   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Podpora japonské entity

Podporují se následující entity:

|Předem připravených entit|```ja-JP``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    -   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Podpora Korejské entity

Podporují se následující entity:

|Předem připravených entit|```ko-KR``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    -   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    -   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    -   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    -   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Podpora entity portugalština (Brazílie)

Podporují se následující entity:

|Předem připravených entit|```pt-BR``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Podpora entity Španělština (Španělsko)

Podporují se následující entity:

|Předem připravených entit|```es-ES``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    ✔   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    ✔   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Španělština (Mexiko) entity podpory

Podporují se následující entity:

|Předem připravených entit|```es-MX``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    -   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    -   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Číslo](luis-reference-prebuilt-number.md)   |    ✔   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    -   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    ✔   |

Pro naleznete v poznámkách [zastaralé předem připravených entit](luis-reference-prebuilt-deprecated.md)

Není k dispozici ve všech subkultury portugalština (Brazílie) – KeyPhrase ```pt-BR```.

## <a name="turkish-entity-support"></a>Podpora turecké entity

Podporují se následující entity:

|Předem připravených entit|```tr-tr``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    -   |
[Měny](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    -   | 
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Číslo](luis-reference-prebuilt-number.md)   |    -   |  
[Pořadí](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Procento](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonní číslo](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Teplota](luis-reference-prebuilt-temperature.md):<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    -   | 
[Adresa URL](luis-reference-prebuilt-url.md)   |    -   |

Pro naleznete v poznámkách [zastaralé předem připravených entit](luis-reference-prebuilt-deprecated.md)

KeyPhrase není k dispozici.


## <a name="contribute-to-prebuilt-entity-cultures"></a>Přispívat do předem připravených entit jazykových verzí
Předem připravených entit jsou vyvíjeny v projektu open source nástrojů pro rozpoznávání textu. [Přispívat](https://github.com/Microsoft/Recognizers-Text) do projektu. Tento projekt obsahuje příklady měny na jazykovou verzi. 

GeographyV2 a PersonName nejsou součástí projektu nástrojů pro rozpoznávání textu. Problémy s těmito předem připravených entit, otevřete [žádost o podporu](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Další postup

Další informace o [číslo](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [měny](luis-reference-prebuilt-currency.md) entity. 
