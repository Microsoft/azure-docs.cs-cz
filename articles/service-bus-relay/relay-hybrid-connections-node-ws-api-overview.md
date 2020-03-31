---
title: Přehled api reléového uzlu Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled rozhraní NODE.js API pro službu Azure Relay. To také ukazuje, jak používat balíček hyco-ws Node.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 2877284c419da4999e23490fc986e5da44e5d92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514506"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Přehled rozhraní API uzlu hybridních připojení přenosu

## <a name="overview"></a>Přehled

Balíček [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) uzlu pro hybridní připojení Azure Relay je postaven na a rozšiřuje balíček [NPM "ws".](https://www.npmjs.com/package/ws) Tento balíček znovu exportuje všechny exporty tohoto základního balíčku a přidává nové exporty, které umožňují integraci s funkcí Hybridní připojení služby Azure Relay. 

Existující aplikace, které `require('ws')` lze `require('hyco-ws')` použít tento balíček s místo, který také umožňuje hybridní scénáře, ve kterém aplikace může naslouchat připojení WebSocket místně z "uvnitř brány firewall" a prostřednictvím hybridní připojení, všechny ve stejnou dobu.
  
## <a name="documentation"></a>Dokumentace

Api jsou [dokumentovány v hlavním balíčku "ws"](https://github.com/websockets/ws/blob/master/doc/ws.md). Tento článek popisuje, jak se tento balíček liší od tohoto směrného plánu. 

Hlavní rozdíly mezi základní balíček a tento 'hyco-ws' je, že přidá `require('hyco-ws').RelayedServer`novou třídu serveru, exportované přes , a několik pomocných metod.

### <a name="package-helper-methods"></a>Pomocné metody balíčků

Na exportu balíčku je k dispozici několik metod nástrojů, na které můžete odkazovat následujícím způsobem:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Pomocné metody jsou určeny pro použití s tímto balíčkem, ale mohou být také použity uzlovým serverem pro povolení webových nebo klientských klientů k vytváření posluchačů nebo odesílatelů. Server používá tyto metody tím, že jim předá identifikátory URI, které vložte krátkodobé tokeny. Tyto identifikátory URI lze také použít s běžnými zásobníky WebSocket, které nepodporují nastavení hlavičky HTTP pro handshake WebSocket. Vkládání tokenů autorizace do identifikátoru URI je podporováno především pro tyto scénáře použití externí knihovny. 

#### <a name="createrelaylistenuri"></a>vytvořitRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Vytvoří platný identifikátor URI naslouchací proces hybridního připojení Azure Relay pro daný obor názvů a cestu. Tento identifikátor URI lze poté použít s přenosovou verzí třídy WebSocketServer.

- `namespaceName`(povinné) – název oboru s kvalifikací domény oboru názvů Azure Relay, který se má použít.
- `path`(povinné) – název existujícího hybridního připojení azure relay v tomto oboru názvů.
- `token`(nepovinné) - dříve vydaný přístupový token přenosu, který je vložen do identifikátoru URI naslouchací procesu (viz následující příklad).
- `id`(nepovinné) - identifikátor sledování, který umožňuje sledování komplexní diagnostiky požadavků.

Hodnota `token` je volitelná a měla by být použita pouze v případě, že není možné odeslat hlavičky HTTP spolu s handshake WebSocket, jako je tomu v případě zásobníku W3C WebSocket.                  


#### <a name="createrelaysenduri"></a>vytvořitRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Vytvoří platný hybridní připojení Azure Relay odeslat URI pro daný obor názvů a cestu. Tento identifikátor URI lze použít s libovolným klientem WebSocket.

- `namespaceName`(povinné) – název oboru s kvalifikací domény oboru názvů Azure Relay, který se má použít.
- `path`(povinné) – název existujícího hybridního připojení azure relay v tomto oboru názvů.
- `token`(nepovinné) - dříve vydaný přístupový token přenosu, který je vložen do identifikátoru URI odeslání (viz následující příklad).
- `id`(nepovinné) - identifikátor sledování, který umožňuje sledování komplexní diagnostiky požadavků.

Hodnota `token` je volitelná a měla by být použita pouze v případě, že není možné odeslat hlavičky HTTP spolu s handshake WebSocket, jako je tomu v případě zásobníku W3C WebSocket.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Vytvoří token sdíleného přístupu Azure Relay (SAS) pro daný cílový identifikátor URI, pravidlo SAS a klíč pravidla SAS, který je platný pro daný počet sekund nebo hodinu od aktuálního okamžiku, pokud je argument vypršení platnosti vynechán.

- `uri`(povinné) - identifikátor URI, pro který má být vydán token. Identifikátor URI je normalizován pro použití schématu HTTP a informace o řetězci dotazu jsou odstraněny.
- `ruleName`(povinné) - Název pravidla SAS pro entitu reprezentovanou daným identifikátorem URI nebo pro obor názvů reprezentované hostitelskou částí URI.
- `key`(povinné) - platný klíč pro pravidlo SAS. 
- `expirationSeconds`(nepovinné) - počet sekund do vypršení platnosti generovaného tokenu. Pokud není zadán, výchozí hodnota je 1 hodina (3600).

Vydaný token uděluje práva spojená se zadaným pravidlem SAS pro danou dobu trvání.

#### <a name="appendrelaytoken"></a>token usiobu

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Tato metoda je funkčně ekvivalentní metodě `createRelayToken` zdokumentované dříve, ale vrátí token správně připojený k vstupnímu identifikátoru URI.

### <a name="class-wsrelayedserver"></a>Třída ws. RerelayedServer

Třída `hycows.RelayedServer` je alternativou `ws.Server` ke třídě, která nenaslouchá v místní síti, ale delegáti, kteří poslouchají službu Azure Relay.

Tyto dvě třídy jsou většinou kompatibilní se `ws.Server` smlouvou, což znamená, že existující aplikace pomocí třídy lze snadno změnit použít relé verze. Hlavní rozdíly jsou v konstruktoru a v dostupných možnostech.

#### <a name="constructor"></a>Konstruktor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

Konstruktor `RelayedServer` podporuje jinou sadu argumentů `Server`než , protože není samostatný naslouchací proces nebo může být vložen do existujícího rozhraní http posluchače. Existuje také méně možností, protože správa WebSocket je z velké části delegována na službu relay.

Argumenty konstruktoru:

- `server`(povinné) - plně kvalifikovaný identifikátor URI pro název hybridního připojení, na kterém má být naslouchán, obvykle konstruovaný s asistenční metodou WebSocket.createRelayListenUri().
- `token`(povinné) - tento argument obsahuje buď dříve vydaný řetězec tokenu nebo funkci zpětného volání, která může být volána k získání takového řetězce tokenu. Možnost zpětného volání je upřednostňována, protože umožňuje obnovení tokenu.

#### <a name="events"></a>Události

`RelayedServer`instance vyzařují tři události, které umožňují zpracovávat příchozí požadavky, navazovat připojení a zjišťovat chybové stavy. Chcete-li zpracovat zprávy, `connect` musíte se přihlásit k odběru události. 

##### <a name="headers"></a>Záhlaví

```JavaScript 
function(headers)
```

Událost `headers` je vyvolána těsně před přijetím příchozího připojení, což umožňuje změnu záhlaví odeslat klientovi. 

##### <a name="connection"></a>připojení

```JavaScript
function(socket)
```

Vydává se při přijetí nového připojení WebSocket. Objekt je typu `ws.WebSocket`, stejný jako u základního balíčku.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Pokud základní server vydává chybu, je předán zde.  

#### <a name="helpers"></a>Pomocníci

Chcete-li zjednodušit spuštění přenášeného serveru a okamžité přihlášení k odběru příchozích připojení, balíček zpřístupňuje jednoduchou pomocnou funkci, která se také používá v příkladech, a to následovně:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Tato metoda volá konstruktor u vytvořit novou instanci RelayedServer a potom přihlásí za předpokladu, zpětné volání 'připojení' událost.
 
##### <a name="relayedconnect"></a>reléConnect

Jednoduše zrcadlení `createRelayedServer` pomocníka `relayedConnect` ve funkci, vytvoří připojení klienta a přihlásí se k odběru 'open' událost na výsledné soketu.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Další kroky
Další informace o Azure Relay najdete na těchto odkazech:
* [Co je Azure Relay?](relay-what-is-it.md)
* [Dostupná rozhraní API pro přenos](relay-api-overview.md)
