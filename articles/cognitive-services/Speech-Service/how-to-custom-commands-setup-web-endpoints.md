---
title: Nastavení webových koncových bodů (Preview)
titleSuffix: Azure Cognitive Services
description: nastavení webových koncových bodů pro vlastní příkazy
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 6f2dfdbb5833b34441b4abba7359ad70c4717d1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602155"
---
# <a name="set-up-web-endpoints"></a>Nastavení webových koncových bodů

V tomto článku se naučíte, jak v aplikaci Vlastní příkazy nastavit webové koncové body, které umožňují vytvářet požadavky HTTP z klientské aplikace. Provedete následující úkoly:

- Nastavení webových koncových bodů webu v aplikaci Vlastní příkazy
- Volání webových koncových bodů webu v aplikaci Vlastní příkazy
- Příjem odpovědí webových koncových bodů
- Integrace odpovědí webových koncových bodů do vlastní datové části JSON, odeslání a vizualizace z klientské aplikace Speech SDK pro UPW v C#

## <a name="prerequisites"></a>Požadavky
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Klíč předplatného Azure pro službu Speech: [Získejte ho zdarma](overview.md#try-the-speech-service-for-free) nebo si ho vytvořte na webu [Azure Portal](https://portal.azure.com)
> * Už [vytvořená aplikace Vlastní příkazy](quickstart-custom-commands-application.md)
> * Klientská aplikace s podporou sady Speech SDK: [Postupy: Odeslání aktivity do klientské aplikace](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Nastavení webových koncových bodů

1. Otevřete aplikaci Vlastní příkazy, kterou jste si vytvořili dříve.
1. Přejděte na Webové koncové body a klikněte na Nový webový koncový bod.

   > [!div class="mx-imgBorder"]
   > ![Nový webový koncový bod](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Název | UpdateDeviceState | Název webového koncového bodu |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | Adresa URL koncového bodu, se ke kterým má vaše aplikace Vlastní příkazy komunikovat |
   | Metoda | POST | Povolené interakce s vaším koncovým bodem (jako jsou GET, POST)|
   | Hlavičky | Klíč: aplikace, hodnota: použijte prvních 8 číslic hodnoty applicationId | Parametry hlavičky, které mají být zahrnuté v hlavičce požadavku|

    > [!NOTE]
    > - Ukázkový webový koncový bod je vytvořený pomocí [Azure Functions](../../azure-functions/index.yml). Připojí se k databázi, ve které se ukládá stav televizoru a ventilátoru.
    > - Navrhovaná hlavička je nutná jenom pro ukázkový koncový bod.
    > - Pokud chcete zajistit, že hodnota hlavičky je v našem ukázkovém koncovém bodu jedinečná,použijte prvních 8 číslic hodnoty applicationId.
    > - Webovým koncovým bodem může v reálném světě být koncový bod pro [centrum IOT](../../iot-hub/about-iot-hub.md), které spravuje vaše zařízení.

1. Klikněte na **Uložit**.

## <a name="call-web-endpoints"></a>Volání webových koncových bodů

1. Přejděte na příkaz **TurnOnOff**, jako pravidlo dokončení vyberte **ConfirmationResponse** a potom vyberte **Přidat akci**.
1. V části **New Action-Type** (Nový typ akce) vyberte **Call web endpoint** (Volání koncového bodu).
1. V části **Edit Action - Endpoints** (Akce pro úpravy – koncové body) vyberte **UpdateDeviceState**. To je webový koncový bod, který jsme vytvořili.  
1. V části **Configuration** (Konfigurace) zadejte následující hodnoty:
   > [!div class="mx-imgBorder"]
   > ![Parametry akce volání koncových bodů](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Koncové body | UpdateDeviceState | Webový koncový bod, který chcete volat v této akci |
   | Parametry dotazů | item={SubjectDevice}&&value={OnOff} | Parametry dotazu, které se mají připojit k adrese URL webového koncového bodu  |
   | Textový obsah | – | Textový obsah požadavku |

    > [!NOTE]
    > - Navrhované parametry dotazů jsou nutné jenom pro ukázkový koncový bod.

1. V části **On Success - Action to execute** (Při úspěchu – Akce, která se má provést) vyberte **Send speech response** (Odeslat odpověď v řeči).

    V části **Simple editor** (Jednoduchý editor) zadejte `{SubjectDevice} is {OnOff}`.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky, který zobrazuje akci při úspěchu – spuštění](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Akce, která se má provést | Send speech response (Odeslat odpověď v řeči) | Akce, která se má provést, pokud se požadavek na webový koncový bod zdaří |

   > [!NOTE]
   > - K polím v odpovědi HTTP můžete také získat přímý přístup pomocí `{YourWebEndpointName.FieldName}`. Příklad: `{UpdateDeviceState.TV}`

1. V části **On Failure - Action to execute** (Při neúspěchu – Akce, která se má provést) vyberte **Send speech response** (Odeslat odpověď v řeči).

    V části **Simple editor** (Jednoduchý editor) zadejte `Sorry, {WebEndpointErrorMessage}`.

   > [!div class="mx-imgBorder"]
   > ![Akce volání koncových bodů při neúspěchu](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Akce, která se má provést | Send speech response (Odeslat odpověď v řeči) | Akce, která se má provést, pokud se požadavek na webový koncový bod nezdaří |

   > [!NOTE]
   > - Parametr `{WebEndpointErrorMessage}` je volitelný. Pokud nechcete zpřístupnit žádnou chybovou zprávu, můžete ho bez obav odebrat.
   > - V rámci našeho ukázkového koncového bodu vracíme odpověď HTTP s podrobnými chybovými zprávami pro běžné chyby, jako jsou například chybějící parametry hlavičky.

### <a name="try-it-out-in-test-portal"></a>Vyzkoušení na testovacím portálu
- Odpověď při úspěchu:
Uložení, natrénování a testování
   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky, který zobrazuje odpověď po úspěšném dokončení.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Odpověď při úspěchu:
Odebrání jednoho z parametrů dotazu, uložení, přetrénování a testování
   > [!div class="mx-imgBorder"]
   > ![Akce volání koncových bodů při úspěchu](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrace s klientskou aplikací

V části [Postupy: Odeslání aktivity do klientské aplikace (Preview)](./how-to-custom-commands-send-activity-to-client.md) jste přidali akci **Send activity to client** (Odeslání aktivity do klientské aplikace). Aktivita se odešle do klientské aplikace bez ohledu na to, jestli je akce **volání webového koncového bodu** úspěšná.
Ve většině případů ale chcete posílat aktivitu do klientské aplikace jenom v případě, že volání webového koncového bodu je úspěšné. V tomto příkladu je to tehdy, když se úspěšně aktualizuje stav zařízení.

1. Odstraňte dříve přidanou akci **Send activity to client** (Odeslání aktivity do klientské aplikace).
1. Upravte volání webového koncového bodu:
    1. V části **Konfigurace** zkontrolujte, že hodnota **Parametry dotazu** je `item={SubjectDevice}&&value={OnOff}`
    1. V části **On Success** (Při úspěchu) změňte **Action to execute** (Akce, která se má provést) na **Send activity to client** (Odeslání aktivity do klientské aplikace).
    1. Následující kód JSON zkopírujte do části **Activity Content** (Obsah aktivity).
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
Teď odesíláte aktivitu do klientské aplikace jenom v případě, že požadavek na webový koncový bod je úspěšný.

### <a name="create-visuals-for-syncing-device-state"></a>Vytváření vizuálů pro synchronizaci stavu zařízení
Následující kód XML přidejte do části `MainPage.xaml` nad blokem `"EnableMicrophoneButton"`.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Synchronizace stavu zařízení

V části `MainPage.xaml.cs` přidejte odkaz `using Windows.Web.Http;`. Do třídy `MainPage` přidejte následující kód. Tato metoda pošle do ukázkového koncového bodu požadavek GET a extrahuje aktuální stav zařízení pro vaši aplikaci. Nezapomeňte změnit parametr `<your_app_name>` na hodnotu použitou v **hlavičce** webového koncového bodu pro Vlastní příkazy.

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Vyzkoušet

1. Spuštění aplikace
1. Klikněte na Sync Device State (Synchronizovat stav zařízení).
Pokud jste vaši aplikaci otestovali s parametrem `turn on tv` v předchozí sekci, televizor se bude zobrazovat jako zapnutý.
    > [!div class="mx-imgBorder"]
    > ![Synchronizace stavu zařízení](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Select Enable microphone (Povolit mikrofon).
1. Vyberte tlačítko pro hlasové ovládání.
1. Vyslovte `turn on the fan`.
1. Vizuální stav ventilátoru by se měl změnit na zapnuto.
    > [!div class="mx-imgBorder"]
    > ![Zapnutí ventilátoru](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Exportovat aplikaci Custom Commands jako vzdálenou dovednost](./how-to-custom-commands-integrate-remote-skills.md)

