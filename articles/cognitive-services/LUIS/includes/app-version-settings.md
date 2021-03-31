---
title: zahrnout soubor
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "83653196"
---
Přečtěte si o [konceptech](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) normalizace a o tom, jak pomocí rozhraní API [verze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) aktualizovat Tato nastavení nebo jak použít část **Správa** na portálu Luis, stránku **Nastavení** .


|Nastavení uživatelského rozhraní|Nastavení rozhraní API|Informace|
|--|--|--|
|Použití nedeterministického školení|`UseAllTrainingData`|Školení používá malé procento negativního vzorkování. Pokud chcete použít všechna data namísto malého negativního vzorkování, nastavte na `true` . |
|Normalizovat diakritická znaménka|`NormalizeDiacritics`|Normalizace diakritických znamének nahradí znaky diakritikou v projevy pomocí regulárních znaků. Toto nastavení je dostupné jenom pro [jazyky](../luis-reference-application-settings.md#diacritics-normalization) , které podporují diakritická znaménka.|
|Normalizovat interpunkci|`NormalizePunctuation`|Normalizace interpunkce znamená, že před tím, než se vaše modely vyškole a ještě před dokončením dotazů na koncové body, se z projevy odeberou interpunkční znaménka.|
|Normalizovat tvary aplikace Word|`NormalizeWordForm`|Ignoruje Word Forms za kořenem.|
