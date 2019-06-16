---
title: Export modelu do mobilní – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak exportovat vašeho modelu pro použití při vytváření mobilních aplikací.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 7bf8217f5076c0a95d4db6c1c7cbea7bc93b91f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65550544"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Export modelu pro použití s mobilními zařízeními

Služba Custom Vision Service umožňuje třídění export ke spuštění v režimu offline. Můžete vložení klasifikátoru exportované do aplikace a spustit místně na zařízení pro klasifikaci v reálném čase.

Služba Custom Vision Service podporuje následující exportů:

* __Tensorflow__ pro __Android__.
* __CoreML__ pro __iOS11__.
* __ONNX__ pro __Windows ML__.
* Windows nebo Linux __kontejneru__. Kontejner obsahuje Tensorflow model a služby kód, který použijete vlastní rozhraní API služby pro zpracování obrazu. 

> [!IMPORTANT]
> Služba Custom Vision Service exportuje jenom __compact__ domén. Modely generované balíky compact domény jsou optimalizované pro omezení v reálném čase klasifikace na mobilních zařízeních. Třídění vytvořených pomocí compact domény může být méně přesné než standardní domény s velkou trénovací data.
>
> Informace o zvýšení vaší třídění, najdete v článku [vylepšení klasifikátoru](getting-started-improving-your-classifier.md) dokumentu.

## <a name="convert-to-a-compact-domain"></a>Převést na compact domény

> [!NOTE]
> Kroky v této části platí pouze pokud máte existující třídění, které není nastavena na compact domény.

K převodu domény existujících třídění, použijte následující kroky:

1. Z [vlastní stránku pro zpracování obrazu](https://customvision.ai), vyberte __Domů__ ikonu k zobrazení seznamu vašich projektů. Můžete také použít [ https://customvision.ai/projects ](https://customvision.ai/projects) zobrazíte vaše projekty.

    ![Obrázek domovské seznamu ikonu a projekty](./media/export-your-model/projects-list.png)

2. Vyberte projekt a pak vyberte __ozubeného kola__ ikonu v pravém horním rohu stránky.

    ![Obrázek ikony ozubeného kolečka](./media/export-your-model/gear-icon.png)

3. V __domén__ vyberte __compact__ domény. Vyberte __uložit změny__ a uložte změny.

    ![Obrázek výběru domény](./media/export-your-model/domains.png)

4. V horní části stránky vyberte __Train__ k přeučování využívající tuto novou doménu.

## <a name="export-your-model"></a>Export modelu

Pokud chcete exportovat po přetrénování modelu, postupujte následovně:

1. Přejděte **výkonu** kartě a vyberte __exportovat__. 

    ![Obrázek ikony exportu](./media/export-your-model/export.png)

    > [!TIP]
    > Pokud __exportovat__ položka není k dispozici, pak vybranou iteraci nepoužívá compact domény. Použití __iterací__ části této stránky můžete vybrat iterace, která používá compact domény a pak vyberte __exportovat__.

2. Vyberte formát exportu a pak vyberte __exportovat__ stáhnout modelu.

## <a name="next-steps"></a>Další postup

Integrace modelu exportované do aplikace si vyzkoušejte jednu z následujících článků nebo ukázky:

* [Použití modelu Tensorflow s Pythonem](export-model-python.md)
* [Použití modelu ONNX s Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Najdete v ukázce pro [CoreML modelu v aplikaci iOS](https://go.microsoft.com/fwlink/?linkid=857726) pro klasifikaci obrázků v reálném čase s Swift.
* Najdete v ukázce pro [Tensorflow modelu v aplikaci pro Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) pro klasifikaci obrázků v reálném čase na Androidu.
* Najdete v ukázce pro [CoreML model s využitím kódu Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) pro klasifikaci obrázků v reálném čase v aplikaci Xamarin iOS.
