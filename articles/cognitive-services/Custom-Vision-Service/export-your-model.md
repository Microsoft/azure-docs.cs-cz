---
title: Export modelu do mobilního prostředí Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Naučte se exportovat model pro použití při vytváření mobilních aplikací.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 554a392a7f815a6e646927f137b1e6c2856099bd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561087"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Export modelu pro použití s mobilními zařízeními

Custom Vision Service umožňuje exportovat třídění pro spuštění offline. Můžete vložit exportovaný klasifikátor do aplikace a spustit ji místně na zařízení pro klasifikaci v reálném čase.

Custom Vision Service podporuje následující exporty:

* __Tensorflow__ pro __Android__.
* __CoreML__ pro __iOS11__.
* __ONNX__ pro __Windows ml__
* __Kontejner__systému Windows nebo Linux. Kontejner zahrnuje model Tensorflow a kód služby pro použití rozhraní Custom Vision Service API. 

> [!IMPORTANT]
> Custom Vision Service pouze exportuje __kompaktní__ domény. Modely generované kompaktními doménami jsou optimalizované pro omezení klasifikace v reálném čase na mobilních zařízeních. Klasifikátory sestavené s kompaktní doménou můžou být mírně méně přesné než standardní doména se stejným objemem školicích dat.
>
> Informace o vylepšení klasifikátoru najdete v dokumentu o [vylepšení svého klasifikátoru](getting-started-improving-your-classifier.md) .

## <a name="convert-to-a-compact-domain"></a>Převést na kompaktní doménu

> [!NOTE]
> Postup v této části platí jenom v případě, že máte existující klasifikátor, který není nastavený na komprimaci domény.

K převedení domény existujícího klasifikátoru použijte následující postup:

1. Na [stránce vlastní vize](https://customvision.ai)vyberte ikonu __Domů__ a zobrazte seznam vašich projektů. Můžete také použít [https://customvision.ai/projects](https://customvision.ai/projects) k zobrazení vašich projektů.

    ![Obrázek seznamu ikona domů a projekty](./media/export-your-model/projects-list.png)

2. Vyberte projekt a pak vyberte ikonu ozubeného __kolečka__ v pravém horním rohu stránky.

    ![Obrázek ikony ozubeného kolečka](./media/export-your-model/gear-icon.png)

3. V části __domény__ vyberte __kompaktní__ doménu. Pokud chcete změny uložit, vyberte __Uložit změny__ .

    ![Obrázek výběru v doméně](./media/export-your-model/domains.png)

4. V horní části stránky vyberte možnost __výuka__ pro přeučení pomocí nové domény.

## <a name="export-your-model"></a>Export modelu

Pokud chcete model exportovat po rekurzi, použijte následující postup:

1. Přejít na kartu **výkon** a vyberte __exportovat__. 

    ![Obrázek ikony exportu](./media/export-your-model/export.png)

    > [!TIP]
    > Pokud položka __exportu__ není k dispozici, vybraná iterace nepoužívá kompaktní doménu. V části __iterace__ této stránky můžete vybrat iteraci, která používá kompaktní doménu, a pak vybrat __exportovat__.

2. Vyberte formát exportu a pak vyberte __exportovat__ ke stažení modelu.

## <a name="next-steps"></a>Další kroky

Integrujte svůj exportovaný model do aplikace, a prozkoumejte jeden z následujících článků nebo ukázek:

* [Použití modelu Tensorflow s Pythonem](export-model-python.md)
* [Použití modelu ONNX s Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Podívejte se na ukázku [modelu CoreML v aplikaci pro iOS](https://go.microsoft.com/fwlink/?linkid=857726) pro klasifikaci imagí v reálném čase pomocí SWIFT.
* Podívejte se na ukázku [modelu Tensorflow v aplikaci pro Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) pro klasifikaci imagí v reálném čase v Androidu.
* Podívejte se na ukázku [modelu CoreML s](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) využitím Xamarin pro klasifikaci imagí v reálném čase v aplikaci Xamarin iOS.
