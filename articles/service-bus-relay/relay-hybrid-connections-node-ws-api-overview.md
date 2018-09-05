---
title: Přehled rozhraní API uzlu pro Azure Relay | Dokumentace Microsoftu
description: Přehled rozhraní API uzel propojení
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
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: bf0173f9c9802be689f7f3a893d381a251a2b16a
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43701132"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Přehled rozhraní API uzlu hybridní připojení přenosu

## <a name="overview"></a>Přehled

[ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) Je založená na uzel balíčku pro Azure Relay Hybrid Connections a rozšiřuje ['ws'](https://www.npmjs.com/package/ws) balíčku NPM. Tento balíček znovu vyexportuje všechny exporty základní balíčku a přidá nový export, které umožňují integraci s funkcí Hybrid Connections služby Azure Relay. 

Existující aplikace, která `require('ws')` můžete použít tento balíček s `require('hyco-ws')` místo toho, který také umožňuje hybridní scénáře, ve kterých může aplikace naslouchání pro připojení pomocí protokolu WebSocket místně od "uvnitř brány" a přes hybridní připojení, vše na stejnou dobu.
  
## <a name="documentation"></a>Dokumentace

Rozhraní API jsou [zdokumentované v balíčku hlavní "ws'](https://github.com/websockets/ws/blob/master/doc/ws.md). Tento článek popisuje, jak tento balíček se liší od tohoto směrného plánu. 

Hlavní rozdíly mezi základní balíček a tento "hyco – ws' je, že přidá novou třídu server exportuje přes `require('hyco-ws').RelayedServer`a několik pomocných metod.

### <a name="package-helper-methods"></a>Balíček pomocné metody

Na exportování balíčku, kterou můžete odkazovat následujícím způsobem jsou k dispozici několik pomocných metod:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Pomocné metody pro použití s tímto balíčkem, ale lze také podle uzlu serveru pro povolení zařízení nebo webových klientů pro vytvoření naslouchacích procesů nebo odesílatelů. Server používá tyto metody předáním identifikátory URI, které krátkodobou tokeny pro vložení. Tyto identifikátory URI lze také se běžné zásobníky protokolu WebSocket, které nepodporují nastavení hlavičky protokolu HTTP pro metodu handshake protokolu WebSocket. Vložení do identifikátoru URI autorizačních tokenech je podporováno především pro tyto scénáře použití externí knihovny. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Vytvoří platný hybridní připojení Azure Relay naslouchací proces identifikátor URI pro daný obor názvů a cestu. Tento identifikátor URI je možné potom verze přenosového WebSocketServer třídy.

- `namespaceName` (požadovaná): domény kvalifikovaný název oboru názvů Azure Relay k použití.
- `path` (povinné) – název existující připojení hybridní Azure Relay v tomto oboru názvů.
- `token` (volitelné) – u dřív vydaných Relay přístupový token, který je vložený v naslouchacím procesu identifikátoru URI (viz následující příklad).
- `id` (volitelné) – sledování identifikátor, který umožňuje začátku do konce diagnostiky sledování požadavků.

`token` Hodnota je volitelná a by měla sloužit pouze pokud není možné k odeslání hlaviček protokolu HTTP spolu s metodou handshake protokolu WebSocket, jako je tomu u stack W3C protokolu WebSocket.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Vytvoří platný odeslání hybridní připojení Azure Relay identifikátor URI pro daný obor názvů a cestu. Pomocí tohoto identifikátoru URI je možné pomocí libovolného objektu websocket na straně klienta.

- `namespaceName` (požadovaná): domény kvalifikovaný název oboru názvů Azure Relay k použití.
- `path` (povinné) – název existující připojení hybridní Azure Relay v tomto oboru názvů.
- `token` (volitelné) – u dřív vydaných Relay přístupový token, který je součástí odeslat identifikátoru URI (viz následující příklad).
- `id` (volitelné) – sledování identifikátor, který umožňuje začátku do konce diagnostiky sledování požadavků.

`token` Hodnota je volitelná a by měla sloužit pouze pokud není možné k odeslání hlaviček protokolu HTTP spolu s metodou handshake protokolu WebSocket, jako je tomu u stack W3C protokolu WebSocket.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Vytvoří token Azure Relay sdíleného přístupového podpisu (SAS) pro danou cílový identifikátor URI, pravidlo SAS a klíč SAS pro pravidlo, který je platný pro zadaný počet sekund nebo hodinu z aktuální instance Pokud vynecháte argument vypršení platnosti.

- `uri` (požadovaná): identifikátor URI, pro který má být vydaný token. Identifikátor URI je normalizovány na použití schématu HTTP a odebrána informací řetězce dotazu.
- `ruleName` (povinné) – název pro entitu reprezentována daný identifikátor URI nebo pro obor názvů reprezentována část hostitele identifikátoru URI SAS pravidla.
- `key` (povinné) – platný klíč SAS pravidla. 
- `expirationSeconds` (volitelné) – počet sekund, dokud vygenerovaný token má vypršet jeho platnost. Pokud není zadán, výchozí hodnota je 1 hodiny (3600).

Vydaný token uděluje práva spojená se zadaným pravidlem SAS pro danou dobu trvání.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Tato metoda je funkčně srovnatelný s `createRelayToken` metoda uvedeno dříve, ale vrátí token správně připojeny k vstup identifikátoru URI.

### <a name="class-wsrelayedserver"></a>Třída ws. RelayedServer

`hycows.RelayedServer` Třídy se o alternativu k `ws.Server` třídu, která nepřijímá požadavky na místní sítě, ale delegáty naslouchání ke službě Azure Relay.

Dvě třídy jsou většinou smlouvy kompatibilní, to znamená, že stávající aplikace s využitím `ws.Server` třídy lze snadno změnit na použití verze přes předávací službu. Hlavní rozdíly jsou v konstruktoru a dostupných možností.

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

`RelayedServer` Konstruktor podporuje jinou sadu argumentů, než `Server`, protože se nejedná o samostatnou naslouchací proces, nebo může být vložen do existující architekturu naslouchací proces protokolu HTTP. K dispozici také méně možností od do služby Relay je do značné míry delegování správy pomocí protokolu WebSocket.

Argumenty konstruktoru:

- `server` (povinné) – úplný identifikátor URI pro hybridní připojení. název, na které se mají očekávat, obvykle zkonstruován pomocí WebSocket.createRelayListenUri() Pomocná metoda.
- `token` Tento argument (povinné) – obsahuje řetězec dříve vystaveného tokenu nebo funkce zpětného volání, která může být volána k získání tokenu řetězce. Možnost zpětného volání je upřednostňované, protože umožňuje obnovit token.

#### <a name="events"></a>Události

`RelayedServer` instance vysílat tři události, které vám umožní zpracování příchozích požadavků, připojení a zjišťování chybové stavy. Musí přihlásit k odběru `connect` událostí ke zpracování zpráv. 

##### <a name="headers"></a>hlavičky

```JavaScript 
function(headers)
```

`headers` Událost se vyvolá, těsně před plánovaným přijmout příchozí připojení k povolení úprav hlavičky k odeslání do klienta. 

##### <a name="connection"></a>připojení

```JavaScript
function(socket)
```

Emitovány při přijetí nového připojení pomocí protokolu WebSocket. Objekt je typu `ws.WebSocket`, stejně jako s základní balíčku.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Pokud podkladový server generuje chybu, je předán tady.  

#### <a name="helpers"></a>Pomocné rutiny

Pro zjednodušení od serveru přes předávací službu a okamžitě se přihlásíte k odběru příchozí připojení, balíček zveřejňuje jednoduché pomocná funkce, který se použije také v příkladech, následujícím způsobem:

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

Tato metoda volá konstruktor k vytvoření nové instance RelayedServer a pak přihlášeno zpětné volání na událost "připojení".
 
##### <a name="relayedconnect"></a>relayedConnect

Jednoduše zrcadlení `createRelayedServer` pomocná funkce, `relayedConnect` vytvoří připojení klienta a odebírá "Otevřít" události na výsledný soketu.

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

## <a name="next-steps"></a>Další postup
Další informace o Azure Relay, najdete pomocí těchto odkazů:
* [Co je Azure Relay?](relay-what-is-it.md)
* [K dispozici předávání přes rozhraní API](relay-api-overview.md)
