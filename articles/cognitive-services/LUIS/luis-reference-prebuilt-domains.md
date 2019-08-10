---
title: Předem sestavený odkaz na doménu – LUIS
titleSuffix: Azure Cognitive Services
description: Referenční informace pro předem připravených domén, které jsou předem sestavené kolekce záměry a entity z Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 6e701396191ec8bf66ece0e2858b9f32857e7b17
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933586"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Předem sestavený odkaz na doménu pro aplikaci LUIS
V tomto materiálu najdete informace o [předem připravených domén](luis-how-to-use-prebuilt-domains.md), které jsou předem sestavené kolekce záměry a entity, které nabízí služba LUIS.

[Vlastní domény](luis-how-to-start-new-app.md), naopak spustit bez záměry a modely. Všechny předem připravených domény záměry a entity můžete přidat do vlastního modelu.

## <a name="supported-domains-across-cultures"></a>Podporované domény napříč kulturami

Jediná podporovaná jazyková verze je angličtina. 

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
|Kalendář|Kalendář je cokoli o osobních schůzkách a událostech, _ne_ k veřejným událostem (například k plánům na světě, kalendářům událostí v Seattlu) nebo k obecným kalendářům (například k tomu, co se dnes nachází v dnešním dni).|
|Komunikace|Žádosti o provedení volání, posílání textů nebo rychlých zpráv, hledání a přidávání kontaktů a různé požadavky související s komunikací (obecně odchozí). Dotazy jenom na název kontaktu nepatří do domény komunikace.|
|Email|E-mail je subdoménou domény komunikace. Hlavně obsahuje žádosti o posílání a přijímání zpráv prostřednictvím e-mailů.|
|HomeAutomation|Doména HomeAutomation poskytuje záměry a entity týkající se řízení inteligentních domácích zařízení. Hlavně podporuje řídicí příkaz týkající se světel a klimatizace, ale má určité možnosti generalizace pro jiné elektrické spotřebiče.|
|Poznámky|Poznámka: doména poskytuje záměry a entity pro vytváření poznámek a zápis položek pro uživatele.|
|Místa|Mezi ně patří firmy, instituce, restaurace, veřejné prostory a adresy. Doména podporuje místo hledání a dotaz na informace o veřejném místě, jako je například poloha, provozní doba a vzdálenost.|
|RestaurantReservation|Rezervační doména restaurace podporuje záměry pro zpracování rezervací pro restaurace.|
|ToDo|Doména ToDo poskytuje typy seznamů úkolů pro uživatele, kteří mají přidat, označit a odstranit položky ToDo.|
|Veřejné služby|Doména nástrojů je obecná doména mezi všemi LUIS předem sestavenými modely, které obsahují běžné záměry a projevy v rozdílných scénářích.|
|Počasí|V případě povětrnostních podmínek se zaměřuje na kontrolu povětrnostní podmínky a Advisory s využitím místa a času nebo při kontrole podle počasí.|
|Web|Webová doména poskytuje záměr a entity pro hledání webu.|
