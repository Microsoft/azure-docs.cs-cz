---
title: Nastavení koncových bodů webu (Preview)
titleSuffix: Azure Cognitive Services
description: nastavení koncových bodů webu pro vlastní příkazy
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 0197bb81fdba8bab20742d95aebaa2028bb90c18
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027677"
---
# <a name="set-up-web-endpoints"></a>Nastavení webových koncových bodů

V tomto článku se naučíte, jak nastavit koncové body webu v aplikaci s vlastními příkazy, které vám umožní vytvářet požadavky HTTP z klientské aplikace. Provedete následující úlohy:

- Nastavení koncových bodů webu v aplikaci Custom Commands
- Volání webových koncových bodů v aplikaci Custom Commands
- Přijmout odpověď na koncové body webu 
- Integrace odpovědi webových koncových bodů do vlastní datové části JSON, odeslání a vizualizace z klientské aplikace sady Speech pro rozpoznávání řeči v C#

## <a name="prerequisites"></a>Požadavky
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Klíč předplatného Azure pro službu Speech Service: [Získejte ho zdarma](get-started.md) nebo ho vytvořte na [Azure Portal](https://portal.azure.com)
> * Dříve [vytvořená aplikace vlastních příkazů](quickstart-custom-commands-application.md)
> * Klientská aplikace s povolenou sadou Speech: [Postup: ukončení aktivity klientské aplikace](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Nastavení koncových bodů webu

1. Otevřete aplikaci Custom Commands, kterou jste vytvořili dříve. 
1. Přejděte do části webové koncové body, klikněte na nový webový koncový bod.

   > [!div class="mx-imgBorder"]
   > ![Nový webový koncový bod](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Name | UpdateDeviceState | Název webového koncového bodu |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | Adresa URL koncového bodu, ke kterému se má vaše aplikace pro vlastní příkazy mluvit |
   | Metoda | POST | Povolené interakce (například GET, POST) s vaším koncovým bodem.|
   | Hlavičky | Klíč: App, Value: Vezměte prvních 8 číslic vaší hodnoty applicationId. | Parametry záhlaví, které mají být zahrnuty v hlavičce požadavku.|

    > [!NOTE]
    > - Příklad webového koncového bodu vytvořeného pomocí [funkce Azure Functions](https://docs.microsoft.com/azure/azure-functions/), která se zastavuje s databází, která ukládá stav zařízení televizoru a ventilátoru
    > - Navrhovaná hlavička je nutná jenom pro ukázkový koncový bod.
    > - Chcete-li se ujistit, že hodnota záhlaví je v našem ukázkovém koncovém bodu jedinečná, vezměte prvních 8 číslic vaší hodnoty applicationId.
    > - V reálném světě může být webový koncový bod koncovým bodem služby [IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) , který spravuje vaše zařízení.

1. Klikněte na **Uložit**.

## <a name="call-web-endpoints"></a>Volání webových koncových bodů

1. Do příkazového řádku **TurnOnOff** vyberte v části pravidlo dokončení možnost **ConfirmationResponse** a pak vyberte **přidat akci**.
1. V části **nový typ akce**vyberte **zavolat webový koncový bod** .
1. V části **Upravit akci – koncové body**vyberte **UpdateDeviceState**, což je webový koncový bod, který jsme vytvořili.  
1. V části **Konfigurace**zadejte následující hodnoty: 
   > [!div class="mx-imgBorder"]
   > ![Volání parametrů akcí webových koncových bodů](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Koncové body | UpdateDeviceState | Koncový bod webu, který chcete zavolat v této akci. |
   | Parametry dotazů | Item = {SubjectDevice} &&Value = {(OnOff)} | Parametry dotazu, které se mají připojit k adrese URL webového koncového bodu  |
   | Obsah těla | Není k dispozici | Obsah těla žádosti |

    > [!NOTE]
    > - Navrhované parametry dotazu jsou potřebné jenom pro ukázkový koncový bod.

1. V případě **úspěchu – akce, která se má provést**, vyberte **Odeslat odezvu řeči**.
    
    V **jednoduchém editoru**zadejte `{SubjectDevice} is {OnOff}` .
   
   > [!div class="mx-imgBorder"]
   > ![Akce webových koncových bodů volání při úspěchu](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Akce, která se má provést | Odeslat odezvu na řeč | Akce, která se má provést, pokud se požadavek na koncový bod webu zdaří |
   
   > [!NOTE]
   > - K polím v odpovědi HTTP můžete také přímo přistupovat pomocí `{YourWebEndpointName.FieldName}` . Příklad: `{UpdateDeviceState.TV}`

1. V případě **selhání – akce, která se má provést**, vyberte **Odeslat odezvu řeči** .

    V **jednoduchém editoru**zadejte `Sorry, {WebEndpointErrorMessage}` .

   > [!div class="mx-imgBorder"]
   > ![Akce webových koncových bodů volání při selhání](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Akce, která se má provést | Odeslat odezvu na řeč | Akce, která se spustí v případě, že se požadavek na koncový bod webu nezdařil |

   > [!NOTE]
   > - Parametr `{WebEndpointErrorMessage}` je volitelný. Pokud nechcete zobrazit žádnou chybovou zprávu, můžete ji odebrat.
   > - V našem ukázkovém koncovém bodě pošleme odpověď HTTP s podrobnými chybovými zprávami pro běžné chyby, jako jsou například chybějící parametry hlaviček. 

### <a name="try-it-out-in-test-portal"></a>Vyzkoušet na testovacím portálu
- Při úspěšné odezvě \
Uložení, výuka a testování
   > [!div class="mx-imgBorder"]
   > ![Akce webových koncových bodů volání při úspěchu](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Při neúspěšné odezvě \
Odebrání jednoho z parametrů dotazu, uložení, reučení a testování
   > [!div class="mx-imgBorder"]
   > ![Akce webových koncových bodů volání při úspěchu](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrace s klientskou aplikací

V tématu [Postup: odeslání aktivity do klientské aplikace (Preview)](./how-to-custom-commands-send-activity-to-client.md)jste přidali **aktivitu odeslat do akce klienta** . Aktivita se pošle do klientské aplikace bez ohledu na to, jestli je akce **webového koncového bodu** úspěšná nebo ne.
Ve většině případů ale přejete si, aby klientská aplikace odesílala aktivity pouze v případě, že volání webového koncového bodu je úspěšné. V tomto příkladu se jedná o úspěšnou aktualizaci stavu zařízení.

1. Odstraňte **aktivitu odeslat do akce klienta** , kterou jste přidali dříve.
1. Upravit koncový bod webu volání: 
    1. V **konfiguraci**se ujistěte, že jsou **parametry dotazu**`item={SubjectDevice}&&value={OnOff}`
    1. V případě **úspěchu**změňte **akci, která se má provést** pro **odeslání aktivity klientovi** .
    1. Zkopírujte následující kód JSON do **obsahu aktivity** .
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![Odeslat aktivitu po úspěchu](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
Po úspěšném dokončení požadavku na koncový bod webu je teď potřeba do klienta odesílat jenom aktivity.

### <a name="create-visuals-for-syncing-device-state"></a>Vytváření vizuálů pro synchronizaci stavu zařízení
Přidejte následující kód XML do `MainPage.xaml` výše uvedeného `"EnableMicrophoneButton"` bloku.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Synchronizovat stav zařízení 

V `MainPage.xaml.cs` přidejte odkaz `using Windows.Web.Http;` . Do třídy přidejte následující kód `MainPage` . Tato metoda pošle do ukázkového koncového bodu požadavek GET a extrahuje aktuální stav zařízení pro vaši aplikaci. Nezapomeňte změnit to, `<your_app_name>` co jste použili v **záhlaví** ve vlastním příkazovém řádku vlastního příkazu.

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

## <a name="try-it-out"></a>Vyzkoušejte si to.

1. Spuštění aplikace
1. Klikněte na synchronizovat stav zařízení. \
Pokud jste otestovali aplikaci `turn on tv` v předchozí části, zobrazí se televizní program jako zapnutý.
    > [!div class="mx-imgBorder"]
    > ![Synchronizovat stav zařízení](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Vyberte Povolit mikrofon.
1. Výběr tlačítka rozhovor
1. Uvést`turn on the fan`
1. Vizuální stav ventilátoru by se měl změnit na zapnuto.
    > [!div class="mx-imgBorder"]
    > ![Zapnout ventilátor](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Povolení procesu CI/CD pro vlastní aplikace příkazů](./how-to-custom-commands-deploy-cicd.md)