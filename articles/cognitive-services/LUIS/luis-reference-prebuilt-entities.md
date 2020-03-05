---
title: Všechny předem připravené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje seznam předem připravených entit, které jsou zahrnuty v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a49452653f8ac4bcc62758d801a235be0929d314
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270587"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entity na jazykovou verzi v modelu LUIS

Language Understanding (LUIS) poskytuje předem připravených entit. Předem připravených entit je obsažen ve vaší aplikaci, LUIS obsahuje odpovídající entity predikce v odpovědi koncového bodu. Všechny projevy příkladu jsou označeny jako také s entitou. Chování předem sestavených entit **nelze** změnit. Pokud není uvedeno jinak, jsou předem připravených entit k dispozici ve všech oblastech aplikace LUIS (jazykové verze). V následující tabulce jsou uvedeny předem připravených entit, které jsou podporovány pro každou jazykovou verzi.

|Kultura|Subkultury|Poznámky:|
|--|--|--|
|Čínština|[zh-CN](#chinese-entity-support)||
|Nizozemština|[nl – NL](#dutch-entity-support)||
|Angličtina|[EN-US (USA)](#english-american-entity-support)||
|Francouzština|[fr – CA (Kanada)](#french-canadian-entity-support), [fr-FR (Francie)](#french-france-entity-support), ||
|Němčina|[de-DE](#german-entity-support)||
|Italština|[IT oddělení IT](#italian-entity-support)||
|Japonština|[ja-JP](#japanese-entity-support)||
|Korejština|[ko – KR](#korean-entity-support)||
|Portugalština|[pt-BR (Brazílie)](#portuguese-brazil-entity-support)||
|Španělština|[ES-ES (Španělsko)](#spanish-spain-entity-support), [ES-MX (Mexiko)](#spanish-mexico-entity-support)||
|Turečtina|[Turečtina](#turkish-entity-support)|V turečtině nejsou podporované předem připravené entity|

## <a name="prediction-endpoint-runtime"></a>Modul runtime koncového bodu předpovědi

Dostupnost předem připravené entity v konkrétním jazyce je určena verzí modulu runtime koncového bodu předpovědi.

## <a name="chinese-entity-support"></a>Podpora čínských entity

Podporují se následující entity:

|Předem připravených entit|```zh-CN``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    V2, V3   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Podpora Nizozemská entity

Podporují se následující entity:

|Předem připravených entit|```nl-NL``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    V2, V3   |
[Hodnotu](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Angličtina (American) entity podpory

Podporují se následující entity:

|Předem připravených entit|```en-US``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    V2, V3   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Podpora entity francouzština (Francie)

Podporují se následující entity:

|Předem připravených entit|```fr-FR``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    V2, V3   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |   -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Francouzština (Kanada) entity podpory

Podporují se následující entity:

|Předem připravených entit|```fr-CA``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    V2, V3   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Podpora německé entity

Podporují se následující entity:

|Předem připravených entit|```de-DE``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    V2, V3   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Podpora italské entity

Předem sestavený věk v italštině, měna, dimenze, číslo a procento _rozlišení_ bylo změněno z v2 a V3 Preview.

Podporují se následující entity:

|Předem připravených entit|```it-IT``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    V2, V3   |
[Hodnotu](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Podpora japonské entity

Podporují se následující entity:

|Předem připravených entit|```ja-JP``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    V2,-   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    V2,-   |
[Hodnotu](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    V2,-   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2,-   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    V2,-   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2,-   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    V2,-   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Podpora Korejské entity

Podporují se následující entity:

|Předem připravených entit|```ko-KR``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    -   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    -   |
[Hodnotu](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    -   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    -   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Podpora entity portugalština (Brazílie)

Podporují se následující entity:

|Předem připravených entit|```pt-BR``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    V2, V3   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Podpora entity Španělština (Španělsko)

Podporují se následující entity:

|Předem připravených entit|```es-ES``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    V2, V3   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Španělština (Mexiko) entity podpory

Podporují se následující entity:

|Předem připravených entit|```es-MX``` |
------|:------:|
[Stáří](luis-reference-prebuilt-age.md):<br>rok<br>měsíc<br>týden<br>den   |    -   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>dolar<br>zlomkové jednotky (ex: pritzker)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    -   |
[Dimenze](luis-reference-prebuilt-dimension.md):<br>svazek<br>Oblast<br>Váha<br>informace (ex: bit nebo byte)<br>Délka (ex: měřidla)<br>rychlost (ex: mil za hodinu)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Automatické](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Řadový](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita.<br>kelvinech.<br>rankine<br>delisle<br>ve stupních Celsia   |    -   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Zobrazit poznámky u [zastaralých předem definovaných entit](luis-reference-prebuilt-deprecated.md)

KeyPhrase není k dispozici ve všech podkulturách portugalštiny (Brazílie) – ```pt-BR```.

## <a name="turkish-entity-support"></a>Podpora pro tureckou entitu

**V turečtině nejsou podporovány žádné předem připravené entity.**

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Přispívat do předem připravených entit jazykových verzí
Předem připravených entit jsou vyvíjeny v projektu open source nástrojů pro rozpoznávání textu. [Přispět](https://github.com/Microsoft/Recognizers-Text) k projektu. Tento projekt obsahuje příklady měny na jazykovou verzi.

GeographyV2 a PersonName nejsou součástí projektu nástrojů pro rozpoznávání textu. V případě problémů s těmito předem vytvořenými entitami prosím otevřete [žádost o podporu](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Další kroky

Seznamte se s entitami [Number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)a [Currency](luis-reference-prebuilt-currency.md) .
