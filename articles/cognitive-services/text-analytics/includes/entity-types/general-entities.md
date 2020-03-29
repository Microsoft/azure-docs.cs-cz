---
title: Obecné pojmenované entity
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211382"
---
## <a name="general-entity-types"></a>Obecné typy entit:

### <a name="person"></a>Person (Osoba)

Rozpoznání jmen osob v textu.

Jazyky:
* Veřejná `Arabic`verze `Czech` `Chinese-Simplified`preview: `English`, `Finnish` `French`, `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , `Dutch`, , , , , , , , , , , , , , a `Danish``Turkish`

| Název podtypu | Popis                                                      | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------------------------|---------------------------------------|
| Není dostupné.          | Jména rozpoznaných `Bill Gates`osob, například ,`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>Typ osoby
Typ zaměstnání nebo role v držení osoby.

Jazyky:
* Veřejná verze preview:`English`

| Název podtypu | Popis                                                                                | K dispozici počínaje verzí modelu |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| Není dostupné.          | Typy úloh `civil engineer`například `chef` `librarian`, `salesperson`, , ,`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Umístění

Přírodní a lidské památky, struktury, geografické rysy a geopolitické entity.

Jazyky:

* Veřejná `Arabic`verze `Czech` `Chinese-Simplified`preview: `English`, `Finnish` `French`, `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , `Dutch`, , , , , , , , , , , , , , a `Danish``Turkish`

| Název podtypu              | Popis                                                                              | K dispozici počínaje verzí modelu |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| Není dostupné.                       | lokality, `Atlantic Ocean`například , `library`, `Eiffel Tower``Statue of Liberty`  | `2019-10-01`                           |
| Geopolitická entita (GPE) - pouze v angličtině| Města, země, státy `Seattle` `Pennsylvania`například , , `South Africa``Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organizace  

Uznávané organizace, korporace, agentury a další skupiny lidí. Například: společnosti, politické skupiny, hudební skupiny, sportovní kluby, vládní orgány a veřejné organizace. Národnosti a náboženství nejsou zahrnuty v tomto typu entity. 

Jazyky: 

* Veřejná `Arabic`verze `Czech` `Chinese-Simplified`preview: `English`, `Finnish` `French`, `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , `Dutch`, , , , , , , , , , , , , , a `Danish``Turkish`

| Název podtypu | Popis                                                                                             | K dispozici počínaje verzí modelu |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| Není dostupné.          | organizace, například `Microsoft` `NASA`, `National Oceanic and Atmospheric Administration`,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Událost  

Historické, společenské a přirozené události.  

Jazyky: 

* Veřejná verze preview:`English`

| Název podtypu | Popis                                                            | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| Není dostupné.          | Události `wedding`jako `hurricane`, `car accident` `solar eclipse`, , ,`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produkt  

Fyzické objekty různých kategorií.  

Jazyky: 

* Veřejná verze preview:`English`

| Název podtypu | Popis                                                                        | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| Není dostupné.          | Například `Microsoft Surface laptop`, `sunglasses` `motorcycle`, `bag`, , ,`Xbox` | `2020-02-01`                           |
| Výpočetní operace    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Dovednost  

Entita popisující schopnosti nebo odborné znalosti.  

Jazyky: 

* Veřejná verze preview:`English`

| Název podtypu | Popis                                                                 | K dispozici počínaje verzí modelu |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| Není dostupné.          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Telefonní číslo

Telefonní čísla (pouze telefonní čísla USA). 

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                                    | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------|----------------------------------------|
| Není dostupné.          | Telefonní čísla v USA, například`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>E-mail

E-mailovou adresu 

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                                      | K dispozici počínaje verzí modelu |
|--------------|--------------------------------------------------|----------------------------------------|
| Není dostupné.          | E-mailová adresa, například`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>zprostředkovatele identity

Adresy URL internetu.

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                                          | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------------|----------------------------------------|
| Není dostupné.          | Adresy URL pro webové stránky, například`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP adresa

Adresa internetového protokolu

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                              | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------|----------------------------------------|
| Není dostupné.          | Síťová adresa například`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Entity data a času. 

* K dispozici počínaje verzí modelu`2019-10-01`

Jazyky:

* Veřejná `Chinese-Simplified`předběžná `French` `German` verze: , `English`, a`Spanish`

| Název podtypu    | Příklady                     |
|-------------|------------------------------|
| Není dostupné.         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Datum  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| Rozsah dat    | `August 2nd to August 5th`         |
| Časový rozsah   | `4-6PM`, `10:00AM to Noon`          |
| Doba trvání | `2.5 minutes`, `one and a half hours`         |
| Nastavit | `every Saturday`         |

###  <a name="quantity"></a>Množství

Čísla a číselná množství. 

* K dispozici počínaje verzí modelu`2019-10-01`

Jazyky:

* Veřejná `Chinese-Simplified`předběžná `French` `German` verze: , `English`, a`Spanish`

| Název podtypu    | Příklady                     |
|-------------|------------------------------|
| Číslo         | `6`, `six`                   |
| Procento  | `50%`, `fifty percent`       |
| Pořadové     | `2nd`, `second`              |
| Věk         | `90 day old`, `30 years old` |
| Měna    | `$10.99`, `€30.00`           |
| Dimenze   | `10 miles`, `40 cm`          |
| Teplota | `32 degrees`, `10°C`         |
