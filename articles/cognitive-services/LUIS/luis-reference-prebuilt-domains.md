---
title: Referenční informace k předem připraveným doménám
titleSuffix: Azure
description: Referenční informace pro předem připravených domén, které jsou předem sestavené kolekce záměry a entity z Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b615185472ede25fd6ceef9cc3e7325375e16ac7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846668"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Informace o předem připravených domény pro aplikace LUIS
V tomto materiálu najdete informace o [předem připravených domén](luis-how-to-use-prebuilt-domains.md), které jsou předem sestavené kolekce záměry a entity, které nabízí služba LUIS.

[Vlastní domény](luis-how-to-start-new-app.md), naopak spustit bez záměry a modely. Všechny předem připravených domény záměry a entity můžete přidat do vlastního modelu.

# <a name="supported-domains-across-cultures"></a>Podporované domény napříč jazykovými verzemi

Pouze podporované jazykové verze je angličtina. 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|Typ entity|description|
|--|--|
|Kalendář|Kalendář se něco o osobní schůzky a události, _není_ veřejné události (jako je například světa ve fotbale plány, kalendářů událostí Seattle) nebo obecný kalendáře (jako je například den je dnes, co fall začít, při Labor Day).|
|Komunikace|Požadavky pro volání, odesílat texty nebo rychlých zpráv, najděte a přidejte kontakty a různé další komunikaci žádosti týkající se (obecně odchozí). Jméno kontaktní osoby pouze dotazy nepatří do domény komunikace.|
|Email|E-mailu je subdoména domény, komunikace. Obsahuje především požadavky na odesílání a příjem zpráv prostřednictvím e-mailů.|
|HomeAutomation|Doména HomeAutomation poskytuje záměry a entity související řízení domácí chytrými zařízeními. Především podporuje příkaz Ovládací prvek související s indikátory a klimatizace, ale má některé schopnosti generalizace pro ostatní electric zařízení.|
|Poznámky|Poznámka: doména poskytuje záměry a entity pro vytváření poznámek a zápis dolů položky pro uživatele.|
|Místa|Místa vložení firmám, instituce, restaurace, veřejné mezer a adresy. Doména podporuje místo hledání a zeptat se na informace na veřejném místě, jako je například umístění, pracovní hodiny a vzdálenost.|
|RestaurantReservation|Restaurace rezervace doména podporuje příkazy pro zpracování rezervace restaurace.|
|ToDo|Doména ToDo poskytuje typy seznamů úkolů pro uživatele k přidání, označení a odstranění jejich položky seznamu úkolů.|
|Veřejné služby|Nástroje doména je obecné domény mezi všechny LUIS předem připravené modely, které obsahuje běžné příkazy a projevy ve scénářích rozdíl.|
|Počasí|Počasí domény se zaměřuje na kontrolu stavu počasí a poradci pro umístění a čas nebo kontrola čas počasí.|
|Web|Doména webové poskytuje záměr a entity pro vyhledávání na webu.|
