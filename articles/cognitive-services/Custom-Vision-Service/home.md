---
title: Co je Custom Vision?
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí služby Custom Vision vytvářet vlastní klasifikátory obrázků v cloudu Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a48c553f1c96b8777e0a591f428dca3f15d7d30e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053641"
---
# <a name="what-is-custom-vision"></a>Co je Custom Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Custom Vision je služba Cognitive Services, která umožňuje vytvářet, nasazovat a vylepšovat vaše vlastní klasifikátory obrázků. Klasifikátor obrázků je služba AI, která aplikuje popisky (které představují _třídy)_ na obrázky podle jejich vizuálních charakteristik. Na rozdíl od služby [Počítačové vidění](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) umožňuje vlastní vize určit štítky, které mají být aplikovány.

## <a name="what-it-does"></a>Co dělá

Služba Custom Vision používá algoritmus strojového učení k použití popisků na obrázky. Vy, vývojář, musíte odeslat skupiny obrázků, které funkce a chybí vlastnosti v otázce. Obrázky označíte sami v době odeslání. Pak algoritmus trénuje na tato data a vypočítá svou vlastní přesnost testováním na stejných obrázcích. Jakmile je algoritmus trénovaný, můžete testovat, přeškolit a nakonec ho použít ke klasifikaci nových obrázků podle potřeb vaší aplikace. Samotný model můžete také exportovat pro offline použití.

### <a name="classification-and-object-detection"></a>Klasifikace a detekce objektů

Funkce služby Custom Vision je možné rozdělit do dvou funkcí. **Klasifikace obrázků** aplikuje na obrázek jeden nebo více popisků. **Detekce objektů** je podobná, ale také vrátí souřadnice v obraze, kde lze nalézt aplikovaný popisek.Object detection is similar, but it also returns the coordinates in the image where the applied label(s) can be found.

### <a name="optimization"></a>Optimalizace

Služba Custom Vision je optimalizována tak, aby rychle rozpoznala hlavní rozdíly mezi obrázky, takže můžete začít vytvářet prototypy modelu s malým množstvím dat. 50 obrázků na štítek je obecně dobrý začátek. Služba však není optimální pro detekci jemných rozdílů v obrazech (například detekci drobných trhlin nebo promáčknutí ve scénářích zajištění kvality).

Kromě toho si můžete vybrat z několika odrůd algoritmu vlastní vidění, které&mdash;jsou optimalizovány pro obrázky s určitým materiálem předmětu, například orientačníbody nebo maloobchodní položky. Další informace naleznete v [tématu Sestavení třídění](getting-started-build-a-classifier.md) průvodce.

## <a name="what-it-includes"></a>Co zahrnuje

Služba Custom Vision je dostupná jako sada nativních sad SDK i prostřednictvím webového rozhraní na [domovské stránce služby Custom Vision](https://customvision.ai/). Můžete vytvořit, otestovat a trénovat model prostřednictvím obou rozhraní nebo použít oba dohromady.

![Domovská stránka služby Custom Vision v okně prohlížeče Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení

Stejně jako u všech služeb Cognitive Services by vývojáři, kteří používají službu Custom Vision, měli znát zásady společnosti Microsoft týkající se zákaznických dat. Další informace najdete na [stránce Služby Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v Centru zabezpečení společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

Postupujte podle [sestavení třídění](getting-started-build-a-classifier.md) průvodce začít používat vlastní vidění na webu, nebo dokončení klasifikace [image kurz](csharp-tutorial.md) k implementaci základní scénář v kódu.
