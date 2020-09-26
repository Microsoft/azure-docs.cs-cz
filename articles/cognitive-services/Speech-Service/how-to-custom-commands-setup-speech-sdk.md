---
title: Integrace s klientskou aplikací s využitím sady Speech SDK
titleSuffix: Azure Cognitive Services
description: Jak vytvářet požadavky na publikované aplikace vlastních příkazů ze sady Speech SDK spuštěné v aplikaci UWP.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: 49922599adb30e6c631f29b4831fda60cc000a40
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360119"
---
# <a name="integrate-with-a-client-application-using-speech-sdk"></a>Integrace s klientskou aplikací pomocí sady Speech SDK

V tomto článku se naučíte, jak pomocí sady Speech SDK spuštěné v aplikaci UWP vytvořit požadavky na publikovanou aplikaci Custom Commands. Aby bylo možné navázat připojení k aplikaci Custom Commands, budete potřebovat:

- Publikování vlastní aplikace příkazů a získání identifikátoru aplikace (ID aplikace)
- Vytvoření klientské aplikace Univerzální platforma Windows (UWP) pomocí sady Speech SDK, která vám umožní komunikovat s vlastními aplikacemi příkazů

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku je nutná vlastní aplikace příkazů. Pokud jste ještě nevytvořili aplikaci Custom Commands, můžete postupovat podle těchto rychlých startů:
> [!div class = "checklist"]
> * [Vytvoření vlastní aplikace příkazů](quickstart-custom-commands-application.md)

Budete také potřebovat:
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) nebo vyšší. Tato příručka je založená na aplikaci Visual Studio 2019.
> * Klíč předplatného Azure pro hlasové služby. [Získejte ho zdarma](overview.md#try-the-speech-service-for-free) nebo ho vytvořte na [Azure Portal](https://portal.azure.com)
> * [Povolení vývoje zařízení](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)

## <a name="step-1-publish-custom-commands-application"></a>Krok 1: publikování aplikace Custom Commands

1. Otevřete dříve vytvořenou aplikaci Custom Commands
1. Přejít na **Nastavení**, vyberte **prostředek Luis** .
1. Pokud není **prostředek předpovědi** přiřazený, vyberte klíč předpovědi dotazu nebo vytvořte nový.

    Klíč předpovědi dotazu se vždy vyžaduje před publikováním aplikace. Další informace o prostředcích LUIS najdete v referenčních informacích k [vytvoření prostředku Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-azure-subscription) .

1. Vraťte se zpět na příkazy pro úpravy, vyberte **publikovat** .

   > [!div class="mx-imgBorder"]
   > ![Publikování aplikace](media/custom-commands/setup-speech-sdk-publish-application.png)

1. Zkopírujte ID aplikace z oznámení o publikování pro pozdější použití.
1. Zkopírujte klíč prostředku pro rozpoznávání řeči pro pozdější použití.

## <a name="step-2-create-a-visual-studio-project"></a>Krok 2: vytvoření projektu sady Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Krok 3: Přidání ukázkového kódu

V tomto kroku přidáme kód XAML, který definuje uživatelské rozhraní aplikace, a přidáte implementaci C# kódu na pozadí.

### <a name="xaml-code"></a>Kód XAML

Vytvořte uživatelské rozhraní aplikace přidáním kódu jazyka XAML.

1. V **Průzkumník řešení**otevřete `MainPage.xaml`

1. V zobrazení jazyka XAML návrháře nahraďte celý obsah následujícím fragmentem kódu:

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
                       Margin="0,10,10,0" Click="EnableMicrophone_ButtonClicked"
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

Zobrazení Návrh se aktualizuje a zobrazí se uživatelské rozhraní aplikace.

### <a name="c-code-behind-source"></a>Zdrojový kód C# – na pozadí

Přidejte zdroj kódu na pozadí, aby aplikace fungovala podle očekávání. Zdroj kódu na pozadí zahrnuje:

- Povinné `using` příkazy pro `Speech` `Speech.Dialog` obory názvů a
- Jednoduchá implementace pro zajištění přístupu přes mikrofon, který je kabelem k obslužné rutině tlačítka
- Základní pomocníky uživatelského rozhraní pro prezentování zpráv a chyb v aplikaci
- Místo pro cestu inicializačního kódu, který se naplní později
- Pomocná funkce pro přehrání textu na řeč (bez podpory streamování)
- Prázdná obslužná rutina tlačítka pro zahájení naslouchání, které se naplní později

Přidejte zdroj kódu na pozadí následujícím způsobem:

1. V **Průzkumník řešení**otevřete zdrojový soubor kódu na pozadí `MainPage.xaml.cs` (seskupeno pod `MainPage.xaml` ).

1. Obsah souboru nahraďte následujícím kódem: 

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.IO;
   using System.Text;
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
    > [!NOTE]
    > Pokud se zobrazí chyba: typ Object je definovaný v sestavení, na které se neodkazuje.
    > 1. Napravo od klienta vaše řešení.
    > 1. Zvolte možnost **Spravovat balíčky NuGet pro řešení**, vyberte **aktualizace** . 
    > 1. Pokud se v seznamu aktualizace zobrazí **Microsoft. NETCore. UniversalWindowsPlatform** , aktualizujte **Microsoft. NETCore. UniversalWindowsPlatform** na nejnovější verzi.

1. Do těla metody přidejte následující kód `InitializeDialogServiceConnector`

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

1. Nahraďte řetězce `YourApplicationId` , `YourSpeechSubscriptionKey` a `YourServiceRegion` vlastními hodnotami vaší aplikace, předplatným řeči a [oblastí](regions.md) .

1. Přidejte následující fragment kódu na konec těla metody `InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
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

1. Do těla `ListenButton_ButtonClicked` metody ve třídě přidejte následující fragment kódu. `MainPage`

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

1. V řádku nabídek vyberte **soubor**  >  **Uložit vše** a uložte změny.

## <a name="try-it-out"></a>Vyzkoušet

1. V řádku nabídek vyberte sestavení sestavit **Build**  >  **řešení** a sestavte aplikaci. Kód by se měl zkompilovat bez chyb.

1. Zvolte **ladění**  >  **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázková aplikace virtuálního pomocníka UWP v C# – rychlý Start](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Vyberte **Povolit mikrofon**. Pokud se zobrazí žádost o oprávnění k přístupu, vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Vyberte **hovořit**a mluvte do mikrofonu zařízení anglickou frázi nebo větu. Váš hlas se přenáší na kanál s přímým přístupem a přepisu na text, který se zobrazí v okně.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: odeslání aktivity do klientské aplikace (Preview)](./how-to-custom-commands-send-activity-to-client.md)
