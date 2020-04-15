---
title: Nastavení aplikace - LUIS
description: Nastavení aplikací pro aplikace pro porozumění jazykům Azure Cognitive Services se ukládá v aplikaci a na portálu.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382207"
---
# <a name="application-settings"></a>Nastavení aplikace

Tato nastavení aplikace jsou uloženy v [exportované](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplikaci a [aktualizovány](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) pomocí rest API. Změna nastavení verze aplikace obnoví stav školení aplikace na neškolený.

Naučte se [koncepty diakritiky](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) a interpunkce.

|Nastavení|Výchozí hodnota|Poznámky|
|--|--|--|
|Normalizovat menstruaci|True|Odstraní interpunkci.|
|NormalizeDiakritiky|True|Odstraní diakritiku.|

## <a name="diacritics-normalization"></a>Normalizace diakritiky

Zapněte normalizaci utterance pro diakritiku souboru aplikace `settings` LUIS JSON v parametru.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

Následující projevy ukazují, jak normalizace diakritiky ovlivňuje projevy:

|S diakritikou nastavenou na false|S diakritikou nastavenou na hodnotu true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Jazyková podpora diakritiky

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brazilská `pt-br` portugalská diakritika

|Diakritika nastavena na hodnotu false|Diakritika nastavena na hodnotu true|
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

#### <a name="dutch-nl-nl-diacritics"></a>Holandská `nl-nl` diakritika

|Diakritika nastavena na hodnotu false|Diakritika nastavena na hodnotu true|
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

#### <a name="french-fr--diacritics"></a>Francouzská `fr-` diakritika

To zahrnuje jak francouzské, tak kanadské subkultury.

|Diakritika nastavena na hodnotu false|Diakritika nastavena na hodnotu true|
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

#### <a name="german-de-de-diacritics"></a>Německá `de-de` diakritika

|Diakritika nastavena na hodnotu false|Diakritika nastavena na hodnotu true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Italská `it-it` diakritika

|Diakritika nastavena na hodnotu false|Diakritika nastavena na hodnotu true|
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

To zahrnuje jak španělské a kanadské mexické.

|Diakritika nastavena na hodnotu false|Diakritika nastavena na hodnotu true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalizace interpunkce

Zapněte normalizaci utterance pro interpunkci do souboru aplikace `settings` LUIS JSON v parametru.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

Následující projevy ukazují, jak interpunkce ovlivňuje projevy:

|S interpunkcí nastavenou na Hodnotu Nepravda|S interpunkcí nastavenou na Hodnotu True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Interpunkce byla odstraněna.

Následující interpunkce je odebrána s `NormalizePunctuation` je nastavena na hodnotu true.

|Interpunkční znaménka|
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

## <a name="next-steps"></a>Další kroky

* Naučte se [koncepty diakritiky](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) a interpunkce.
