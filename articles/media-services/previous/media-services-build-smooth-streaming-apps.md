---
title: Smooth Streaming kurz aplikací Windows Store | Dokumentace Microsoftu
description: Další informace o použití Azure Media Services k vytvoření C# aplikace Windows Store pomocí ovládacího prvku XML MediaElement k přehrávání obsahu Smooth Stream.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: juliako
ms.openlocfilehash: 9558a924ee151f47033178b0762a39e2d1e1f538
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828311"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Jak vytvořit využívající Smooth Streaming aplikace Windows Store

Technologie Smooth Streaming klienta SDK pro Windows 8 umožňuje vývojářům vytvářet aplikace pro Windows Store, které můžete přehrát na vyžádání i živé vysílání funkce Smooth Streaming obsah. Kromě základních přehrávání obsah Smooth Streaming, sada SDK poskytuje také bohaté funkce, jako je ochrana Microsoft PlayReady, omezení úrovně kvality Live DVR, zvukový datový proud přepínání, poslouchání aktualizace stavu (jako jsou například změny úrovně kvality) a chybové události a tak dále. Další informace o podporovaných funkcích najdete v tématu [poznámky k verzi](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Další informace najdete v tématu [Player Framework pro systém Windows 8](http://playerframework.codeplex.com/). 

Tento kurz obsahuje čtyři lekce:

1. Vytvoření základní technologie Smooth Streaming Store aplikaci
2. Přidání posuvníku pro ovládací prvek průběh média
3. Vyberte datové proudy technologie Smooth Streaming
4. Vyberte sleduje technologie Smooth Streaming

## <a name="prerequisites"></a>Požadavky
> [!NOTE]
> Verze projekty Windows Store 8.1 a starší se nepodporují v sadě Visual Studio 2017.  Další informace najdete v tématu [Cílení na platformy a kompatibilita v sadě Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8, 32bitový nebo 64bitové.
* Verzí sady Visual Studio 2012 do 2015.
* [Microsoft Smooth Streaming Klientská sada SDK pro Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Dokončené řešení pro každá lekce si můžete stáhnout z ukázky kódu vývojáře MSDN (kód galerie): 

* [Lekce 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) – jednoduchý systém Windows 8 Smooth Streaming Media Player 
* [Lekce 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) – jednoduchý systém Windows 8 Smooth Streaming Media Player pruh posuvník ovládacího prvku 
* [Lekce 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) – Windows 8 Smooth Streaming Media Player se výběrem Stream  
* [Lekce 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) – Windows 8 Smooth Streaming Media Player se výběrem sledování.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lesson 1: Vytvoření základní technologie Smooth Streaming Store aplikaci

V této lekci vytvoříte aplikace Windows Store pomocí ovládacího prvku MediaElement Stream hladké přehrávání obsahu.  Spuštěné aplikace vypadá takto:

![Aplikace využívající Smooth Streaming Windows Store – příklad][PlayerApplication]

Další informace o vývoji aplikací Windows Store naleznete v tématu [vývoj skvělé aplikace pro Windows 8](https://msdn.microsoft.com/windows/apps/br229512.aspx). V této lekci obsahuje následující postupy:

1. Vytvoření projektu aplikace Windows Store
2. Návrh uživatelského rozhraní (XAML)
3. Úprava souboru kódu na pozadí
4. Kompilace a testování aplikace

**Vytvoření projektu Windows Store**

1. Spuštění sady Visual Studio; jsou podporovány verze 2012 do 2015.
2. V nabídce **Soubor** klikněte na tlačítko **Nový** a pak klikněte na tlačítko **Projekt**.
3. Z tohoto dialogového okna Nový projekt zadejte nebo vyberte následující hodnoty:

| Název | Hodnota |
| --- | --- |
| Šablony skupiny |Nainstalované, šablony/Visual C#Windows Store |
| Šablona |Prázdná aplikace (XAML) |
| Název |SSPlayer |
| Umístění |C:\SSTutorials |
| Název řešení |SSPlayer |
| Vytvořit adresář pro řešení |(vybráno) |

1. Klikněte na **OK**.

**Chcete-li přidat odkaz na technologie Smooth Streaming klientské sady SDK**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **SSPlayer**a potom klikněte na tlačítko **přidat odkaz**.
2. Zadejte nebo vyberte tyto hodnoty:

| Název | Hodnota |
| --- | --- |
| Referenční skupiny |Windows/rozšíření |
| Referenční informace |Vyberte Microsoft Smooth Streaming Klientská sada SDK pro Windows 8 a Microsoft Visual C++ Runtime Package |

1. Klikněte na **OK**. 

Po přidání odkazy, musíte vybrat cílovou platformu (x64 nebo x86), přidávání odkazů nebude fungovat pro konfiguraci platformy tak libovolný procesor.  V Průzkumníku řešení uvidíte žlutou upozornění značky pro tyto přidat odkazy.

**K návrhu uživatelského rozhraní player**

1. V Průzkumníku řešení klikněte dvakrát klikněte na **MainPage.xaml** ho otevřete v zobrazení Návrh.
2. Vyhledejte **&lt;mřížky&gt;** a **&lt;/Grid&gt;** značky XAML soubor a vložte následující kód mezi dvěma značkami:
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
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
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
   Řízení elementu MediaElement se používá k přehrávání médií. Posuvník s názvem sliderProgress se použije v další lekci k řízení průběhu média.
3. Stisknutím klávesy **CTRL + S** k uložení souboru.

Řízení elementu MediaElement nepodporuje vysílání funkce Smooth Streaming obsahu out-of-box. Pokud chcete povolit podporu technologie Smooth Streaming, je nutné zaregistrovat obslužnou rutinu Bajtový proud Smooth Streaming příponu názvu souboru a typu MIME.  Pro registraci, použijte metodu MediaExtensionManager.RegisterByteStreamHandler Windows.Media oboru názvů.

V tomto souboru XAML jsou některé obslužné rutiny události související s ovládacími prvky.  Je nutné definovat tyto obslužné rutiny událostí.

**Úprava souboru kódu na pozadí**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **MainPage.xaml**a potom klikněte na tlačítko **zobrazit kód**.
2. Na začátek souboru přidejte následující příkaz using:
   
        using Windows.Media;
3. Na začátku **MainPage** třídy, přidejte následující datový člen:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. Na konci **MainPage** konstruktoru, přidejte následující dva řádky:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. Na konci **MainPage** třídy, vložte následující kód:
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
Obslužná rutina události sliderProgress_PointerPressed je zde definované.  Existují další funguje chcete udělat, abyste získali, jak funguje, kterému se věnujeme v další lekci tohoto kurzu.
6. Stisknutím klávesy **CTRL + S** k uložení souboru.

Dokončené souboru kódu by měl vypadat nějak takto:

![Codeview v aplikaci Visual Studio z technologie Smooth Streaming Windows Store][CodeViewPic]

**Ke kompilaci a testování aplikace**

1. Z **sestavení** nabídky, klikněte na tlačítko **nástroje Configuration Manager**.
2. Změna **platformou aktivního řešení** tak, aby odpovídaly svou vývojovou platformu.
3. Stisknutím klávesy **F6** ke kompilaci projektu. 
4. Stisknutím klávesy **F5** spusťte aplikaci.
5. V horní části aplikace můžete buď použít výchozí nastavení adresy URL technologie Smooth Streaming nebo zadat jiný. 
6. Klikněte na tlačítko **nastavit zdroj**. Protože **automatického přehrávání** je povolená ve výchozím nastavení, se automaticky přehrání média.  Můžete řídit pomocí média **Přehrát**, **pozastavení** a **Zastavit** tlačítka.  Můžete ovládat média svazku pomocí svislý posuvník.  Ale vodorovný posuvník pro řízení průběhu média není dosud plně implementována. 

Dokončili jste lesson1.  V této lekci pomocí ovládacího prvku MediaElement k přehrávání obsahu Smooth Streaming.  V další lekci se přidá posuvníku pro ovládací prvek průběh obsah Smooth Streaming.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lesson 2: Přidání posuvníku pro ovládací prvek průběh média

V lekci 1 vytvořil aplikaci Windows Store pomocí ovládacího prvku MediaElement XAML pro přehrávání mediálního obsahu Smooth Streaming.  Obsahuje některé funkce základní média jako je spuštění, zastavení a pozastavit.  V této lekci přidáte ovládací prvek posuvníku panelu do aplikace.

V tomto kurzu používáme časovače aktualizace na základě aktuální pozice ovládacího prvku MediaElement pozice posuvníku.  Posuvník počáteční a koncový čas také je potřeba aktualizovat v případě živého obsahu.  Toto lépe půjde v případě aktualizace adaptivní zdroje.

Zdroje média jsou objekty, které generují data média.  Překladač zdroj přebírá adresu URL nebo bajt datového proudu a vytvoří zdroj odpovídající médium pro daný obsah.  Překladač zdroje je standardní způsob pro aplikace, které chcete vytvořit zdroje média. 

V této lekci obsahuje následující postupy:

1. Zaregistrujte obslužné rutiny technologie Smooth Streaming 
2. Přidání obslužné rutiny událostí na úrovni správce adaptivní zdroje
3. Přidání obslužných rutin událostí na úrovni adaptivní zdroje
4. Přidání obslužných rutin událostí elementu MediaElement
5. Přidejte posuvník související kód
6. Kompilace a testování aplikace

**Zaregistrujte obslužné rutiny Bajtový proud Smooth Streaming a předat propertyset**

1. V Průzkumníku řešení klikněte pravým tlačítkem myši klikněte na tlačítko **MainPage.xaml**a potom klikněte na tlačítko **zobrazit kód**.
2. Na začátek souboru přidejte následující příkaz using:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. Na začátku třídy MainPage, přidejte následující datové členy:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Uvnitř **MainPage** konstruktoru, přidejte následující kód za **to. Inicializovat Components();**  řádku a registraci kódu řádků napsaného v předchozí lekci:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. Uvnitř **MainPage** konstruktor, upravit dva RegisterByteStreamHandler metody pro přidání uvedené parametry:

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
6. Stisknutím klávesy **CTRL + S** k uložení souboru.

**Chcete-li přidat obslužné rutiny událostí na úrovni správce adaptivní zdroje**

1. V Průzkumníku řešení klikněte pravým tlačítkem myši klikněte na tlačítko **MainPage.xaml**a potom klikněte na tlačítko **zobrazit kód**.
2. Uvnitř **MainPage** třídy, přidejte následující datový člen:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. Na konci **MainPage** třídy, přidejte následující obslužnou rutinu události:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. Na konci **MainPage** konstruktoru, přidejte následující řádek, který přihlásit k odběru události otevřete adaptivní zdroje:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Stisknutím klávesy **CTRL + S** k uložení souboru.

**Chcete-li přidat obslužné rutiny událostí na úrovni adaptivní zdroje**

1. V Průzkumníku řešení klikněte pravým tlačítkem myši klikněte na tlačítko **MainPage.xaml**a potom klikněte na tlačítko **zobrazit kód**.
2. Uvnitř **MainPage** třídy, přidejte následující datový člen:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. Na konci **MainPage** třídy, přidejte následující obslužné rutiny události:

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
4. Na konci **mediaElement AdaptiveSourceOpened** metodu, přidejte následující kód k odběru události:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Stisknutím klávesy **CTRL + S** k uložení souboru.

Stejné události jsou k dispozici na adaptivní správce úroveň zdroje, které lze použít pro funkce, které jsou společné pro všechny prvky médií v aplikaci pro zpracování. Každý AdaptiveSource zahrnuje vlastní události a všechny události AdaptiveSource bude kaskádových pod AdaptiveSourceManager.

**Chcete-li přidat mediální prvek obslužné rutiny událostí**

1. V Průzkumníku řešení klikněte pravým tlačítkem myši klikněte na tlačítko **MainPage.xaml**a potom klikněte na tlačítko **zobrazit kód**.
2. Na konci **MainPage** třídy, přidejte následující obslužné rutiny události:

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
3. Na konci **MainPage** konstruktoru, přidejte následující kód do dolní index pro události:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Stisknutím klávesy **CTRL + S** k uložení souboru.

**Chcete-li přidat posuvníku týkající se kódu**

1. V Průzkumníku řešení klikněte pravým tlačítkem myši klikněte na tlačítko **MainPage.xaml**a potom klikněte na tlačítko **zobrazit kód**.
2. Na začátek souboru přidejte následující příkaz using:

   ```csharp
        using Windows.UI.Core;
   ```
3. Uvnitř **MainPage** třídy, přidejte následující datové členy:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. Na konci **MainPage** konstruktoru, přidejte následující kód:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. Na konci **MainPage** třídy, přidejte následující kód:

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
   > CoreDispatcher slouží ke změnám vlákno uživatelského rozhraní od jiných vlákno uživatelského rozhraní. V případě problémové místo v dispečerského vlákna můžete použít dispečer poskytované učitelského si klade za cíl k aktualizaci prvku uživatelského rozhraní pro vývojáře.  Příklad:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. Na konci **mediaElement_AdaptiveSourceStatusUpdated** metodu, přidejte následující kód:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. Na konci **MediaOpened** metodu, přidejte následující kód:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Stisknutím klávesy **CTRL + S** k uložení souboru.

**Ke kompilaci a testování aplikace**

1. Stisknutím klávesy **F6** ke kompilaci projektu. 
2. Stisknutím klávesy **F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí nastavení adresy URL technologie Smooth Streaming nebo zadat jiný. 
4. Klikněte na tlačítko **nastavit zdroj**. 
5. Otestujte posuvníku.

Dokončili jste Lekce 2.  V této lekci jste přidali ovládací prvek posuvník do aplikace. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lesson 3: Vyberte datové proudy technologie Smooth Streaming
Technologie Smooth Streaming je schopen ke streamování obsahu s více zvukové stopy jazyka, které lze vybrat prohlížeče.  V této lekci vám umožní uživatelům k výběru datové proudy. V této lekci obsahuje následující postupy:

1. Upravte soubor XAML
2. Úprava souboru kódu na pozadí
3. Kompilace a testování aplikace

**K úpravě souboru XAML**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **MainPage.xaml**a potom klikněte na tlačítko **Návrhář zobrazení**.
2. Vyhledejte &lt;Grid.RowDefinitions&gt;a upravit RowDefinitions tak bude vypadat takto:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Uvnitř &lt;mřížky&gt;&lt;/Grid&gt; značky, přidejte následující kód k definování ovládacím prvku, aby uživatelé mohli zobrazit seznam dostupných datových proudů a vybrat datových proudů:

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
4. Stisknutím klávesy **CTRL + S** a uložte změny.

**Úprava souboru kódu na pozadí**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **MainPage.xaml**a potom klikněte na tlačítko **zobrazit kód**.
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
3. Na začátku třídy MainPage, přidejte následující definice proměnné:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. Uvnitř třídy MainPage přidejte následující oblasti:
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
5. Vyhledejte metodu mediaElement_ManifestReady, přidat následující kód na konec funkce:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Proto při MediaElement manifestu je připraven, kód načte seznam dostupných datových proudů a naplní pole se seznamem uživatelského rozhraní se seznamem.
6. Uvnitř třídy MainPage vyhledejte uživatelské rozhraní tlačítka klepněte na tlačítko události oblasti a pak přidejte následující definice funkce:
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
**Ke kompilaci a testování aplikace**

1. Stisknutím klávesy **F6** ke kompilaci projektu. 
2. Stisknutím klávesy **F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí nastavení adresy URL technologie Smooth Streaming nebo zadat jiný. 
4. Klikněte na tlačítko **nastavit zdroj**. 
5. Výchozí jazyk je audio_eng. Došlo k pokusu o přepnutí mezi audio_eng a audio_es. Pokaždé, když vyberete nového datového proudu, musíte kliknout na tlačítko Odeslat.

Dokončili jste Lekce 3.  V této lekci se přidat funkci, která zvolte datové proudy.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lesson 4: Vyberte sleduje technologie Smooth Streaming
Prezentace technologie Smooth Streaming může obsahovat více videosoubory zakódovány různé úrovně kvality (přenosové rychlosti) a jejich řešení. V této lekci vám umožní uživatelům vybrat stopy. V této lekci obsahuje následující postupy:

1. Upravte soubor XAML
2. Úprava souboru kódu na pozadí
3. Kompilace a testování aplikace

**K úpravě souboru XAML**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **MainPage.xaml**a potom klikněte na tlačítko **Návrhář zobrazení**.
2. Vyhledejte &lt;mřížky&gt; značka s názvem **gridStreamAndBitrateSelection**, přidat následující kód na konci značky:
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
3. Stisknutím klávesy **CTRL + S** se uložit změny he

**Úprava souboru kódu na pozadí**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **MainPage.xaml**a potom klikněte na tlačítko **zobrazit kód**.
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
3. Na začátku třídy MainPage, přidejte následující definice proměnné:
   ```csharp
        private List<Track> availableTracks;
   ```
4. Uvnitř třídy MainPage přidejte následující oblasti:
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
5. Vyhledejte metodu mediaElement_ManifestReady, přidat následující kód na konec funkce:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. Uvnitř třídy MainPage vyhledejte uživatelské rozhraní tlačítka klepněte na tlačítko události oblasti a pak přidejte následující definice funkce:
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
**Ke kompilaci a testování aplikace**

1. Stisknutím klávesy **F6** ke kompilaci projektu. 
2. Stisknutím klávesy **F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí nastavení adresy URL technologie Smooth Streaming nebo zadat jiný. 
4. Klikněte na tlačítko **nastavit zdroj**. 
5. Ve výchozím nastavení jsou vybrány všechny stopy datový proud videa. Experimentovat frekvence změny bit, můžete vybrat k dispozici nejnižší přenosové rychlosti a vyberte k dispozici nejvyšší přenosové rychlosti. Klikněte na Odeslat musí po každé změně.  Zobrazí se změny kvalita videa.

Dokončili jste Lekce 4.  V této lekci se přidat funkci, která zvolte stop.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Další materiály:
* [Jak vytvořit aplikaci pomocí pokročilých funkcí, technologie Smooth Streaming JavaScript systému Windows 8](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Technický přehled služby technologie Smooth Streaming](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

