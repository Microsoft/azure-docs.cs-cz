---
title: Co je Custom Vision?
titleSuffix: Azure Cognitive Services
description: Naučte se používat službu Azure Custom Vision k vytváření vlastních klasifikátorů obrázků AI a detektorů objektů v cloudu Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: rozpoznávání obrázků, identifikátor obrázku, aplikace pro rozpoznávání obrázků, vlastní vize
ms.openlocfilehash: 7274fed961c4bbac8841e1c741f54031614cea5c
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562986"
---
# <a name="what-is-custom-vision"></a>Co je Custom Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Custom Vision je služba pro rozpoznávání imagí, která umožňuje sestavovat, nasazovat a zdokonalovat vlastní identifikátory imagí. Identifikátor obrázku používá popisky (které reprezentují třídy nebo objekty) k obrázkům podle jejich vizuálních vlastností. Na rozdíl od služby [Počítačové zpracování obrazu](../computer-vision/overview.md) Custom Vision umožňuje zadat popisky a naučit vlastní modely pro jejich rozpoznání.

## <a name="what-it-does"></a>Co dělá

Služba Custom Vision používá algoritmus strojového učení pro analýzu imagí. Vy, vývojář, odešlete skupiny imagí, které jsou součástí a chybí příslušné charakteristiky. Obrázky můžete označovat sami v době odeslání. Pak algoritmus nahlasuje tato data a vypočte svou vlastní přesnost tím, že se na stejných obrázcích otestuje sám. Jakmile je algoritmus vyučený, můžete ho otestovat, předávat a nakonec použít v aplikaci pro rozpoznávání imagí ke klasifikaci nových imagí. Samotný model můžete také exportovat pro offline použití.

### <a name="classification-and-object-detection"></a>Klasifikace a detekce objektů

Funkce služby Custom Vision je možné rozdělit do dvou funkcí. **Klasifikace obrázku** používá jeden nebo více štítků na obrázek. **Detekce objektu** je podobná, ale také vrací souřadnice v obrázku, kde lze nalézt použité popisky.

### <a name="optimization"></a>Optimalizace

Služba Custom Vision je optimalizovaná tak, aby rychle rozpoznala hlavní rozdíly mezi obrázky, takže můžete začít s vytvářením prototypů modelu s malým množstvím dat. 50 obrázků na popisek jsou obecně dobrým startem. Služba ale není optimální pro detekci drobných rozdílů v obrázcích (například rozpoznávání menších prasklin nebo odsazení ve scénářích zabezpečování kvality).

Kromě toho si můžete vybrat z několika variant Custom Vision algoritmu, které jsou optimalizované pro obrázky s určitým materiálem předmětu &mdash; , například orientačních bodů nebo maloobchodních položek. Další informace naleznete v tématu [sestavování klasifikátoru](getting-started-build-a-classifier.md) nebo [sestavení vodítek objektu pro rozpoznávání](get-started-build-detector.md) .

## <a name="what-it-includes"></a>Co zahrnuje

Custom Vision Service je k dispozici jako sada nativních sad SDK a také prostřednictvím webového rozhraní na [webu Custom Vision](https://customvision.ai/). Můžete vytvořit, otestovat a naučit model prostřednictvím obou rozhraní nebo obojího použít společně.

![Custom Vision web v okně prohlížeče Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech Cognitive Services by měli vývojáři, kteří používají Custom Vision službu, znát zásady Microsoftu pro zákaznická data. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Postupujte podle pokynů průvodce [sestavením klasifikátoru](getting-started-build-a-classifier.md) a začněte používat Custom Vision na webovém portálu nebo dokončete [rychlý Start knihovny klienta](quickstarts/image-classification.md) pro implementaci základních scénářů v kódu.