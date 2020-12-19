---
title: Rychlý Start – připojení týmu ke schůzce
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: c72083f205fae77de366125e666cee479fd46805
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97692029"
---
## <a name="prerequisites"></a>Předpoklady

- Funkční [komunikační služba volá aplikaci](../getting-started-with-calling.md).
- [Nasazení týmů](/deployoffice/teams-install).

## <a name="enable-teams-interoperability"></a>Povolit interoperabilitu týmů

Funkce interoperability týmů je aktuálně v privátní verzi Preview. Pokud chcete povolit tuto funkci pro prostředek komunikačních služeb, pošlete prosím e-mail [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) s:

1. ID předplatného pro předplatné Azure, které obsahuje váš prostředek komunikačních služeb.
2. ID tenanta vašich týmů. Nejjednodušší způsob, jak to získat, je [získat a sdílet odkaz na tým](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f).

Abyste mohli tuto funkci používat, musíte být členem vlastnící organizace obou entit.

## <a name="add-the-teams-ui-controls"></a>Přidat ovládací prvky uživatelského rozhraní týmů

Nahraďte kód v index.html následujícím fragmentem kódu.
Textové pole se použije k zadání kontextu schůzky týmů a tlačítko se použije k připojení k zadané schůzce:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Povolit ovládací prvky uživatelského rozhraní týmů

Nahraďte obsah souboru client.js následujícím fragmentem kódu.

```javascript
import { CallClient } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
    })
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Získat odkaz na schůzku týmů

Odkaz na schůzku týmů lze načíst pomocí rozhraní Graph API. Tato část je podrobně popsána v [dokumentaci ke grafům](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
Komunikační služby, které volá sadu SDK, přijímají úplný tým odkaz na schůzku. Tento odkaz se vrátí jako součást `onlineMeeting` prostředku, který je přístupný v rámci [ `joinWebUrl` vlastnosti](/graph/api/resources/onlinemeeting?view=graph-rest-beta) . můžete také získat požadované informace o schůzce z adresy URL **připojení ke schůzce** v rámci pozvánky na schůzku.

## <a name="run-the-code"></a>Spuštění kódu

Uživatelé nástroje Webpack můžou použít `webpack-dev-server` k sestavení a spuštění vaší aplikace. Spusťte následující příkaz pro vytvoření balíčku aplikace hostitele na místním webovém serveru:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otevřete prohlížeč a přejděte na http://localhost:8080/ . Měli byste vidět následující:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Snímek obrazovky dokončené aplikace JavaScriptu":::

Do textového pole vložte kontext týmů a stiskněte *připojit týmy schůzky* , aby se připojili k týmům, které se nacházejí v rámci vaší aplikace komunikační služby.
