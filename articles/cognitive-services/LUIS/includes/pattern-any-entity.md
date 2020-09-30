---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.openlocfilehash: 5eb2517b4b71b61dd70406f345c005d007d9e3ef
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535949"
---
Entita Pattern.any umožňuje vyhledat data volného tvaru, kde je kvůli formulaci entity obtížné odlišit konec entity od zbytku promluvy.

Aplikace pro lidské zdroje pomáhá zaměstnancům najít formuláře společnosti.

|Promluva|
|--|
|Where is **HRF-123456**? (Kde je HRF-123456?)|
|Who authored **HRF 123234**? (Kdo vytvořil HRF 123234?)|
|**HRF-456098** is published in French? (Je HRF-456098 ve francouzštině?)|

Každý formulář však má formátovaný název použitý v předchozí tabulce i popisný název, jako je například `Request relocation from employee new to the company 2018 version 5`.

Projevy s popisnými názvy formulářů vypadají takto:

|Promluva|
|--|
|Where is **Request relocation from employee new to the company 2018 version 5**? (Kde je Žádost o přeložení nového zaměstnance společnosti z roku 2018 verze 5?)|
|Who authored **Request relocation from employee new to the company 2018 version 5**? (Kdo vytvořil Žádost o přeložení nového zaměstnance společnosti z roku 2018 verze 5?)|
|**Request relocation from employee new to the company 2018 version 5**? (Je Žádost o přeložení nového zaměstnance společnosti z roku 2018 verze 5 ve francouzštině?)|

Různá délka promluv zahrnuje slova, která můžou službě LUIS komplikovat určení konce entity. Když ve vzoru použijete entitu Pattern.any, můžete určit začátek a konec názvu formuláře, aby služba LUIS mohla správně extrahovat název formuláře.

|Příklad šablony promluvy|
|--|
|Where is {FormName}[?] (Kde je {FormName}[?])|
|Who authored {FormName}[?] (Kdo vytvořil {FormName}[?])|
|{FormName} is published in French[?] (Je {FormName} ve francouzštině[?])|

Kontrola vzoru. jakékoli [referenční](../reference-entity-pattern-any.md) informace