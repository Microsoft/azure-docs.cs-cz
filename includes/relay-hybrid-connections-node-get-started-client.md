---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 9d4f7faa18ee7fae158afb42b8c42287e61dd103
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571931"
---
### <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Vytvořte nový soubor JavaScript s názvem `sender.js`.

### <a name="add-the-relay-npm-package"></a>Přidání balíčku NPM služby Relay

Spusťte z příkazového řádku uzlu ve složce projektu příkaz `npm install hyco-ws`.

### <a name="write-some-code-to-send-messages"></a>Napsání kódu pro odesílání zpráv

1. Na začátek souboru `sender.js` přidejte následující konstanty (`constants`):
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Do souboru `sender.js` přidejte následující konstanty s podrobnostmi o hybridním připojení. Zástupné symboly v závorkách nahraďte hodnotami, které jste získali při vytváření hybridního připojení.
   
   1. `const ns` – Obor názvů služby Relay. Nezapomeňte použít plně kvalifikovaný obor názvů, například `{namespace}.servicebus.windows.net`.
   2. `const path` – Název hybridního připojení.
   3. `const keyrule` – Název klíče SAS.
   4. `const key` – Hodnota klíče SAS.

3. Do souboru `sender.js` přidejte následující kód:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    Soubor sender.js by měl vypadat takto:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

