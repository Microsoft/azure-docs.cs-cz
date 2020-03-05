---
title: Nastavení aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Nastavení aplikací pro porozumění aplikacím v Azure Cognitive Services se ukládají do aplikace a portálu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: d1ead09f6248a6ad14646371aa70b42b57cf8e3f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270805"
---
# <a name="application-settings"></a>Nastavení aplikace

Tato nastavení aplikace jsou uložená v [exportované](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplikaci a [aktualizovaná](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) pomocí rozhraní REST API. Změna nastavení verze aplikace obnoví stav školení aplikace na neučení.

|Nastavení|Výchozí hodnota|Poznámky:|
|--|--|--|
|NormalizePunctuation|True|Odebere interpunkci.|
|NormalizeDiacritics|True|Odebere diakritická znaménka.|

## <a name="diacritics-normalization"></a>Normalizace diakritiky

V parametru `settings` zapněte normalizaci utterance pro diakritická znaménka na váš soubor aplikace LUIS JSON.

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

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brazilské portugalštiny `pt-br` diakritiky

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

#### <a name="dutch-nl-nl-diacritics"></a>Nizozemská `nl-nl` diakritika

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

#### <a name="french-fr--diacritics"></a>`fr-` diakritiky ve francouzštině

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

#### <a name="italian-it-it-diacritics"></a>`it-it` diakritiky pro italštinu

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

#### <a name="spanish-es--diacritics"></a>Španělština `es-` diakritiky

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

Zapněte normalizaci utterance pro interpunkci do souboru aplikace LUIS JSON v parametru `settings`.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

Následující projevy ukazuje, jak interpunkce má vliv na projevy:

|S interpunkčním znaménkem nastaveným na false|S interpunkčním znaménkem nastaveným na true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Odebraná interpunkce

Následující interpunkční znaménko se odebere s `NormalizePunctuation` je nastavená na hodnotu true.

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
