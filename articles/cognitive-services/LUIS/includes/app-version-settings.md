---
title: zahrnout soubor
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590978"
---
|Úroveň|Nastavení uživatelského rozhraní|Nastavení rozhraní API|Informace|
|--|--|--|--|
|Aplikace|Nastavit koncové body jako veřejné|`Public`|Kdokoli má přístup k vaší veřejné aplikaci, pokud mají klíč předpovědi a zná ID vaší aplikace. |
|Verze|Použití nedeterministického školení|`UseAllTrainingData`|Školení používá malé procento negativního vzorkování. Pokud chcete použít všechna data namísto malého negativního vzorkování, nastavte na `true` . |
|Verze|Normalizovat diakritická znaménka|`NormalizeDiacritics`|Normalizace diakritických znamének nahradí znaky diakritikou v projevy pomocí regulárních znaků.|
|Verze|Normalizovat interpunkci|`NormalizePunctuation`|Normalizace interpunkce znamená, že před tím, než se vaše modely vyškole a ještě před dokončením dotazů na koncové body, se z projevy odeberou interpunkční znaménka.|
|Verze|Normalizovat tvary aplikace Word|`NormalizeWordForm`|Ignoruje Word Forms za kořenem.|

Přečtěte si o [konceptech](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) normalizace a o tom, jak pomocí rozhraní API [aplikací](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) a [verzí](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) aktualizovat Tato nastavení nebo jak použít část **Správa** na portálu Luis, stránku **nastavení aplikace** .