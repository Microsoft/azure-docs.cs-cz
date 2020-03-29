---
title: Nejčastější dotazy – vlastní překladač
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se vlastnípřekladatel služby Azure Cognitive Services.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 97d399f4a8ec704fd90eb6c49f0835be7e9e4537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836600"
---
# <a name="custom-translator-frequently-asked-questions"></a>Nejčastější dotazy na vlastní překladač

Tento článek obsahuje odpovědi na nejčastější dotazy týkající [se vlastního překladače](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Jaká jsou aktuální omezení v custom translatoru?

Existují omezení a omezení s ohledem na velikost souboru, trénování modelu a nasazení modelu. Mějte tato omezení na paměti při nastavování školení k vytvoření modelu v vlastní překladač.

- Odeslané soubory musí mít velikost menší než 100 MB.
- Jednojazyčná data nejsou podporována.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Kdy bych měl požádat o nasazení pro překladový systém, který byl vyškolen?

Vytvoření optimálního překladového systému pro váš projekt může trvat několik školení. Můžete zkusit použít více trénovacích dat nebo pečlivěji filtrovaná data, pokud skóre BLEU a/ nebo výsledky testů nejsou uspokojivé. Měli byste být přísní a opatrní při navrhování tuningové sady a testovací sady, abyste byli plně reprezentativní pro terminologii a styl materiálu, který chcete přeložit. Můžete být liberálnější při vytváření dat školení a experimentovat s různými možnostmi. Požádejte o nasazení systému, pokud jste spokojeni s překlady ve výsledcích systémových testů, nemáte k tomu žádná další data, která byste mohli přidat do školení, abyste vylepšili trénovaný systém, a chcete získat přístup k trénovaného modelu prostřednictvím testovacích prostředí.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Kolik trénovaných systémů lze v projektu nasadit?

Na každý projekt lze nasadit pouze jeden trénovaný systém. Vytvoření vhodného překladatelského systému pro váš projekt může trvat několik školení a doporučujeme vám požádat o nasazení školení, které vám poskytne nejlepší výsledek. Kvalitu školení můžete určit podle skóre BLEU (vyšší je lepší) a pokonzultaci s recenzenty, než se rozhodnete, že kvalita překladů je vhodná pro nasazení.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Kdy mohu očekávat nasazení mých školení?

Nasazení obvykle trvá méně než hodinu.

## <a name="how-do-you-access-a-deployed-system"></a>Jak se dostanete k nasazenému systému?

K nasazeným systémům lze přistupovat prostřednictvím rozhraní Microsoft Translator Text API V3 zadáním id kategorie. Další informace o rozhraní Translator Text API naleznete na webové stránce [Reference rozhraní API.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Jak lze přeskočit zarovnání a přerušení věty, pokud jsou data již zarovnána?

Vlastní překladač přeskočí zarovnání věty a přerušení věty pro soubory TMX a pro textové soubory s `.align` příponou. `.align`soubory dávají uživatelům možnost přeskočit vlastní překladatelvěty lámání a zarovnání proces u souborů, které jsou dokonale zarovnány, a není třeba žádné další zpracování. Doporučujeme `.align` používat rozšíření pouze pro soubory, které jsou dokonale zarovnané.

Pokud počet extrahovaných vět neodpovídá dvěma souborům se stejným základním názvem, vlastní `.align` překladač bude stále spouštět zarovnání vět y na soubory.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Snažil jsem se nahrát můj TMX, ale říká, že "zpracování dokumentů se nezdařilo".

Ujistěte se, že TMX odpovídá specifikaci TMX 1.4b na . <https://www.gala-global.org/tmx-14b>
