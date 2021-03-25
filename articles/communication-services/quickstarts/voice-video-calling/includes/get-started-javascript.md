---
title: Rychlý Start – přidání volání VOIP do webové aplikace pomocí komunikačních služeb Azure
description: V tomto kurzu se naučíte používat službu Azure Communication Services, která volá sadu SDK pro JavaScript.
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0c70e01aa4f27e40a2de5cddf329cae9ffe261bc
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108257"
---
V tomto rychlém startu se dozvíte, jak spustit volání pomocí komunikačních služeb Azure, které volají sadu SDK pro JavaScript.

> [!NOTE]
> Tento dokument používá verzi 1.0.0-beta. 10 pro volání sady SDK.


## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktivní LTS a verze LTS údržby (Doporučené 8.11.1 a 10.14.1).
- Aktivní prostředek služby Communication Services. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Přístupový token uživatele pro vytvoření instance klienta volání. Naučte se [vytvářet a spravovat tokeny přístupu uživatelů](../../access-tokens.md).


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

Zde je kód:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Vytvořte soubor v kořenovém adresáři vašeho projektu s názvem **client.js** , který bude obsahovat aplikační logiku pro tento rychlý Start. Přidejte následující kód pro import volajícího klienta a získejte odkazy na prvky modelu DOM, abychom mohli připojit naši obchodní logiku. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce volání sady SDK služby Azure Communications:

| Název                             | Description                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient je hlavní vstupní bod pro volání sady SDK.                                                                       |
| CallAgent                        | CallAgent se používá ke spouštění a správě volání.                                                                                            |
| AzureCommunicationTokenCredential | Třída AzureCommunicationTokenCredential implementuje rozhraní CommunicationTokenCredential, které se používá k vytvoření instance CallAgent. |


## <a name="authenticate-the-client"></a>Ověření klienta

Musíte nahradit `<USER_ACCESS_TOKEN>` platným uživatelským tokenem pro přístup k vašemu prostředku. Pokud ještě nemáte k dispozici token, přečtěte si dokumentaci k [tokenu uživatele](../../access-tokens.md) . Pomocí rozhraní `CallClient` inicializujte `CallAgent` instanci s a, `CommunicationTokenCredential` která nám umožní přijímat volání. Přidejte následující kód pro **client.js**:

```javascript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;
}
init();
```

## <a name="start-a-call"></a>Spustit volání

Přidejte obslužnou rutinu události pro zahájení volání při `callButton` kliknutí na tlačítko:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ id: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Ukončení volání

Přidat naslouchací proces události pro ukončení aktuálního volání při kliknutí na `hangUpButton` tlačítko:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

`forEveryone`Vlastnost ukončí volání všech účastníků volání.

## <a name="run-the-code"></a>Spuštění kódu

Použijte `webpack-dev-server` k sestavení a spuštění vaší aplikace. Spusťte následující příkaz pro vytvoření balíčku aplikace Host na místním serveru:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otevřete prohlížeč a přejděte na http://localhost:8080/ . Měli byste vidět následující:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Snímek obrazovky dokončené aplikace JavaScriptu":::

Odchozí volání VOIP můžete vytvořit zadáním ID uživatele v textovém poli a kliknutím na tlačítko **Spustit volání** . Volání se `8:echo123` připojí ke robotu s odezvou. to je skvělé pro začátek a ověření, jestli vaše zvuková zařízení fungují.
