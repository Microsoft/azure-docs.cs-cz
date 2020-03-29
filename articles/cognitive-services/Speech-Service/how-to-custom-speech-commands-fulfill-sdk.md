---
title: Jak splnit příkazy od klienta pomocí sady Speech SDK
titleSuffix: Azure Cognitive Services
description: V tomto článku vysvětlujeme, jak zpracovat vlastní příkazy aktivity na klienta s sadou Speech SDK.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367734"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Plnit příkazy od klienta pomocí sady Speech SDK (Preview)

Chcete-li dokončit úkoly pomocí aplikace Vlastní příkazy, můžete odeslat vlastní datové části do připojeného klientského zařízení.

V tomto článku budete:

- Definování a odeslání vlastní datové části JSON z aplikace Vlastní příkazy
- Příjem a vizualizace vlastního obsahu datové části JSON z klientské aplikace sady C# UWP Speech SDK

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Klíč předplatného Azure pro službu Speech
  - [Získejte jeden zdarma](get-started.md) nebo si ho vytvořte na [webu Azure Portal](https://portal.azure.com)
- Dříve vytvořená aplikace Vlastní příkazy
  - [Úvodní příručka: Vytvoření vlastního příkazu s parametry (náhled)](./quickstart-custom-speech-commands-create-parameters.md)
- Klientská aplikace s povolenou sadou Speech SDK
  - [Úvodní příručka: Připojení k aplikaci Custom Command pomocí sady Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Volitelné: Rychlé zahájení

Tento článek popisuje krok za krokem, jak vytvořit klientskou aplikaci pro rozhovor s aplikací Vlastní příkazy. Pokud dáváte přednost ponořit přímo do, kompletní, připravený ke kompilaci zdrojový kód použitý v tomto článku je k dispozici v [řeči SDK ukázky](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Splňte s datovou částí JSON

1. Otevření dříve vytvořené aplikace Vlastní příkazy z [aplikace Speech Studio](https://speech.microsoft.com/)
1. Zkontrolujte část **Pravidla dokončení** a ujistěte se, že máte dříve vytvořené pravidlo, které reaguje zpět na uživatele.
1. Chcete-li odeslat datovou část přímo klientovi, vytvořte nové pravidlo pomocí akce Odeslat aktivitu.

   > [!div class="mx-imgBorder"]
   > ![Odeslat pravidlo dokončení aktivity](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Název pravidla | AktualizovatStav zařízení | Název popisující účel pravidla |
   | Podmínky | Povinný parametr `OnOff` - a`SubjectDevice` | Podmínky, které určují, kdy může být pravidlo spuštěno |
   | Akce | `SendActivity`(viz níže) | Akce, která má být v případě, že je splněna podmínka pravidla |

   > [!div class="mx-imgBorder"]
   > ![Odeslat datovou část aktivity](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Vytváření vizuálů pro zařízení ve stavu zapnutí nebo vypnutí

V [úvodním seznamu: Připojení k aplikaci custom command pomocí sady Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md) jste `turn on the tv` `turn off the fan`vytvořili klientskou aplikaci sady Speech SDK, která zpracovávala příkazy, jako je například . Nyní přidejte některé vizuály, abyste viděli výsledek těchto příkazů.

Přidání polí s popiskem s textem označujícím **zapnuto** nebo **vypnuto** pomocí následujícího xml přidaného do`MainPage.xaml.cs`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Zpracování přizpůsobitelné datové části

Teď, když jste vytvořili datovou část JSON, můžete přidat odkaz na [knihovnu JSON.NET](https://www.newtonsoft.com/json) pro zpracování rekonstruované.

> [!div class="mx-imgBorder"]
> ![Odeslat datovou část aktivity](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

V `InitializeDialogServiceConnector` přidejte následující `ActivityReceived` do obslužné rutiny události. Další kód extrahuje užitečné zatížení z aktivity a odpovídajícím způsobem změní vizuální stav televizoru nebo ventilátoru.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Vyzkoušejte si to.

1. Spuštění aplikace
1. Vybrat možnost Povolit mikrofon
1. Výběr tlačítka Talk
1. Říct`turn on the tv`
1. Vizuální stav televizoru by se měl změnit na "Zapnuto"

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postup: Přidání ověření do parametrů vlastního příkazu (náhled)](./how-to-custom-speech-commands-validations.md)
