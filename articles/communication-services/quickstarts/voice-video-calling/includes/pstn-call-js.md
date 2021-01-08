---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 2f884a09e6b51b1c72034a62eaea601a4e69ecd2
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024188"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Telefonní číslo získané v prostředku komunikačních služeb. [Jak získat telefonní číslo](../../telephony-sms/get-phone-number.md).
- A `User Access Token` povolí klienta volání. Další informace o [tom, jak získat `User Access Token` ](../../access-tokens.md)


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
      id="callee-phone-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 230px;"
    />
    <div>
      <button id="call-phone-button" type="button">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-phone-button" type="button" disabled="true">
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
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;

const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential('your-token-here');
    callAgent = await callClient.createCallAgent(tokenCredential);
  //  callButton.disabled = false;
}

init();

```

## <a name="start-a-call-to-phone"></a>Spustit telefonování

Zadejte telefonní číslo, které jste získali v prostředku služby Communications Services, který se použije ke spuštění volání:
> [!WARNING]
> Všimněte si, že telefonní čísla by měla být zadána ve formátu E. 164 mezinárodní standard. (např.: + 12223334444)

Přidejte obslužnou rutinu události pro zahájení volání telefonního čísla, které jste zadali při `callPhoneButton` kliknutí na tlačítko:


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.call(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: 'YOUR AZURE REGISTERED PHONE NUMBER HERE: +12223334444'}
  });
  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>Ukončení volání na telefon

Přidat naslouchací proces události pro ukončení aktuálního volání při kliknutí na `hangUpPhoneButton` tlačítko:

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

`forEveryone`Vlastnost ukončí volání všech účastníků volání.

## <a name="run-the-code"></a>Spuštění kódu

Použijte `webpack-dev-server` k sestavení a spuštění vaší aplikace. Spusťte následující příkaz, který zabalí hostitele aplikace na místním serveru.


```console
npx webpack-dev-server --entry ./client.js --output bundle.js
```

Otevřete prohlížeč a přejděte na `http://localhost:8080/` . Měli byste vidět následující:

:::image type="content" source="../media/javascript/pstn-calling-javascript-app.png" alt-text="Snímek obrazovky dokončené aplikace JavaScriptu":::

Volání do reálného telefonního čísla můžete umístit zadáním telefonního čísla do pole přidané text a kliknutím na tlačítko **Spustit telefonní hovor** .

> [!WARNING]
> Všimněte si, že telefonní čísla by měla být zadána ve formátu E. 164 mezinárodní standard. (např.: + 12223334444)
