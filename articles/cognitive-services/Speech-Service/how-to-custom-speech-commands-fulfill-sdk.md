---
title: Jak vyplnit příkazy z klienta pomocí sady Speech SDK
titleSuffix: Azure Cognitive Services
description: V tomto článku vysvětlujeme, jak zpracovávat vlastní příkazy v klientovi pomocí sady Speech SDK.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871782"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Doplňování příkazů z klienta pomocí sady Speech SDK (Preview)

Chcete-li dokončit úlohy pomocí vlastní aplikace příkazů, můžete odesílat vlastní datové části do připojeného klientského zařízení.

V tomto článku:

- Definování a odeslání vlastní datové části JSON z aplikace Custom Commands
- Příjem a vizualizace vlastního obsahu datové části JSON z klientské aplikace sady Speech pro rozpoznávání řeči v C#

## <a name="prerequisites"></a>Požadavky
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Klíč předplatného Azure pro službu Speech Service: [Získejte ho zdarma](get-started.md) nebo ho vytvořte na [Azure Portal](https://portal.azure.com)
> * Dříve vytvořená aplikace vlastních příkazů: [rychlý Start: Vytvoření vlastního příkazu s parametry (Preview)](./quickstart-custom-speech-commands-create-parameters.md)
> * Klientská aplikace podporující sadu Speech SDK: [rychlý Start: připojení k vlastnímu příkazu aplikace pomocí sady Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Volitelné: rychlý začátek

Tento článek popisuje, jak vytvořit klientskou aplikaci, aby provedla komunikaci s aplikacemi pro vlastní příkazy. Pokud dáváte přednost podrobněi přímo v, je k dispozici kompletní zdrojový kód připravený k zkompilování, který je použit v tomto článku, v [ukázkách sady Speech SDK](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Plnění s datovou částí JSON

1. Otevřete aplikaci Custom Commands, kterou jste dříve vytvořili v části [rychlý Start: Vytvoření vlastního příkazu s parametry.](./quickstart-custom-speech-commands-create-parameters.md)
1. Podívejte se na část **pravidla dokončování** a ujistěte se, že máte dříve vytvořené pravidlo, které reaguje zpátky na uživatele.
1. Pokud chcete poslat datovou část přímo klientovi, vytvořte nové pravidlo s akcí odeslat aktivitu.

   > [!div class="mx-imgBorder"]
   > ![Odeslat pravidlo dokončení aktivity](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Název pravidla | UpdateDeviceState | Název popisující účel pravidla |
   | Podmínky | Povinný parametr- `OnOff` a`SubjectDevice` | Podmínky, které určují, kdy se pravidlo dá spustit |
   | Akce | `SendActivity`(viz níže) | Akce, která se má provést, když je podmínka pravidla pravdivá |

1. Zkopírujte obsah JSON níže do **obsahu aktivity** .
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![Datová část aktivity odeslání](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>Vytváření vizuálů pro stav zařízení nebo jeho vypnutí

V [rychlém startu: Připojte se k vlastnímu příkazu aplikace pomocí sady Speech SDK](./quickstart-custom-speech-commands-speech-sdk.md), vytvořili jste klientskou aplikaci sady Speech SDK, `turn on the tv`která `turn off the fan`zpracovává příkazy, jako je například. V případě přidaných vizuálů vidíte výsledek těchto příkazů.

Přidat pole s popiskem s textem **, který označuje nebo** **vypíná** pomocí následujícího kódu XML přidaných do`MainPage.xaml`

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
### <a name="add-reference-libraries"></a>Přidat knihovny odkazů

Vzhledem k tomu, že jste vytvořili datovou část JSON, je nutné přidat odkaz na knihovnu [JSON.NET](https://www.newtonsoft.com/json) pro zpracování deserializace.
- Napravo od klienta vaše řešení.
- Zvolte možnost **Spravovat balíčky NuGet pro řešení**, vyberte **instalovat** . 
- V seznamu aktualizace vyhledejte **Newtonsoft. JSON** , aktualizujte **Microsoft. NETCore. UniversalWindowsPlatform** na nejnovější verzi.

> [!div class="mx-imgBorder"]
> ![Datová část aktivity odeslání](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

V souboru MainPage. XAML. cs přidejte
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>Zpracovat obdrženou datovou část

V `InitializeDialogServiceConnector`, nahraďte `ActivityReceived` obslužnou rutinu události následujícím kódem. Upravená `ActivityReceived` obslužná rutina události extrahuje datovou část z aktivity a odpovídajícím způsobem změní vizuální stav televizoru nebo ventilátoru.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

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

## <a name="try-it-out"></a>Vyzkoušejte si to.

1. Spuštění aplikace
1. Vyberte Povolit mikrofon.
1. Výběr tlačítka rozhovor
1. Uvést`turn on the tv`
1. Vizuální stav televizoru by se měl změnit na zapnuto
   > [!div class="mx-imgBorder"]
   > ![Datová část aktivity odeslání](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: Přidání ověření do vlastních parametrů příkazu (Preview)](./how-to-custom-speech-commands-validations.md)
