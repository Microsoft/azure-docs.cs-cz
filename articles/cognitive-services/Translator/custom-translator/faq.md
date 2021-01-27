---
title: Nejčastější dotazy – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se vlastního překladatele Azure Cognitive Services.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 001314817b0c18a8023258d01bcfb02eaaffe79b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895828"
---
# <a name="custom-translator-frequently-asked-questions"></a>Vlastní Překladatel – Nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se [vlastního překladatele](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Jaká jsou aktuální omezení vlastního překladatele?

Existují omezení a omezení týkající se velikosti souboru, školení modelů a nasazení modelu. Při nastavování vašeho školení pro sestavování modelu ve vlastním překladateli mějte na paměti tato omezení.

- Odeslané soubory musí mít velikost menší než 100 MB.
- Data Monolingual se nepodporují.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Kdy bych chtěl požádat o nasazení systému překladu, který je školený?

Vytvoření optimálního systému překladu pro váš projekt může trvat několik školení. Možná budete chtít zkusit použít více školicích dat nebo více pečlivě filtrovaných dat, pokud BLEU skóre nebo výsledky testu nejsou uspokojivé. Měli byste být přísní a opatrní v návrhu vaší sady pro optimalizaci a sady testů, aby byly plně reprezentativní pro terminologii a styl materiálu, který chcete přeložit. Při vytváření školicích dat můžete provést větší možnosti a experimentovat s různými možnostmi. Vyžádejte nasazení systému, pokud jste spokojeni s překlady ve výsledcích testů systému, nemusíte mít žádná další data, která by bylo možné přidat do školení pro zlepšení vašeho vyučeného systému, a chcete získat přístup k školicímu modelu prostřednictvím rozhraní API.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Kolik naučených systémů lze nasadit v projektu?

V jednom projektu lze nasadit pouze jeden vycvičený systém. Může provést několik školení pro vytvoření vhodného systému překladu pro váš projekt a doporučujeme vám, abyste požádali o nasazení školení, které vám poskytne nejlepší výsledek. Můžete určit kvalitu školení BLEU skóre (vyšší je lepší) a konzultovat s kontrolory před tím, než se rozhodnete, že kvalita překladů je vhodná pro nasazení.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Kdy můžu očekávat, že se mají nasadit moje školení?

Nasazení obvykle trvá méně než hodinu.

## <a name="how-do-you-access-a-deployed-system"></a>Jak přistupujete k nasazenému systému?

Nasazené systémy jsou k dispozici prostřednictvím Microsoft Translator Text API V3 zadáním KódKategorie. Další informace o Translator Text API najdete na webové stránce [referenčního rozhraní API](../reference/v3-0-reference.md) .

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Návody přeskočit zarovnání a konce věty, pokud jsou moje data už zarovnané na větu?

Vlastní Překladatel přeskočí zarovnání věty a konce věty pro soubory TMX a textové soubory s `.align` příponou. `.align` soubory dávají uživatelům možnost přeskočit krok a proces zarovnání věty vlastního překladatele pro soubory, které jsou dokonale zarovnané a nemusí provádět žádné další zpracování. `.align`Pro soubory, které jsou dokonale zarovnané, doporučujeme použít rozšíření.

Pokud se počet extrahovaných vět neshoduje se dvěma soubory se stejným základním názvem, vlastní Překladatel pořád spustí zarovnání věty u `.align` souborů.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Zkoušel jsem odeslat svůj TMX, ale říká "zpracování dokumentu se nezdařilo"


Ujistěte se, že TMX odpovídá specifikaci TMX 1.4 b na adrese <https://www.gala-global.org/tmx-14b> .