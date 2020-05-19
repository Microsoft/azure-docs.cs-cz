---
title: Nastavení aplikace – LUIS
description: Nastavení aplikací pro porozumění aplikacím v Azure Cognitive Services se ukládají do aplikace a portálu.
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 7b545e0959a43520b7d643ef8c0658a1e1a3b295
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590977"
---
# <a name="app-and-version-settings"></a>Nastavení aplikace a verze

Tato nastavení se ukládají do [exportované](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplikace a aktualizují se pomocí rozhraní REST API nebo portálu Luis.

Změna nastavení verze aplikace obnoví stav školení aplikace na neučení.

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


Mezi odkazy a příklady textu patří:

* [Interpunkční znaménka](#punctuation-normalization)
* [Diakritiku](#diacritics-normalization)

## <a name="diacritics-normalization"></a>Normalizace diakritiky

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

Následující projevy ukazuje, jak interpunkce má vliv na projevy:

|S interpunkčním znaménkem nastaveným na false|S interpunkčním znaménkem nastaveným na true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Odebraná interpunkce

Následující interpunkční znaménko je odebráno s `NormalizePunctuation` nastavením na hodnotu true.

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

* Seznamte se s [Koncepty](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) diakritických znamének a interpunkce.
