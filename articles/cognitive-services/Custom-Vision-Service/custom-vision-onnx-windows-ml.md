---
title: Použití modelu ONNX s Windows ML – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Naučte se vytvořit aplikaci Windows UWP, která používá model ONNX exportovaný ze služby Azure Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: 58ced0c45d66223ac3e40112126e92a4539db32d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616072"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Použití modelu ONNX z Custom Vision s Windows ML (Preview)

Naučte se používat model ONNX exportovaný ze služby Custom Vision s Windows ML (Preview).

V této příručce se dozvíte, jak použít soubor ONNX exportovaný z Custom Vision Service s Windows ML. Ukázkovou aplikaci UWP použijete s vlastním vyškolenou klasifikátorovou imagí.

## <a name="prerequisites"></a>Předpoklady

* Windows 10 verze 1809 nebo vyšší
* Windows SDK pro Build 17763 nebo vyšší
* Sadou Visual Studio 2017 verze 15.7 nebo novější s povolenou úlohou __vývoje pro Univerzální platformu Windows__
* V počítači je povolen vývojářský režim. Další informace najdete v tématu [Povolení vývoje zařízení](/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Informace o ukázkové aplikaci

Vložená aplikace je obecná aplikace Windows UWP. Umožňuje vybrat obrázek z počítače, který pak zpracovává místně uložený klasifikační model. Vedle obrázku se zobrazí značky a skóre vrácené modelem.

## <a name="get-the-example-code"></a>Získání ukázkového kódu

Ukázková aplikace je k dispozici na [COGNITIVE Services ONNX Custom Vision ukázkové](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) úložiště na GitHubu. Naklonujte ho na místní počítač a otevřete *SampleOnnxEvaluationApp. sln* v aplikaci Visual Studio.

## <a name="test-the-application"></a>Testování aplikace

1. Ke spuštění aplikace ze sady Visual Studio použijte klávesu `F5`. Může se zobrazit výzva k povolení vývojářského režimu.
1. Po spuštění aplikace pomocí tlačítka vyberte obrázek k vyhodnocení. Výchozí ONNX model je vyškolen pro klasifikaci různých typů plankton.

## <a name="use-your-own-model"></a>Použití vlastního modelu

Pokud chcete použít vlastní model klasifikátoru obrázků, postupujte takto:

1. Vytvořte a natrénujte klasifikátor pomocí služby Custom Vision. Pokyny k tomu, jak to provést, najdete v tématu [Vytvoření a výuka třídění](./getting-started-build-a-classifier.md). Použijte jednu z **kompaktních** domén, jako je například **General (Compact)**. 
   * Pokud máte existující klasifikátor, který používá jinou doménu, můžete ho převést na **komprimaci** v nastavení projektu. Pak projekt znovu nahlaste, než budete pokračovat.
1. Exportujte model. Přepněte na kartu výkon a vyberte iteraci, která byla vyškolena s **kompaktní** doménou. Vyberte tlačítko **exportovat** , které se zobrazí. Pak vyberte **ONNX** a pak **exportujte**. Jakmile bude soubor připravený, vyberte tlačítko **Stáhnout**. Další informace o možnostech exportu najdete v tématu [Export modelu](./export-your-model.md).
1. Otevřete stažený soubor *. zip* a extrahujte z něj soubor *model. Onnx* . Tento soubor obsahuje model třídění.
1. V Průzkumník řešení v aplikaci Visual Studio klikněte pravým tlačítkem myši na složku **assets** a vyberte možnost __Přidat existující položku__. Vyberte soubor ONNX.
1. V Průzkumník řešení klikněte pravým tlačítkem na soubor ONNX a vyberte **vlastnosti**. Změňte následující vlastnosti souboru:
   * __Akce sestavení__  ->  __Obsah__
   * __Kopírovat do výstupního adresáře__  ->  __Kopírovat, pokud je novější__
1. Pak otevřete soubor _MainPage. XAML. cs_ a změňte hodnotu `_ourOnnxFileName` na název vašeho souboru ONNX.
1. Použijte `F5` k sestavení a spuštění projektu.
1. Klikněte na tlačítko a vyberte obrázek k vyhodnocení.

## <a name="next-steps"></a>Další kroky

Pokud chcete objevit další způsoby, jak exportovat a používat model služby Custom Vision, podívejte se na následující dokumenty:

* [Export modelu](./export-your-model.md)
* [Použití exportovaného modelu Tensorflow v aplikaci pro Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Použití exportovaného modelu CoreML v aplikaci pro Swift iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Použití exportovaného modelu CoreML v aplikaci pro iOS s Xamarinem](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Další informace o používání modelů ONNX s Windows ML najdete v tématu [integrace modelu do vaší aplikace pomocí Windows ml](/windows/ai/windows-ml/integrate-model).