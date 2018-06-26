---
title: Vlastní model vize ONNX s Windows ML - kognitivní služby | Microsoft Docs
description: Naučte se vytvářet aplikace pro UPW v systému Windows, který používá model ONNX exportovaný z kognitivní služby.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 0b128ba1800e74c20c09a9c5711c8473f1dd00d0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939451"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Kurz: Použijte model ONNX z vlastní vize s Windows ML (preview)

Naučte se používat model ONNX exportovaný z službu vlastní vize s Windows ML (preview).

Informace v tomto dokumentu ukazuje, jak můžete použít soubor ONNX exportovaný z službu vlastní vize s ML systému Windows. Příklad aplikaci Windows UWP je k dispozici. Modulu trained model, který dokáže rozpoznat psi a kočky je součástí v příkladu. Kroky jsou také uvedeny na tom, jak můžete použít vlastní modelu v tomto příkladu.

> [!div class="checklist"]
> * O aplikaci příklad
> * Získání ukázkového kódu
> * Spuštění ukázkového
> * Použít vlastní model

## <a name="prerequisites"></a>Požadavky

* Zařízení Windows 10 s:

    * Fotoaparátu.

    * Visual Studio 2017 verze 15.7 nebo novější s __vývoj pro univerzální platformu Windows__ zatížení, povolené.

    * Režim vývojáře je povolen. Další informace najdete v tématu [povolit zařízení pro vývoj](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentu.

* (Volitelné) Soubor ONNX exportovat ze služby vize vlastní. Další informace najdete v tématu [exportovat modelu pro použití s mobilními zařízeními](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentu.

    > [!NOTE]
    > Vlastní model, postupujte podle kroků v [použít vlastní model](#use-your-own-model) části.

## <a name="about-the-example-app"></a>O aplikaci příklad

Aplikace je obecná aplikace Windows UWP. Kamera na zařízení s Windows 10 používá k poskytování bitové kopie do modelu. Značky a skóre vrácený modelu se zobrazí pod náhledu videa.

* Jako data pocházejí ale fotoaparátu, [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) slouží k extrakci jednotlivé snímky. Snímky jsou odesílány modelu pro vyhodnocování.

* Model vrátí značky, které bylo trénink na a float hodnotu, která určuje, jak jisti, je, že bitová kopie obsahuje daná položka.

### <a name="the-ui"></a>Uživatelské rozhraní

Uživatelské rozhraní pro ukázková aplikace je vytvořený pomocí __CaptureElement__ a __TextBlock__ ovládací prvky. CaptureElement zobrazí náhled videa z fotoaparátu a objektu TextBlock výsledky vrácené z modelu. 

### <a name="the-model"></a>Model

Model (`cat-or-dog.onnx`) součástí v příkladu byla vytvořena a cvičení pomocí služby kognitivní vize vlastní služby. Pro cvičný model byl exportován pak jako ONNX model. Další informace o používání této služby najdete v tématu [jak sestavit třídění](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) a [exportovat modelu pro použití s mobilními zařízeními](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumenty.

> [!IMPORTANT]
> S malého PSA a cat bitových kopií bylo cvičení modelu součástí v příkladu. Proto nemusí být nejvhodnější na světě v rozpozná psi a kočky.

### <a name="the-model-class-file"></a>Soubor modelu – třída

Když přidáte soubor ONNX do aplikace Windows v Kombinaci, vytvoří soubor .cs. Tento soubor má stejný název jako `.onnx` souboru (`cat-or-dog` v tomto příkladu) a obsahuje třídy používané pro práci s modelem z jazyka C#. Entity v generovaná třída ale může mít názvy jako `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Je možné bezpečně přejmenovat tyto položky (klikněte pravým tlačítkem myši, přejmenujte) popisný název.

> [!NOTE]
> Ukázkový kód je teď vyčleněný vygenerované třídy a metody názvy takto:
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Přístup k fotoaparátu

__Možnosti__ ve `Package.appxmanifest` souboru je nakonfigurována pro povolení přístupu k webová kamera a mikrofon.

> [!NOTE]
> I když v tomto příkladu nepoužívá zvuk, musel jsem povolit mikrofon předtím, než bylo možné k přístup k fotoaparátu v zařízení.

Aplikace se pokusí získat fotoaparátu na zadní straně zařízení, pokud je k dispozici. Použije [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) třída zahájíte zaznamenávání video z kamery. [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) se používá k zachycení snímků videa a odešlete je do modelu.

## <a name="get-the-example-code"></a>Získání ukázkového kódu

Ukázková aplikace je k dispozici na [ https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP ](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>Spuštění ukázkového

1. Použití `F5` klíče a spusťte aplikaci v sadě Visual Studio. Zobrazí se výzva k povolení režimu vývojáře. Další informace najdete v tématu [povolit zařízení pro vývoj](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) dokumentu.

2. Po zobrazení výzvy, umožňuje aplikaci přístup k fotoaparátu a mikrofon na vašem zařízení.

3. Bod fotoaparát na pes nebo kočka. Skóre pro zda bitová kopie obsahuje pes nebo kočka se zobrazí pod ve verzi preview v aplikaci.

    > [!TIP]
    > Pokud nemáte pes nebo cat užitečný, můžete fotografie PSA nebo cat.

## <a name="use-your-own-model"></a>Použít vlastní model

Pokud chcete používat vlastní model, použijte následující kroky:

> [!IMPORTANT]
> Postup v této části přejmenujte na aktuální model (cat nebo dog.cs) a Refaktorovat třídy a metody názvy nový model. Toto je zamezení kolizím s modelem příklad.

1. Cvičení modelu pomocí vlastní vize služby. Informace o tom, jak trénování modelu najdete v tématu [jak sestavit třídění](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier).

2. Exportujte jako ONNX model naučeného modelu. Informace o tom, jak exportovat model najdete v tématu [exportovat modelu pro použití s mobilními zařízeními](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) dokumentu.

3. V Průzkumníku řešení klikněte pravým tlačítkem myši __cat nebo dog.cs__ a přejmenujte ji na __cat nebo dog.txt__. Přejmenování ho zabraňuje kolize názvů s novým modelem.

    > [!TIP]
    > Můžete také použít odlišné názvy pro názvy tříd v novém modelu, ale opakovaného použití existující názvy je jednodušší.

4. V Průzkumníku řešení klikněte pravým tlačítkem myši __VisionApp__ položku a pak vyberte __přidat__ > __existující položky...__ .

5. Pokud chcete vygenerovat třídu modelu, vyberte soubor ONNX chcete importovat a pak vyberte __přidat__ tlačítko. Novou třídu se stejným názvem jako soubor ONNX (ale `.cs` rozšíření) je přidána v Průzkumníku řešení.

6. Otevřete soubor generovaný .cs a vyhledání názvů následující položky:

    > [!IMPORTANT]
    > Použijte tento příklad `cat-or-dog.txt` souboru jako vodítko k rozpoznat třídy a funkce.

    * Třída, která definuje vstupní modelu. Vygenerovaný název může být podobné `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Přejmenování této třídy lze __ModelInput__.
    * Třída, která definuje výstup modelu. Vygenerovaný název může být podobné `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Přejmenování této třídy lze __ModelOutput__.
    * Třída, která definuje modelu. Vygenerovaný název může být podobné `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Přejmenování této třídy lze __modelu__.
    * Metoda, která vytvoří model. Vygenerovaný název může být podobné `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Přejmenujte tuto metodu za účelem __CreateModel__.

7. V Průzkumníku řešení přesunout `.onnx` soubor do __prostředky__ složky. 

8. Chcete-li zahrnout soubor ONNX v balíčku aplikace, vyberte `.onnx` souboru a nastavit __akce sestavení__ k __obsahu__ ve vlastnostech.

9. Otevřete __MainPage.xaml.cs__ souboru. Vyhledejte následující řádek a změňte název souboru do nového `.onnx` souboru:

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    Tato změna načte nový model za běhu.

10. Sestavte a spusťte aplikaci. Teď používá nový model skóre pro bitové kopie.

## <a name="next-steps"></a>Další postup

Chcete-li zjistit další způsoby, jak vyexportovat a použít vlastní vize modelu, najdete v následujících dokumentech:

* [Export modelu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Použít exportovaný Tensorflow modelu v aplikaci pro Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Použít exportovaný CoreML modelu v aplikaci Swift iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Použití exportovat CoreML modelu v aplikaci pro systém iOS pomocí Xamarinu](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Další informace o používání ONNX modely se službou Windows ML najdete v tématu [integrovat modelu do vaší aplikace pomocí Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model) dokumentu.
