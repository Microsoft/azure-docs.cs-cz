---
title: Nastavení aplikace
titleSuffix: Azure Cognitive Services
description: Vysvětlení nastavení aplikace pro Language understanding aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 3682b9e0c38344be1522440290b46f8c10bd5607
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275910"
---
# <a name="application-settings"></a>Nastavení aplikace

Tato nastavení aplikace jsou uložené v [exportovat](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplikace a [aktualizovat](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) pomocí rozhraní REST API. Změna nastavení verze aplikace obnoví stav vaší aplikace školení Nezkušený.

|Nastavení|Výchozí hodnota|Poznámky|
|--|--|--|
|NormalizePunctuation|Pravda|Odebere interpunkce.|
|NormalizeDiacritics|Pravda|Odebere znaky s diakritikou.|

## <a name="diacritics-normalization"></a>Normalizace diakritiku 

Zapnout utterance normalizace pro znaky s diakritikou v souboru aplikace LUIS JSON `settings` parametru.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Následující projevy ukazují, jak diakritiku normalizace ovlivňuje projevy:

|S diakritikou nastavena na hodnotu false|S diakritikou nastavenou na hodnotu true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Podpora jazyků pro diakritiku

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brazilská portugalština `pt-br` diakritiku

|Znaky s diakritikou nastavena na hodnotu false|Znaky s diakritikou nastavenou na hodnotu true|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`| 
|||

#### <a name="dutch-nl-nl-diacritics"></a>Holandština `nl-nl` diakritiku

|Znaky s diakritikou nastavena na hodnotu false|Znaky s diakritikou nastavenou na hodnotu true|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`| 
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Francouzština `fr-` diakritiku

To zahrnuje francouzština a Kanadské subkultury.

|Znaky s diakritikou nastavena na hodnotu false|Znaky s diakritikou nastavenou na hodnotu true|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`| 
|`ê`|`e`| 
|`î`|`i`| 
|`ô`|`o`| 
|`û`|`u`| 
|`ç`|`c`| 
|`ë`|`e`| 
|`ï`|`i`| 
|`ü`|`u`| 
|`ÿ`|`y`| 

#### <a name="german-de-de-diacritics"></a>Němčina `de-de` diakritiku

|Znaky s diakritikou nastavena na hodnotu false|Znaky s diakritikou nastavenou na hodnotu true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Italština `it-it` diakritiku

|Znaky s diakritikou nastavena na hodnotu false|Znaky s diakritikou nastavenou na hodnotu true|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó`|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Španělština `es-` diakritiku

To zahrnuje španělština a Kanadské mexických.

|Znaky s diakritikou nastavena na hodnotu false|Znaky s diakritikou nastavenou na hodnotu true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalizace interpunkce

Zapnout utterance normalizace pro interpunkce v souboru aplikace LUIS JSON `settings` parametru.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

Následující projevy ukazují, jak ovlivňuje projevy diakritiky:

|S diakritikou nastavena na hodnotu False|S diakritikou nastavena na hodnotu True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Interpunkce

Následující znaky interpunkce Odebereme s `NormalizePunctuation` je nastavena na hodnotu true.

|Interpunkce|
|--|
|`-`| 
|`.`| 
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
