---
title: Jak vyplnit příkazy z klienta pomocí sady Speech SDK
titleSuffix: Azure Cognitive Services
description: V tomto článku vysvětlujeme, jak zpracovávat vlastní příkazy v klientovi pomocí sady Speech SDK.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79367734"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Doplňování příkazů z klienta pomocí sady Speech SDK (Preview)

Chcete-li dokončit úlohy pomocí vlastní aplikace příkazů, můžete odesílat vlastní datové části do připojeného klientského zařízení.

V tomto článku:

- Definování a odeslání vlastní datové části JSON z aplikace Custom Commands
- Příjem a vizualizace vlastního obsahu datové části JSON z klientské aplikace sady Speech pro rozpoznávání řeči v C#

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Klíč předplatného Azure pro službu Speech Service
  - [Získejte ho zdarma](get-started.md) nebo ho vytvořte na [Azure Portal](https://portal.azure.com)
- Dříve vytvořená aplikace vlastních příkazů
  - [Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview)](./quickstart-custom-speech-commands-create-parameters.md)
- Klientská aplikace podporující sadu Speech SDK
  - [Rychlý Start: připojení k vlastnímu příkazu aplikace pomocí sady Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Volitelné: rychlý začátek

Tento článek popisuje, jak vytvořit klientskou aplikaci, aby provedla komunikaci s aplikacemi pro vlastní příkazy. Pokud dáváte přednost podrobněi přímo v, je k dispozici kompletní zdrojový kód připravený k zkompilování, který je použit v tomto článku, v [ukázkách sady Speech SDK](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Plnění s datovou částí JSON

1. Otevřete dříve vytvořenou aplikaci Custom Commands z nástroje [Speech Studio](https://speech.microsoft.com/)
1. Podívejte se na část **pravidla dokončování** a ujistěte se, že máte dříve vytvořené pravidlo, které reaguje zpátky na uživatele.
1. Pokud chcete poslat datovou část přímo klientovi, vytvořte nové pravidlo s akcí odeslat aktivitu.

   > [!div class="mx-imgBorder"]
   > ![Odeslat pravidlo dokončení aktivity](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Název pravidla | UpdateDeviceState | Název popisující účel pravidla |
   | Podmínky | Povinný parametr- `OnOff` a`SubjectDevice` | Podmínky, které určují, kdy se pravidlo dá spustit |
   | Akce | `SendActivity`(viz níže) | Akce, která se má provést, když je podmínka pravidla pravdivá |

   > [!div class="mx-imgBorder"]
   > ![Datová část aktivity odeslání](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Vytváření vizuálů pro stav zařízení nebo jeho vypnutí

V [rychlém startu: Připojte se k vlastnímu příkazu aplikace pomocí sady Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md) , kterou jste vytvořili jako klientskou aplikaci pro `turn on the tv`sadu `turn off the fan`Speech SDK, která zpracovává příkazy, jako je například. Teď přidejte nějaké vizuály, abyste mohli vidět výsledek těchto příkazů.

Přidat pole s popiskem s textem **, který označuje nebo** **vypíná** pomocí následujícího kódu XML přidaných do`MainPage.xaml.cs`

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

## <a name="handle-customizable-payload"></a>Zpracovat přizpůsobitelnou datovou část

Teď, když jste vytvořili datovou část JSON, můžete přidat odkaz na knihovnu [JSON.NET](https://www.newtonsoft.com/json) pro zpracování deserializace.

> [!div class="mx-imgBorder"]
> ![Datová část aktivity odeslání](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Do `InitializeDialogServiceConnector` obslužné rutiny `ActivityReceived` události přidejte následující. Další kód extrahuje datovou část z aktivity a odpovídajícím způsobem změní vizuální stav televizního vysílání nebo ventilátoru.

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
1. Vyberte Povolit mikrofon.
1. Výběr tlačítka rozhovor
1. Uvést`turn on the tv`
1. Vizuální stav televizoru by se měl změnit na zapnuto

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: Přidání ověření do vlastních parametrů příkazu (Preview)](./how-to-custom-speech-commands-validations.md)
