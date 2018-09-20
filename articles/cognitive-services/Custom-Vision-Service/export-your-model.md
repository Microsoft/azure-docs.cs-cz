---
title: Export modelu do mobilní – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak exportovat vašeho modelu pro použití při vytváření mobilních aplikací.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 50417e9e1722e69c24185c05ea5e286e6b13e8c7
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364937"
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

Exportované model začlenit do aplikace. Několik ukázkových aplikací jsou k dispozici:

* Ukázka pro [použití exportované CoreML modelu v aplikaci iOS](https://go.microsoft.com/fwlink/?linkid=857726) pro klasifikaci obrázků v reálném čase s Swift
* Ukázková aplikace pro iOS [pomocí exportovaného CoreML model s využitím kódu Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) pro klasifikaci obrázků v reálném čase 
* Ukázky pro [pomocí exportovaného Tensorflow modelu v aplikaci pro Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) pro klasifikaci obrázků v reálném čase 
* [Použití modelu Tensorflow s Windows](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-model-python)
* Ukázky pro [exportované modelu ONNX pomocí Windows Machine Learning](https://azure.microsoft.com/resources/samples/cognitive-services-onnx-customvision-sample/)
