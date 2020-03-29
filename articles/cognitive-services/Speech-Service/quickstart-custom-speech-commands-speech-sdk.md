---
title: 'Úvodní příručka: Připojení k aplikaci Vlastní příkazy pomocí sady Speech SDK – služba Řeč'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte klientskou aplikaci sady Speech SDK s vlastními příkazy.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 9e324af0b90f595b5b7af2a417a562efb193d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156773"
---
# <a name="quickstart-connect-to-a-custom-commands-application-with-the-speech-sdk-preview"></a>Úvodní příručka: Připojení k aplikaci vlastních příkazů pomocí sady Speech SDK (Preview)

Po vytvoření hostované aplikace vlastní příkazy, můžete začít mluvit s ním z klientského zařízení.

V tomto článku budete:

- Publikování aplikace vlastních příkazů a získání identifikátoru aplikace (ID aplikace)
- Vytvoření klientské aplikace pomocí sady Speech SDK, která vám umožní mluvit s aplikací Vlastní příkazy

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku je vyžadována aplikace vlastní chod. Pokud jste ještě nevytvořili aplikaci Vlastní příkazy, můžete tak učinit v těchto předchozích rychlých startech:

- [Úvodní příručka: Vytvoření vlastního příkazu (náhled)](./quickstart-custom-speech-commands-create-new.md)
- [Úvodní příručka: Vytvoření vlastního příkazu s parametry (náhled)](./quickstart-custom-speech-commands-create-parameters.md)

Budete také potřebovat:

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Klíč předplatného Azure pro hlasové služby. [Získejte jeden zdarma](get-started.md) nebo si ho vytvořte na [webu Azure Portal](https://portal.azure.com)

## <a name="optional-get-started-fast"></a>Volitelné: Rychlé zahájení

Tento rychlý start krok za krokem popisuje, jak vytvořit klientskou aplikaci pro připojení k aplikaci Vlastní příkazy. Pokud dáváte přednost ponořit přímo do, kompletní, připravený ke kompilaci zdrojový kód použitý v tomto `quickstart` rychlém startu je k dispozici v [ukázkách sady Speech SDK](https://aka.ms/csspeech/samples) ve složce.

## <a name="step-1-publish-custom-commands-application"></a>Krok 1: Publikovat vlastní příkazy aplikace

1. Otevřete [dříve vytvořenou aplikaci Vlastní příkazy](./quickstart-custom-speech-commands-create-new.md) a vyberte **Publikovat.**

   > [!div class="mx-imgBorder"]
   > ![Publikování aplikace](media/custom-speech-commands/fulfill-sdk-publish-application.png)

1. Kopírování ID aplikace z oznámení publikování pro pozdější použití

## <a name="step-2-create-a-visual-studio-project"></a>Krok 2: Vytvoření projektu sady Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Krok 3: Přidání ukázkového kódu

V tomto kroku přidáme kód XAML, který definuje uživatelské rozhraní aplikace, a přidáme implementaci kódu C#.

### <a name="xaml-code"></a>Kód XAML

Vytvořte uživatelské rozhraní aplikace přidáním kódu XAML.

1. V **Průzkumníku řešení**otevřete`MainPage.xaml`

1. V zobrazení XAML návrháře nahraďte celý obsah následujícím fragmentem kódu:

   ```xml
   <Page
       x:Class="helloworld.MainPage"
       xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="using:helloworld"
       xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
       xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
       mc:Ignorable="d"
       Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid>
           <StackPanel Orientation="Vertical" HorizontalAlignment="Center"
                       Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
               <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"
                       Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
                       Margin="0,10,10,0" Click="ListenButton_ButtonClicked"
                       Height="35"/>
               <StackPanel x:Name="StatusPanel" Orientation="Vertical"
                           RelativePanel.AlignBottomWithPanel="True"
                           RelativePanel.AlignRightWithPanel="True"
                           RelativePanel.AlignLeftWithPanel="True">
                   <TextBlock x:Name="StatusLabel" Margin="0,10,10,0"
                              TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                   <Border x:Name="StatusBorder" Margin="0,0,0,0">
                       <ScrollViewer VerticalScrollMode="Auto"
                                     VerticalScrollBarVisibility="Auto" MaxHeight="200">
                           <!-- Use LiveSetting to enable screen readers to announce
                                the status update. -->
                           <TextBlock
                               x:Name="StatusBlock" FontWeight="Bold"
                               AutomationProperties.LiveSetting="Assertive"
                               MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}"
                               Margin="10,10,10,20" TextWrapping="Wrap"  />
                       </ScrollViewer>
                   </Border>
               </StackPanel>
           </StackPanel>
           <MediaElement x:Name="mediaElement"/>
       </Grid>
   </Page>
   ```

Návrhové zobrazení je aktualizováno tak, aby zobrazovala uživatelské rozhraní aplikace.

### <a name="c-code-behind-source"></a>C# kód na pozadí zdroje

Přidejte zdroj s kódem na pozadí tak, aby aplikace fungovala podle očekávání. Zdroj kódu na pozadí zahrnuje:

- Povinné `using` příkazy `Speech` `Speech.Dialog` pro obory názvů a
- Jednoduchá implementace pro zajištění přístupu k mikrofonu, připojená k obslužné rutině tlačítek
- Základní pomocníci ui pro prezentaci zpráv a chyb v aplikaci
- Přistávací bod pro cestu inicializace kódu, který bude naplněn později
- Pomocník pro přehrávání textu na řeč (bez podpory streamování)
- Prázdná obslužná rutina tlačítka pro zahájení poslechu, která bude naplněna později

Přidejte zdroj s kódem na pozadí následujícím způsobem:

1. V **Průzkumníku řešení**otevřete zdrojový `MainPage.xaml.cs` soubor s `MainPage.xaml`kódem na pozadí (seskupený pod )

1. Nahraďte obsah souboru následujícím kódem:

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.Diagnostics;
   using System.IO;
   using System.Text;
   using Windows.Foundation;
   using Windows.Storage.Streams;
   using Windows.UI.Xaml;
   using Windows.UI.Xaml.Controls;
   using Windows.UI.Xaml.Media;

   namespace helloworld
   {
       public sealed partial class MainPage : Page
       {
           private DialogServiceConnector connector;

           private enum NotifyType
           {
               StatusMessage,
               ErrorMessage
           };

           public MainPage()
           {
               this.InitializeComponent();
           }

           private async void EnableMicrophone_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               bool isMicAvailable = true;
               try
               {
                   var mediaCapture = new Windows.Media.Capture.MediaCapture();
                   var settings =
                       new Windows.Media.Capture.MediaCaptureInitializationSettings();
                   settings.StreamingCaptureMode =
                       Windows.Media.Capture.StreamingCaptureMode.Audio;
                   await mediaCapture.InitializeAsync(settings);
               }
               catch (Exception)
               {
                   isMicAvailable = false;
               }
               if (!isMicAvailable)
               {
                   await Windows.System.Launcher.LaunchUriAsync(
                       new Uri("ms-settings:privacy-microphone"));
               }
               else
               {
                   NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
               }
           }

           private void NotifyUser(
               string strMessage, NotifyType type = NotifyType.StatusMessage)
           {
               // If called from the UI thread, then update immediately.
               // Otherwise, schedule a task on the UI thread to perform the update.
               if (Dispatcher.HasThreadAccess)
               {
                   UpdateStatus(strMessage, type);
               }
               else
               {
                   var task = Dispatcher.RunAsync(
                       Windows.UI.Core.CoreDispatcherPriority.Normal,
                       () => UpdateStatus(strMessage, type));
               }
           }

           private void UpdateStatus(string strMessage, NotifyType type)
           {
               switch (type)
               {
                   case NotifyType.StatusMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Green);
                       break;
                   case NotifyType.ErrorMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Red);
                       break;
               }
               StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text)
                   ? strMessage : "\n" + strMessage;

               if (!string.IsNullOrEmpty(StatusBlock.Text))
               {
                   StatusBorder.Visibility = Visibility.Visible;
                   StatusPanel.Visibility = Visibility.Visible;
               }
               else
               {
                   StatusBorder.Visibility = Visibility.Collapsed;
                   StatusPanel.Visibility = Visibility.Collapsed;
               }
               // Raise an event if necessary to enable a screen reader
               // to announce the status update.
               var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
               if (peer != null)
               {
                   peer.RaiseAutomationEvent(
                       Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
               }
           }

           // Waits for and accumulates all audio associated with a given
           // PullAudioOutputStream and then plays it to the MediaElement. Long spoken
           // audio will create extra latency and a streaming playback solution
           // (that plays audio while it continues to be received) should be used --
           // see the samples for examples of this.
           private void SynchronouslyPlayActivityAudio(
               PullAudioOutputStream activityAudio)
           {
               var playbackStreamWithHeader = new MemoryStream();
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
               playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
               playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

               byte[] pullBuffer = new byte[2056];

               uint lastRead = 0;
               do
               {
                   lastRead = activityAudio.Read(pullBuffer);
                   playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
               }
               while (lastRead == pullBuffer.Length);

               var task = Dispatcher.RunAsync(
                   Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                   mediaElement.SetSource(
                       playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                   mediaElement.Play();
               });
           }

           private void InitializeDialogServiceConnector()
           {
               // New code will go here
           }

           private async void ListenButton_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               // New code will go here
           }
       }
   }
   ```

1. Přidejte následující kód do těla metody`InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Nahrazení řetězců `YourApplicationId`, `YourSpeechSubscriptionKey`a `YourServiceRegion` vlastními hodnotami pro aplikaci, předplatné řeči a [oblast](regions.md)

1. Připomňujte následující fragment kódu na konec těla metody`InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
   {
       NotifyUser(
           $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

       if (activityReceivedEventArgs.HasAudio)
       {
           SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
       }
   };

   // Canceled will be signaled when a turn is aborted or experiences an error condition
   connector.Canceled += (sender, canceledEventArgs) =>
   {
       NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
       if (canceledEventArgs.Reason == CancellationReason.Error)
       {
           NotifyUser(
               $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
       }
   };

   // Recognizing (not 'Recognized') will provide the intermediate recognized text
   // while an audio stream is being processed
   connector.Recognizing += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
   };

   // Recognized (not 'Recognizing') will provide the final recognized text
   // once audio capture is completed
   connector.Recognized += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
   };

   // SessionStarted will notify when audio begins flowing to the service for a turn
   connector.SessionStarted += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
   };

   // SessionStopped will notify when a turn is complete and
   // it's safe to begin listening again
   connector.SessionStopped += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
   };
   ```

1. Přidejte následující fragment kódu do těla `ListenButton_ButtonClicked` metody `MainPage` ve třídě

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. V řádku nabídek zvolte **Uložit** > **vše,** chcete-li uložit změny.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. Na řádku nabídek zvolte **Sestavení** > **sestavení řešení** k sestavení aplikace. Kód by se měl zkompilovat bez chyb.

1. **Chcete-li** > spustit aplikaci, zvolte Ladění**spouštění ladění** (nebo stisknutím **klávesy F5).** Zobrazí se okno **Helloworld.**

   ![Ukázka aplikace virtuálníasistentky UPW v C# - rychlý start](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Vyberte **povolit mikrofon**. Pokud se objeví žádost o přístupová oprávnění, vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Vyberte **Talk**a mluvte anglickou frází nebo větou do mikrofonu zařízení. Vaše řeč je přenášena do kanálu Direct Line Speech a přepsána na text, který se zobrazí v okně.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postup: Plnění příkazů na straně klienta pomocí sady Speech SDK (preview)](./how-to-custom-speech-commands-fulfill-sdk.md)
> [Postup: Přidání ověření do parametrů vlastního příkazu (náhled)](./how-to-custom-speech-commands-validations.md)
