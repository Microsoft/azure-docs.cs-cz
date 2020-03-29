---
title: Export modelu na mobilní zařízení – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak exportovat model pro použití při vytváření mobilních aplikací nebo spustit místně pro klasifikaci v reálném čase.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718952"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Export modelu pro použití s mobilními zařízeními

Vlastní služba Vision Service umožňuje, aby se klasifikátory exportovaly do režimu offline. Exportovaný klasifikátor můžete vložit do aplikace a spustit místně na zařízení pro klasifikaci v reálném čase.

## <a name="export-options"></a>Volby exportu

Služba Custom Vision Service podporuje následující exporty:

* __Tensorflow__ pro __Android__.
* __CoreML__ pro __iOS11__.
* __ONNX__ pro __Windows ML__.
* __[Vision AI Developer Kit](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* __Kontejner Dockeru__ pro architekturu Windows, Linux nebo ARM. Kontejner obsahuje model Tensorflow a kód služby pro použití rozhraní API vlastní vize.

> [!IMPORTANT]
> Vlastní služba Vision Service exportuje pouze __kompaktní__ domény. Modely generované kompaktními doménami jsou optimalizovány pro omezení klasifikace v reálném čase na mobilních zařízeních. Klasifikátory vytvořené s kompaktní doménou mohou být o něco méně přesné než standardní doména se stejným množstvím trénovacích dat.
>
> Informace o vylepšení klasifikátorů najdete v dokumentu [Vylepšování klasifikátoru.](getting-started-improving-your-classifier.md)

## <a name="convert-to-a-compact-domain"></a>Převod na kompaktní doménu

> [!NOTE]
> Kroky v této části platí pouze v případě, že máte existující model, který není nastaven na kompaktní doménu.

Chcete-li převést doménu existujícího modelu, postupujte takto:

1. Na [webu Vlastní vize](https://customvision.ai)vyberte ikonu __Domů__ a zobrazte seznam svých projektů.

    ![Obrázek domovské ikony a seznamu projektů](./media/export-your-model/projects-list.png)

1. Vyberte projekt a pak vyberte ikonu __Ozubená kola__ v pravém horním mase stránky.

    ![Obrázek ikony ozubeného kola](./media/export-your-model/gear-icon.png)

1. V části __Domény__ vyberte jednu z __kompaktních__ domén. Chcete-li změny uložit, vyberte __uložit změny.__ 

    > [!NOTE]
    > Pro Vision AI Dev Kit projekt musí být vytvořen s __doménou Obecné (Kompaktní)__ a musíte zadat možnost **Vision AI Dev Kit** v části **Export schopnosti.**

    ![Obrázek výběru domén](./media/export-your-model/domains.png)

1. V horní části stránky vyberte __Vlak__ pro přeškolit pomocí nové domény.

## <a name="export-your-model"></a>Export modelu

Chcete-li model exportovat po přeškolení, postupujte takto:

1. Přejděte na kartu **Výkon** a vyberte __Exportovat__. 

    ![Obrázek ikony exportu](./media/export-your-model/export.png)

    > [!TIP]
    > Pokud položka __Export__ není k dispozici, vybraná iterace nepoužívá kompaktní doménu. V části __Iterace__ na této stránce vyberte iteraci, která používá kompaktní doménu, a pak vyberte __Exportovat__.

1. Vyberte požadovaný formát exportu a pak vyberte __Exportovat__ a stáhněte model.

## <a name="next-steps"></a>Další kroky

Integrujte exportovaný model do aplikace tak, že prozkoumáte jeden z následujících článků nebo ukázek:

* [Použití modelu Tensorflow s Pythonem](export-model-python.md)
* [Použití modelu ONNX se systémem Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Podívejte se na ukázku [modelu CoreML v aplikaci pro iOS](https://go.microsoft.com/fwlink/?linkid=857726) pro klasifikaci obrázků v reálném čase s Swiftem.
* Podívejte se na ukázku [modelu Tensorflow v aplikaci pro Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) pro klasifikaci obrázků v reálném čase v systému Android.
* Podívejte se na ukázku [pro model CoreML s Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) pro klasifikaci obrázků v reálném čase v aplikaci Xamarin iOS.
