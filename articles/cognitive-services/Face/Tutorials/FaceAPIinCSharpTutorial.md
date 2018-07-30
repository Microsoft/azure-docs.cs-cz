---
title: Kurz k rozhraní API pro rozpoznávání tváře v C# | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto kurzu vytvoříte jednoduchou aplikaci pro Windows, která využívá službu Rozpoznávání tváře Cognitive Services k rozpoznání a orámování tváří na obrázku.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: e4f2192c40f0b650b31ed59642dee89e42eca703
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125927"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image"></a>Kurz: Vytvoření aplikace WPF k detekci a orámování tváří na obrázku

V tomto kurzu vytvoříte aplikaci Windows Presentation Framework (WPF), která používá službu Rozpoznávání tváře prostřednictvím klientské knihovny pro .NET. Tato aplikace detekuje tváře v obrázku, kolem každé tváře nakreslí rámeček a zobrazí popis tváře na stavovém řádku. Kompletní kód ukázky je dostupný na GitHubu v tématu [Detekce a orámování tváří na obrázku ve Windows](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample).

![Snímek obrazovky zobrazující rozpoznané tváře orámované obdélníky](../Images/getting-started-cs-detected.png)

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> - Vytvoření aplikace WPF
> - Instalace klientské knihovny služby Rozpoznávání tváře
> - Použití klientské knihovny k rozpoznání tváří v obrázku
> - Zakreslení rámečku kolem každé rozpoznané tváře
> - Zobrazení popisu tváře na stavovém řádku

## <a name="prerequisites"></a>Požadavky

- Ke spuštění této ukázky budete potřebovat klíč předplatného. Klíče bezplatného zkušebního předplatného můžete získat v tématu [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/). Pro Visual Studio 2017 se vyžaduje úloha vývoje desktopových aplikací .NET. Tento kurz využívá Visual Studio 2017 Community Edition.
- Balíček NuGet klientské knihovny [Microsoft.Azure.CognitiveServices.Vision.Face 2.0.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.0.0-preview). Stažení tohoto balíčku není nutné. Pokyny k instalaci jsou uvedené dál.

## <a name="create-the-visual-studio-solution"></a>Vytvoření řešení v sadě Visual Studio

Postupujte podle těchto kroků a vytvořte projekt aplikace WPF pro Windows.

1. Otevřete Visual Studio a v nabídce **Soubor** klikněte na **Nový** a potom na **Projekt**.
   - V sadě Visual Studio 2017 rozbalte **Nainstalováno** a potom **Další jazyky**. Vyberte **Visual C#** a potom **Aplikace WPF (.NET Framework)**.
   - V sadě Visual Studio 2015 rozbalte **Nainstalováno** a potom **Šablony**. Vyberte **Visual C#** a potom **Aplikace WPF**.
1. Pojmenujte tuto aplikaci **FaceTutorial** a klikněte na **OK**.

## <a name="install-the-face-service-client-library"></a>Instalace klientské knihovny služby Rozpoznávání tváře

Při instalaci klientské knihovny postupujte podle těchto pokynů.

1. V nabídce **Nástroje** vyberte **Správce balíčků NuGet** a potom **Konzola Správce balíčků**.
1. Do pole **Konzola správce balíčků**, vložte následující text a potom stiskněte **Enter**.

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.0.0-preview`

## <a name="add-the-initial-code"></a>Přidání počátečního kódu

### <a name="mainwindowxaml"></a>MainWindow.xaml

Otevřete soubor *MainWindow.xaml* (tip: k přepnutí podoken použijte **ikonu se šipkami nahoru/dolů**) a nahraďte jeho obsah následujícím kódem. Tento kód xaml slouží k vytvoření okna uživatelského rozhraní. Všimněte si obslužných rutin událostí `FacePhoto_MouseMove` a `BrowseButton_Click`.

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Rozbalte *MainWindow.xaml* a potom otevřete *MainWindow.xaml.cs* a nahraďte jeho obsah následujícím kódem. Červené podtržení vlnovkou můžete ignorovat – po prvním sestavení zmizí.

První dva řádky naimportují obory názvů klientské knihovny. Potom se vytvoří `FaceClient`, předá se klíč předplatného a v konstruktoru `MainWindow` se nastaví oblast Azure. Dvě metody, `BrowseButton_Click` a `FacePhoto_MouseMove`, odpovídají obslužným rutinám událostí deklarovaným v *MainWindow.xaml*.

`BrowseButton_Click` vytvoří `OpenFileDialog`, který uživateli umožňuje vybrat obrázek jpg. Tento obrázek se načte a zobrazí v hlavním okně. Zbývající kód pro `BrowseButton_Click` a kód pro `FacePhoto_MouseMove` se vloží v dalších krocích.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string baseUri =
            "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(baseUri, UriKind.Absolute))
            {
                faceClient.BaseUri = new Uri(baseUri);
            }
            else
            {
                MessageBox.Show(baseUri,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>Zadání klíče předplatného a ověření nebo změna oblasti

- V souboru *MainWindow.xaml.cs* najděte následující řádek a nahraďte `<Subscription Key>` klíčem předplatného rozhraní API pro rozpoznávání tváře:

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- V souboru *MainWindow.xaml.cs* najděte následující řádek a nahraďte nebo ověřte klíč předplatného přidružený k oblasti Azure:

    ```csharp
    private const string baseUri =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
    ```

    Ujistěte se, že umístění je stejné jako to, ve kterém jste získali klíče předplatného. Pokud jste klíče předplatného získali například z oblasti **westus**, `Westcentralus` nahraďte `Westus`.

    Pokud jste klíče předplatného získali pomocí bezplatné zkušební verze, oblastí pro vaše klíče je **westcentralus**, takže není nutná žádná změna.

### <a name="test-the-app"></a>Otestování aplikace

Stisknutím klávesy **Start** v nabídce otestujte vaši aplikaci. Po otevření okna klikněte na **Procházet** v levém dolním rohu. Zobrazí se dialogové okno **Otevřít soubor**, ve kterém můžete vyhledat a vybrat fotografii. Ta se potom v tomto okně zobrazí.

![Snímek obrazovky ukazující nezměněný obrázek tváří](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>Nahrání obrázku pro rozpoznávání tváří

Nejjednodušším způsobem rozpoznávání tváří je volání metody `FaceClient.Face.DetectWithStreamAsync`, která zabalí metodu [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozhraní API pro nahrání místního obrázku.

Následující metodu vložte za metodu `FacePhoto_MouseMove` ve třídě `MainWindow`:

Vytvoří se seznam atributů tváří k analýze a nahraný soubor obrázku se načte do `Stream`. Oboje se předá volání `DetectWithStreamAsync`.

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>Zakreslení obdélníků kolem jednotlivých tváří

Přidejte kód pro zakreslení obdélníku kolem každé rozpoznané tváře na obrázku.

V *MainWindow.xaml.cs* přidejte modifikátor `async` do metody `BrowseButton_Click`.

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

Na konec metody `BrowseButton_Click` za řádek `FacePhoto.Source = bitmapSource` vložte následující kód.

Seznam rozpoznaných tváří se naplní voláním `UploadAndDetectFaces`. Potom se kolem každé tváře nakreslí obdélník a upravený obrázek se zobrazí v hlavním okně.

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>Popis tváří v obrázku

Následující metodu připojte za metodu `UploadAndDetectFaces` do třídy `MainWindow`.

Tato metoda převede atributy tváře na řetězec, který příslušnou tvář popisuje. Tento řetězec se zobrazí, když ukazatel myši najede na obdélník tváře.

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>Zobrazení popisu tváře

Nahraďte metodu `FacePhoto_MouseMove` následujícím kódem.

Tato obslužná rutina události zobrazí řetězec popisu tváře, když ukazatel myši najede na obdélník tváře.

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci a vyhledejte obrázek, který obsahuje nějakou tvář. Počkejte několik sekund, než služba Rozpoznávání tváře zareaguje. Potom se na obrázku kolem tváří zobrazí červené obdélníky. Pokud nad obdélník tváře nastavíte ukazatel myši, ve stavovém pruhu se zobrazí popis této tváře.

![Snímek obrazovky zobrazující rozpoznané tváře orámované obdélníky](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>Souhrn

V tomto kurzu jste se naučili základní postup použití klientské knihovny služby Rozpoznávání tváře a vytvořili aplikaci pro zobrazení a orámování tváří v obrázku.

## <a name="next-steps"></a>Další kroky

Přečtěte si víc o detekci a používání význačných rysů tváří.

> [!div class="nextstepaction"]
> [Postup při rozpoznávání tváří v obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
