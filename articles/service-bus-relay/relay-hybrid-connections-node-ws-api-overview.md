---
title: Přehled rozhraní API Azure předávání uzlu | Microsoft Docs
description: Předávání přehled API uzlu
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 696e3f77a283cc31d3c8f6007a839480ae8eb984
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019449"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Předávání API uzlu hybridní připojení – přehled

## <a name="overview"></a>Přehled

[ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) Balíček uzel pro hybridní připojení předávání přes Azure je založená na a rozšiřuje ['ws'](https://www.npmjs.com/package/ws) balíčku NPM. Tento balíček znovu vyexportuje všechny exporty tento základní balíček a přidá nové export, které umožňují integrace s funkcí hybridní připojení služby předávání přes Azure. 

Existující aplikace, `require('ws')` můžete použít tento balíček s `require('hyco-ws')` místo toho, která umožňuje také hybridní scénáře, ve kterých můžete aplikaci přijímat připojení protokolu WebSocket místně z "v bráně firewall" a pomocí hybridních připojení, všechny ve stejnou dobu.
  
## <a name="documentation"></a>Dokumentace

Rozhraní API jsou [zdokumentované v balíčku hlavní 'ws'](https://github.com/websockets/ws/blob/master/doc/ws.md). Tento článek popisuje, jak tento balíček se liší od tohoto směrného plánu. 

Je klíčové rozdíly mezi základní balíček a tato 'hyco-ws' Přidá novou třídu serveru exportovat prostřednictvím `require('hyco-ws').RelayedServer`a několik pomocné metody.

### <a name="package-helper-methods"></a>Balíček pomocné metody

Na exportu balíček, na kterou odkazujete takto jsou k dispozici několik pomocné metody:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Pomocné metody pro použití s tímto balíčkem jsou, ale mohou sloužit také serverem uzel pro povolení vytvořit naslouchací procesy nebo odesílatelé web nebo zařízení klientům. Server používá tyto metody předáním identifikátory URI, který vložení krátkodobou tokeny. Tyto identifikátory URI můžete použít taky s běžné zásobníky protokolu WebSocket, které nepodporují nastavení hlavičky protokolu HTTP pro handshake protokolu WebSocket. Vložení do identifikátor URI autorizace tokeny je podporována především pro tyto scénáře použití knihovny externí. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Vytvoří platný Azure předávání hybridní připojení naslouchací proces identifikátor URI pro daný obor názvů a cestu. Tento identifikátor URI pak lze verzí předávání WebSocketServer třídy.

- `namespaceName`(požadovaná) domény kvalifikovaný název oboru názvů předávání přes Azure používat.
- `path`(požadovaná) název existující připojení hybridní předávání přes Azure v daném oboru názvů.
- `token`(volitelné) – pro přístup k dřív vydaných předávání token, který je součástí naslouchací proces identifikátor URI (viz následující příklad).
- `id`(volitelné) – sledování identifikátor, který umožňuje sledování začátku do konce diagnostiky požadavků.

`token` Hodnota je volitelná a musí být použit pouze pokud není možné k odeslání hlaviček protokolu HTTP společně s handshake protokolu WebSocket, jako je tomu u zásobník protokolu W3C WebSocket.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Vytvoří platný odesílání Azure předávání hybridní připojení identifikátor URI pro daný obor názvů a cestu. Tento identifikátor URI lze použít s jakýmkoli klientem protokolu WebSocket.

- `namespaceName`(požadovaná) domény kvalifikovaný název oboru názvů předávání přes Azure používat.
- `path`(požadovaná) název existující připojení hybridní předávání přes Azure v daném oboru názvů.
- `token`(volitelné) – pro přístup k dřív vydaných předávání token, který se vloží v odesílání identifikátor URI (viz následující příklad).
- `id`(volitelné) – sledování identifikátor, který umožňuje sledování začátku do konce diagnostiky požadavků.

`token` Hodnota je volitelná a musí být použit pouze pokud není možné k odeslání hlaviček protokolu HTTP společně s handshake protokolu WebSocket, jako je tomu u zásobník protokolu W3C WebSocket.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Vytvoří token Azure předávání sdíleného přístupového podpisu (SAS) pro daný cílový identifikátor URI, pravidlo SAS a pravidlo klíče SAS, který je platný pro zadaný počet sekund nebo jednu hodinu z aktuální Pokud argument vypršení platnosti je vynechán.

- `uri`(povinné) - URI, pro kterou má být vydaný token. Identifikátor URI je normalizovány na použít schéma HTTP a informací řetězce dotazu se odstraní.
- `ruleName`(povinné) – název pro entitu reprezentovanou objektem daný identifikátor URI, nebo pro obor názvů reprezentována část hostitele identifikátor URI SAS pravidla.
- `key`(povinné) - platný klíč SAS pravidla. 
- `expirationSeconds`(volitelné) – počet sekund, dokud by měla vypršet vygenerovaný token. Pokud není zadáno, výchozí hodnota je 1 hodina (3600).

Vystavený token uděluje práv spojených se zadaným pravidlem SAS pro danou dobu trvání.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Tato metoda je funkčně srovnatelný `createRelayToken` metoda dříve, ale vrátí token správně připojí k identifikátoru URI vstupu.

### <a name="class-wsrelayedserver"></a>Třída ws. RelayedServer

`hycows.RelayedServer` Třída je alternativa k `ws.Server` třídu, která komunikovat v místní síti, ale delegáti naslouchání službu předávání přes Azure.

Dvě třídy jsou většinou kontrakt kompatibilní, znamená to, že existující aplikace pomocí `ws.Server` třídy lze snadno změnit na použití přenosu verze. Hlavní rozdíly jsou v konstruktoru a dostupných možností.

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

`RelayedServer` Konstruktor podporuje jinou sadu argumentů než `Server`, protože se nejedná o samostatnou naslouchací proces, nebo je možné jej vkládat do představuje existující rozhraní naslouchací proces protokolu HTTP. Existují také méně možností k dispozici vzhledem k tomu, že do předávací služby je z velké části delegování správy protokolu WebSocket.

Argumenty konstruktoru:

- `server`(povinné) - plně kvalifikovaného identifikátoru URI pro hybridní připojení název, na kterém se má naslouchat, obvykle zkonstruovat s pomocnou metodu WebSocket.createRelayListenUri().
- `token`Tento argument (povinné) - obsahuje řetězec tokenu dřív vydaných nebo funkce zpětného volání, která může být volána k získání tokenu řetězce. Na možnost je upřednostňované, protože umožňuje obnovení tokenu.

#### <a name="events"></a>Události

`RelayedServer`instance emitování tři události, které vám umožní zpracovávat příchozí požadavky, vytvořit připojení a zjistit chybové stavy. Přihlášení k odběru `connect` událostí pro zpracování zprávy. 

##### <a name="headers"></a>hlavičky

```JavaScript 
function(headers)
```

`headers` Událost se vyvolá, těsně před přijmout příchozí připojení, povolení úprav hlavičky k odeslání do klienta. 

##### <a name="connection"></a>připojení

```JavaScript
function(socket)
```

Vygenerované při byla přijata nové připojení protokolu WebSocket. Objekt je typu `ws.WebSocket`, stejně jako s základní balíčku.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Pokud základní server vysílá chybu, je zde předají.  

#### <a name="helpers"></a>Pomocné rutiny

Pro zjednodušení spuštění přenosu serveru a okamžitě se přihlásíte k odběru příchozí připojení, zpřístupní balíček jednoduché podpůrná funkce, která je použita také v příkladech následujícím způsobem:

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

Tato metoda volá konstruktor k vytvoření nové instance RelayedServer a potom jako odběratel zadané zpětné volání na událost, připojení.
 
##### <a name="relayedconnect"></a>relayedConnect

Jednoduše zrcadlení `createRelayedServer` pomocné rutiny ve funkci `relayedConnect` vytvoří připojení klienta a přihlásí se k odběru události 'open' na výsledný soketu.

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
Další informace o předávání přes Azure, najdete pomocí těchto odkazů:
* [Co je Azure Relay?](relay-what-is-it.md)
* [K dispozici předávání přes rozhraní API](relay-api-overview.md)
