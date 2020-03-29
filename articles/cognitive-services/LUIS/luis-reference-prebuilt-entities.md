---
title: Všechny předem sestavené entity – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje seznamy předem vytvořených entit, které jsou zahrnuty v porozumění jazykům (LUIS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219717"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entity na jazykovou verzi v modelu LUIS

Porozumění jazykům (LUIS) poskytuje předem vytvořené entity. Když je do vaší aplikace zahrnuta předem sestavená entita, služba LUIS zahrne odpovídající predikci entity do odpovědi koncového bodu. Všechny ukázkové projevy jsou také označeny entitou. Chování předem vytvořených entit **nelze** změnit. Pokud není uvedeno jinak, předem vytvořené entity jsou k dispozici ve všech národních prostředích aplikací LUIS (jazykové verze). V následující tabulce jsou uvedeny předem vytvořené entity, které jsou podporovány pro každou jazykovou verzi.

|Culture|Subkultur|Poznámky|
|--|--|--|
|Chinese|[zh-CN](#chinese-entity-support)||
|Nizozemština|[nl-NL](#dutch-entity-support)||
|Angličtina|[en-US (americká)](#english-american-entity-support)||
|Francouzština|[fr-CA (Kanada)](#french-canadian-entity-support), [fr-FR (Francie),](#french-france-entity-support) ||
|Němčina|[de-DE](#german-entity-support)||
|Italština|[it-IT](#italian-entity-support)||
|Japonština|[ja-JP](#japanese-entity-support)||
|Korejština|[ko-KR](#korean-entity-support)||
|Portugalština|[pt-BR (Brazílie)](#portuguese-brazil-entity-support)||
|Španělština|[es-ES (Španělsko)](#spanish-spain-entity-support), [es-MX (Mexiko)](#spanish-mexico-entity-support)||
|Turečtina|[Turečtina](#turkish-entity-support)|Žádné předem vytvořené entity podporované v turečtině|

## <a name="prediction-endpoint-runtime"></a>Doba běhu koncového bodu předpovědi

Dostupnost předem vytvořené entity v určitém jazyce je určena verzí runtime koncového bodu předpověď.

## <a name="chinese-entity-support"></a>Podpora čínských subjektů

Podporovány jsou následující entity:

|Předem sestavená entita|```zh-CN``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozsah dat<br>time<br>časový rozsah   |    V2, V3   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Podpora nizozemských subjektů

Podporovány jsou následující entity:

|Předem sestavená entita|```nl-NL``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Podpora anglických (amerických) subjektů

Podporovány jsou následující entity:

|Předem sestavená entita|```en-US``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozsah dat<br>time<br>časový rozsah   |    V2, V3   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Podpora francouzských (francouzských) subjektů

Podporovány jsou následující entity:

|Předem sestavená entita|```fr-FR``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozsah dat<br>time<br>časový rozsah   |    V2, V3   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |   -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Podpora francouzských (kanadských) subjektů

Podporovány jsou následující entity:

|Předem sestavená entita|```fr-CA``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozsah dat<br>time<br>časový rozsah   |    V2, V3   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Podpora německých subjektů

Podporovány jsou následující entity:

|Předem sestavená entita|```de-DE``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozsah dat<br>time<br>časový rozsah   |    V2, V3   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Podpora italských subjektů

Italský předdefinovaný věk, měna, dimenze, číslo, procentuální _rozlišení_ změněno z náhledu V2 a V3.

Podporovány jsou následující entity:

|Předem sestavená entita|```it-IT``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Podpora japonských entit

Podporovány jsou následující entity:

|Předem sestavená entita|```ja-JP``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    V2, -   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    V2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    V2, -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, -   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    V2, -   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Podpora korejské entity

Podporovány jsou následující entity:

|Předem sestavená entita|```ko-KR``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    -   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    -   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    -   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    -   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Podpora portugalského (brazilského) subjektu

Podporovány jsou následující entity:

|Předem sestavená entita|```pt-BR``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozsah dat<br>time<br>časový rozsah   |    V2, V3   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Podpora španělského (španělského) subjektu

Podporovány jsou následující entity:

|Předem sestavená entita|```es-ES``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    V2, V3   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozsah dat<br>time<br>časový rozsah   |    V2, V3   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    V2, V3   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Podpora španělského (mexického) subjektu

Podporovány jsou následující entity:

|Předem sestavená entita|```es-MX``` |
------|:------:|
[Věk](luis-reference-prebuilt-age.md):<br>year<br>month<br>Týden<br>day   |    -   |
[Měna (peníze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>frakční jednotka (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozsah dat<br>time<br>časový rozsah   |    -   |
[Rozměr](luis-reference-prebuilt-dimension.md):<br>Objem<br>oblast<br>Hmotnost<br>informace (ex: bit/byte)<br>délka (ex: metr)<br>rychlost (například: míle za hodinu)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografieV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Číslo](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Pořadové](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procento](luis-reference-prebuilt-percentage.md)   |    -   |
[Jméno osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Teplota:](luis-reference-prebuilt-temperature.md)<br>Fahrenheita<br>Kelvin<br>rankine (rak.)<br>delisle (Fr.)<br>Celsia   |    -   |
[Adresa URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Viz poznámky [k zastaralé předem sestavené entity](luis-reference-prebuilt-deprecated.md)

KeyPhrase není k dispozici ve všech subkulturách ```pt-BR```portugalštiny (Brazílie) - .

## <a name="turkish-entity-support"></a>Podpora tureckých subjektů

**V turečtině nejsou podporovány žádné předem vytvořené entity.**

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

## <a name="contribute-to-prebuilt-entity-cultures"></a>Přispívat k předem vytvořeným kulturám entit
Předem sestavené entity jsou vyvinuty v projektu open-source rozpoznávání textu. [Přispějte](https://github.com/Microsoft/Recognizers-Text) k projektu. Tento projekt obsahuje příklady měny na jazykovou verzi.

GeographyV2 a PersonName nejsou zahrnuty v projektu rozpoznávání textu. V případě problémů s těmito předem vytvořenými entitami otevřete [žádost o podporu](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Další kroky

Informace o [číslech](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)a [měnových](luis-reference-prebuilt-currency.md) entitách.
