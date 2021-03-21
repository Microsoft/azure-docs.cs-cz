---
title: Přehled rozhraní API uzlů Azure Relay | Microsoft Docs
description: Tento článek poskytuje přehled rozhraní Node.js API pro službu Azure Relay. Také ukazuje, jak použít balíček uzlu hyco-WS.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 558f49c09203192ff4cbb1af392eaeef8d705c94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91263483"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Přehled rozhraní API pro přenos Hybrid Connections uzlů

## <a name="overview"></a>Přehled

[`hyco-ws`](https://www.npmjs.com/package/hyco-ws)Balíček Node pro Azure Relay Hybrid Connections je postaven na a rozšiřuje balíček [WS '](https://www.npmjs.com/package/ws) npm '. Tento balíček znovu exportuje všechny exporty tohoto základního balíčku a přidává nové exporty, které umožňují integraci s funkcí služby Azure Relay Hybrid Connections. 

Stávající aplikace, které `require('ws')` mohou používat tento balíček `require('hyco-ws')` , ale také umožňují hybridní scénáře, ve kterých aplikace může naslouchat připojením pomocí protokolu WebSocket místně z brány firewall a prostřednictvím Hybrid Connections, ve stejnou dobu.
  
## <a name="documentation"></a>Dokumentace

Rozhraní API jsou [zdokumentována v hlavním balíčku ' WS '](https://github.com/websockets/ws/blob/master/doc/ws.md). Tento článek popisuje, jak se tento balíček liší od tohoto směrného plánu. 

Klíčové rozdíly mezi základním balíčkem a tímto ' hyco-WS ' tvoří novou třídu serveru, která je exportována prostřednictvím `require('hyco-ws').RelayedServer` a několik pomocných metod.

### <a name="package-helper-methods"></a>Pomocné metody balíčku

Při exportu balíčku je k dispozici několik pomocných metod, které můžete odkazovat následujícím způsobem:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Pomocné metody jsou určeny pro použití s tímto balíčkem, ale lze je použít také serverem Node pro povolení webového nebo zařízení klientů pro vytváření posluchačů nebo odesílatelů. Server používá tyto metody předáním identifikátorů URI, které vkládají krátkodobé tokeny. Tyto identifikátory URI se dají použít i u běžných zásobníků WebSocket, které nepodporují nastavení hlaviček HTTP pro metodu handshake protokolu WebSocket. Vložení autorizačních tokenů do identifikátoru URI je podporováno primárně pro tyto scénáře použití knihovny. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Vytvoří platný identifikátor URI naslouchacího procesu hybridního připojení Azure Relay pro daný obor názvů a cestu. Tento identifikátor URI je pak možné použít spolu s verzí Relay třídy WebSocketServer.

- `namespaceName` (povinné) – název kvalifikovaný pro doménu Azure Relay oboru názvů, který se má použít.
- `path` (požadováno) – název existujícího Azure Relay Hybrid Connection v tomto oboru názvů.
- `token` (volitelné) – dříve vydaný přístupový token přenosu, který je vložený v identifikátoru URI naslouchacího procesu (viz následující příklad).
- `id` (volitelné) – identifikátor sledování, který umožňuje komplexní diagnostické sledování požadavků.

`token`Hodnota je volitelná a měla by se používat jenom v případě, že není možné odesílat hlavičky HTTP společně s metodou handshake protokolu WebSocket, protože se jedná o případ se zásobníkem W3C WebSocket.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Vytvoří platný identifikátor URI pro odeslání Azure Relay hybridního připojení pro daný obor názvů a cestu. Tento identifikátor URI lze použít u libovolného klienta protokolu WebSocket.

- `namespaceName` (povinné) – název kvalifikovaný pro doménu Azure Relay oboru názvů, který se má použít.
- `path` (požadováno) – název existujícího Azure Relay Hybrid Connection v tomto oboru názvů.
- `token` (volitelné) – dříve vydaný přístupový token přenosu, který je vložený v identifikátoru URI pro odeslání (viz následující příklad).
- `id` (volitelné) – identifikátor sledování, který umožňuje komplexní diagnostické sledování požadavků.

`token`Hodnota je volitelná a měla by se používat jenom v případě, že není možné odesílat hlavičky HTTP společně s metodou handshake protokolu WebSocket, protože se jedná o případ se zásobníkem W3C WebSocket.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Vytvoří token SAS (Azure Relay Shared Access Signature) pro daný cílový identifikátor URI, pravidlo SAS a klíč pravidla SAS, který je platný pro daný počet sekund nebo hodinu od aktuálního času, pokud je argument vypršení platnosti vynechán.

- `uri` (požadováno) – identifikátor URI, pro který má být token vydán. Identifikátor URI je normalizován na použití schématu HTTP a informace o řetězci dotazu jsou odstraněny.
- `ruleName` (povinné) – název pravidla SAS buď pro entitu reprezentované daným identifikátorem URI, nebo pro obor názvů reprezentovaný částí hostitele identifikátoru URI.
- `key` (povinné) – platný klíč pro pravidlo SAS. 
- `expirationSeconds` (volitelné) – počet sekund do vypršení platnosti vygenerovaného tokenu. Pokud není zadaný, výchozí hodnota je 1 hodina (3600).

Vydaný token připůjčuje práva přidružená k zadanému pravidlu SAS pro danou dobu trvání.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Tato metoda je funkčně ekvivalentní `createRelayToken` metodě popsané dříve, ale vrátí token správně připojený ke vstupnímu identifikátoru URI.

### <a name="class-wsrelayedserver"></a>Třída WS. RelayedServer

`hycows.RelayedServer`Třída je alternativou ke `ws.Server` třídě, která nenaslouchá v místní síti, ale deleguje naslouchá službě Azure Relay.

Tyto dvě třídy jsou většinou kompatibilní se smlouvou, což znamená, že existující aplikaci využívající `ws.Server` třídu lze snadno změnit na použití předané verze. Hlavní rozdíly jsou v konstruktoru a v dostupných možnostech.

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

`RelayedServer`Konstruktor podporuje jinou sadu argumentů než `Server` , protože se nejedná o samostatný naslouchací proces, nebo může být vložen do existujícího rozhraní NASLOUCHACÍHO procesu http. K dispozici je také méně dostupných možností, protože správa protokolu WebSocket je v podstatě delegována na předávací službu.

Argumenty konstruktoru:

- `server` (požadováno) – plně kvalifikovaný identifikátor URI pro název hybridního připojení, na kterém se má naslouchat, obvykle vytvořený pomocí pomocné metody WebSocket. createRelayListenUri ().
- `token` (povinné) – Tento argument obsahuje buď dříve vydaný řetězec tokenu, nebo funkci zpětného volání, která může být volána pro získání takového řetězce tokenu. Možnost zpětného volání je upřednostňovaná, protože umožňuje obnovení tokenu.

#### <a name="events"></a>Události

`RelayedServer` instance emitují tři události, které umožňují zpracovávat příchozí požadavky, navázat připojení a zjišťovat chybové stavy. Abyste mohli zpracovávat zprávy, musíte se přihlásit k odběru `connect` události. 

##### <a name="headers"></a>záhlaví

```JavaScript 
function(headers)
```

`headers`Událost se vyvolá těsně před přijetím příchozího připojení a umožňuje změnu hlaviček odesílaných klientovi. 

##### <a name="connection"></a>připojení

```JavaScript
function(socket)
```

Vygenerováno při přijetí nového připojení pomocí protokolu WebSocket. Objekt je typu `ws.WebSocket` stejný jako u základního balíčku.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Pokud podkladový Server vygeneruje chybu, přepošle se sem.  

#### <a name="helpers"></a>Pomocné rutiny

Aby bylo možné zjednodušit spuštění předaného serveru a okamžitě se přihlásit k odběru příchozích připojení, balíček zpřístupňuje jednoduchou pomocnou funkci, která se používá také v příkladech:

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

Tato metoda volá konstruktor pro vytvoření nové instance třídy RelayedServer a pak se přihlásí k odběru události ' Connection ' poskytnuté zpětné volání.
 
##### <a name="relayedconnect"></a>relayedConnect

Stačí zrcadlit `createRelayedServer` pomocníka ve funkci, `relayedConnect` vytvoří připojení klienta a přihlásí se k odběru události Open výsledného soketu.

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
* [Dostupná předávací rozhraní API](relay-api-overview.md)
