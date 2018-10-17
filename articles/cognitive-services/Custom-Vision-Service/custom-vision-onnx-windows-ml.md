---
title: 'Kurz: Použití modelu ONNX s Windows ML – Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Naučte se vytvořit aplikaci Windows UWP, která používá model ONNX exportovaný ze služby Azure Cognitive Services.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 3a9e9bc92ce38c4bb8d6d83c8017fa223342e7d2
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365600"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Kurz: Použití modelu ONNX ze služby Custom Vision s Windows ML (Preview)

Naučte se používat model ONNX exportovaný ze služby Custom Vision s Windows ML (Preview).

Informace v tomto dokumentu ukazují, jak používat soubor ONNX exportovaný ze služby Custom Vision Service s Windows ML. K dispozici je ukázková aplikace Windows UWP. Součástí ukázky je vytrénovaný model, který umí rozpoznat psy a kočky. Dále je k dispozici postup, jak s touto ukázkou použít vlastní model.

> [!div class="checklist"]
> * Informace o ukázkové aplikaci
> * Získání ukázkového kódu
> * Spuštění ukázky
> * Použití vlastního modelu

## <a name="prerequisites"></a>Požadavky

* Zařízení s Windows 10 s:

    * Fotoaparátem

    * Sadou Visual Studio 2017 verze 15.7 nebo novější s povolenou úlohou __vývoje pro Univerzální platformu Windows__

    * Povoleným vývojářským režimem Další informace najdete v dokumentu týkajícím se [povolení zařízení pro vývoj](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

* (Volitelné) Soubor ONNX exportovaný ze služby Custom Vision Další informace najdete v dokumentu týkajícím se [exportu modelu pro použití s mobilními zařízeními](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

    > [!NOTE]
    > Pokud chcete použít vlastní model, postupujte podle kroků v části [Použití vlastního modelu](#use-your-own-model).

## <a name="about-the-example-app"></a>Informace o ukázkové aplikaci

Aplikace je obecná aplikace Windows UWP (Univerzální platforma Windows). K poskytování obrázků do modelu používá fotoaparát vašeho zařízení s Windows 10. Značky a skóre vrácené modelem se zobrazují pod náhledem videa.

* Data jsou poskytována prostřednictvím fotoaparátu a k extrahování jednotlivých snímků se používá [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader). Snímky se posílají do modelu pro vyhodnocení.

* Model vrátí značky, na které byl vytrénován, a hodnotu float, která indikuje míru jistoty, že daný obrázek danou položku obsahuje.

### <a name="the-ui"></a>Uživatelské rozhraní

Uživatelské rozhraní pro ukázkovou aplikaci se vytváří pomocí ovládacích prvků __CaptureElement__ a __TextBlock__. CaptureElement zobrazuje náhled videa z fotoaparátu a TextBlock zobrazuje výsledky vrácené z modelu. 

### <a name="the-model"></a>Model

Model (`cat-or-dog.onnx`) poskytovaný s ukázkou byl vytvořen a vytrénován pomocí služby Cognitive Services Custom Vision. Vytrénovaný model se pak exportoval jako model ONNX. Další informace o používání této služby najdete v dokumentech týkajících se [sestavení klasifikátoru](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) a [exportu modelu pro použití s mobilními zařízeními](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

> [!IMPORTANT]
> Model, který je k dispozici s ukázkou, byl vytrénován pomocí malé sady obrázků psů a koček. Nemusí to tedy být ten nejlepší model na světě pro rozpoznávání psů a koček.

### <a name="the-model-class-file"></a>Soubor třídy modelu

Když přidáte soubor ONNX do aplikace Windows UWP, vytvoří soubor .cs. Tento soubor má stejný název jako soubor `.onnx` (`cat-or-dog` v této ukázce) a obsahuje třídy používané pro práci s modelem z jazyka C#. Entity ve vygenerované třídě ale mohou mít názvy jako `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Tyto položky můžete bezpečně přejmenovat (klikněte pravým tlačítkem, přejmenujte) na popisný název.

> [!NOTE]
> Ukázkový kód refaktoroval názvy vygenerované třídy a metody na následující názvy:
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Přístup k fotoaparátu

Karta __Capabilities__ (Možnosti) v souboru `Package.appxmanifest` je nakonfigurovaná tak, aby povolovala přístup k webové kameře a mikrofonu.

> [!NOTE]
> I když se v této ukázce zvuk nepoužívá, bylo potřeba povolit mikrofon, aby se na mém zařízení mohl povolit přístup k fotoaparátu.

Aplikace se pokusí získat přístup k fotoaparátu na zadní straně vašeho zařízení, pokud je k dispozici. K zahájení zachytávání videa z fotoaparátu použije třídu [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture). [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) se používá k zachycení snímků videa a jejich odeslání do modelu.

## <a name="get-the-example-code"></a>Získání ukázkového kódu

Ukázková aplikace je k dispozici na adrese [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>Spuštění ukázky

1. Ke spuštění aplikace ze sady Visual Studio použijte klávesu `F5`. Může se zobrazit výzva k povolení vývojářského režimu. Další informace najdete v dokumentu týkajícím se [povolení zařízení pro vývoj](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

2. Při zobrazení výzvy povolte aplikaci přístup k fotoaparátu a mikrofonu vašeho zařízení.

3. Namiřte fotoaparát na psa nebo kočku. Skóre indikující, jestli obrázek obsahuje psa nebo kočku, se zobrazuje pod náhledem v aplikaci.

    > [!TIP]
    > Pokud psa nebo kočku nemáte po ruce, můžete použít fotografii psa nebo kočky.

## <a name="use-your-own-model"></a>Použití vlastního modelu

Pokud chcete použít vlastní model, postupujte následovně:

> [!IMPORTANT]
> Kroky v této části přejmenují aktuální model (cat-or-dog.cs) a refaktorují názvy třídy a metody nového modelu. Zabrání se tak konfliktům názvů s ukázkovým modelem.

1. Model vytrénujte pomocí služby Custom Vision. Informace o tom, jak vytrénovat model, najdete v tématu týkajícím se [sestavení klasifikátoru](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier).

2. Vytrénovaný model exportujte jako model ONNX. Informace o tom, jak vyexportovat model, najdete v tématu týkajícím se [exportu modelu pro použití s mobilními zařízeními](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

3. V Průzkumníku řešení klikněte pravým tlačítkem myši na soubor __cat-or-dog.cs__ a přejmenujte ho na __cat-or-dog.txt__. Přejmenováním zabráníte konfliktu názvu s novým modelem.

    > [!TIP]
    > V novém modelu byste také mohli použít jiné názvy pro třídy, použití stávajících názvů je ale jednodušší.

4. V Průzkumníku řešení klikněte pravým tlačítkem myši na položku __VisionApp__ a pak vyberte __Přidat__ > __Existující položka__.

5. Pokud chcete pro model vygenerovat třídu, vyberte soubor ONNX, který chcete naimportovat, a pak vyberte tlačítko __Přidat__. Do Průzkumníka řešení se přidá nová třída se stejným názvem jako soubor ONNX (ale s příponou `.cs`).

6. Otevřete vygenerovaný soubor .cs a vyhledejte názvy následujících položek:

    > [!IMPORTANT]
    > Ukázkový soubor `cat-or-dog.txt` použijte jako vodítko k rozpoznání tříd a funkcí.

    * Třída, která definuje vstup modelu. Vygenerovaný název může být podobný jako `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Tuto třídu přejmenujte na __ModelInput__.
    * Třída, která definuje výstup modelu. Vygenerovaný název může být podobný jako `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Tuto třídu přejmenujte na __ModelOutput__.
    * Třída, která definuje model. Vygenerovaný název může být podobný jako `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Tuto třídu přejmenujte na __Model__.
    * Metoda, která vytvoří model. Vygenerovaný název může být podobný jako `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Tuto metodu přejmenujte na __CreateModel__.

7. V Průzkumníku řešení přesuňte soubor `.onnx` do složky __prostředků__. 

8. Pokud chcete zahrnout soubor ONNX do balíčku aplikace, vyberte soubor `.onnx` a __Akci sestavení__ ve vlastnostech nastavte na __Obsah__.

9. Otevřete soubor __MainPage.xaml.cs__. Vyhledejte následující řádek a změňte název souboru na nový soubor `.onnx`:

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    Tato změna načte nový model v době běhu.

10. Sestavte a spusťte aplikaci. K vyhodnocení obrázků teď použije nový model.

## <a name="next-steps"></a>Další kroky

Pokud chcete objevit další způsoby, jak exportovat a používat model služby Custom Vision, podívejte se na následující dokumenty:

* [Export modelu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Použití exportovaného modelu Tensorflow v aplikaci pro Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Použití exportovaného modelu CoreML v aplikaci pro Swift iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Použití exportovaného modelu CoreML v aplikaci pro iOS s Xamarinem](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Další informace o používání modelů ONNX s Windows ML najdete v dokumentu týkajícím se [integrace modelu do aplikace s Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model).
