---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 009bd57fdb82b8463352da8dc63c9aeebceab09b
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779872"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Telefonní číslo získané v prostředku komunikačních služeb. [Jak získat telefonní číslo](../../telephony-sms/get-phone-number.md).
- A `User Access Token` povolí klienta volání. Další informace o [tom, jak získat `User Access Token` ](../../access-tokens.md)
- Dokončete rychlé [zprovoznění, abyste mohli začít s přidáváním volání do aplikace](../getting-started-with-calling.md) .

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

- Chcete-li zobrazit telefonní čísla přidružená ke zdroji komunikačních služeb, přihlaste se k [Azure Portal](https://portal.azure.com/), vyhledejte prostředek komunikačních služeb a otevřete kartu **telefonní čísla** v levém navigačním podokně.
- Můžete sestavit a spustit aplikaci pomocí komunikačních služeb Azure s voláním klientské knihovny pro JavaScript:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js
```

## <a name="setting-up"></a>Nastavení

### <a name="add-pstn-functionality-to-your-app"></a>Přidání funkce veřejné sítě do vaší aplikace

Rozšíří vaše rozložení pomocí ovládacích prvků vytáčení telefonního čísla.

Umístěte tento kód na konec `<body />` oddílu **index.html**před `<script />` značkami:

```html
<input 
  id="callee-phone-input"
  type="text"
  placeholder="Phone number you would like to dial"
  style="margin-bottom:1em; width: 230px;"
/>
<div>
  <button id="call-phone-button" type="button">
    Start Phone Call
  </button>
  &nbsp;
  <button id="hang-up-phone-button" type="button" disabled="true">
    Hang Up Phone Call
  </button>
</div>
```

Rozšíříte logiku aplikace pomocí funkcí telefonního subsystému.

Přidejte tento kód do **client.js**:

```javascript
const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");
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
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: '+18336528005'}
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


![Snímek obrazovky dokončené aplikace JavaScriptu](../media/javascript/pstn-calling-javascript-app.png)

Volání do reálného telefonního čísla můžete umístit zadáním telefonního čísla do pole přidané text a kliknutím na tlačítko **Spustit telefonní hovor** .

> [!WARNING]
> Všimněte si, že telefonní čísla by měla být zadána ve formátu E. 164 mezinárodní standard. (např.: + 12223334444)
