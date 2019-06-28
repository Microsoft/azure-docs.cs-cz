---
title: 'Rychlý start: Vlastní hlasové první virtuálních asistentů (Preview), C# (UPW) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte C# aplikace univerzální platformy Windows (UPW) s použitím Cognitive Services řeči Software Development Kit (SDK). Připojíte se klientská aplikace mohla dříve vytvořeného bot Bot Framework konfigurován pro použití s přímým přístupem řeči řádek kanál. Aplikace je sestavená pomocí balíčku NuGet sady SDK pro řeč a Microsoft Visual Studio 2017.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: 4044f8d48efae4e8423f780c85e0f3ccfde12461
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467060"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>Rychlý start: Vytvoření první hlasové virtuálních asistentů se sadou SDK pro řeč, UPW

Rychlí průvodci jsou také k dispozici pro [speech to text](quickstart-csharp-uwp.md), [převod textu na řeč](quickstart-text-to-speech-csharp-uwp.md) a [překlad řeči](quickstart-translate-speech-uwp.md).

V tomto článku budete vyvíjet C# aplikace univerzální platformy Windows (UPW) s použitím [sadou SDK pro řeč](speech-sdk.md). Program se připojí k dříve vytvořený a nakonfigurovaný bot umožňující hlasové první virtuálních asistentů prostředí z klientské aplikace. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

> [!NOTE]
> Univerzální platforma Windows umožňuje vyvíjet aplikace, které běží na všech zařízeních podporujících Windows 10 včetně počítačů, Xboxu, Surface Hubu a dalších.

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro hlasové služby. [Získat zdarma](get-started.md) neexistuje, vytvořte ho na [webu Azure portal](https://portal.azure.com).
* Dříve vytvořeného bot nakonfigurovanou [kanál s přímým přístupem řádku řeči](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

    > [!NOTE]
    > Řeči řádku s přímým přístupem (Preview) je momentálně dostupný v podmnožině oblastí s hlasové služby. Najdete [seznam podporovaných oblastí pro virtuálních asistentů hlasové první](regions.md#voice-first-virtual-assistants) a ujistěte se prostředky nasadí v těchto oblastech.

## <a name="optional-get-started-fast"></a>Volitelné: Rychlý začátek

V tomto rychlém startu popíše krok za krokem, jak vytvořit jednoduchý klientské aplikace pro připojení k rozpoznávání řeči robota. Pokud chcete rovnou, dokončena, připraveno kompilace zdrojového kódu v tomto rychlém startu je k dispozici v [ukázky řeči SDK](https://aka.ms/csspeech/samples) pod `quickstart` složky.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Uživatelské rozhraní aplikace je definované pomocí XAML. Otevřete `MainPage.xaml` v Průzkumníku řešení. V zobrazení návrháři XAML, nahraďte celý obsah s níže.

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
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" Margin="0,10,10,0" Click="ListenButton_ButtonClicked" Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" RelativePanel.AlignBottomWithPanel="True" RelativePanel.AlignRightWithPanel="True" RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce the status update. -->
                            <TextBlock x:Name="StatusBlock" FontWeight="Bold" AutomationProperties.LiveSetting="Assertive"
                    MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

1. Otevřít zdrojový soubor kódu na pozadí `MainPage.xaml.cs`. Najdete ji seskupené pod nadpisem `MainPage.xaml`. Nahraďte obsah následujícím kódem. Zde je, co tato ukázka obsahuje: 

    * Pomocí příkazů pro zpracování řeči a Speech.Dialog obory názvů
    * Jednoduchá implementace zajistit přístup k mikrofonu, připojené k rutině tlačítka
    * Základní pomocné rutiny uživatelského rozhraní k zobrazení zprávy a chyby v aplikaci
    * Bod cílová cesta inicializace kódu, který naplní se později
    * Pomocné rutiny k přehrání zpět převod textu na řeč (bez podpora streamování)
    * Obslužné rutiny prázdný tlačítko zahájit naslouchání, která naplní později

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

            private async void EnableMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) ? strMessage : "\n" + strMessage;

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
                // Raise an event if necessary to enable a screen reader to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for accumulates all audio associated with a given PullAudioOutputStream and then plays it to the
            // MediaElement. Long spoken audio will create extra latency and a streaming playback solution (that plays
            // audio while it continues to be received) should be used -- see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(PullAudioOutputStream activityAudio)
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

                var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. V dalším kroku vytvoříte `DialogServiceConnector` informace o vašem předplatném. Do těla metody přidejte následující `InitializeDialogServiceConnector`, stačí nahradit řetězce `YourChannelSecret`, `YourSpeechSubscriptionKey`, a `YourServiceRegion` vlastními hodnotami pro svého robota, předplatné řeči a [oblasti](regions.md).

    > [!NOTE]
    > Řeči řádku s přímým přístupem (Preview) je momentálně dostupný v podmnožině oblastí s hlasové služby. Najdete [seznam podporovaných oblastí pro virtuálních asistentů hlasové první](regions.md#voice-first-virtual-assistants) a ujistěte se prostředky nasadí v těchto oblastech.

    > [!NOTE]
    > Informace o konfiguraci vašeho robota a načítání tajného klíče kanálu, naleznete v dokumentaci rozhraní Bot Framework pro [kanál s přímým přístupem řádku řeči](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

    ```csharp
    // create a DialogServiceConfig by providing a bot secret key and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret"; // Your channel secret
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region. Note: only a subset of regions are currently supported

    var botConfig = DialogServiceConfig.FromBotSecret(channelSecret, speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

1. `DialogServiceConnector` spoléhá na několik událostí ke komunikaci se svou činnost robota, výsledky rozpoznávání řeči a další informace. Přidání obslužných rutin pro tyto události, přidáním následující na konec těla `InitializeDialogServiceConnector`.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

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
            NotifyUser($"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };
    // Recognizing (not 'Recognized') will provide the intermediate recognized text while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };
    // Recognized (not 'Recognizing') will provide the final recognized text once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };
    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };
    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Konfiguraci a obslužné rutiny událostí zaregistrované `DialogServiceConnector` teď právě potřebuje naslouchat. Přidejte následující text `ListenButton_ButtonClicked` metodu `MainPage` třídy.

    ```csharp
    private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
    {
        if (connector == null)
        {
            InitializeDialogServiceConnector();
            // Optional step to speed up first interaction: if not called, connection happens automatically on first use
            var connectTask = connector.ConnectAsync();
        }

        try
        {
            // Start sending audio to your speech-enabled bot
            var listenTask = connector.ListenOnceAsync();

            // You can also send activities to your bot as JSON strings -- Microsoft.Bot.Schema can simplify this
            string speakActivity = @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
            await connector.SendActivityAsync(speakActivity);

        }
        catch (Exception ex)
        {
            NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
        }
    }
    ```

1. Uložte všechny změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. Vyberte z nabídky panelu Nástroje sady Visual Studio, **sestavení** > **sestavit řešení**. Kód by se teď měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-uwp-08-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. Vyberte z nabídky panelu Nástroje sady Visual Studio, **ladění** > **spustit ladění**, nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-uwp-09-start-debugging.png "Spuštění aplikace s laděním")

1. Otevře se okno. Ve vaší aplikaci, vyberte **povolit mikrofon**a berou na vědomí žádost o oprávnění, která se otevře.

    ![Snímek obrazovky s žádostí o oprávnění](media/sdk/qs-csharp-uwp-10-access-prompt.png "Spuštění aplikace s laděním")

1. Vyberte **obraťte se na svého robota**a Mluvte anglickou fráze nebo větu do vašeho zařízení mikrofon. Hlasu je přenášet do kanálu s přímým přístupem řádku řeči a převede na text, který se zobrazí v okně.

    ![Snímek obrazovky úspěšně bot zapnout](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png "zapnout úspěšné robota")

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření a nasazení základního robota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Další informace najdete v tématech

- [O virtuálních asistentů hlasové první](voice-first-virtual-assistants.md)
- [Získejte klíč předplatného hlasových služeb zdarma](get-started.md)
- [Vlastní probuzení slova](speech-devices-sdk-create-kws.md)
- [Připojení s přímým přístupem řádku řeči pro svého robota](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Prozkoumejte C# ukázky na Githubu](https://aka.ms/csspeech/samples)
