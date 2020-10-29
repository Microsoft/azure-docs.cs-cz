---
title: 'Kurz: detekce a zobrazení dat obličeje v imagi pomocí .NET SDK'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci pro Windows, která používá službu obličeje k detekci a orámování plošek v imagi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 6ff97e52d005ba1e91194b449377653317876163
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912799"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Kurz: Vytvoření aplikace WPF (Windows Presentation Framework) pro zobrazení obličejových dat v obrázku

V tomto kurzu se naučíte, jak pomocí služby Azure Face Service přes .NET Client SDK detekovat plošky v imagi a potom prezentovat tato data v uživatelském rozhraní. Vytvoříte aplikaci WPF, která detekuje plošky, nakreslí rámeček kolem každé plošky a ve stavovém řádku zobrazí popis obličeje. 

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> - Vytvoření aplikace WPF
> - Instalace klientské knihovny pro tvář
> - Použití klientské knihovny k rozpoznání tváří v obrázku
> - Zakreslení rámečku kolem každé rozpoznané tváře
> - Zobrazit popis zvýrazněné plochy na stavovém řádku

![Snímek obrazovky zobrazující rozpoznané tváře orámované obdélníky](../Images/getting-started-cs-detected.png)

Kompletní vzorový kód je k dispozici v [ukázkovém úložišti pro rozpoznávání tváře CSharp](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) na GitHubu.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/cognitive-services/), ještě než začnete. 


## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" vytvořte prostředek "  target="_blank"> pro vytváření obličeje a vytvořte na Azure Portal prostředek, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku** .
    * K připojení aplikace k Face API budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* [Vytvořte proměnné prostředí](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pro řetězec klíčového a koncového bodu služby s názvem `FACE_SUBSCRIPTION_KEY` a v `FACE_ENDPOINT` uvedeném pořadí.
- Libovolná edice sady [Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

Postupujte podle těchto kroků a vytvořte nový projekt aplikace WPF.

1. V aplikaci Visual Studio otevřete dialogové okno Nový projekt. Rozbalte položku **nainstalované** , potom **Visual C#** a pak vyberte **aplikace WPF (.NET Framework)** .
1. Pojmenujte tuto aplikaci **FaceTutorial** a klikněte na **OK** .
1. Získejte požadované balíčky NuGet. V Průzkumník řešení klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet** ; pak vyhledejte a nainstalujte následující balíček:
    - [Microsoft. Azure. Cognitiveservices Account. Vision. Face 2.6.0-Preview. 1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)

## <a name="add-the-initial-code"></a>Přidání počátečního kódu

V této části přidáte základní architekturu aplikace bez jeho funkcí specifických pro plošku.

### <a name="create-the-ui"></a>Vytvoření uživatelského rozhraní

Otevřete *MainWindow. XAML* a nahraďte obsah následujícím kódem &mdash; . Tento kód vytvoří okno uživatelského rozhraní. `FacePhoto_MouseMove`Metody a `BrowseButton_Click` jsou obslužné rutiny událostí, které budete později definovat.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Vytvoření hlavní třídy

Otevřete *MainWindow.XAML.cs* a přidejte obory názvů klientské knihovny společně s dalšími potřebnými obory názvů. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Dále do třídy **MainWindow** vložte následující kód. Tento kód vytvoří instanci **FaceClient** pomocí klíče předplatného a koncového bodu.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Dále přidejte konstruktor **MainWindow** . Zkontroluje řetězec adresy URL koncového bodu a přidruží ho k objektu klienta.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Nakonec přidejte do třídy metody **BrowseButton_Click** a **FacePhoto_MouseMove** . Tyto metody odpovídají obslužným rutinám událostí deklarovaným v souboru *MainWindow. XAML* . Metoda **BrowseButton_Click** vytvoří **OpenFileDialog** , který umožňuje uživateli vybrat obrázek. jpg. Pak zobrazí obrázek v hlavním okně. V pozdějších krocích budete vkládat zbývající kód pro **BrowseButton_Click** a **FacePhoto_MouseMove** . Všimněte si také `faceList` odkaz &mdash; na seznam objektů **DetectedFace** . Tento odkaz na místo, kde bude vaše aplikace ukládat a volat skutečná data obličeje.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Vyzkoušet aplikaci

Stisknutím klávesy **Start** v nabídce otestujte vaši aplikaci. Po otevření okna aplikace klikněte v levém dolním rohu na tlačítko **Procházet** . Zobrazí se dialogové okno **otevřít soubor** . Vyberte obrázek ze systému souborů a ověřte, zda se zobrazí v okně. Pak aplikaci zavřete a přejděte k dalšímu kroku.

![Snímek obrazovky ukazující nezměněný obrázek tváří](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Nahrát obrázek a detekovat plošky

Vaše aplikace detekuje obličeje voláním metody **FaceClient. Face. DetectWithStreamAsync** , která zabalí REST API [zjišťování](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) pro nahrání místní image.

Vložte následující metodu do třídy **MainWindow** pod metodou **FacePhoto_MouseMove** . Tato metoda definuje seznam atributů obličeje pro načtení a čtení odeslaného souboru obrázku do **datového proudu** . Pak předá oba tyto objekty voláním metody **DetectWithStreamAsync** .

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Kreslení obdélníků kolem plošek

Dále přidáte kód, který vykreslí obdélník kolem každého zjištěného obličeje v obrázku. Do třídy **MainWindow** vložte následující kód na konec metody **BrowseButton_Click** za `FacePhoto.Source = bitmapSource` řádkem. Tento kód naplní seznam zjištěných plošek od volání **UploadAndDetectFaces** . Pak nakreslí obdélník kolem každé plošky a v hlavním okně se zobrazí upravený obrázek.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Popsat obličeje

Přidejte následující metodu do třídy **MainWindow** pod metodou **UploadAndDetectFaces** . Tato metoda převede načtené atributy tváře na řetězec popisující obličej.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Zobrazení popisu tváře

Do metody **FacePhoto_MouseMove** přidejte následující kód. Tato obslužná rutina události zobrazuje řetězec popisu obličeje v `faceDescriptionStatusBar` okamžiku, kdy se ukazatel myši nachází nad zjištěným obdélníkem obličeje.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci a vyhledejte obrázek, který obsahuje nějakou tvář. Počkejte několik sekund, než služba Rozpoznávání tváře zareaguje. Na všech plochách v obrázku byste měli vidět červený obdélník. Pokud přesunete ukazatel myši na rámeček obličeje, popis této plochy by se měl zobrazit ve stavovém řádku.

![Snímek obrazovky zobrazující rozpoznané tváře orámované obdélníky](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili základní proces používání sady Face Service .NET SDK a vytvořili jste aplikaci pro detekci a orámování plošek v obraze. V dalším kroku se dozvíte více o podrobnostech detekce obličeje.

> [!div class="nextstepaction"]
> [Postup při rozpoznávání tváří v obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)