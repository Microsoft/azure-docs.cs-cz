---
title: Exportovat svůj model služby vize vlastní do mobilních služeb Azure kognitivní - vlastní vize Service - | Microsoft Docs
description: Další informace o exportu modelu pro použití při vytváření mobilních aplikací.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: ce8f42d6239867dd217cddfc61a27d7835dc9c9b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343876"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Export vašeho modelu pro použití s mobilními zařízeními

Vlastní vize Service umožňuje třídění export ke spuštění v režimu offline. Můžete vložit vaše exportovaný třídění do aplikace a ji spustit místně na zařízení pro klasifikaci v reálném čase. 

Vlastní služba vize podporuje následující exporty:

* __Tensorflow__ pro __Android__.
* __CoreML__ pro __iOS11__.
* __ONNX__ pro __Windows ML__.
* Windows nebo Linux __kontejneru__. Kontejner obsahuje Tensorflow modelu a služby kódu pro použití rozhraní API služby vize vlastní. 

> [!IMPORTANT]
> Exportuje vlastní služba vize jenom __compact__ domén. Modely generované compact domény jsou optimalizované pro omezení v reálném čase klasifikace na mobilních zařízeních. Třídění vytvořené s nástroji compact domény může být mírně méně přesný než standardní domény se stejnou velikostí Cvičná data.
>
> Informace o zvýšení vaší třídění najdete v tématu [zlepšení vaší třídění](getting-started-improving-your-classifier.md) dokumentu.

## <a name="convert-to-a-compact-domain"></a>Převést na compact domény

> [!NOTE]
> Postup v této části platí pouze pro máte existující třídění, který není nastavený na compact domény.
 
Převést doménu existující třídění, použijte následující postup:

1. Z [vlastní vize stránku](https://customvision.ai), vyberte __Domů__ ikonu zobrazíte seznam vašich projektů. Můžete také [ https://customvision.ai/projects ](https://customvision.ai/projects) zobrazíte vašich projektů.

    ![Obrázek domovské seznamu ikonu a projektů](./media/export-your-model/projects-list.png)

2. Vyberte projekt a pak vyberte __ozubené kolečko__ ikonu v pravém horním rohu stránky na stránku.

    ![Obrázek ikony zařízení](./media/export-your-model/gear-icon.png)

3. V __domén__ vyberte __compact__ domény. Vyberte __uložit změny__ a uložte změny.

    ![Obrázek výběru domény](./media/export-your-model/domains.png)

4. Z horní části stránky, vyberte __Train__ k přeučování využívající tuto novou doménu.

## <a name="export-your-model"></a>Export modelu

Export modelu po retraining, použijte následující kroky:

1. Přejděte na **výkonu** a vyberte __exportovat__. 

    ![Obrázek ikony exportu](./media/export-your-model/export.png)

    > [!TIP]
    > Pokud __exportovat__ položka není k dispozici, pak vybrané iterace nepoužívá compact domény. Použití __iterací__ části této stránce vyberte iterace, která používá compact doménu a pak vyberte __exportovat__.

2. Vyberte formát exportu a pak vyberte __exportovat__ ke stažení modelu.

## <a name="next-steps"></a>Další postup

Integrate váš model exportovaný do aplikace. Několik ukázkových aplikací jsou k dispozici:

* Ukázka pro [pomocí exportovaný CoreML modelu v aplikaci pro systém iOS](https://go.microsoft.com/fwlink/?linkid=857726) pro klasifikaci v reálném čase bitové kopie s Swift
* Ukázkové aplikace iOS pro [exportovaný modelu CoreML pomocí Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) pro klasifikaci v reálném čase bitové kopie 
* Ukázka pro [pomocí exportovaný Tensorflow modelu v aplikaci Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) pro klasifikaci v reálném čase bitové kopie 
* [Použití modelu Tensorflow systému Windows](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/export-model-python)
* Ukázka pro [exportovaný ONNX modelu pomocí Windows Machine Learning](https://azure.microsoft.com/en-us/resources/samples/cognitive-services-onnx-customvision-sample/)
