---
title: 'Kurz: Použití modelu ONNX s Windows ML – Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Naučte se vytvořit aplikaci Windows UWP, která používá model ONNX exportovaný ze služby Azure Cognitive Services.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 10/16/2018
ms.author: larryfr
ms.openlocfilehash: 55235c8c209a17a494c3879d61e22665ba7a893b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224900"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Kurz: Pomocí modelu ONNX ze Custom Vision ML Windows (preview)

Naučte se používat model ONNX exportovaný ze služby Custom Vision s Windows ML (Preview).

Informace v tomto dokumentu ukazují, jak používat soubor ONNX exportovaný ze služby Custom Vision Service s Windows ML. K dispozici je ukázková aplikace Windows UWP. Součástí příkladu je také natrénovaný model podporující rozpoznávání. Dále je k dispozici postup, jak s touto ukázkou použít vlastní model.

> [!div class="checklist"]
> * Informace o ukázkové aplikaci
> * Získání ukázkového kódu
> * Spuštění ukázky
> * Použití vlastního modelu

## <a name="prerequisites"></a>Požadavky

* Sestavení Windows 10 17738 nebo novější

* Sada Windows SDK pro sestavení 17738 nebo novější

* Sadou Visual Studio 2017 verze 15.7 nebo novější s povolenou úlohou __vývoje pro Univerzální platformu Windows__

* Povoleným vývojářským režimem Další informace najdete v dokumentu týkajícím se [povolení zařízení pro vývoj](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Informace o ukázkové aplikaci

Aplikace je obecná aplikace Windows UWP (Univerzální platforma Windows). Umožňuje vybrat obrázek z počítače, který se pak odešle do modelu. Vedle obrázku se zobrazí značky a skóre vrácené modelem.

## <a name="get-the-example-code"></a>Získání ukázkového kódu

Ukázková aplikace je k dispozici na adrese [https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/](https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/).

## <a name="run-the-example"></a>Spuštění ukázky

1. Ke spuštění aplikace ze sady Visual Studio použijte klávesu `F5`. Může se zobrazit výzva k povolení vývojářského režimu. Další informace najdete v dokumentu týkajícím se [povolení zařízení pro vývoj](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

2. Po spuštění aplikace pomocí tlačítka vyberte obrázek k vyhodnocení.

## <a name="use-your-own-model"></a>Použití vlastního modelu

Pokud chcete použít vlastní model, postupujte následovně:

1. [Vytvořte a natrénujte](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) klasifikátor pomocí služby Custom Vision. Pokud chcete model exportovat, vyberte __kompaktní__ doménu, jako je například doména **Obecná (kompaktní)**. Pokud chcete exportovat existující klasifikátor, vyberte ikonu ozubeného kolečka v pravém horním rohu a převeďte doménu na kompaktní. V __Nastavení__ zvolte kompaktní model a pak projekt uložte a natrénujte.  

1. Přejděte na kartu Výkon a [exportujte model](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model). Vyberte iteraci natrénovanou s použitím kompaktní domény a zobrazí se tlačítko Exportovat. Vyberte *Exportovat*, *ONNX*, *ONNX1.2* a pak *Exportovat*. Jakmile bude soubor připravený, vyberte tlačítko *Stáhnout*.

1. Umístěte soubor ONNX do složky __Assets__ vašeho projektu. 

1. V Průzkumníku řešení klikněte pravým tlačítkem na složku Assets a vyberte __Přidat existující položku__. Vyberte soubor ONNX.

1. V Průzkumníku řešení vyberte soubor ONNX ze složky Assets. Změňte následující vlastnosti souboru: 
    
    * __Akce sestavení__ -> __Obsah__
    * __Kopírovat do výstupního adresáře__ -> __Kopírovat, pokud je novější__

1. Hodnotu proměnné `_onnxFileNames` změňte na název souboru ONNX. Upravte také hodnotu proměnné `ClassLabel` na počet popisků, které model obsahuje.

1. Proveďte sestavení a spuštění.

1. Klikněte na tlačítko a vyberte obrázek k vyhodnocení.

## <a name="next-steps"></a>Další postup

Pokud chcete objevit další způsoby, jak exportovat a používat model služby Custom Vision, podívejte se na následující dokumenty:

* [Export modelu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Použití exportovaného modelu Tensorflow v aplikaci pro Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Použití exportovaného modelu CoreML v aplikaci pro Swift iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Použití exportovaného modelu CoreML v aplikaci pro iOS s Xamarinem](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Další informace o používání modelů ONNX s Windows ML najdete v dokumentu týkajícím se [integrace modelu do aplikace s Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model).
