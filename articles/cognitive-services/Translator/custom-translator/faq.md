---
title: Časté otázky – vlastní Translator
titleSuffix: Azure Cognitive Services
description: Poskytuje odpovědi na nejčastější dotazy týkající se vlastní překladač.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: reference
ms.openlocfilehash: 415866bc4ef396a1b9397c4996d65cf10d78400b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54854781"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy o [vlastní Translator](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Jaká jsou aktuální omezení v Translator vlastní?

Existují omezení a limity s ohledem na velikost souboru, cvičení modelu a nasazení modelu. Mějte na tato omezení při nastavování trénování sestavit model ve vlastní překladač.

- Zadané soubory musí být menší než 100 MB.

- Jeden jazyk data se nepodporuje.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Pokud by měl požádat o nasazení pro překlad systému, který byl vyškolila?

Může trvat několik školení k vytvoření optimální překladový systém pro váš projekt. Můžete chtít zkuste použít další trénovacích dat nebo více pečlivě filtrovaná data, pokud nejsou uspokojivé BLEU skóre a / nebo výsledky testů. By měl být striktní a pozor při navrhování ladění sady a vašeho testu nastavte, plně reprezentativní terminologie a stylu materiálů, které chcete přeložit. Může být volnější v sestavování trénovacích dat a experimentovat s různými možnostmi. Žádost o nasazení systému, když jste spokojeni s tím, překlady v výsledkům testů systému neobsahují žádná další data pro přidání do školení k vylepšení trénovaného systému, a chcete získat přístup přes rozhraní API trénovaného modelu.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Kolik trénovaného systémy je možné nasadit v projektu?

Pouze jeden trénovaného systém lze nasadit na projekt. Může trvat několik školení k vytvoření systému vhodný překladu pro váš projekt a doporučujeme vám žádost o nasazení školení, který vám poskytne nejlepší výsledek. Můžete určit kvalitu na školení podle skóre BLEU (vyšší je lepší) a konzultaci s kontrolory před rozhodování o tom, že kvalitu překladu je vhodný pro nasazení.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Když můžou očekávat Moje školení k nasazení?

Nasazení obvykle trvá méně než hodinu.

## <a name="how-do-you-access-a-deployed-system"></a>Jak můžete přistupovat k nasazený systém?

Nasazené systémy přístupné prostřednictvím Microsoft Translator Text API V3 zadáním ID kategorie. Další informace o rozhraní Translator Text API najdete v [Reference k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) webové stránky.

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Jak přeskočit zarovnání a rozdělení dat je již věty zarovnané věty?

Vlastní překladač přeskočí zarovnání věty a větu zásadní pro soubory TMX a vyhledat textové soubory s `.align` rozšíření. `.align` soubory uživatelům možnost pro přeskočení vlastní Translator věty dopadem na dřívější kód a zarovnání proces pro soubory, které jsou perfektně zarovnaný a nutné žádné další zpracování. Doporučujeme používat `.align` rozšíření pouze pro soubory, které jsou perfektně zarovnaný.

Pokud počet extrahované věty se neshoduje s dva soubory se stejný základní název, vlastní Translator bude stále spuštěn aligner větu `.align` soubory.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Pokus o nahrání Moje TMX, ale to říká "dokumentu zpracování se nezdařilo".

Ujistěte se, TMX odpovídá TMX 1.4b specifikace na <https://www.gala-global.org/tmx-14b>.
