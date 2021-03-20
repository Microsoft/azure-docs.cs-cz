---
title: Export modelu do mobilního prostředí Custom Vision Service
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak tento model exportovat pro použití při vytváření mobilních aplikací nebo v místním prostředí pro klasifikaci v reálném čase.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: be28a10f8a96d21d64d08cfacee4543c065168ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85391753"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Export modelu pro použití s mobilními zařízeními

Custom Vision Service umožňuje exportovat třídění pro spuštění offline. Můžete vložit exportovaný klasifikátor do aplikace a spustit ji místně na zařízení pro klasifikaci v reálném čase.

## <a name="export-options"></a>Možnosti exportu

Custom Vision Service podporuje následující exporty:

* __Tensorflow__ pro __Android__.
* __CoreML__ pro __iOS11__.
* __ONNX__ pro __Windows ml__
* __[Sada Vision AI Developer Kit](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* __Kontejner Docker__ pro architekturu Windows, Linux nebo ARM. Kontejner zahrnuje model Tensorflow a kód služby pro použití rozhraní Custom Vision API.

> [!IMPORTANT]
> Custom Vision Service pouze exportuje __kompaktní__ domény. Modely generované kompaktními doménami jsou optimalizované pro omezení klasifikace v reálném čase na mobilních zařízeních. Klasifikátory sestavené s kompaktní doménou můžou být mírně méně přesné než standardní doména se stejným objemem školicích dat.
>
> Informace o vylepšení klasifikátoru najdete v dokumentu o [vylepšení svého klasifikátoru](getting-started-improving-your-classifier.md) .

## <a name="convert-to-a-compact-domain"></a>Převést na kompaktní doménu

> [!NOTE]
> Postup v této části platí jenom v případě, že máte existující model, který není nastavený na komprimaci domény.

Pokud chcete převést doménu existujícího modelu, proveďte následující kroky:

1. Na [webu Custom Vision](https://customvision.ai)vyberte ikonu __Domů__ a zobrazte seznam vašich projektů.

    ![Obrázek seznamu ikona domů a projekty](./media/export-your-model/projects-list.png)

1. Vyberte projekt a pak vyberte ikonu __ozubeného kolečka__ v pravém horním rohu stránky.

    ![Obrázek ikony ozubeného kolečka](./media/export-your-model/gear-icon.png)

1. V části __domény__ vyberte jednu z __kompaktních__ domén. Pokud chcete změny uložit, vyberte __Uložit změny__ . 

    > [!NOTE]
    > V sadě Vision AI dev Kit se musí projekt vytvořit s __obecnou (kompaktní)__ doménou a v části **Možnosti exportu** musíte zadat možnost **Vision AI dev Kit** .

    ![Obrázek výběru v doméně](./media/export-your-model/domains.png)

1. V horní části stránky vyberte možnost __výuka__ pro přeučení pomocí nové domény.

## <a name="export-your-model"></a>Export modelu

Pokud chcete model exportovat po rekurzi, použijte následující postup:

1. Přejít na kartu **výkon** a vyberte __exportovat__. 

    ![Obrázek ikony exportu](./media/export-your-model/export.png)

    > [!TIP]
    > Pokud položka __exportu__ není k dispozici, vybraná iterace nepoužívá kompaktní doménu. V části __iterace__ této stránky můžete vybrat iteraci, která používá kompaktní doménu, a pak vybrat __exportovat__.

1. Vyberte požadovaný formát exportu a pak vyberte __exportovat__ ke stažení modelu.

## <a name="next-steps"></a>Další kroky

Integrujte svůj exportovaný model do aplikace, a prozkoumejte jeden z následujících článků nebo ukázek:

* [Použití modelu Tensorflow s Pythonem](export-model-python.md)
* [Použití modelu ONNX s Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Podívejte se na ukázku [modelu CoreML v aplikaci pro iOS](https://go.microsoft.com/fwlink/?linkid=857726) pro klasifikaci imagí v reálném čase pomocí SWIFT.
* Podívejte se na ukázku [modelu Tensorflow v aplikaci pro Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) pro klasifikaci imagí v reálném čase v Androidu.
* Podívejte se na ukázku [modelu CoreML s](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) využitím Xamarin pro klasifikaci imagí v reálném čase v aplikaci Xamarin iOS.
