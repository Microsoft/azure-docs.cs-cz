---
title: Smooth Streaming kurzu pro aplikace pro Windows Store | Microsoft Docs
description: Naučte se, jak pomocí Azure Media Services vytvořit aplikaci pro Windows Store v C# s použitím ovládacího prvku XML MediaElement k přehrávání hladkého obsahu streamu.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 20c7068fa0cb56699fdbc6d75b279abadd583832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017099"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Postup sestavení Smooth Streaming aplikace pro Windows Store

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Sada SDK Smooth Streaming klienta pro systém Windows 8 umožňuje vývojářům vytvářet aplikace pro Windows Store, které mohou hrát a živě Smooth Streaming obsah. Kromě základního přehrávání Smooth Streaming obsahu poskytuje sada SDK také bohatě funkční funkce, jako je Microsoft PlayReady Protection, omezení na úrovni kvality, Live DVR, přepínání zvukových streamů, naslouchání aktualizacím stavu (například změny úrovně kvality) a chybové události atd. Další informace o podporovaných funkcích najdete v [poznámkách k verzi](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Další informace najdete v tématu [rozhraní přehrávače pro systém Windows 8](https://playerframework.codeplex.com/). 

Tento kurz obsahuje čtyři lekce:

1. Vytvoření základní aplikace Smooth Streaming Storu
2. Přidání posuvníku pro řízení průběhu multimédií
3. Vybrat Smooth Streaming datové proudy
4. Výběr Smooth Streamingch stop

## <a name="prerequisites"></a>Požadavky
> [!NOTE]
> Projekty Windows Store verze 8,1 a starší nejsou podporovány v aplikaci Visual Studio 2017.  Další informace najdete v tématu [Cílení na platformy a kompatibilita v sadě Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Systém Windows 8 32-bit nebo 64-bit.
* Verze sady Visual Studio 2012 až 2015.
* [Sada Microsoft Smooth Streaming Client SDK pro systém Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Dokončené řešení pro každou lekci si můžete stáhnout z ukázek kódu pro vývojáře MSDN (Galerie kódu): 

* [Lekce 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) – jednoduchá Smooth Streaming Windows 8 Media Player 
* [Lekce 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) – jednoduchá Smooth Streaming systému Windows 8 Media Player s ovládacím prvkem posuvníku, 
* [Lekce 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) – Smooth Streaming Media Player Windows 8 s výběrem streamu,  
* [Lekce 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907)  – Smooth Streaming Windows 8 Media Player se sledováním výběru.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lekce 1: Vytvoření základní aplikace Smooth Streaming Storu

V této lekci vytvoříte aplikaci pro Windows Store s ovládacím prvkem MediaElement, abyste mohli přehrávat plynulé streamování obsahu.  Běžící aplikace vypadá takto:

![Příklad Smooth Streaming aplikace pro Windows Store][PlayerApplication]

Další informace o vývoji aplikací pro Windows Store najdete v tématu [vývoj skvělých aplikací pro Windows 8](https://developer.microsoft.com/en-us/windows/). Tato lekce obsahuje následující postupy:

1. Vytvořit projekt pro Windows Store
2. Návrh uživatelského rozhraní (XAML)
3. Upravit soubor kódu na pozadí
4. Kompilace a testování aplikace

### <a name="to-create-a-windows-store-project"></a>Vytvoření projektu Windows Store

1. Spustit Visual Studio; jsou podporovány verze 2012 až 2015.
1. V nabídce **soubor** klikněte na příkaz **Nový** a potom klikněte na **projekt**.
1. V dialogovém okně Nový projekt zadejte nebo vyberte následující hodnoty:

    | Name | Hodnota |
    | --- | --- |
    | Skupina šablon |Nainstalované/šablony/Visual C#/Windows Store |
    | Template (Šablona) |Prázdná aplikace (XAML) |
    | Name |SSPlayer |
    | Umístění |C:\SSTutorials |
    | Název řešení |SSPlayer |
    | Vytvořit adresář pro řešení |Vyberte |

1. Klikněte na **OK**.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Přidání odkazu na sadu SDK pro Smooth Streaming klienta

1. Z Průzkumník řešení klikněte pravým tlačítkem na **SSPlayer** a pak klikněte na **Přidat odkaz**.
1. Zadejte nebo vyberte tyto hodnoty:

    | Name | Hodnota |
    | --- | --- |
    | Referenční skupina |Windows/rozšíření |
    | Reference |Vyberte sadu Microsoft Smooth Streaming Client SDK pro Windows 8 a balíček Microsoft Visual C++ Runtime |

1. Klikněte na **OK**. 

Po přidání odkazů musíte vybrat cílovou platformu (x64 nebo x86). Přidání odkazů nebude fungovat pro žádnou konfiguraci platformy CPU.  V Průzkumníku řešení uvidíte u těchto přidaných odkazů žlutou výstražnou značku.

### <a name="to-design-the-player-user-interface"></a>Postup při návrhu uživatelského rozhraní přehrávače

1. V Průzkumník řešení poklikejte na **MainPage. XAML** a otevře se v zobrazení Návrh.
2. Vyhledejte **&lt; mřížku &gt;** a **&lt; /Grid &gt;** označí soubor XAML a vložte následující kód mezi dvě značky:

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   Ovládací prvek MediaElement se používá k přehrání média. Ovládací prvek posuvník s názvem sliderProgress bude použit v další lekci k řízení průběhu multimédií.
3. Uložte soubor stisknutím kláves **CTRL + S** .

Ovládací prvek MediaElement nepodporuje dopředný obsah Smooth Streaming. Chcete-li povolit podporu Smooth Streaming, je nutné zaregistrovat obslužnou rutinu Smooth Streaming bajtového datového proudu podle přípony názvu souboru a typu MIME.  Chcete-li se zaregistrovat, použijte metodu MediaExtensionManager. RegisterByteStreamHandler oboru názvů Windows. Media.

V tomto souboru XAML jsou k ovládacím prvkům přidruženy některé obslužné rutiny událostí.  Je nutné definovat tyto obslužné rutiny událostí.

### <a name="to-modify-the-code-behind-file"></a>Úprava souboru kódu na pozadí

1. Z Průzkumník řešení klikněte pravým tlačítkem na **MainPage. XAML** a pak klikněte na **Zobrazit kód**.
2. V horní části souboru přidejte následující příkaz using:

    ```csharp
        using Windows.Media;
    ```

3. Na začátku třídy **MainPage** přidejte následující datový člen:

    ```csharp
        private MediaExtensionManager extensions = new MediaExtensionManager();
    ```

4. Na konci konstruktoru **MainPage** přidejte následující dva řádky:

    ```csharp
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
    ```

5. Na konci třídy **MainPage** vložte následující kód:
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
   Zde je definována obslužná rutina události sliderProgress_PointerPressed.  K dispozici je více práce, kterou je možné využít k tomu, aby fungovalo v další lekci tohoto kurzu.
6. Uložte soubor stisknutím kláves **CTRL + S** .

Hotový soubor kódu na pozadí bude vypadat takto:

![CodeView v aplikaci Visual Studio Smooth Streaming aplikace pro Windows Store][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>Kompilace a testování aplikace

1. V nabídce **sestavení** klikněte na příkaz **Configuration Manager**.
2. Změňte **platformu aktivního řešení** tak, aby odpovídala vaší vývojové platformě.
3. Pro zkompilování projektu stiskněte klávesu **F6** . 
4. Stisknutím klávesy **F5** spusťte aplikaci.
5. V horní části aplikace můžete buď použít výchozí adresu URL Smooth Streaming, nebo zadat jinou adresu. 
6. Klikněte na **nastavit zdroj**. Vzhledem k tomu, že je ve výchozím nastavení povoleno **Automatické přehrávání** , médium se automaticky hraje.  Média můžete ovládat pomocí tlačítek **Přehrát**, **pozastavit** a **zastavit** .  Hlasitost média lze ovládat pomocí svislého posuvníku.  Vodorovný posuvník pro řízení průběhu multimédií ale ještě není úplně implementovaný. 

Dokončili jste lesson1.  V této lekci použijete ovládací prvek MediaElement k přehrávání Smooth Streaming obsahu.  V další lekci přidáte posuvník pro řízení průběhu Smooth Streaming obsahu.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lekce 2: Přidání posuvníku pro řízení průběhu multimédií

V lekci 1 jste vytvořili aplikaci pro Windows Store s ovládacím prvkem MediaElement XAML pro přehrávání Smooth Streaming mediálního obsahu.  Obsahuje některé základní funkce multimédií, jako je spuštění, zastavení a pozastavení.  V této lekci přidáte ovládací prvek posuvníku do aplikace.

V tomto kurzu použijeme časovač k aktualizaci polohy posuvníku na základě aktuální pozice ovládacího prvku MediaElement.  V případě živého obsahu je potřeba také aktualizovat posuvník čas zahájení a ukončení.  To se dá lépe zpracovat v události Adaptivní aktualizace zdroje.

Zdroje médií jsou objekty, které generují data médií.  Překladač zdroje přebírá adresu URL nebo bajtový proud a vytvoří příslušný zdroj média pro daný obsah.  Překladač zdroje je standardní způsob, jak aplikace vytvářet zdroje médií. 

Tato lekce obsahuje následující postupy:

1. Registrace obslužné rutiny Smooth Streaming 
2. Přidat obslužné rutiny událostí úrovně správce adaptivního zdroje
3. Přidat obslužné rutiny událostí adaptivní zdrojové úrovně
4. Přidat obslužné rutiny událostí MediaElement
5. Přidat kód související s posuvníkem
6. Kompilace a testování aplikace

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>Registrace obslužné rutiny bajtového datového proudu Smooth Streaming a předání PropertySet –u

1. V Průzkumník řešení klikněte pravým tlačítkem na **MainPage. XAML** a pak klikněte na **Zobrazit kód**.
2. Na začátku souboru přidejte následující příkaz using:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. Na začátku třídy MainPage přidejte následující datové členy:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Uvnitř konstruktoru **MainPage** přidejte následující kód za řádek **this.Inikomponenty tialize ();** a řádky registračního kódu napsané v předchozí lekci:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. V konstruktoru **MainPage** upravte dvě metody RegisterByteStreamHandler, abyste přidali parametry z:

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. Uložte soubor stisknutím kláves **CTRL + S** .

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Přidání obslužné rutiny události úrovně správce adaptivního zdroje

1. V Průzkumník řešení klikněte pravým tlačítkem na **MainPage. XAML** a pak klikněte na **Zobrazit kód**.
2. Uvnitř třídy **MainPage** přidejte následující datový člen:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. Na konci třídy **MainPage** přidejte následující obslužnou rutinu události:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. Na konci konstruktoru **MainPage** přidejte následující řádek, kterým se přihlásíte k odběru adaptivní události Open Source:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Uložte soubor stisknutím kláves **CTRL + S** .

### <a name="to-add-adaptive-source-level-event-handlers"></a>Přidání obslužných rutin událostí adaptivní zdrojové úrovně

1. V Průzkumník řešení klikněte pravým tlačítkem na **MainPage. XAML** a pak klikněte na **Zobrazit kód**.
2. Uvnitř třídy **MainPage** přidejte následující datový člen:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. Na konci třídy **MainPage** přidejte následující obslužné rutiny událostí:

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. Na konci metody **AdaptiveSourceOpened mediaElement** přidejte následující kód pro přihlášení k odběru událostí:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Uložte soubor stisknutím kláves **CTRL + S** .

Stejné události jsou také k dispozici na úrovni adaptivního zdrojového správce, které lze použít pro zpracování funkcí společných pro všechny prvky média v aplikaci. Každý AdaptiveSource zahrnuje vlastní události a všechny události AdaptiveSource budou v oblasti AdaptiveSourceManager do sebe.

### <a name="to-add-media-element-event-handlers"></a>Přidání obslužných rutin událostí mediálního prvku

1. V Průzkumník řešení klikněte pravým tlačítkem na **MainPage. XAML** a pak klikněte na **Zobrazit kód**.
2. Na konci třídy **MainPage** přidejte následující obslužné rutiny událostí:

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. Na konci konstruktoru **MainPage** přidejte do událostí do dolního indexu následující kód:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Uložte soubor stisknutím kláves **CTRL + S** .

### <a name="to-add-slider-bar-related-code"></a>Přidání kódu souvisejícího s posuvníkem

1. V Průzkumník řešení klikněte pravým tlačítkem na **MainPage. XAML** a pak klikněte na **Zobrazit kód**.
2. Na začátku souboru přidejte následující příkaz using:

   ```csharp
        using Windows.UI.Core;
   ```
3. Uvnitř třídy **MainPage** přidejte následující datové členy:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. Na konci konstruktoru **MainPage** přidejte následující kód:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. Na konci třídy **MainPage** přidejte následující kód:

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > CoreDispatcher se používá k provádění změn vlákna uživatelského rozhraní z vlákna, které není v uživatelském rozhraní. V případě kritických míst ve vlákně dispečera může vývojář zvolit, že se má použít dispečer poskytovaný prvky uživatelského rozhraní, které mají v úmyslu aktualizovat.  Například:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. Na konci **mediaElement_AdaptiveSourceStatusUpdated** metody přidejte následující kód:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. Na konci metody **MediaOpened** přidejte následující kód:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Uložte soubor stisknutím kláves **CTRL + S** .

### <a name="to-compile-and-test-the-application"></a>Kompilace a testování aplikace

1. Pro zkompilování projektu stiskněte klávesu **F6** . 
2. Stisknutím klávesy **F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí adresu URL Smooth Streaming, nebo zadat jinou adresu. 
4. Klikněte na **nastavit zdroj**. 
5. Otestujte posuvník.

Dokončili jste lekci v lekci 2.  V této lekci jste přidali posuvník do aplikace. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lekce 3: výběr streamů Smooth Streaming
Smooth Streaming může streamovat obsah pomocí zvukového zvuku s více jazyky, které mohou čtenáři vybrat.  V této lekci umožníte uživatelům vybrat streamy. Tato lekce obsahuje následující postupy:

1. Úprava souboru XAML
2. Upravit soubor kódu na pozadí
3. Kompilace a testování aplikace

### <a name="to-modify-the-xaml-file"></a>Úprava souboru XAML

1. Z Průzkumník řešení klikněte pravým tlačítkem myši na **MainPage. XAML** a potom klikněte na tlačítko **Návrhář zobrazení**.
2. Vyhledejte &lt; Grid. RowDefinitions &gt; a upravte RowDefinitions tak, aby vypadaly takto:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Uvnitř &lt; &gt; &lt; značek/Grid mřížky &gt; přidejte následující kód pro definování ovládacího prvku ListBox, aby uživatelé viděli seznam dostupných datových proudů a vybrali streamy:

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. Změny uložte stisknutím **kombinace kláves CTRL + S** .

### <a name="to-modify-the-code-behind-file"></a>Úprava souboru kódu na pozadí

1. Z Průzkumník řešení klikněte pravým tlačítkem na **MainPage. XAML** a pak klikněte na **Zobrazit kód**.
2. V oboru názvů SSPlayer přidejte novou třídu:

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. Na začátku třídy MainPage přidejte následující definice proměnných:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. Uvnitř třídy MainPage přidejte následující oblast:
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. Vyhledejte metodu mediaElement_ManifestReady a na konci funkce přidejte následující kód:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Takže když je manifest MediaElement připravený, kód získá seznam dostupných datových proudů a naplní seznam uživatelského rozhraní seznamem.
6. Uvnitř třídy MainPage Najděte tlačítka uživatelského rozhraní a klikněte na oblast události a přidejte následující definici funkce:
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>Kompilace a testování aplikace

1. Pro zkompilování projektu stiskněte klávesu **F6** . 
2. Stisknutím klávesy **F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí adresu URL Smooth Streaming, nebo zadat jinou adresu. 
4. Klikněte na **nastavit zdroj**. 
5. Výchozí jazyk je audio_eng. Zkuste přepínat mezi audio_eng a audio_es. Pokaždé, když vyberete nový datový proud, musíte kliknout na tlačítko Odeslat.

Dokončili jste lekci 3.  V této lekci přidáte funkci pro výběr datových proudů.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lekce 4: výběr sledování Smooth Streaming

Smooth Streaming prezentace může obsahovat více videosouborů zakódovaných s různými úrovněmi kvality (přenosové rychlosti) a rozlišení. V této lekci umožníte uživatelům vybrat sledovat. Tato lekce obsahuje následující postupy:

1. Úprava souboru XAML
2. Upravit soubor kódu na pozadí
3. Kompilace a testování aplikace

### <a name="to-modify-the-xaml-file"></a>Úprava souboru XAML

1. Z Průzkumník řešení klikněte pravým tlačítkem myši na **MainPage. XAML** a potom klikněte na tlačítko **Návrhář zobrazení**.
2. Vyhledejte &lt; značku Grid &gt; s názvem **gridStreamAndBitrateSelection**, na konci značky přidejte následující kód:
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. Změny uložíte stisknutím **kombinace kláves CTRL + S** .

### <a name="to-modify-the-code-behind-file"></a>Úprava souboru kódu na pozadí

1. Z Průzkumník řešení klikněte pravým tlačítkem na **MainPage. XAML** a pak klikněte na **Zobrazit kód**.
2. V oboru názvů SSPlayer přidejte novou třídu:
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. Na začátku třídy MainPage přidejte následující definice proměnných:
   ```csharp
        private List<Track> availableTracks;
   ```
4. Uvnitř třídy MainPage přidejte následující oblast:
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. Vyhledejte metodu mediaElement_ManifestReady a na konci funkce přidejte následující kód:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. Uvnitř třídy MainPage Najděte tlačítka uživatelského rozhraní a klikněte na oblast události a přidejte následující definici funkce:
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>Kompilace a testování aplikace

1. Pro zkompilování projektu stiskněte klávesu **F6** . 
2. Stisknutím klávesy **F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí adresu URL Smooth Streaming, nebo zadat jinou adresu. 
4. Klikněte na **nastavit zdroj**. 
5. Ve výchozím nastavení jsou vybrané všechny stopy streamu videa. Pokud chcete experimentovat změny přenosové rychlosti, můžete vybrat nejnižší dostupnou přenosovou rychlost a pak vybrat nejvyšší dostupnou přenosovou rychlost. Po každé změně musíte kliknout na Odeslat.  Můžete vidět změny kvality videa.

Dokončili jste lekci 4.  V této lekci přidáte funkci pro výběr možnosti sledovat.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Další zdroje informací:
* [Postup vytvoření Smooth Streaming aplikace s Windows 8 JavaScript s pokročilými funkcemi](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Technický přehled Smooth Streaming](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
