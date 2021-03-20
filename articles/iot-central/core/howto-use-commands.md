---
title: Použití příkazů zařízení v řešení Azure IoT Central
description: Použití příkazů zařízení v řešení Azure IoT Central. V tomto kurzu se dozvíte, jak jako vývojář zařízení používat příkazy zařízení v klientské aplikaci do vaší aplikace IoT Central v Azure.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e53bf377a7ef8f2293debd288ba25ef8f04ff4fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610994"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Používání příkazů v řešení Azure IoT Central

V této příručce se dozvíte, jak jako vývojář zařízení používat příkazy definované v šabloně zařízení.

Operátor může použít uživatelské rozhraní IoT Central k volání příkazu na zařízení. Příkazy řídí chování zařízení. Například operátor může zavolat příkaz pro restartování zařízení nebo shromažďování diagnostických dat.

Zařízení může:

* Okamžité reakce na příkaz.
* Odpoví na IoT Central, když obdrží příkaz a později se IoT Central upozorní na dokončení *dlouho běžícího příkazu* .

Ve výchozím nastavení příkazy očekávají, že se zařízení připojí a selže, pokud zařízení není dostupné. Pokud vyberete možnost **fronta, pokud** je v uživatelském rozhraní šablony zařízení možnost offline, příkaz může být zařazen do fronty, dokud zařízení nebude online. Tyto *příkazy offline* jsou popsány v samostatné části dále v tomto článku.

## <a name="define-your-commands"></a>Definovat příkazy

Do zařízení se odešlou standardní příkazy, které zařízení vyzvat k nějakému účelu. Příkaz může obsahovat parametry s dalšími informacemi. Například příkaz k otevření ventilu na zařízení může mít parametr, který určuje, kolik se má ventil otevřít. Příkazy mohou také obdržet návratovou hodnotu, když zařízení dokončí příkaz. Například příkaz, který žádá zařízení o spuštění některé diagnostiky, může získat diagnostické zprávy jako návratovou hodnotu.

Příkazy jsou definovány jako součást šablony zařízení. Následující snímek obrazovky ukazuje definici příkazu **Get Max-Min Report** v šabloně zařízení **termostatu** . Tento příkaz má oba parametry požadavků a odpovědí: 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Snímek obrazovky s příkazem pro získání maximálního počtu minimálních sestav ve termostatu šablona zařízení":::

Následující tabulka ukazuje nastavení konfigurace pro funkci příkazu:

| Pole             |Description|
|-------------------|-----------|
|Zobrazovaný název       |Hodnota příkazu, která se používá na řídicích panelech a formulářích.|
| Name            | Název příkazu IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. Toto pole musí být alfanumerické. Kód zařízení používá tuto hodnotu **názvu** .|
| Typ funkce | Systému.|
| Zařadit do fronty, pokud je offline | Určuje, zda má být příkaz *přepnut do režimu offline* . |
| Description     | Popis funkce příkazu|
| Komentář     | Jakékoli komentáře k funkci příkazu.|
| Žádost     | Datová část pro příkaz zařízení.|
| Odpověď     | Datová část odezvy příkazu zařízení.|

Následující fragment kódu ukazuje reprezentaci formátu JSON příkazu v modelu zařízení. V tomto příkladu je hodnota odpovědi komplexní typ **objektu** s více poli:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> Model zařízení můžete exportovat ze stránky šablona zařízení.

Tuto definici příkazu můžete propojit s uživatelským rozhraním obrazovky pomocí následujících polí:

* `@type` Chcete-li určit typ funkce: `Command`
* `name` pro hodnotu příkazu.

Volitelná pole, jako je zobrazované jméno a popis, umožňují přidat další podrobnosti k rozhraní a funkcím.

## <a name="standard-commands"></a>Standardní příkazy

V této části se dozvíte, jak zařízení pošle hodnotu odpovědi hned po přijetí příkazu.

Následující fragment kódu ukazuje, jak zařízení může reagovat na příkaz ihned po úspěšném odeslání kódu:

> [!NOTE]
> Tento článek používá Node.js pro jednoduchost. Další jazykové příklady najdete v kurzu [Vytvoření a připojení klientské aplikace do aplikace Azure IoT Central](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

Volání pro `onDeviceMethod` nastavení `commandHandler` metody. Obslužná rutina příkazu:

1. Kontroluje název příkazu.
1. Pro `getMaxMinReport` příkaz volá, `getMaxMinReportObject` aby načetla hodnoty, které mají být zahrnuty do návratového objektu.
1. Volá se, `sendCommandResponse` aby se odpověď poslala zpět na IoT Central. Tato odpověď zahrnuje `200` kód odpovědi pro indikaci úspěchu.

Následující snímek obrazovky ukazuje, jak se v uživatelském rozhraní IoT Central zobrazí úspěšná odpověď příkazu:

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="Snímek obrazovky ukazující, jak zobrazit datovou část příkazu pro standardní příkaz":::

## <a name="long-running-commands"></a>Dlouhotrvající příkazy

V této části se dozvíte, jak může zařízení zpozdit odeslání potvrzení, že příkaz byl konkurenční.

Následující fragment kódu ukazuje, jak může zařízení implementovat dlouho běžící příkaz:

> [!NOTE]
> Tento článek používá Node.js pro jednoduchost. Další jazykové příklady najdete v kurzu [Vytvoření a připojení klientské aplikace do aplikace Azure IoT Central](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

Volání pro `onDeviceMethod` nastavení `commandHandler` metody. Obslužná rutina příkazu:

1. Kontroluje název příkazu.
1. Volá se, `sendCommandResponse` aby se odpověď poslala zpět na IoT Central. Tato odpověď zahrnuje `202` kód odpovědi pro indikaci nevyřízených výsledků.
1. Dokončí dlouho běžící operace.
1. Používá hlášenou vlastnost se stejným názvem jako příkaz, který oznamuje IoT Central, že byl příkaz dokončen.

Následující snímek obrazovky ukazuje, jak se v uživatelském rozhraní IoT Central zobrazuje odezva příkazu, když obdrží kód odpovědi 202:

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Snímek obrazovky, který zobrazuje okamžitou reakci ze zařízení":::

Následující snímek obrazovky ukazuje IoT Central uživatelské rozhraní, když obdrží aktualizaci vlastnosti, která indikuje, že je příkaz dokončený:

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="Snímek obrazovky, který ukazuje dlouho běžící příkaz dokončen":::

## <a name="offline-commands"></a>Offline příkazy

V této části se dozvíte, jak zařízení zpracovává offline příkaz. Pokud je zařízení online, může offline příkaz zpracovat ihned po jeho přijetí. Pokud je zařízení offline, při dalším připojení k IoT Central zpracuje příkaz offline. Zařízení nemůžou odeslat návratovou hodnotu v reakci na offline příkaz.

> [!NOTE]
> Tento článek používá Node.js pro jednoduchost.

Následující snímek obrazovky ukazuje offline příkaz s názvem **GenerateDiagnostics**. Parametr Request je objekt s vlastností DateTime s názvem **čas_spuštění** a vlastností výčtu Integer s názvem **bank**:

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="Snímek obrazovky, který zobrazuje uživatelské rozhraní pro příkaz v režimu offline":::

Následující fragment kódu ukazuje, jak může klient naslouchat offline příkazům a zobrazovat obsah zprávy:

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

Výstup z předchozího fragmentu kódu ukazuje datovou část s hodnotami **StartTime** a **bank** . Seznam vlastností obsahuje název příkazu v položce seznamu **název metody** :

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

> [!NOTE]
> Výchozí hodnota TTL (Time to Live) pro offline příkazy je 24 hodin, po jejichž uplynutí vyprší platnost zprávy.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat příkazy v aplikaci IoT Central v Azure, najdete v [části datové části](concepts-telemetry-properties-commands.md) informace o parametrech příkazů a [Vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central](tutorial-connect-device.md) , abyste viděli kompletní ukázky kódu v různých jazycích.
