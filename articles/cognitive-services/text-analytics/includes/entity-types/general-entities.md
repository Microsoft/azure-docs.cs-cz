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
ms.openlocfilehash: f95d7613926cf332a498cca84563dbc0ebcbbe9b
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086886"
---
## <a name="general-entity-types"></a>Obecné typy entit:

### <a name="person"></a>Person (Osoba)

Rozpoznat jména osob v textu.

Jazyky:
* Verze Public Preview: `English`

| Název podtypu | Popis                                                      | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------------------------|---------------------------------------|
| Není k dispozici          | Rozpoznané jméno osoby, například `Bill Gates`, `Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
Typ úlohy nebo role držené osobou.

Jazyky:
* Verze Public Preview: `English`

| Název podtypu | Popis                                                                                | K dispozici počínaje verzí modelu |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| Není k dispozici          | Typy úloh například `civil engineer`, `salesperson`, `chef`, `librarian`, `nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Umístění

Přirozené a uživatelsky vytvořené orientačních bodů, struktur, geografických funkcí a geopolitických entit.

Jazyky:

* Verze Public Preview: `English`

| Název podtypu              | Popis                                                                              | K dispozici počínaje verzí modelu |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| Není k dispozici                       | umístění, například `Atlantic Ocean`, `library`, `Eiffel Tower``Statue of Liberty`  | `2019-10-01`                           |
| Geopolitická entita (GPE) | Města, země, státy, například `Seattle`, `Pennsylvania`, `South Africa``Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organizace  

Uznávané organizace, společnosti, organizace a další skupiny lidí. Například: společnosti, politické skupiny, hudební pásma, sportovní klub, státní orgány a veřejné organizace. Státní příslušníky a náboženství nejsou zahrnuté do tohoto typu entity. 

Jazyky: 

* Verze Public Preview: `English`

| Název podtypu | Popis                                                                                             | K dispozici počínaje verzí modelu |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| Není k dispozici          | organizace, například `Microsoft`, `NASA`, `National Oceanic and Atmospheric Administration``VOA` | `2019-10-01`                           |

### <a name="event"></a>Událost  

Historické, sociální a přirozené události, které se vyskytují.  

Jazyky: 

* Verze Public Preview: `English`

| Název podtypu | Popis                                                            | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| Není k dispozici          | Události jako `wedding`, `hurricane`, `car accident`, `solar eclipse`, `American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produkt  

Fyzické objekty různých kategorií.  

Jazyky: 

* Verze Public Preview: `English`

| Název podtypu | Popis                                                                        | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| Není k dispozici          | Například `Microsoft Surface laptop`, `sunglasses`, `motorcycle``bag`, `Xbox` | `2020-02-01`                           |
| Výpočetní operace    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Tuhle  

Entita popisující schopnost nebo odbornost.  

Jazyky: 

* Verze Public Preview: `English`

| Název podtypu | Popis                                                                 | K dispozici počínaje verzí modelu |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| Není k dispozici          | `nursing`, `data mining`, `linguistics`, `critical thinking``photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Telefonní číslo

Telefonní čísla (jenom telefonní čísla USA). 

Jazyky:

* Verze Public Preview: `English`

| Název podtypu | Popis                                    | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------|----------------------------------------|
| Není k dispozici          | Telefonní čísla USA, například `(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

E-mailovou adresu. 

Jazyky:

* Verze Public Preview: `English`

| Název podtypu | Popis                                      | K dispozici počínaje verzí modelu |
|--------------|--------------------------------------------------|----------------------------------------|
| Není k dispozici          | E-mailová adresa, například `support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>Adresa URL

Internetové adresy URL.

Jazyky:

* Verze Public Preview: `English`

| Název podtypu | Popis                                          | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------------|----------------------------------------|
| Není k dispozici          | Adresy URL webů, například `https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP adresa

Adresa Internet Protocol

Jazyky:

* Verze Public Preview: `English`

| Název podtypu | Popis                              | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------|----------------------------------------|
| Není k dispozici          | Síťová adresa například `10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Entity data a času. 

* K dispozici počínaje verzí modelu `2019-10-01`

Jazyky:

* Verze Public Preview: `English`

| Název podtypu    | Příklady                     |
|-------------|------------------------------|
| Není k dispozici         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Datum  | `May 2nd, 2017`, `05/02/2017`       |
| Čas     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Doba trvání | `2.5 minutes`, `one and a half hours`         |
| Nastavit | `every Saturday`         |

###  <a name="quantity"></a>Množství

Čísla a číselná množství. 

* K dispozici počínaje verzí modelu `2019-10-01`

Jazyky:

* Verze Public Preview: `English`

| Název podtypu    | Příklady                     |
|-------------|------------------------------|
| Číslo         | `6`, `six`                   |
| Procento  | `50%`, `fifty percent`       |
| Řadový     | `2nd`, `second`              |
| Věk         | `90 day old`, `30 years old` |
| Currency    | `$10.99`, `€30.00`           |
| Globální   | `10 miles`, `40 cm`          |
| Temperature | `32 degrees`, `10°C`         |
