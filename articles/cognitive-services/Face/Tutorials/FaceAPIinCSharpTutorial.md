---
title: 'Kurz: Zjišťování a zobrazení dat obličeje v obraze pomocí sady .NET SDK'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci pro Windows, která používá službu Face ke zjišťování a vytváření rámů v obraze.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a5cf3c59c94134e1d0751c1467cd324a95c366eb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78898820"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Kurz: Vytvoření aplikace WPF (Windows Presentation Framework) pro zobrazení dat obličeje v obraze

V tomto kurzu se dozvíte, jak používat službu Azure Face prostřednictvím sady .NET klienta SDK, ke zjištění tváří v bitové kopii a pak prezentovat tato data v unovém rozhraní. Vytvoříte aplikaci WPF, která detekuje plochy, nakreslí rámeček kolem každé plochy a zobrazí popis plochy ve stavovém řádku. 

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> - Vytvoření aplikace WPF
> - Instalace klientské knihovny Face
> - Použití klientské knihovny k rozpoznání tváří v obrázku
> - Zakreslení rámečku kolem každé rozpoznané tváře
> - Zobrazení popisu zvýrazněné plochy na stavovém řádku

![Snímek obrazovky zobrazující rozpoznané tváře orámované obdélníky](../Images/getting-started-cs-detected.png)

Kompletní ukázkový kód je k dispozici v [ukázkovém](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) úložišti Cognitive Face CSharp na GitHubu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. 


## <a name="prerequisites"></a>Požadavky

- Klíč předplatného Face. Můžete získat bezplatný klíč zkušebního předplatného od [společnosti Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo postupujte podle pokynů v [tématu Vytvoření účtu služeb Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) abyste se přihlásili ke službě Face a získali klíč. Potom [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec koncového `FACE_SUBSCRIPTION_KEY` klíče `FACE_ENDPOINT`a služby s názvem a , resp.
- Libovolné vydání [Visual Studia 2015 nebo 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

Následujícím postupem vytvořte nový projekt aplikace WPF.

1. V sadě Visual Studio otevřete dialogové okno Nový projekt. Rozbalte **možnost Nainstalováno**, potom **Visual C#** a vyberte **wpf aplikaci (.NET Framework).**
1. Pojmenujte tuto aplikaci **FaceTutorial** a klikněte na **OK**.
1. Získejte požadované balíčky NuGet. Klikněte pravým tlačítkem myši na projekt v Průzkumníku řešení a vyberte **Spravovat balíčky NuGet**; potom vyhledejte a nainstalujte následující balíček:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.5.0-preview.1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1)

## <a name="add-the-initial-code"></a>Přidání počátečního kódu

V této části přidáte základní rámec aplikace bez jejích funkcí specifických pro obličej.

### <a name="create-the-ui"></a>Vytvoření nového ui

Otevřete *MainWindow.xaml* a nahraďte&mdash;obsah následujícím kódem, který tento kód vytvoří okno uživatelského okna. Metody `FacePhoto_MouseMove` `BrowseButton_Click` a jsou obslužné rutiny událostí, které budete definovat později.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Vytvoření hlavní třídy

Otevřete *MainWindow.xaml.cs* a přidejte obory názvů klientské knihovny spolu s dalšími nezbytnými obory názvů. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Dále vložte následující kód do třídy **MainWindow.** Tento kód vytvoří instanci **FaceClient** pomocí klíče předplatného a koncového bodu.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Dále přidejte **MainWindow** konstruktor. Zkontroluje řetězec adresy URL koncového bodu a pak jej přidruží k objektu klienta.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Nakonec přidejte **metody BrowseButton_Click** a **FacePhoto_MouseMove** do třídy. Tyto metody odpovídají obslužné rutiny události deklarované v *MainWindow.xaml*. Metoda **BrowseButton_Click** vytvoří **openfiledialog**, který umožňuje uživateli vybrat obrázek JPG. Potom zobrazí obrázek v hlavním okně. Zbývající kód pro **BrowseButton_Click** a **FacePhoto_MouseMove** vložíte v pozdějších krocích. Všimněte `faceList` si&mdash;také odkazu na seznam objektů **DetectedFace.** Tento odkaz je místo, kde vaše aplikace bude ukládat a volat skutečná data obličeje.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Vyzkoušejte aplikaci

Stisknutím klávesy **Start** v nabídce otestujte vaši aplikaci. Po otevření okna aplikace klikněte v levém dolním rohu na **Procházet.** Měl by se zobrazit dialogové okno **Otevřít soubor.** Vyberte obrázek ze souborového systému a ověřte, zda se zobrazí v okně. Potom aplikaci zavřete a překroucete k dalšímu kroku.

![Snímek obrazovky ukazující nezměněný obrázek tváří](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Nahrání obrázku a detekce tváří

Vaše aplikace detekuje tváře voláním **faceclient.Face.DetectWithStreamAsync** metoda, která zabalí [rozhraní DETECT](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API pro nahrávání místní image.

Pod metodu **FacePhoto_MouseMove** vložte následující metodu do třídy **MainWindow.** Tato metoda definuje seznam atributů face pro načtení a přečte odeslaný soubor obrázku do **datového proudu**. Potom předá oba tyto objekty volání metody **DetectWithStreamAsync.**

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Kreslení obdélníků kolem ploch

Dále přidáte kód pro nakreslení obdélníku kolem každé zjištěné plochy v obraze. Ve třídě **MainWindow** vložte následující kód na konec **metody** `FacePhoto.Source = bitmapSource` BrowseButton_Click za řádek. Tento kód naplní seznam zjištěných tváří z volání **UploadAndDetectFaces**. Pak nakreslí obdélník kolem každé plochy a zobrazí upravený obrázek v hlavním okně.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Popsat plochy

Přidejte následující metodu **mainwindow** třídy pod **UploadAndDetectFaces** metoda. Tato metoda převede načtení plochy atributy do řetězce popisující plochu.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Zobrazení popisu tváře

Přidejte následující kód do **metody FacePhoto_MouseMove.** Tato obslužná rutina události zobrazí řetězec popisu obličeje v `faceDescriptionStatusBar` případě, že kurzor najede myší na obdélník zjištěné plochy.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci a vyhledejte obrázek, který obsahuje nějakou tvář. Počkejte několik sekund, než služba Rozpoznávání tváře zareaguje. Na každé z ploch v obraze byste měli vidět červený obdélník. Pokud přesunete ukazatel myši nad obdélník plochy, měl by se popis této plochy zobrazit na stavovém řádku.

![Snímek obrazovky zobrazující rozpoznané tváře orámované obdélníky](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili základní proces pro použití služby Face .NET SDK a vytvořili aplikaci pro detekci a vytváření rámů v obraze. Dále se dozvíte více o podrobnostech detekce obličeje.

> [!div class="nextstepaction"]
> [Postup při rozpoznávání tváří v obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
