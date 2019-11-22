---
title: Obecné pojmenované entity
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: a4c911fa077c2ec332974fb2f0c5abcdef21307c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284094"
---
## <a name="general-entity-types"></a>Obecné typy entit:

### <a name="person"></a>Person (Osoba)
Rozpoznané názvy a jiné osoby v textu.
Jazyky
* Verze Public Preview: `English`

| Název podtypu | Popis             |
|--------------|-------------------------|
| neuvedeno          | Rozpoznané názvy, například `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Umístění

Přírodní a uživatelsky vytvořené orientačních bodů, struktur a geografických funkcí.

Jazyky


* Verze Public Preview: `English`

| Název podtypu | Popis                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| neuvedeno          | umístění, například `Atlantic Ocean`, `library`, `Eiffel Tower``Statue of Liberty` |

### <a name="organization"></a>Organizace  

Uznávané organizace, společnosti, organizace a další skupiny lidí. Například: společnosti, politické skupiny, hudební pásma, sportovní klub, státní orgány a veřejné organizace. Státní příslušníky a náboženství nejsou zahrnuté do tohoto typu entity. Jazyky 

* Verze Public Preview: `English`

| Název podtypu | Popis                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| neuvedeno          | organizace, například `Microsoft``NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Telefonní číslo

Telefonní čísla (jenom telefonní čísla USA). 

Jazyky


* Verze Public Preview: `English`

| Název podtypu | Popis                                  |
|----------|----------------------------------------------|
| neuvedeno         | Telefonní čísla pro USA, například `(312) 555-0176`. |

### <a name="email"></a>Email

E-mailovou adresu 

Jazyky


* Verze Public Preview: `English`

| Název podtypu | Popis                                  |
|----------|----------------------------------------------|
| neuvedeno         | E-mailová adresa, například `support@contoso.com` |

### <a name="url"></a>Adresa URL

Internetové adresy URL.

Jazyky


* Verze Public Preview: `English`

| Název podtypu | Popis                                           |
|----------|-------------------------------------------------------|
| neuvedeno         | Adresy URL webů, například `https://www.bing.com`. |

###  <a name="number"></a>Číslo

Čísla a číselná množství. 

Jazyky


* Verze Public Preview: `English`

| Název podtypu    | Příklady                     |
|-------------|------------------------------|
| neuvedeno         | `6`, `six`                   |
| Procento  | `50%`, `fifty percent`       |
| Pořadí     | `2nd`, `second`              |
| Currency    | `$10.99`, `€30.00`           |
| Dimenze   | `10 miles`, `40 cm`          |
| Teplota | `32 degrees`, `10°C`         |
