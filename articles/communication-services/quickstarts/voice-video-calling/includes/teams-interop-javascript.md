---
title: Rychlý Start – připojení týmu ke schůzce
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: dd75e5e97e5dca898ba10e91528782861fb949ec
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114624"
---
## <a name="prerequisites"></a>Předpoklady

- Funkční [komunikační služba volá aplikaci](../getting-started-with-calling.md).
- [Nasazení týmů](https://docs.microsoft.com/deployoffice/teams-install).

## <a name="enable-teams-interoperability"></a>Povolit interoperabilitu týmů

Funkce interoperability týmů je aktuálně v privátní verzi Preview. Pokud chcete povolit tuto funkci pro prostředek komunikačních služeb, pošlete prosím e-mail [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) s:

1. ID předplatného pro předplatné Azure, které obsahuje váš prostředek komunikačních služeb.
2. ID tenanta vašich týmů. Nejjednodušší způsob, jak to získat, je [získat a sdílet odkaz na tým](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team).

Abyste mohli tuto funkci používat, musíte být členem vlastnící organizace obou entit.

## <a name="add-the-teams-ui-controls"></a>Přidat ovládací prvky uživatelského rozhraní týmů

Přidejte nové textové pole a tlačítko v rámci HTML. Textové pole se použije k zadání kontextu schůzky týmů a tlačítko se použije k připojení k zadané schůzce:

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
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Povolit ovládací prvky uživatelského rozhraní týmů

Nyní můžeme vytvořit vazby na tlačítko **schůze JOIN Teams** na kód, který se připojí k dodanému týmu:

```javascript
meetingButton.addEventListener("click", () => {
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>Získat kontext schůzky

Kontext týmů lze načíst pomocí rozhraní Graph API. Tato část je podrobně popsána v [dokumentaci ke grafům](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?view=graph-rest-beta&tabs=http).

Požadované informace o schůzce můžete také získat z adresy URL **připojení schůzky** v samotném pozvání schůzky.

## <a name="run-the-code"></a>Spuštění kódu

Uživatelé nástroje Webpack můžou použít `webpack-dev-server` k sestavení a spuštění vaší aplikace. Spusťte následující příkaz pro vytvoření balíčku aplikace hostitele na místním webovém serveru:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otevřete prohlížeč a přejděte na http://localhost:8080/ . Měli byste vidět následující:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Snímek obrazovky dokončené aplikace JavaScriptu":::

Do textového pole vložte kontext týmů a stiskněte *připojit týmy schůzky* , aby se připojili k týmům, které se nacházejí v rámci vaší aplikace komunikační služby.

