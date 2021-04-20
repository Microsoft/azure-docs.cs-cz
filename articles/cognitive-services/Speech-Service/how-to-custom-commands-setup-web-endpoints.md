---
title: Nastavení webových koncových bodů
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
ms.openlocfilehash: 5f1d5318140dd14c5024e8dd3ad0def0afc7f378
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725903"
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
> * Vlastní aplikace příkazů (viz [Vytvoření hlasového asistenta s použitím vlastních příkazů](quickstart-custom-commands-application.md))
> * Klientská aplikace podporující sadu Speech SDK (viz [integrace s klientskou aplikací s využitím sady Speech SDK](how-to-custom-commands-setup-speech-sdk.md))

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Nasazení externího koncového bodu webu pomocí aplikace Azure Function App

Pro účely tohoto kurzu potřebujete koncový bod HTTP, který uchovává stavy pro všechna zařízení, která jste nastavili v příkazu **TurnOnOff** vaší aplikace pro vlastní příkazy.

Pokud již máte webový koncový bod, který chcete volat, přejděte k [Další části](#setup-web-endpoints-in-custom-commands). Případně další část poskytuje podrobné informace o výchozím hostovaném koncovém bodu webu, který můžete použít, pokud chcete tuto část přeskočit.

### <a name="input-format-of-azure-function"></a>Vstupní formát funkce Azure Functions

V dalším kroku nasadíte koncový bod pomocí [Azure Functions](../../azure-functions/index.yml).
Následuje formát události vlastního příkazu, která je předána funkci Azure Functions. Tyto informace použijte při psaní aplikace funkce Azure Functions.

```json
{
  "conversationId": "string",
  "currentCommand": {
    "name": "string",
    "parameters": {
      "SomeParameterName": "string",
      "SomeOtherParameterName": "string"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "string",
      "SomeOtherGlobalParameterName": "string"
  }
}
```

    
Následující tabulka popisuje klíčové atributy tohoto vstupu:
        
| Atribut | Vysvětlení |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | Jedinečný identifikátor konverzace Všimněte si, že toto ID může vytvořit klientská aplikace. |
| **currentCommand** | Příkaz, který je aktuálně aktivní v konverzaci. |
| **Jméno** | Název příkazu `parameters`Atribut je mapa s aktuálními hodnotami parametrů. |
| **currentGlobalParameters** | Mapa, jako `parameters` je, která se používá pro globální parametry. |


Pro funkci **DeviceState** Azure Functions bude mít ukázková událost, která bude vypadat jako v následujícím příkladu. Bude fungovat jako **vstup** do aplikace Function App.
    
```json
{
  "conversationId": "someConversationId",
  "currentCommand": {
    "name": "TurnOnOff",
    "parameters": {
      "item": "tv",
      "value": "on"
    }
  }
}
```

### <a name="azure-function-output-for-a-custom-command-app"></a>Výstup funkce Azure pro vlastní aplikaci příkazu

Pokud je výstup z vaší funkce Azure spotřebovaný vlastní aplikací příkazů, měl by se zobrazit v následujícím formátu. Podrobnosti najdete v tématu [aktualizace příkazu z webového koncového bodu](./how-to-custom-commands-update-command-from-web-endpoint.md) .

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

### <a name="azure-function-output-for-a-client-application"></a>Výstup funkce Azure pro klientskou aplikaci

Pokud klientská aplikace spotřebovává výstup z vaší funkce Azure, může výstup převzít jakékoli formuláře, které klientská aplikace vyžaduje.

Pro náš koncový bod **DeviceState** je výstup funkce Azure využíván klientskou aplikací namísto aplikace Custom Commands. Příklad výstupu funkce Azure Function by měl vypadat takto:
    
```json
{
  "TV": "on",
  "Fan": "off"
}
``` 

Tento výstup by měl být zapsaný do externího úložiště, abyste mohli udržovat stav zařízení. Stav externího úložiště se použije v níže uvedené části [integrace s klientskou aplikací](#integrate-with-client-application) .


### <a name="deploy-azure-function"></a>Nasazení funkce Azure Functions

Poskytujeme ukázku, kterou můžete nakonfigurovat a nasadit jako aplikaci Azure Functions. Pokud chcete vytvořit účet úložiště pro naši ukázku, postupujte podle těchto kroků.
 
1. Vytvořte úložiště tabulek pro uložení stavu zařízení. V Azure Portal vytvořte nový prostředek typu **účet úložiště** podle názvu **devicestate**.
1. Zkopírujte hodnotu **připojovacího řetězce** z **Devicestate-> přístupových klíčů**. Do staženého ukázkového Function App kódu budete muset přidat tento řetězec s tajnými klíči.
1. Stáhněte si ukázkový [kód Function App](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/custom-commands/quick-start).
1. Otevřete stažené řešení v aplikaci Visual Studio 2019. V **Connections.jszapnuto**, nahraďte **STORAGE_ACCOUNT_SECRET_CONNECTION_STRING** tajným klíčem z kroku 2.
1.  Stáhněte si kód **DeviceStateAzureFunction** .

Pokud chcete nasadit ukázkovou aplikaci Azure Functions, postupujte podle těchto kroků.

1. [Nasaďte](../../azure-functions/index.yml) aplikaci Azure Functions.
1. Počkejte na úspěšné nasazení a přejít na nasazený prostředek na Azure Portal. 
1. V levém podokně vyberte **funkce** a pak vyberte **DeviceState**.
1.  V novém okně vyberte **kód + test** a pak vyberte **získat adresu URL funkce**.
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Nastavení koncových bodů webu ve vlastních příkazech

Pojďme službu Azure Functions připojit k existující aplikaci Custom Commands.
V této části použijete existující výchozí koncový bod **DeviceState** . Pokud jste vytvořili vlastní webový koncový bod pomocí funkce Azure Functions nebo jinak, použijte místo výchozího `https://webendpointexample.azurewebsites.net/api/DeviceState` .

1. Otevřete aplikaci Vlastní příkazy, kterou jste si vytvořili dříve.
1. Přejděte na **koncové body webu**, klikněte na **Nový webový koncový bod**.

   > [!div class="mx-imgBorder"]
   > ![Nový webový koncový bod](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | --------------- | ----------- |
   | Název | UpdateDeviceState | Název webového koncového bodu |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | Adresa URL koncového bodu, se ke kterým má vaše aplikace Vlastní příkazy komunikovat |
   | Metoda | POST | Povolené interakce s vaším koncovým bodem (jako jsou GET, POST)|
   | Hlavičky | Klíč: aplikace, hodnota: použijte prvních 8 číslic hodnoty applicationId | Parametry hlavičky, které mají být zahrnuté v hlavičce požadavku|

    > [!NOTE]
    > - Ukázkový koncový bod webu vytvořený pomocí [Azure Functions](../../azure-functions/index.yml), který se připojí k databázi, která uloží stav zařízení televizoru a ventilátoru.
    > - Navrhovaná hlavička je nutná jenom pro ukázkový koncový bod.
    > - Chcete-li se ujistit, že hodnota záhlaví je v našem ukázkovém koncovém bodu jedinečná, vezměte prvních 8 číslic svého identifikátoru **ApplicationId**.
    > - V reálném světě může být webový koncový bod koncovým bodem služby [IoT Hub](../../iot-hub/about-iot-hub.md) , který spravuje vaše zařízení.

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
- Na reakci na úspěch, uložení, školení a testování.
   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky, který zobrazuje odpověď po úspěšném dokončení.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- V případě neúspěšné reakce odeberte jeden z parametrů dotazu, uložte, reučení a otestujte.
   > [!div class="mx-imgBorder"]
   > ![Akce volání koncových bodů při úspěchu](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrace s klientskou aplikací

V rámci [možnosti odesílat vlastní příkazy klientské aplikaci](./how-to-custom-commands-send-activity-to-client.md)jste přidali **aktivitu odeslat do akce klienta** . Aktivita se odešle do klientské aplikace bez ohledu na to, jestli je akce **volání webového koncového bodu** úspěšná.
Obvykle však chcete, aby klientská aplikace odesílala aktivity pouze v případě, že volání webového koncového bodu je úspěšné. V tomto příkladu je to tehdy, když se úspěšně aktualizuje stav zařízení.

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
Po úspěšném požadavku na koncový bod webu je teď potřeba do klienta odesílat jenom aktivity.

### <a name="create-visuals-for-syncing-device-state"></a>Vytváření vizuálů pro synchronizaci stavu zařízení

Do `MainPage.xaml` výše uvedeného bloku **EnableMicrophoneButton** přidejte následující kód XML.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Synchronizace stavu zařízení

V části `MainPage.xaml.cs` přidejte odkaz `using Windows.Web.Http;`. Do třídy `MainPage` přidejte následující kód. Tato metoda pošle do ukázkového koncového bodu požadavek GET a extrahuje aktuální stav zařízení pro vaši aplikaci. Nezapomeňte změnit to, `<your_app_name>` co jste použili v **záhlaví** ve vlastním příkazovém koncovém panelu příkazů.

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

1. Spusťte aplikaci.
1. Klikněte na Sync Device State (Synchronizovat stav zařízení).
Pokud jste otestovali aplikaci `turn on tv` v předchozí části, měli byste vidět televizní pořad. 
    > [!div class="mx-imgBorder"]
    > ![Synchronizace stavu zařízení](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Vyberte **Povolit mikrofon**.
1. Vyberte tlačítko **rozhovor** .
1. Řekněme `turn on the fan` . Vizuální stav ventilátoru by se měl změnit na **zapnuto**.
    > [!div class="mx-imgBorder"]
    > ![Zapnutí ventilátoru](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Exportovat aplikaci Custom Commands jako vzdálenou dovednost](./how-to-custom-commands-integrate-remote-skills.md)
