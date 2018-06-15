---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 04cb694f556d1b53344c0fd95947a258170c4f88
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33905199"
---
### <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Pokud jste při vytváření služby Relay zakázali možnost Vyžaduje autorizaci klientů, můžete odeslat žádosti na adresu URL hybridních připojení pomocí libovolného prohlížeče. Pro přístup k chráněným koncovým bodům je nutné vytvořit a předat token v hlavičce `ServiceBusAuthorization`, která se tady zobrazí.

Začněte tím, že vytvoříte nový soubor JavaScript s názvem `sender.js`.

### <a name="add-the-relay-npm-package"></a>Přidání balíčku NPM služby Relay

Spusťte z příkazového řádku uzlu ve složce projektu příkaz `npm install hyco-https`. Tento balíček také importuje běžný balíček `https`. Hlavním rozdílem na straně klienta je, že balíček poskytuje funkce k vytvoření identifikátorů URI a tokenů služby Relay.

### <a name="write-some-code-to-send-messages"></a>Napsání kódu pro odesílání zpráv

1. Na začátek souboru `sender.js` přidejte následující konstanty (`constants`):
   
    ```js
    const https = require('hyco-https');
    ```

2. Do souboru `sender.js` přidejte následující konstanty s podrobnostmi o hybridním připojení. Zástupné symboly v závorkách nahraďte hodnotami, které jste získali při vytváření hybridního připojení.
   
   1. `const ns` – Obor názvů služby Relay. Nezapomeňte použít plně kvalifikovaný obor názvů, například `{namespace}.servicebus.windows.net`.
   2. `const path` – Název hybridního připojení.
   3. `const keyrule` – Název klíče SAS.
   4. `const key` – Hodnota klíče SAS.

3. Do souboru `sender.js` přidejte následující kód. Všimněte si, že kód se od běžného použití klienta HTTPS Node.js významně neliší. Přidá se jenom autorizační hlavička.
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    Soubor sender.js by měl vypadat takto:
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

