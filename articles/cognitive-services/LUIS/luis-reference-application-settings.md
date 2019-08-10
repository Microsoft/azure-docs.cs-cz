---
title: Nastavení aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Pochopení nastavení aplikací pro jazyk porozumění aplikacím
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 6d516590b90efd937789fa0847b707d2521f6459
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932581"
---
# <a name="application-settings"></a>Nastavení aplikace

Tato nastavení aplikace jsou uložená v [exportované](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplikaci a [aktualizovaná](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) pomocí rozhraní REST API. Změna nastavení verze aplikace obnoví stav školení aplikace na neučení.

|Nastavení|Výchozí hodnota|Poznámky|
|--|--|--|
|NormalizePunctuation|Pravda|Odebere interpunkci.|
|NormalizeDiacritics|Pravda|Odebere diakritická znaménka.|

## <a name="diacritics-normalization"></a>Normalizace diakritiky 

V `settings` parametru zapněte normalizaci utterance pro diakritická znaménka na váš soubor aplikace Luis JSON.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Následující projevy ukazuje, jakým způsobem normalizace diakritiky ovlivňuje projevy:

|S diakritikou nastavenou na hodnotu false|S diakritikou nastavenou na hodnotu true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Jazyková podpora pro diakritická znaménka

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brazilské brazilská `pt-br` diakritika

|Diakritická znaménka nastavena na hodnotu false|Diakritická znaménka nastavená na true|
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

#### <a name="dutch-nl-nl-diacritics"></a>Nizozemská `nl-nl` diakritická znaménka

|Diakritická znaménka nastavena na hodnotu false|Diakritická znaménka nastavená na true|
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

#### <a name="french-fr--diacritics"></a>Francouzské `fr-` diakritiky

To zahrnuje francouzské i kanadské jazykové kultury.

|Diakritická znaménka nastavena na hodnotu false|Diakritická znaménka nastavená na true|
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

#### <a name="german-de-de-diacritics"></a>Německé `de-de` diakritiky

|Diakritická znaménka nastavena na hodnotu false|Diakritická znaménka nastavená na true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Nizozemská `it-it` diakritická znaménka

|Diakritická znaménka nastavena na hodnotu false|Diakritická znaménka nastavená na true|
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

#### <a name="spanish-es--diacritics"></a>Španělská `es-` diakritika

To zahrnuje jak španělštinu, tak i kanadskou mexický.

|Diakritická znaménka nastavena na hodnotu false|Diakritická znaménka nastavená na true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalizace interpunkce

Zapněte normalizaci utterance pro interpunkci do souboru aplikace Luis JSON v `settings` parametru.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

Následující projevy ukazuje, jak mají diakritická znaménka vliv na projevy:

|S diakritikou nastavenou na hodnotu false|S diakritikou nastavenou na hodnotu true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Odebraná interpunkce

Následující interpunkční znaménko je odebráno s `NormalizePunctuation` nastavením na hodnotu true.

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
