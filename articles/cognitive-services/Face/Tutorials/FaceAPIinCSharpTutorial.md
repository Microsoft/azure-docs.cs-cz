---
title: 'Kurz: Zjištění a zobrazení dat pro rozpoznávání tváře v obrázku pomocí sady .NET SDK'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci s Windows, který používá rozhraní API pro rozpoznávání tváře ke zjišťování a snímků tváří v obrázku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 11/05/2018
ms.author: pafarley
ms.openlocfilehash: ff3063193fa2a8f71f441aa64c8835fd7528ff4e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882620"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>Kurz: Vytvoření aplikace WPF pro zobrazení dat pro rozpoznávání tváře v obrázku

V tomto kurzu se dozvíte, jak používat rozhraní API pro rozpoznávání tváře Azure prostřednictvím klienta .NET SDK, k rozpoznávání tváří v obrázku a potom prezentovat data v uživatelském rozhraní. Vytvoříte jednoduchou aplikaci Windows Presentation Framework (WPF), který detekuje tváře, nakreslí rámeček kolek každou plochu a zobrazí popis rozhraním pro rozpoznávání tváře ve stavovém řádku. 

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> - Vytvoření aplikace WPF
> - Nainstalujte klientské knihovny rozhraní API pro rozpoznávání tváře
> - Použití klientské knihovny k rozpoznání tváří v obrázku
> - Zakreslení rámečku kolem každé rozpoznané tváře
> - Zobrazit popis zvýrazněné rozpoznávání tváře ve stavovém řádku

![Snímek obrazovky zobrazující rozpoznané tváře orámované obdélníky](../Images/getting-started-cs-detected.png)

Úplný ukázkový kód je k dispozici v [ukázce CSharp Cognitive pro rozpoznávání tváře](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) úložišti na Githubu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 


## <a name="prerequisites"></a>Požadavky

- Klíč rozhraní API pro rozpoznávání tváře předplatného. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby API pro rozpoznávání tváře a získejte klíč.
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

Postupujte podle těchto kroků a vytvořte nový projekt aplikace WPF.

1. V sadě Visual Studio otevřete dialogové okno Nový projekt. Rozbalte **nainstalováno**, pak **Visual C#** a pak vyberte **aplikace WPF (.NET Framework)**.
1. Pojmenujte tuto aplikaci **FaceTutorial** a klikněte na **OK**.
1. Získejte požadované balíčky NuGet. Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **spravovat balíčky NuGet**; poté najít a nainstalovat balíček následující:
    - Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview

## <a name="add-the-initial-code"></a>Přidání počátečního kódu

V této části přidáte základní architekturu pro aplikaci bez jeho funkce specifické pro rozpoznávání tváře.

### <a name="create-the-ui"></a>Vytvoření uživatelského rozhraní

Otevřít *souboru MainWindow.xaml* a nahraďte jeho obsah následujícím kódem&mdash;tím se vytvoří okno uživatelského rozhraní. Všimněte si, že `FacePhoto_MouseMove` a `BrowseButton_Click` jsou obslužné rutiny událostí, které se definují později.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?range=1-18)]

### <a name="create-the-main-class"></a>Vytvořit hlavní třída

Otevřít *MainWindow.xaml.cs* a přidejte klientské knihovny obory názvů, společně s další nezbytné obory názvů. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=1-12)]

V dalším kroku vložte následující kód **hlavního okna MainWindow** třídy. Tím se vytvoří **FaceClient** instance pomocí klíč předplatného, které je nutné zadat sami. Je nutné nastavit také řetězec oblasti `faceEndpoint` správné oblastí pro vaše předplatné (najdete v článku [dokumenty k rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) seznam všechny koncové body oblasti).

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=18-46)]

Potom vložte následující kód do **hlavního okna MainWindow** metody.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=50-61)]

Nakonec přidejte **BrowseButton_Click** a **FacePhoto_MouseMove** metody do třídy. Tyto příkazy odpovídají obslužné rutiny událostí, které jsou deklarované v *souboru MainWindow.xaml*. **BrowseButton_Click** metoda vytvoří **OpenFileDialog**, který umožňuje uživateli vybrat obrázek ve formátu .jpg. Pak zobrazí obrázek v hlavním okně. Vloží kód zbývající **BrowseButton_Click** a **FacePhoto_MouseMove** v dalších krocích. Všimněte si také `faceList` odkaz&mdash;seznam **DetectedFace** objekty. To je, kde se vaše aplikace ukládat a volání skutečné tváře data.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=64-90,146)]

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=148-150,187)]

### <a name="try-the-app"></a>Zkuste aplikaci

Stisknutím klávesy **Start** v nabídce otestujte vaši aplikaci. Když se otevře okno aplikace, klikněte na tlačítko **Procházet** v levém dolním rohu. A **otevřít soubor** by se měla zobrazit dialogové okno. Vybrat obrázek z vašeho systému souborů a ověřte, zda se zobrazí v okně. Zavřete aplikaci a pokračujte k dalšímu kroku.

![Snímek obrazovky ukazující nezměněný obrázek tváří](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Nahrajte obrázek a rozpoznávání tváří

Vaše aplikace bude voláním rozpoznávání tváří **FaceClient.Face.DetectWithStreamAsync** metoda, která zabalí [rozpoznat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozhraní REST API pro nahrávání místní image.

Vložte následující metodu v **hlavního okna MainWindow** třídy níže **FacePhoto_MouseMove** metody. To definuje seznam obličejových atributů k načtení a přečte soubor bitové kopie odeslaných do **Stream**. Pak předá obě tyto objekty **DetectWithStreamAsync** volání metody.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=189-226)]

## <a name="draw-rectangles-around-faces"></a>Kreslení obdélníků kolem tváří

V dalším kroku přidáte kód pro vykreslení obdélníku kolem každou zjištěnou tvář na obrázku. V **hlavního okna MainWindow** třídy, vložte následující kód na konci **BrowseButton_Click** metoda, poté, co `FacePhoto.Source = bitmapSource` řádku. Tím vyplníte seznam zjištěných tváří z volání **UploadAndDetectFaces**. Nakreslí obdélník kolem každou plochu a upravenou image se zobrazí v hlavním okně.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=92-145)]

## <a name="describe-the-faces"></a>Popište tváří

Přidejte následující metodu do **hlavního okna MainWindow** třídy níže **UploadAndDetectFaces** metody. Načtená obličejových atributů to převede na řetězec, který popisuje typ písma.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=228-286)]

## <a name="display-the-face-description"></a>Zobrazení popisu tváře

Přidejte následující kód, který **FacePhoto_MouseMove** metody. Tato obslužná rutina události zobrazí řetězce popisu pro rozpoznávání tváře ve `faceDescriptionStatusBar` při ukazatelem přejde obdélník zjištěných pro rozpoznávání tváře.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=151-186)]


## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci a vyhledejte obrázek, který obsahuje nějakou tvář. Počkejte několik sekund, než služba Rozpoznávání tváře zareaguje. Měli byste vidět červeným rámečkem na všech tváří na obrázku. Pokud najeďte myší nad obdélníku pro rozpoznávání tváře, by se zobrazit popis této pro rozpoznávání tváře ve stavovém řádku.

![Snímek obrazovky zobrazující rozpoznané tváře orámované obdélníky](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu se naučili základní proces pomocí sady .NET SDK služby pro rozpoznávání tváře a vytvořili aplikaci ke zjišťování a snímků tváří v obrázku. Další informace o podrobnosti o rozpoznávání tváří v dalším kroku.

> [!div class="nextstepaction"]
> [Postup při rozpoznávání tváří v obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
