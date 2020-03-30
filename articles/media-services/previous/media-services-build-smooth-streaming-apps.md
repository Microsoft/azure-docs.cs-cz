---
title: Výuka aplikace pro Windows Store pro hladké streamování | Dokumenty společnosti Microsoft
description: Přečtěte si, jak pomocí Služby Azure Media Services vytvořit aplikaci Pro Windows Store v Jazyce C# s ovládacím prvkem XML MediaElement pro přehrávání obsahu Smooth Stream.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 9ff961638aa170948d51793a21e86d18dd7e1d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016791"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Jak vytvořit aplikaci pro Windows Store s plynulým streamováním  

Sada SDK klienta plynulého streamování pro Windows 8 umožňuje vývojářům vytvářet aplikace pro Windows Store, které mohou přehrávat obsah plynulého streamování na vyžádání a živě vysílat plynulé streamování. Kromě základního přehrávání obsahu Plynulého streamování poskytuje sada SDK také bohaté funkce, jako je ochrana Microsoft PlayReady, omezení úrovně kvality, živé přepínání DVR, přepínání zvukových proudů, naslouchání aktualizacím stavu (jako jsou změny úrovně kvality) a chybové události a tak dále. Další informace o podporovaných funkcích naleznete v [poznámkách k verzi](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Další informace naleznete v [tématu Player Framework pro Windows 8](https://playerframework.codeplex.com/). 

Tento výukový program obsahuje čtyři lekce:

1. Vytvoření základní aplikace úložiště hladkých datových proudů
2. Přidání posuvníku k ovládání průběhu médií
3. Vybrat plynulé streamovací streamy
4. Vybrat plynulé streamovací skladby

## <a name="prerequisites"></a>Požadavky
> [!NOTE]
> Projekty Windows Storu verze 8.1 a starší nejsou ve Visual Studiu 2017 podporované.  Další informace najdete v tématu [Cílení na platformy a kompatibilita v sadě Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8 32bitový nebo 64bitový.
* Visual Studio verze 2012 až 2015.
* [Sada Microsoft Smooth Streaming Client SDK pro Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Dokončené řešení pro každou lekci lze stáhnout z ukázky kódu vývojáře MSDN (Galerie kódu): 

* [Lekce 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - Jednoduchý Windows 8 Hladké Streaming Media Player, 
* [Lekce 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - Jednoduchý Windows 8 Smooth Streaming Media Player s jezdcem Bar Ovládání, 
* [Lekce 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - Windows 8 Smooth Streaming Media Player s výběrem streamu,  
* [Lekce 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - Windows 8 Smooth Streaming Media Player s výběrem stop.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lekce 1: Vytvoření základní aplikace úložiště hladkých datových proudů

V této lekci vytvoříte aplikaci pro Windows Store s ovládacím prvkem MediaElement pro přehrávání obsahu Smooth Stream.  Spuštěná aplikace vypadá takto:

![Příklad aplikace pro windows store hladké streamování][PlayerApplication]

Další informace o vývoji aplikací pro Windows Store najdete v [tématu Vývoj skvělých aplikací pro Windows 8](https://msdn.microsoft.com/windows/apps/br229512.aspx). Tato lekce obsahuje následující postupy:

1. Vytvoření projektu pro Windows Store
2. Návrh uživatelského rozhraní (XAML)
3. Změna kódu za souborem
4. Kompilace a testování aplikace

### <a name="to-create-a-windows-store-project"></a>Vytvoření projektu pro Windows Store

1. Spuštění sady Visual Studio; 2012 až 2015.
1. V nabídce **SOUBOR** klepněte na tlačítko **Nový**a potom klepněte na **položku Project**.
1. V dialogovém okně Nový projekt zadejte nebo vyberte následující hodnoty:

    | Name (Název) | Hodnota |
    | --- | --- |
    | Skupina šablon |Nainstalované/Šablony/Visual C#/Windows Store |
    | Šablona |Prázdná aplikace (XAML) |
    | Name (Název) |SSPlayer |
    | Umístění |C:\SSTutorials |
    | Název řešení |SSPlayer |
    | Vytvořit adresář pro řešení |(vybráno) |

1. Klikněte na tlačítko **OK**.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Přidání odkazu na sadu SDK klienta pro hladké streamování

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na **položku SSPlayer**a potom klepněte na příkaz **Přidat odkaz**.
1. Zadejte nebo vyberte tyto hodnoty:

    | Name (Název) | Hodnota |
    | --- | --- |
    | Referenční skupina |Windows/Rozšíření |
    | Odkaz |Vyberte sadu Microsoft Smooth Streaming Client SDK pro Windows 8 a balíček runtime Microsoft Visual C++ |

1. Klikněte na tlačítko **OK**. 

Po přidání odkazů je nutné vybrat cílovou platformu (x64 nebo x86), přidání odkazů nebude fungovat pro konfiguraci platformy procesoru.  V průzkumníku řešení uvidíte žlutou varovnou značku pro tyto přidané odkazy.

### <a name="to-design-the-player-user-interface"></a>Návrh uživatelského rozhraní přehrávače

1. V Průzkumníku řešení poklepejte na **soubor MainPage.xaml** a otevřete jej v návrhovém zobrazení.
2. Vyhledejte ** &lt;&gt; značku Mřížka** a ** &lt;/Grid&gt; ** soubor XAML a mezi tyto dvě značky vložte následující kód:

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
   Ovládací prvek MediaElement se používá k přehrávání médií. Ovládací prvek posuvníku s názvem posuvníkPrůběh bude použit v další lekci k řízení průběhu média.
3. Soubor uložte stisknutím **kláves CTRL+S.**

Ovládací prvek MediaElement nepodporuje obsah plynulého streamování out-of-box. Chcete-li povolit podporu plynulého streamování, musíte zaregistrovat obslužnou rutinu datových proudů datových proudů plynulého streamování podle přípony názvu souboru a typu MIME.  Chcete-li se zaregistrovat, použijte metodu MediaExtensionManager.RegisterByteStreamHandler oboru názvů Windows.Media.

V tomto souboru XAML jsou některé obslužné rutiny událostí přidruženy k ovládacím prvkům.  Tyto obslužné rutiny událostí je nutné definovat.

### <a name="to-modify-the-code-behind-file"></a>Změna kódu za souborem

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na **soubor MainPage.xaml**a potom klepněte na příkaz **Zobrazit kód**.
2. V horní části souboru přidejte následující příkaz using:
   
        using Windows.Media;
3. Na začátku **mainpage** třídy přidejte následující datový člen:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. Na konec konstruktoru **MainPage** přidejte následující dva řádky:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
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
   Zde je definována obslužná rutina události sliderProgress_PointerPressed.  Existuje více prací, které je třeba udělat, aby to fungovalo, což bude zahrnuto v další lekci tohoto kurzu.
6. Soubor uložte stisknutím **kláves CTRL+S.**

Dokončený kód za souborem musí vypadat takto:

![Codeview v visual studiu aplikace pro Windows Store pro plynulé streamování][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>Kompilace a testování aplikace

1. V nabídce **BUILD** klepněte na **položku Configuration Manager**.
2. Změňte **aktivní platformu řešení** tak, aby odpovídala vaší vývojové platformě.
3. Stisknutím **klávesy F6** zkompilujte projekt. 
4. Stisknutím **klávesy F5** spusťte aplikaci.
5. V horní části aplikace můžete buď použít výchozí adresu URL hladkého streamování, nebo zadat jinou. 
6. Klepněte na **tlačítko Nastavit zdroj**. Protože je ve výchozím nastavení povoleno **automatické přehrávání,** média se automaticky přehrávají.  Média můžete ovládat pomocí tlačítek **Přehrát**, **Pozastavit** a **Zastavit.**  Hlasitost média můžete ovládat pomocí svislého posuvníku.  Horizontální jezdec pro kontrolu průběhu médií však ještě není plně implementován. 

Dokončili jste lekci1.  V této lekci použijete ovládací prvek MediaElement k přehrávání obsahu plynulého streamování.  V další lekci přidáte posuvník pro řízení průběhu obsahu plynulého streamování.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lekce 2: Přidání posuvníku pro řízení průběhu médií

V lekci 1 jste vytvořili aplikaci pro Windows Store s ovládacím prvkem MediaElement XAML pro přehrávání plynulého mediálního obsahu streamování.  Přichází některé základní mediální funkce, jako je start, stop a pauza.  V této lekci přidáte do aplikace ovládací prvek posuvníku.

V tomto kurzu použijeme časovač k aktualizaci polohy posuvníku na základě aktuální pozice ovládacího prvku MediaElement.  V případě živého obsahu je také třeba aktualizovat čas zahájení a ukončení posuvníku.  To lze lépe zpracovat v události adaptivní aktualizace zdrojové aktualizace.

Zdroje médií jsou objekty, které generují mediální data.  Zdrojový překladač převezme datový proud ADRESY URL nebo bajtů a vytvoří pro daný obsah příslušný zdroj médií.  Zdrojový překladač je standardní způsob, jak aplikace vytvářet zdroje médií. 

Tato lekce obsahuje následující postupy:

1. Registrace obslužné rutiny hladkého streamování 
2. Přidání obslužných rutin událostí na úrovni adaptivního správce zdrojů
3. Přidání obslužných rutin událostí adaptivní zdrojové úrovně
4. Přidání obslužných rutin událostí MediaElement
5. Přidání kódu souvisejícího s posuvníkem
6. Kompilace a testování aplikace

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>Chcete-li zaregistrovat obslužnou rutinu datového proudu plynulého streamování a předat sadu vlastností

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na **soubor MainPage.xaml**a potom klikněte na **příkaz Zobrazit kód**.
2. Na začátku souboru přidejte následující příkaz using:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. Na začátku třídy MainPage přidejte následující datové členy:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Uvnitř **konstruktoru MainPage,** přidejte následující kód za **toto. Inicializovat components();** řádku a řádky registračního kódu napsané v předchozí lekci:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. Uvnitř konstruktoru **MainPage** upravte dvě metody RegisterByteStreamHandler a přidejte parametry forth:

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
6. Soubor uložte stisknutím **kláves CTRL+S.**

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Přidání obslužné rutiny události adaptivního správce zdrojů

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na **soubor MainPage.xaml**a potom klikněte na **příkaz Zobrazit kód**.
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
4. Na konci konstruktoru **MainPage** přidejte následující řádek, abyste se přihlásili k odběru adaptivní source open event:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Soubor uložte stisknutím **kláves CTRL+S.**

### <a name="to-add-adaptive-source-level-event-handlers"></a>Přidání obslužných rutin událostí adaptivní úrovně zdrojového zdroje

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na **soubor MainPage.xaml**a potom klikněte na **příkaz Zobrazit kód**.
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
4. Na konci **metody mediaElement AdaptiveSourceOpened** přidejte následující kód pro přihlášení k odběru událostí:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Soubor uložte stisknutím **kláves CTRL+S.**

Stejné události jsou k dispozici také na úrovni adaptivního zdrojového jeslí, které lze použít pro zpracování funkcí společných pro všechny mediální prvky v aplikaci. Každý AdaptiveSource obsahuje své vlastní události a všechny AdaptiveSource události budou kaskádovitě v rámci AdaptiveSourceManager.

### <a name="to-add-media-element-event-handlers"></a>Přidání obslužných rutin událostí Media Element

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na **soubor MainPage.xaml**a potom klikněte na **příkaz Zobrazit kód**.
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
3. Na konci konstruktoru **MainPage** přidejte k událostem do dolního indexu následující kód:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Soubor uložte stisknutím **kláves CTRL+S.**

### <a name="to-add-slider-bar-related-code"></a>Přidání kódu souvisejícího s posuvníkem

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na **soubor MainPage.xaml**a potom klikněte na **příkaz Zobrazit kód**.
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
5. Na konci **mainpage** třídy přidejte následující kód:

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
   > CoreDispatcher se používá k provádění změn vlákna uživatelského rozhraní z vlákna mimo uživatelské rozhraní. V případě problémového místa ve vlákně dispečera může vývojář použít dispečera poskytovaného prvkem uživatelského rozhraní, který hodlají aktualizovat.  Například:

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
7. Na konci **MediaOpened** metoda, přidejte následující kód:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Soubor uložte stisknutím **kláves CTRL+S.**

### <a name="to-compile-and-test-the-application"></a>Kompilace a testování aplikace

1. Stisknutím **klávesy F6** zkompilujte projekt. 
2. Stisknutím **klávesy F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí adresu URL hladkého streamování, nebo zadat jinou. 
4. Klepněte na **tlačítko Nastavit zdroj**. 
5. Otestujte posuvník.

Dokončili jste lekci 2.  V této lekci jste do aplikace přidali posuvník. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lekce 3: Vyberte plynulé streamovací proudy
Plynulé streamování je schopno streamovat obsah pomocí vícejazykových zvukových stop, které jsou volitelné diváky.  V této lekci umožníte divákům vybrat datové proudy. Tato lekce obsahuje následující postupy:

1. Úprava souboru XAML
2. Změna kódu za souborem
3. Kompilace a testování aplikace

### <a name="to-modify-the-xaml-file"></a>Úprava souboru XAML

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na **soubor MainPage.xaml**a potom klepněte na příkaz **Zobrazit Návrháře**.
2. Vyhledejte &lt;soubor Grid.RowDefinitions&gt;a upravte definice řádků tak, aby vypadaly takto:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Uvnitř &lt;značek&gt;&lt;Grid&gt; /Grid přidejte následující kód, který definuje ovládací prvek seznamu, aby uživatelé mohli zobrazit seznam dostupných datových proudů a vybrat datové proudy:

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
4. Stisknutím **kláves CTRL+S** uložte změny.

### <a name="to-modify-the-code-behind-file"></a>Změna kódu za souborem

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na **soubor MainPage.xaml**a potom klepněte na příkaz **Zobrazit kód**.
2. Uvnitř oboru názvů SSPlayer přidejte novou třídu:

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
5. Vyhledejte metodu mediaElement_ManifestReady a na konec funkce připotážte následující kód:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Takže když MediaElement manifest je připraven, kód získá seznam dostupných datových proudů a naplní seznam ui se seznamem.
6. Uvnitř třídy MainPage vyhledejte oblast událostí s tlačítky ui a přidejte následující definici funkce:
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

1. Stisknutím **klávesy F6** zkompilujte projekt. 
2. Stisknutím **klávesy F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí adresu URL hladkého streamování, nebo zadat jinou. 
4. Klepněte na **tlačítko Nastavit zdroj**. 
5. Výchozí jazyk je audio_eng. Zkuste přepínat mezi audio_eng a audio_es. Pokaždé, když vyberete nový datový proud, musíte kliknout na tlačítko Odeslat.

Dokončili jste lekci 3.  V této lekci přidáte funkce pro výběr datových proudů.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lekce 4: Vyberte skladby plynulého streamování

Prezentace Plynulého streamování může obsahovat více video souborů kódovaných s různými úrovněmi kvality (přenosové rychlosti) a rozlišeními. V této lekci umožníte uživatelům vybrat skladby. Tato lekce obsahuje následující postupy:

1. Úprava souboru XAML
2. Změna kódu za souborem
3. Kompilace a testování aplikace

### <a name="to-modify-the-xaml-file"></a>Úprava souboru XAML

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na **soubor MainPage.xaml**a potom klepněte na příkaz **Zobrazit Návrháře**.
2. Vyhledejte &lt;&gt; značku Grid s názvem **gridStreamAndBitrateSelection**, připojujte na konec značky následující kód:
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
3. Stisknutím **kláves CTRL+S** uložte změny.

### <a name="to-modify-the-code-behind-file"></a>Změna kódu za souborem

1. V Průzkumníku řešení klepněte pravým tlačítkem myši na **soubor MainPage.xaml**a potom klepněte na příkaz **Zobrazit kód**.
2. Uvnitř oboru názvů SSPlayer přidejte novou třídu:
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
5. Vyhledejte metodu mediaElement_ManifestReady a na konec funkce připotážte následující kód:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. Uvnitř třídy MainPage vyhledejte oblast událostí s tlačítky ui a přidejte následující definici funkce:
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

1. Stisknutím **klávesy F6** zkompilujte projekt. 
2. Stisknutím **klávesy F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí adresu URL hladkého streamování, nebo zadat jinou. 
4. Klepněte na **tlačítko Nastavit zdroj**. 
5. Ve výchozím nastavení jsou vybrány všechny stopy datového proudu videa. Chcete-li experimentovat se změnami přenosové rychlosti, můžete vybrat nejnižší dostupnou přenosovou rychlost a pak vybrat nejvyšší dostupnou přenosovou rychlost. Po každé změně musíte klepnout na tlačítko Odeslat.  Můžete vidět změny kvality videa.

Dokončili jste lekci 4.  V této lekci přidáte funkce pro výběr stop.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Další zdroje:
* [Jak vytvořit smooth streaming Windows 8 JavaScript aplikace s pokročilými funkcemi](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Technický přehled pro plynulé streamování](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

