---
title: Jak aktualizovat projekt na rozhraní API 3.0
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak aktualizovat vlastní projekty vize z předchozí verze rozhraní API do rozhraní API 3.0.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647506"
---
# <a name="update-to-the-30-api"></a>Aktualizace rozhraní API 3.0

Vlastní vize nyní dosáhla obecné dostupnosti a prošla aktualizací rozhraní API.
Tato aktualizace obsahuje několik nových funkcí a, co je důležité, několik nejnovějších změn:

* Rozhraní API předpověď je nyní rozdělena do dvou na základě typu projektu.
* Možnost exportu sady Vision AI Developer Kit (VAIDK) vyžaduje vytvoření projektu určitým způsobem.
* Výchozí iterace byly odebrány ve prospěch publikování / zrušení publikování pojmenované iterace.

Tato příručka vám ukáže, jak aktualizovat projekty pro práci s novou verzí rozhraní API. Úplný seznam změn najdete v poznámkách k [verzi.](release-notes.md)

## <a name="use-the-updated-prediction-api"></a>Použití aktualizovaného rozhraní API pro předpověď

2.x API používá stejné volání předpověď pro image třídění a objekt detektor projektů. Oba typy projektu byly přijatelné pro **PredictImage** a **PredictImageUrl** volání. Počínaje 3.0 jsme toto rozhraní API rozdělili tak, že je třeba spárovat typ projektu s voláním:

* Použijte **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** a **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** získat předpovědi pro projekty klasifikace obrázků.
* Pomocí **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** a **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** získat předpovědi pro projekty detekce objektů.

## <a name="use-the-new-iteration-publishing-workflow"></a>Použití nového pracovního postupu publikování iterace

2.x API používá výchozí iteraci nebo zadané ID iterace zvolit iteraci použít pro předpověď. Počínaje 3.0 jsme přijali tok publikování, kdy nejprve publikujete iteraci pod zadaným názvem z rozhraní API školení. Potom předat název metody předpověď určit, které iterace použít.

> [!IMPORTANT]
> 3.0 API nepoužívají výchozí funkci iterace. Dokud nezakážeme starší api, můžete nadále používat 2.x API přepnout iterace jako výchozí. Tato api budou udržovány po určitou dobu a můžete volat **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metoda označit iteraci jako výchozí.

### <a name="publish-an-iteration"></a>Publikování iterace

Jakmile je iterace trénovaná, můžete ji zpřístupnit pro předpověď pomocí **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** metody. Chcete-li publikovat iteraci, budete potřebovat ID prostředku předpověď, která je k dispozici na stránce nastavení webu CustomVision.

![Stránka Nastavení webu Vlastní vize s nastíněným ID prostředku předpovědi.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Tyto informace můžete získat také z [portálu Azure,](https://portal.azure.com) přejdete na prostředek Vlastní předpověď vidění a vyberete **vlastnosti**.

Po publikování iterace aplikace můžete použít pro předpověď zadáním názvu v jejich volání rozhraní API předpověď. Chcete-li iteraci nedostupné pro volání předpověď, použijte **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API.

## <a name="next-steps"></a>Další kroky

* [Referenční dokumentace pro školicí rozhraní API (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Predikční referenční dokumentace API (REST)](https://go.microsoft.com/fwlink/?linkid=865445)