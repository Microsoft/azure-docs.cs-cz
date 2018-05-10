---
title: Funkce Smooth Streaming kurzu aplikace Windows Store | Microsoft Docs
description: Naučte se používat Azure Media Services k vytvoření aplikace Windows Store jazyka C# s ovládacím prvkem XML MediaElement, který datový proud Smooth přehrávání obsahu.
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
ms.openlocfilehash: f55e8aa652d65ea751a77742fa5823b09d9ee87b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Jak vytvořit funkce Smooth Streaming aplikaci pro Windows Store

Technologie Smooth Streaming klienta SDK pro Windows 8 umožňuje vývojářům vytvářet aplikace pro Windows Store, které můžete přehrát na vyžádání i živé vysílání funkce Smooth Streaming obsah. Kromě základních přehrávání technologie Smooth Streaming obsahu, že sada SDK poskytuje také bohaté funkce, jako jsou Microsoft PlayReady ochrany, kvality úrovně omezení, Live formátu DVR, zvuk stream přepínání, naslouchá stav aktualizace (jako jsou například změny úrovně kvality) a chybové události a tak dále. Další informace o podporovaných funkcích najdete v tématu [poznámky k verzi](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Další informace najdete v tématu [Player Framework pro Windows 8](http://playerframework.codeplex.com/). 

Tento kurz obsahuje čtyři lekce:

1. Vytvořte základní technologie Smooth Streaming aplikace úložiště
2. Přidat posuvníku k řízení průběh média
3. Vyberte vysílání funkce Smooth Streaming datové proudy
4. Vyberte vysílání funkce Smooth Streaming sleduje

## <a name="prerequisites"></a>Požadavky
> [!NOTE]
> Visual Studio 2017 nepodporuje projekty verze Windows Store 8.1 a starší.  Další informace najdete v tématu [Cílení na platformy a kompatibilita v sadě Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8 32bitové nebo 64bitové verze.
* Verze Visual Studio 2012 prostřednictvím 2015.
* [Microsoft klienta funkce Smooth Streaming SDK pro systém Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Dokončené řešení pro každé lekce si můžete stáhnout z ukázky kódu vývojáře MSDN (Galerie kódů): 

* [Lekce 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) – jednoduchý systém Windows 8 funkce Smooth Streaming Media Player 
* [Lekce 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) – jednoduchý systém Windows 8 funkce Smooth Streaming Media Player pomocí jezdce lze ovládací prvek, 
* [Lekce 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) – Windows 8 funkce Smooth Streaming Media Player s výběrem datového proudu  
* [Lekce 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) – Windows 8 funkce Smooth Streaming Media Player s výběrem sledovat.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lekce 1: Vytvoření základní technologie Smooth Streaming aplikace úložiště

V této lekci vytvoříte aplikaci pro Windows Store s ovládacím prvkem MediaElement datový proud Smooth přehrávání obsahu.  Běžící aplikaci vypadá takto:

![Příklad aplikace Smooth Streaming Windows Store][PlayerApplication]

Další informace o vývoji aplikací Windows Store najdete v tématu [vývoji kvalitních aplikací pro Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). V této lekci obsahuje následující postupy:

1. Vytvoření projektu Windows Store
2. Návrh uživatelského rozhraní (XAML)
3. Úprava souboru kódu
4. Kompilace a testování aplikace

**Chcete-li vytvořit projekt Windows Store**

1. Spusťte Visual Studio; jsou podporovány verze 2012 prostřednictvím 2015.
2. V nabídce **Soubor** klikněte na tlačítko **Nový** a pak klikněte na tlačítko **Projekt**.
3. Z tohoto dialogového okna Nový projekt zadejte nebo vyberte tyto hodnoty:

| Název | Hodnota |
| --- | --- |
| Skupina šablon |Nainstalovaná, šablony/Visual C# / Windows Store |
| Šablona |Prázdná aplikace (XAML) |
| Název |SSPlayer |
| Umístění |C:\SSTutorials |
| Název řešení |SSPlayer |
| Vytvořit adresář pro řešení |(vybráno) |

1. Klikněte na **OK**.

**Chcete-li přidat odkaz na technologie Smooth Streaming Client SDK**

1. V Průzkumníku řešení klikněte pravým tlačítkem **SSPlayer**a potom klikněte na **přidat odkaz na**.
2. Zadejte nebo vyberte tyto hodnoty:

| Název | Hodnota |
| --- | --- |
| Odkaz na skupinu |Rozšíření/Windows |
| Referenční informace |Vyberte Microsoft klienta funkce Smooth Streaming SDK pro systém Windows 8 a balíček Microsoft Visual C++ Runtime |

1. Klikněte na **OK**. 

Po přidání odkazů, je nutné vybrat cílovou platformu (x64 nebo x86), přidávání odkazů nebude fungovat pro jakýkoli procesor platformy konfiguraci.  V Průzkumníku řešení zobrazí se žlutý označit upozornění pro tyto přidat odkazy.

**Při návrhu player uživatelského rozhraní**

1. V Průzkumníku řešení poklikejte na **MainPage.xaml** a otevře se v zobrazení návrhu.
2. Vyhledejte **&lt;mřížky&gt;** a **&lt;/Grid&gt;** značky souboru XAML a vložte následující kód mezi dvě značky:

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
   
   Prvek MediaElement se používá k přehrávání média. Posuvník s názvem sliderProgress se použije v další lekce k řízení průběh média.
3. Stiskněte klávesu **CTRL + S** k uložení souboru.

Ovládací prvek MediaElement nepodporuje vysílání funkce Smooth Streaming obsahu out-of-box. Pokud chcete povolit podporu technologie Smooth Streaming, je nutné zaregistrovat obslužné rutiny byte – datový proud Smooth Streaming příponu názvu souboru a typ MIME.  Pokud chcete zaregistrovat, můžete použít metodu MediaExtensionManager.RegisterByteStremHandler Windows.Media oboru názvů.

V tomto souboru XAML některé obslužné rutiny událostí jsou přidruženy k ovládacích prvků.  Je nutné zadat tyto obslužné rutiny událostí.

**K úpravě souboru kódu**

1. V Průzkumníku řešení klikněte pravým tlačítkem **MainPage.xaml**a potom klikněte na **kód zobrazení**.
2. Na začátek souboru přidejte následující příkaz using:
   
        using Windows.Media;
3. Na začátku **MainPage** třídy, přidejte následující datový člen:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. Na konci **MainPage** konstruktoru, přidejte následující dva řádky:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. Na konci **MainPage** třídy, vložte následující kód:
   
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

Obslužné rutiny události sliderProgress_PointerPressed je definována v tomto poli.  Existují další funguje udělat, aby ho pracuje, získat, který se bude vztahovat v další lekci tohoto kurzu.
6. Stiskněte klávesu **CTRL + S** k uložení souboru.

Dokončení kódu na pozadí se vypadat například takto:

![Codeview v aplikaci Visual Studio o technologie Smooth Streaming Windows Store][CodeViewPic]

**Pro zkompilování a testování aplikace**

1. Z **sestavení** nabídky, klikněte na tlačítko **nástroje Configuration Manager**.
2. Změna **platforma Active řešení** tak, aby odpovídaly vývojové platformy.
3. Stiskněte klávesu **F6** kompilace projektu. 
4. Stisknutím klávesy **F5** spusťte aplikaci.
5. V horní části aplikace můžete buď použít výchozí nastavení adresy URL technologie Smooth Streaming nebo zadejte jiný. 
6. Klikněte na tlačítko **nastavit zdroj**. Protože **automatické přehrávání** je povoleno ve výchozím nastavení, se automaticky přehrání média.  Můžete řídit média pomocí **přehrání**, **pozastavení** a **Zastavit** tlačítka.  Můžete ovládat svazku média pomocí svislé posuvníku.  Ale vodorovné posuvník pro řízení průběh média není plně dosud implementována. 

Dokončili jste lesson1.  V této lekci použijte ovládací prvek MediaElement k přehrávání obsahu technologie Smooth Streaming.  V další lekci přidáte jezdce k řízení průběh obsah Smooth Streaming.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lekce 2: Přidání posuvníku k řízení průběh média

V Lekce 1 jste vytvořili aplikaci pro Windows Store s ovládacím prvkem MediaElement XAML k přehrávání technologie Smooth Streaming mediální obsah.  Některé funkce základní média jako spuštění, zastavení nebo pozastavení pochází.  V této lekci přidáte ovládací prvek typu jezdec panelu do aplikace.

V tomto kurzu budeme používat časovač aktualizovat pozice posuvníku založené na aktuální pozici MediaElement ovládacího prvku.  Posuvník počáteční a koncová čas také je potřeba aktualizovat v případě živý obsah.  To může lépe ošetřit v události adaptivní zdroj aktualizace.

Média zdroje jsou objekty, které generují data média.  Překladač zdroj přebírá adresu URL nebo bajtů datového proudu a vytvoří odpovídající médium zdroj pro tento obsah.  Překladač zdroj je standardní způsob pro aplikace, které chcete vytvořit médium zdroje. 

V této lekci obsahuje následující postupy:

1. Zaregistrovat obslužná rutina technologie Smooth Streaming 
2. Přidání obslužných rutin událostí na úrovni správce adaptivní zdroje
3. Přidání obslužných rutin událostí na úrovni adaptivní zdroje
4. Přidání obslužných rutin událostí MediaElement
5. Přidat posuvníku panelu související kódu
6. Kompilace a testování aplikace

**K registraci obslužné rutiny byte – datový proud Smooth Streaming a předat propertyset**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **MainPage.xaml**a potom klikněte na **kód zobrazení**.
2. Na začátek souboru přidejte následující příkaz using:

        using Microsoft.Media.AdaptiveStreaming;
3. Na začátku MainPage třídy, přidejte následující členy dat:

         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
4. Uvnitř **MainPage** konstruktoru, přidejte následující kód po **to. Inicializace Components();**  řádku a registrace kód napsaný v předchozí lekci řádky:

        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
5. Uvnitř **MainPage** konstruktoru, upravte tyto dvě metody RegisterByteStreamHandler přidat stanovilo parametry:

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
6. Stiskněte klávesu **CTRL + S** k uložení souboru.

**Přidání obslužné rutiny událostí na úrovni správce adaptivní zdroje**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **MainPage.xaml**a potom klikněte na **kód zobrazení**.
2. Uvnitř **MainPage** třídy, přidejte následující datový člen:
   
     privátní adaptiveSource AdaptiveSource = null;
3. Na konci **MainPage** třídy, přidejte následující obslužnou rutinu události:
   
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
4. Na konci **MainPage** konstruktoru, přidejte následující řádek k odběru události open adaptivní zdroje:
   
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
5. Stiskněte klávesu **CTRL + S** k uložení souboru.

**Přidání obslužných rutin událostí na úrovni adaptivní zdroje**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **MainPage.xaml**a potom klikněte na **kód zobrazení**.
2. Uvnitř **MainPage** třídy, přidejte následující datový člen:
   
     privátní AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate;   privátní manifestu manifestObject;
3. Na konci **MainPage** třídy, přidejte následující obslužné rutiny událostí:

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
4. Na konci **mediaElement AdaptiveSourceOpened** metoda, přidejte následující kód k odběru události:
   
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
5. Stiskněte klávesu **CTRL + S** k uložení souboru.

Stejné události jsou k dispozici na adaptivní zdroj Manager úrovni také, které lze použít pro zpracování funkce, které jsou společné pro všechny elementy média v aplikaci. Každý AdaptiveSource zahrnuje vlastní události a všechny události AdaptiveSource předána pod AdaptiveSourceManager.

**Chcete-li přidat Element média obslužné rutiny událostí**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **MainPage.xaml**a potom klikněte na **kód zobrazení**.
2. Na konci **MainPage** třídy, přidejte následující obslužné rutiny událostí:

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
3. Na konci **MainPage** konstruktoru, přidejte následující kód do dolní index k událostem:

         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
4. Stiskněte klávesu **CTRL + S** k uložení souboru.

**Chcete-li přidat posuvníku související kódu**

1. V Průzkumníku řešení klikněte pravým tlačítkem na **MainPage.xaml**a potom klikněte na **kód zobrazení**.
2. Na začátek souboru přidejte následující příkaz using:
      
        using Windows.UI.Core;
3. Uvnitř **MainPage** třídy, přidejte následující členy dat:
   
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
4. Na konci **MainPage** konstruktoru, přidejte následující kód:
   
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
5. Na konci **MainPage** třídy, přidejte následující kód:

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
      
>[!NOTE]
>CoreDispatcher slouží ke změnám vlákna uživatelského rozhraní od jiných vlákna uživatelského rozhraní. V případě úzkým místem na vlákno dispečera můžete vybrat vývojáře použít dispečera poskytované si chtít aktualizovat prvek uživatelského rozhraní.  Příklad:
   
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
6. Na konci **mediaElement_AdaptiveSourceStatusUpdated** metoda, přidejte následující kód:

         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
7. Na konci **MediaOpened** metoda, přidejte následující kód:

         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
8. Stiskněte klávesu **CTRL + S** k uložení souboru.

**Pro zkompilování a testování aplikace**

1. Stiskněte klávesu **F6** kompilace projektu. 
2. Stisknutím klávesy **F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí nastavení adresy URL technologie Smooth Streaming nebo zadejte jiný. 
4. Klikněte na tlačítko **nastavit zdroj**. 
5. Otestujte posuvníku.

Když jste dokončili Lekce 2.  V této lekci přidané jezdce do aplikace. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lekce 3: Vyberte vysílání funkce Smooth Streaming datové proudy
Technologie Smooth Streaming je umožňující obsah datového proudu s více zvukových stop jazyk, které jsou lze vybrat pomocí prohlížeče.  V této lekci povolíte prohlížečům vyberte datových proudů. V této lekci obsahuje následující postupy:

1. Upravte soubor XAML
2. Upravte soubor behand kódu
3. Kompilace a testování aplikace

**K úpravě souboru XAML**

1. V Průzkumníku řešení klikněte pravým tlačítkem **MainPage.xaml**a potom klikněte na **Návrhář zobrazení**.
2. Vyhledejte &lt;Grid.RowDefinitions&gt;a upravte RowDefinitions tak bude vypadat takto:
   
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
3. Uvnitř &lt;mřížky&gt;&lt;/Grid&gt; značky, přidejte následující kód k definování ListBox – ovládací prvek, aby uživatelé mohli zobrazit seznam dostupných datových proudů a vybrat datové proudy:

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
4. Stiskněte klávesu **CTRL + S** a uložte změny.

**K úpravě souboru kódu**

1. V Průzkumníku řešení klikněte pravým tlačítkem **MainPage.xaml**a potom klikněte na **kód zobrazení**.
2. Uvnitř SSPlayer oboru názvů přidejte novou třídu:
   
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
3. Na začátku MainPage třídy, přidejte následující definice proměnné:
   
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
4. Uvnitř MainPage třídy přidejte následující oblasti:
   
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
   
            // Select the frist video stream from the list if no video stream is selected
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
   
            // Select the frist audio stream from the list if no audio steam is selected.
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
5. Metoda mediaElement_ManifestReady najít, doplňovací následující kód na konci funkce:
   
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   
    Proto když MediaElement manifest je připraven, kód získá seznam dostupných datových proudů a naplní pole se seznamem uživatelského rozhraní se seznamem.
6. Uvnitř MainPage třídy, vyhledejte rozhraní tlačítka klikněte na události oblast a potom přidejte následující definice funkce:
   
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Pro zkompilování a testování aplikace**

1. Stiskněte klávesu **F6** kompilace projektu. 
2. Stisknutím klávesy **F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí nastavení adresy URL technologie Smooth Streaming nebo zadejte jiný. 
4. Klikněte na tlačítko **nastavit zdroj**. 
5. Výchozí jazyk je audio_eng. Zkuste přepínat mezi audio_eng a audio_es. Při, vyberte nového datového proudu, musíte kliknout na tlačítko pro odeslání.

Když jste dokončili Lekce 3.  V této lekci přidáte funkci zvolit datové proudy.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lekce 4: Vyberte vysílání funkce Smooth Streaming sleduje
Prezentace technologie Smooth Streaming může obsahovat více videosouborů zakódovaných pomocí různé úrovně kvality (přenosové rychlosti) a jejich řešení. V této lekci povolíte uživatelům vybrat sleduje. V této lekci obsahuje následující postupy:

1. Upravte soubor XAML
2. Upravte soubor behand kódu
3. Kompilace a testování aplikace

**K úpravě souboru XAML**

1. V Průzkumníku řešení klikněte pravým tlačítkem **MainPage.xaml**a potom klikněte na **Návrhář zobrazení**.
2. Vyhledejte &lt;mřížky&gt; značky s názvem **gridStreamAndBitrateSelection**, přidejte následující text na konci značky:
   
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
3. Stiskněte klávesu **CTRL + S** se uložit změny he

**K úpravě souboru kódu**

1. V Průzkumníku řešení klikněte pravým tlačítkem **MainPage.xaml**a potom klikněte na **kód zobrazení**.
2. Uvnitř SSPlayer oboru názvů přidejte novou třídu:
   
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
3. Na začátku MainPage třídy, přidejte následující definice proměnné:
   
        private List<Track> availableTracks;
4. Uvnitř MainPage třídy přidejte následující oblasti:
   
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
5. Metoda mediaElement_ManifestReady najít, doplňovací následující kód na konci funkce:
   
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
6. Uvnitř MainPage třídy, vyhledejte rozhraní tlačítka klikněte na události oblast a potom přidejte následující definice funkce:
   
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }

**Pro zkompilování a testování aplikace**

1. Stiskněte klávesu **F6** kompilace projektu. 
2. Stisknutím klávesy **F5** spusťte aplikaci.
3. V horní části aplikace můžete buď použít výchozí nastavení adresy URL technologie Smooth Streaming nebo zadejte jiný. 
4. Klikněte na tlačítko **nastavit zdroj**. 
5. Ve výchozím nastavení jsou vybrány všechny sleduje video datového proudu. Experimentovat míry změny bit, můžete vybrat nejnižší přenosová rychlost k dispozici a pak vyberte nejvyšší přenosovou rychlost, k dispozici. Musíte kliknout na odeslání po každé změně.  Zobrazí se změny kvalitu videa.

Když jste dokončili Lekce 4.  V této lekci přidáte funkci vybrat sleduje.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Další prostředky:
* [Jak sestavit aplikaci technologie Smooth Streaming JavaScript Windows 8 s pokročilé funkce](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Technologie Smooth Streaming technický přehled](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

