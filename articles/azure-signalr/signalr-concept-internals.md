---
title: Interní informace služby Azure SignalR Service
description: Další informace o internalech služby Azure SignalR, architektuře, připojení a způsobu přenosu dat.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 62afa5ee6993aa1bb3c7b5926e5320ab1fa510a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157592"
---
# <a name="azure-signalr-service-internals"></a>Interní informace služby Azure SignalR Service

Služba Azure SignalR je postavená na ASP.NET core signalr framework. Podporuje také ASP.NET SignalR jako funkci náhledu.

> Pro podporu ASP.NET SignalR služba Azure SignalR reimplementuje ASP.NET datový protokol SignalR nad rámec ASP.NET Core

Můžete snadno migrovat místní ASP.NET aplikace Core SignalR pro práci se službou SignalR, s několika řádky změny kódu.

Následující diagram popisuje typickou architekturu při použití služby SignalR s aplikačním serverem.

Rozdíly od samoobslužné ASP.NET aplikace Core SignalR jsou také diskutovány.

![Architektura](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Připojení serveru

Vlastní hostované ASP.NET aplikační server Core SignalR naslouchá klientům a přímo je propojuje.

Pomocí služby SignalR již aplikační server nepřijímá trvalá připojení klientů, místo toho:

1. Koncový `negotiate` bod je vystavena Azure SignalR Service SDK pro každé rozbočovače.
1. Tento koncový bod bude reagovat na požadavky klienta na vyjednávání a přesměrovat klienty na službu SignalR.
1. Nakonec budou klienti připojeni ke službě SignalR.

Další informace naleznete [v tématu Připojení klienta](#client-connections).

Po spuštění aplikačního serveru 
- Pro ASP.NET Core SignalR azure signalr service sdk otevře 5 websocket připojení na rozbočovač na signalr služby. 
- Pro ASP.NET SignalR azure signalr service sdk otevře 5 websocket připojení na rozbočovač na signalr služby a jeden na aplikaci WebSocket připojení.

5 připojení WebSocket je výchozí hodnota, kterou lze změnit v [konfiguraci](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount).

Zprávy klientům a od klientů budou do těchto připojení multiplexovány.

Tato připojení zůstanou po celou dobu připojena ke službě SignalR. Pokud je připojení k serveru odpojeno kvůli problému se sítí,
- všichni klienti, kteří jsou obsluhováni tímto připojením k serveru odpojit (další informace o něm naleznete v [tématu Přenos dat mezi klientem a serverem](#data-transmit-between-client-and-server));
- připojení k serveru se automaticky znovu připojí.

## <a name="client-connections"></a>Připojení klientů

Při použití služby SignalR se klienti připojují ke službě SignalR namísto aplikačního serveru.
Existují dva kroky k navázání trvalých připojení mezi klientem a službou SignalR.

1. Klient odešle požadavek na vyjednání na aplikační server. Pomocí sady Azure SignalR Service SDK vrátí aplikační server odpověď přesměrování pomocí adresy URL a přístupového tokenu služby SignalR.

- Pro ASP.NET Core SignalR vypadá typická odpověď přesměrování takto:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- Pro ASP.NET SignalR vypadá typická odpověď přesměrování takto:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Po obdržení odpovědi na přesměrování klient použije novou adresu URL a přístupový token ke spuštění normálního procesu pro připojení ke službě SignalR.

Další informace o [přenosových protokolech](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md)ASP.NET Core SignalR .

## <a name="data-transmit-between-client-and-server"></a>Přenos dat mezi klientem a serverem

Když je klient připojen ke službě SignalR, runtime služby najde připojení k serveru, které bude sloužit tomuto klientovi.
- Tento krok se stane pouze jednou a je mapování 1:1 mezi klientem a serverem připojení.
- Mapování je udržováno ve službě SignalR, dokud se klient nebo server neodpojí.

V tomto okamžiku aplikační server obdrží událost s informacemi z nového klienta. Na aplikačním serveru je vytvořeno logické připojení ke klientovi. Datový kanál je vytvořen z klienta na aplikační server prostřednictvím služby SignalR.

Služba SignalR přenáší data z klienta na server aplikace párování. A data z aplikačního serveru budou odeslána mapovaným klientům.

Jak můžete vidět, služba Azure SignalR je v podstatě logická transportní vrstva mezi aplikačním serverem a klienty. Všechna trvalá připojení jsou převedena na službu SignalR.
Aplikační server potřebuje pouze zpracovat obchodní logiku ve třídě rozbočovače, bez obav o připojení klientů.