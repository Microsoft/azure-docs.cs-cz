---
title: 'Rychlý Start: Custom Voice Assistant C# , (UWP) – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte aplikaci C# Univerzální platforma Windows (UWP) pomocí sady Cognitive Services Speech Software Development Kit (SDK). Klientská aplikace se připojuje k dřív vytvořeným robotem rozhraní robota, který je nakonfigurovaný tak, aby používal kanál Direct line Speech. Aplikace je sestavená pomocí balíčku NuGet sady Speech SDK a Microsoft Visual Studio 2019.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: af9395b8f75a9097d4449571fd54be6259a7b9f3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119767"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-uwp"></a>Rychlý Start: vytvoření hlasového asistenta pomocí sady Speech SDK, UWP

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp), [syntézu řeči](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp)a [Překlad řeči](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp).

V tomto článku budete vyvíjet aplikaci C# Univerzální platforma Windows (UWP) pomocí [sady Speech SDK](speech-sdk.md). Program se připojí k dřív vytvořeným a nakonfigurovaným robotům, aby umožnil prostředí hlasového asistenta z klientské aplikace. Aplikace je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2019 (libovolná edice).

> [!NOTE]
> Univerzální platforma Windows umožňuje vyvíjet aplikace, které běží na všech zařízeních podporujících Windows 10 včetně počítačů, Xboxu, Surface Hubu a dalších.

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klíč předplatného Azure pro službu Speech Service. [Získejte ho zdarma](get-started.md) nebo ho vytvořte na [Azure Portal](https://portal.azure.com).
* Dřív vytvořený robot nakonfigurovaný s [kanálem Direct line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

  > [!NOTE]
  > Přečtěte si [seznam podporovaných oblastí pro hlasové asistenty](regions.md#voice-assistants) a zajistěte, aby byly prostředky nasazeny v jedné z těchto oblastí.

## <a name="optional-get-started-fast"></a>Volitelné: rychlý začátek

V tomto rychlém startu se dozvíte, jak vytvořit klientskou aplikaci, aby se připojila k vašemu robotovi podporujícímu rozpoznávání řeči. Pokud upřednostňujete podrobně přímo v, je kompletní zdrojový kód připravený k zkompilování použitý v tomto rychlém startu k dispozici v [ukázkách sady Speech SDK](https://aka.ms/csspeech/samples) ve složce `quickstart`.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

Nyní přidejte kód jazyka XAML, který definuje uživatelské rozhraní aplikace, a přidejte implementaci kódu na C# pozadí.

### <a name="xaml-code"></a>Kód XAML

Nejprve vytvoříte uživatelské rozhraní aplikace přidáním kódu jazyka XAML:

1. V **Průzkumník řešení**otevřete `MainPage.xaml`.

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
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
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

### <a name="c-code-behind-source"></a>C#zdroj kódu na pozadí

Pak přidáte zdroj kódu na pozadí, aby aplikace fungovala podle očekávání. Zdroj kódu na pozadí zahrnuje:

- příkazy `using` pro obory názvů `Speech` a `Speech.Dialog`
- Jednoduchá implementace pro zajištění přístupu přes mikrofon, který je kabelem k obslužné rutině tlačítka
- Základní pomocníky uživatelského rozhraní pro prezentování zpráv a chyb v aplikaci
- Místo pro cestu inicializačního kódu, který se naplní později
- Pomocná funkce pro přehrání textu na řeč (bez podpory streamování)
- Prázdná obslužná rutina tlačítka pro zahájení naslouchání, které se naplní později

Zdroj kódu na pozadí přidáte pomocí následujících kroků:

1. V **Průzkumník řešení**otevřete zdrojový soubor kódu na pozadí `MainPage.xaml.cs`. (Je seskupeno pod `MainPage.xaml`.)

1. Nahraďte obsah souboru následujícím fragmentem kódu:

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

1. Do těla metody `InitializeDialogServiceConnector`přidejte následující fragment kódu. Tento kód vytvoří `DialogServiceConnector` s informacemi o předplatném.

    ```csharp
    // Create a BotFrameworkConfig by providing a Speech service subscription key
    // the RecoLanguage property is optional (default en-US)
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region.

    var botConfig = BotFrameworkConfig.FromSubscription(speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > Přečtěte si [seznam podporovaných oblastí pro hlasové asistenty](regions.md#voice-assistants) a zajistěte, aby byly prostředky nasazeny v jedné z těchto oblastí.

   > [!NOTE]
   > Informace o konfiguraci robota a načtení tajného klíče kanálu najdete v dokumentaci k rozhraní bot pro [kanál Direct line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

1. Nahraďte řetězce `YourChannelSecret`, `YourSpeechSubscriptionKey`a `YourServiceRegion` vlastními hodnotami pro váš robot, předplatné a [oblast](regions.md)řeči.

1. Přidejte následující fragment kódu na konec těla metody `InitializeDialogServiceConnector`. Tento kód nastaví obslužné rutiny pro události, které se `DialogServiceConnector`, aby komunikovaly své aktivity robota, výsledky rozpoznávání řeči a další informace.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
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

1. Do těla metody `ListenButton_ButtonClicked` ve třídě `MainPage` přidejte následující fragment kódu. Tento kód nastaví `DialogServiceConnector` k naslouchání, protože už jste vytvořili konfiguraci a zaregistrovali obslužné rutiny událostí.

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. V řádku nabídek vyberte **soubor** > **Uložit vše** a uložte provedené změny.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Teď jste připraveni sestavit a otestovat svoji aplikaci.

1. V řádku nabídek výběrem možnosti **sestavit** > **Sestavit řešení** sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázka aplikace hlasového asistenta C# UWP v – rychlý Start](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Vyberte možnost **Povolit mikrofon**a když se zobrazí žádost o přístupové oprávnění, vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Vyberte možnost **mluvit na robota**a mluvte do mikrofonu zařízení anglickou frázi nebo větu. Váš hlas se přenáší na kanál s přímým přístupem a přepisu na text, který se zobrazí v okně.
<!--
    ![Successful bot response](media/voice-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření a nasazení základního chatbota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Viz také

- [O hlasových asistentcích](voice-assistants.md)
- [Získejte zdarma klíč předplatného služby Speech](get-started.md)
- [Vlastní klíčová slova](speech-devices-sdk-create-kws.md)
- [Připojení přímého Lineového rozpoznávání řeči k robotovi](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)
