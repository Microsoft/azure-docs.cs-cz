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
ms.openlocfilehash: d0fd3e88bdb25fdfd430924ef6b7f571d070b733
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33905206"
---
### <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Vytvořte nový soubor JavaScript s názvem `listener.js`.

### <a name="add-the-relay-npm-package"></a>Přidání balíčku NPM služby Relay

Spusťte z příkazového řádku uzlu ve složce projektu příkaz `npm install hyco-https`.

### <a name="write-some-code-to-handle-requests"></a>Zápis kódu pro zpracování žádostí

1. Na začátek souboru `listener.js` přidejte následující konstantu.

    ```js
    const https = require('hyco-https');
    ```
2. Do souboru `listener.js` přidejte následující konstanty s podrobnostmi o hybridním připojení. Zástupné symboly v závorkách nahraďte hodnotami, které jste získali při vytváření hybridního připojení.

   1. `const ns` – Obor názvů služby Relay. Nezapomeňte použít plně kvalifikovaný obor názvů, například `{namespace}.servicebus.windows.net`.
   2. `const path` – Název hybridního připojení.
   3. `const keyrule` – Název klíče SAS.
   4. `const key` – Hodnota klíče SAS.

3. Do souboru `listener.js` přidejte následující kód. :

    Všimněte si, že kód se příliš neliší od libovolného jednoduchého příkladu serveru HTTP, který můžete najít v kurzech Node.js pro začátečníky. Odlišné je jenom použití `createRelayedServer` místo obvyklé funkce `createServer`.

    ```js
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```
    Soubor listener.js by měl vypadat takto:
   
    ```js
    const https = require('hyco-https');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```

