---
title: Obecné pojmenované entity
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 630f04bf2cc9e7de6331f9d25754a20fe2327d76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779721"
---
Při odesílání požadavků do koncového bodu se vrátí následující kategorie entit `/entities/recognition/general` .

| Kategorie   | Subcategory | Description                          | Spouští se verze modelu.                                                    | Poznámky |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| Osoba     | Není k dispozici         | Jména lidí.  | `2019-10-01`  | Vráceno také NER v 2.1 |
| PersonType | Není k dispozici         | Typy úloh nebo role držené osobou. | `2020-02-01` | |
|Umístění    | Není k dispozici         | Přírodní a lidský orientačních bodů, struktur, geografických funkcí a geopolitických entit     |  `2019-10-01` | Vráceno také NER v 2.1 |
|Umístění     | Geopolitická entita (GPE)        | Města, země nebo oblasti, státy.      | `2020-02-01` | |
|Umístění     | Strukturované                       | Struktury manmade. | `2020-04-01` | |
|Umístění     | Zeměpisné       | Geografické a přirozené funkce, jako jsou řeky, oceány a Deserts. |  `2020-04-01` | |
|Organizace  | Není k dispozici | Společnosti, politické skupiny, hudební pásma, sportovní klub, státní orgány a veřejné organizace.  | `2019-10-01` | Státní příslušníky a náboženství nejsou zahrnuté do tohoto typu entity. Vráceno také NER v 2.1 |
|Organizace | Lékař | Lékařské společnosti a skupiny. | `2020-04-01` |  |
|Organizace | Burzovní Exchange | Burzovní skupiny Exchange. | `2020-04-01` | |
| Organizace | Sport | Organizace související s sportem. | `2020-04-01` |  |
| Událost  | Není k dispozici | Historické, společenské a přirozeně vyskytující se události. | `2020-02-01` |  |
| Událost  | Kulturní | Kulturní události a svátky. | `2020-04-01` | |
| Událost  | Len | Přirozeně vyskytující se události. | `2020-04-01` |  |
| Událost  | Sport | Sportovní události.  | `2020-04-01` | |
| Produkt | Není k dispozici | Fyzické objekty různých kategorií. | `2020-02-01` | |
| Produkt | Výpočetní produkty | Výpočetní produkty. |  `2020-02-01 ` | |
| Tuhle | Není k dispozici | Schopnost, dovednosti nebo odbornost. | `2020-02-01` |  |
| Adresa | Není k dispozici | Úplné poštovní adresy.  | `2020-04-01` |  |
| PhoneNumber | Není k dispozici | Telefonní čísla (jenom USA a telefonní čísla EU). | `2019-10-01` | Vráceno také NER v 2.1 |
| E-mail | Není k dispozici | E-mailové adresy. | `2019-10-01` | Vráceno také NER v 2.1 |
| URL | Není k dispozici | Adresy URL webů. | `2019-10-01` | Vráceno také NER v 2.1  |
| IP adresa | Není k dispozici | Síťové IP adresy. | `2019-10-01` | Vráceno také NER v 2.1 |
| DateTime | Není k dispozici | Data a denní doba. | `2019-10-01` | Vráceno také NER v 2.1 | 
| Datum a čas | Datum | Kalendářní data kalendáře | `2019-10-01` | Vráceno také NER v 2.1 |
| Datum a čas | Čas | Denní doba | `2019-10-01` | Vráceno také NER v 2.1 |
| Datum a čas | Rozsah dat | Rozsahy dat. | `2019-10-01` | Vráceno také NER v 2.1 |
| Datum a čas | Časový rozsah | Časové rozsahy. | `2019-10-01` | Vráceno také NER v 2.1 |
| Datum a čas | Doba trvání | Dob trvání. | `2019-10-01` | Vráceno také NER v 2.1 |
| Datum a čas | Sada | Nastavte opakující se časy. |  `2019-10-01` | Vráceno také NER v 2.1 |
| Množství | Není k dispozici | Čísla a číselná množství. | `2019-10-01` | Vráceno také NER v 2.1  |
| Množství | Číslo | Hodnoty. | `2019-10-01` | Vráceno také NER v 2.1 |
| Množství | Procento | Procenta.| `2019-10-01` | Vráceno také NER v 2.1 |
| Množství | Řádová číslovka | Pořadová čísla | `2019-10-01` | Vráceno také NER v 2.1 |
| Množství | Věk | Ve věku. | `2019-10-01` |  Vráceno také NER v 2.1 |
| Množství | Měna | Měnami. | `2019-10-01` | Vráceno také NER v 2.1 |
| Množství | Rozměr | Rozměry a měření. | `2019-10-01` | Vráceno také NER v 2.1 |
| Množství | Teplota | Teplot. | `2019-10-01` | Vráceno také NER v 2.1 |
