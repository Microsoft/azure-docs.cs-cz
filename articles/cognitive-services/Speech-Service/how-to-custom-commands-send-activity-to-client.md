---
title: Odeslat aktivitu vlastních příkazů klientské aplikaci
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak odeslat aktivitu z aplikace Custom Commands do klientské aplikace, na které běží sada Speech SDK.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52a4dbc4ff01515af8cd7d2503877184a09f7e64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "94566091"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Odeslat aktivitu vlastních příkazů klientské aplikaci

V tomto článku se dozvíte, jak odeslat aktivitu z aplikace Custom Commands do klientské aplikace, na které běží sada Speech SDK.

Dokončili jste následující úkoly:

- Definování a odeslání vlastní datové části JSON z aplikace Custom Commands
- Příjem a vizualizace vlastního obsahu datové části JSON z klientské aplikace sady Speech pro rozpoznávání řeči v C#

## <a name="prerequisites"></a>Požadavky
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) nebo vyšší. Tato příručka používá Visual Studio 2019
> * Klíč předplatného Azure pro službu Speech: [Získejte ho zdarma](overview.md#try-the-speech-service-for-free) nebo si ho vytvořte na webu [Azure Portal](https://portal.azure.com)
> * Už [vytvořená aplikace Vlastní příkazy](quickstart-custom-commands-application.md)
> * Klientská aplikace podporující sadu Speech SDK: [Postupy: integrace s klientskou aplikací pomocí sady Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Nastavit aktivitu odeslání na klienta 
1. Otevřete aplikaci Custom Commands, kterou jste vytvořili dříve.
1. Vyberte příkaz **TurnOnOff** , v části pravidlo dokončování vyberte **ConfirmationResponse** a pak vyberte **přidat akci** .
1. V části **nový typ akce** vyberte **Odeslat aktivitu do klienta** .
1. Zkopírujte obsah JSON níže do **obsahu aktivity** .
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
1. Klikněte na **Uložit** a vytvořte nové pravidlo s akcí odeslat aktivitu, **výukou** a **publikováním** změny.

   > [!div class="mx-imgBorder"]
   > ![Odeslat pravidlo dokončení aktivity](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrace s klientskou aplikací

V tématu [Postupy: nastavení klientské aplikace pomocí sady Speech SDK (Preview)](./how-to-custom-commands-setup-speech-sdk.md)jste vytvořili klientskou aplikaci UWP se sadou Speech SDK, která zpracovává příkazy `turn on the tv` , jako je například `turn off the fan` . V případě přidaných vizuálů vidíte výsledek těchto příkazů.

Chcete-li přidat pole s popiskem s textem, **který označuje nebo** **vypíná**, přidejte následující blok XML StackPanel do `MainPage.xaml` .

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>Přidat knihovny odkazů

Vzhledem k tomu, že jste vytvořili datovou část JSON, je nutné přidat odkaz na knihovnu [JSON.NET](https://www.newtonsoft.com/json) pro zpracování deserializace.

1. Napravo od klienta vaše řešení.
1. Zvolte možnost **Spravovat balíčky NuGet pro řešení**, vyberte **Procházet** . 
1. Pokud jste již nainstalovali **Newtonsoft.jsna**, ujistěte se, že je její verze alespoň 12.0.3. Pokud ne, klikněte na **Spravovat balíčky NuGet pro řešení – aktualizace** a vyhledejte **Newtonsoft.jsna** webu. Tato příručka používá verzi 12.0.3.

    > [!div class="mx-imgBorder"]
    > ![Datová část aktivity odeslání](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Také se ujistěte, že balíček NuGet **Microsoft. NETCore. UniversalWindowsPlatform** je alespoň 6.2.10. Tato příručka používá verzi 6.2.10.

V souboru MainPage. XAML. cs přidejte

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>Zpracovat přijatou datovou část

V `InitializeDialogServiceConnector` , nahraďte `ActivityReceived` obslužnou rutinu události následujícím kódem. Upravená `ActivityReceived` obslužná rutina události extrahuje datovou část z aktivity a změní vizuální stav televizního vysílání nebo ventilátoru.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Vyzkoušet

1. Spuštění aplikace
1. Select Enable microphone (Povolit mikrofon).
1. Vyberte tlačítko pro hlasové ovládání.
1. Vyslovte `turn on the tv`.
1. Vizuální stav televizoru by se měl změnit na zapnuto
   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky, který ukazuje, že je teď vizuální stav na T V.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: nastavení koncových bodů webu (Preview)](./how-to-custom-commands-setup-web-endpoints.md)
