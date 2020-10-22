---
title: Rychlý Start – přidání volání VOIP do webové aplikace pomocí komunikačních služeb Azure
description: V tomto kurzu se dozvíte, jak používat komunikační služby Azure, které volají klientskou knihovnu pro JavaScript.
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 652566efda4d4f274dc5700d35bcf45c1ebfb9e2
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92346951"
---
V tomto rychlém startu se dozvíte, jak spustit volání pomocí komunikačních služeb Azure, které volají klientskou knihovnu pro JavaScript.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktivní LTS a verze LTS údržby (Doporučené 8.11.1 a 10.14.1).
- Aktivní prostředek služby Communication Services. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Přístupový token uživatele pro vytvoření instance klienta volání. Naučte se [vytvářet a spravovat tokeny přístupu uživatelů](../../access-tokens.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Otevřete okno terminálu nebo příkazového řádku pro svoji aplikaci vytvořte nový adresář a přejděte do něj.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Spusťte `npm init -y` , chcete-li vytvořit **package.js** pro soubor s výchozími nastaveními.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalace balíčku

Pomocí `npm install` příkazu nainstalujte pro JavaScript službu Azure Communication Services volání klientské knihovny.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Pro tento rychlý Start jsou doporučovány následující verze nástroje Webpack:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

V tomto rychlém startu se k vytvoření sady prostředků aplikace používá Webpack. Spusťte následující příkaz pro instalaci balíčků Webpack, Webpack-CLI a Webpack-dev-Server npm a seznamte se s nimi jako se závislostmi vývoje v **package.jsna**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Vytvořte soubor **index.html** v kořenovém adresáři projektu. Tento soubor použijeme ke konfiguraci základního rozložení, které umožní uživateli umístit na robota komunikace Azure.

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
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce komunikačních služeb Azure, které volají klientskou knihovnu:

| Název                             | Popis                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient je hlavní vstupní bod pro volání klientské knihovny.                                                                       |
| CallAgent                        | CallAgent se používá ke spouštění a správě volání.                                                                                            |
| AzureCommunicationUserCredential | Třída AzureCommunicationUserCredential implementuje rozhraní CommunicationUserCredential, které se používá k vytvoření instance CallAgent. |


## <a name="authenticate-the-client"></a>Ověření klienta

Musíte nahradit `<USER_ACCESS_TOKEN>` platným uživatelským tokenem pro přístup k vašemu prostředku. Pokud ještě nemáte k dispozici token, přečtěte si dokumentaci k [tokenu uživatele](../../access-tokens.md) . Pomocí rozhraní `CallClient` inicializujte `CallAgent` instanci s a, `CommunicationUserCredential` která nám umožní přijímat volání. Přidejte následující kód pro **client.js**:

```javascript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
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
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
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
