---
title: Interní informace o Azure služby SignalR
description: Přehled interní informace služby Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 12/23/2018
ms.author: zhshang
ms.openlocfilehash: 4522e394384fa74b8578f0afb3e059d0caad0c39
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811818"
---
# <a name="azure-signalr-service-internals"></a>Interní informace o Azure služby SignalR

Službě Azure SignalR je nástavbou funkce SignalR technologie ASP.NET Core framework. Funkce SignalR technologie ASP.NET podporuje také jako funkce ve verzi preview.

> K podpoře funkce SignalR technologie ASP.NET, služby Azure SignalR reimplements protokol funkce SignalR technologie ASP.NET jako nadstavby rozhraní framework ASP.NET Core

Můžete snadno migrovat místní aplikace SignalR technologie ASP.NET Core pro práci s knihovnou SignalR službou po zadání několika řádků kódu změny.

Následující diagram popisuje typickou architekturu při použití s aplikačního serveru služby SignalR.

Rozdíly v místním prostředí aplikací SignalR technologie ASP.NET Core jsou také popsány.

![Architektura](./media/signalr-internals/arch.png)

## <a name="server-connections"></a>Připojení k serveru

V místním prostředí aplikačního serveru funkce SignalR technologie ASP.NET Core naslouchají a klienti se připojí přímo.

Pomocí služby SignalR aplikační server je už nepřijímáme trvalé klientská připojení, místo toho:

1. A `negotiate` koncový bod je zveřejněný prostřednictvím sady SDK služby Azure SignalR pro každý rozbočovač.
1. Tento koncový bod bude reagovat na požadavky klientů, vyjednávání a přesměrovat klienty pro služby SignalR.
1. Nakonec klienti se připojí pro služby SignalR.

Další informace najdete v tématu [připojení klientů](#client-connections).

Po zahájení aplikačního serveru 
- Sada SDK služby Azure SignalR pro funkci SignalR technologie ASP.NET Core, otevře se 5 připojení pomocí protokolu WebSocket na Centrum pro služby SignalR. 
- Pro funkci SignalR technologie ASP.NET sady SDK služby Azure SignalR otevře 5 připojení pomocí protokolu WebSocket na Centrum pro služby SignalR a jedna na připojení soketu WebSocket aplikace.

5 připojení pomocí protokolu WebSocket je výchozí hodnota, která lze změnit v [konfigurace](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount).

Zprávy od klientů bude multiplexní do těchto připojení.

Tato připojení zůstanou připojené ke službě SignalR celou dobu. Připojení k serveru po odpojení sítě problému
- Všichni klienti, kteří obsluhuje tento odpojení připojení serveru (Další informace o tom najdete v tématu [přenášet Data mezi klientem a serverem](#data-transmit-between-client-and-server));
- připojení k serveru spustí, opětovné připojení automaticky.

## <a name="client-connections"></a>Připojení klientů

Při použití služby SignalR klienti připojovat k službě SignalR místo aplikačního serveru.
Existují dva kroky k vytvoření trvalého připojení mezi klientem a služby SignalR.

1. Klient odešle požadavek negotiate k aplikačnímu serveru. Pomocí sady SDK služby Azure SignalR aplikační server vrátí odpověď přesměrování se adresa URL služby SignalR a přístupový token.

- Pro funkci SignalR technologie ASP.NET Core typické přesměrování odpověď vypadá takto:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- Pro funkci SignalR technologie ASP.NET typické přesměrování odpověď vypadá takto:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Po přijetí odpovědi na přesměrování, používá klient novou adresu URL a tokenu přístupu ke spuštění normální proces pro připojení k službě SignalR.

Další informace o ASP.NET Core SignalR [přenosu protokolů](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md).

## <a name="data-transmit-between-client-and-server"></a>Přenos dat mezi klientem a serverem

Klient je připojen ke službě SignalR, modulu runtime service najdete sloužit tento klient připojení k serveru
- V tomto kroku dojde jenom jednou a je mapování 1: 1 mezi klientem a serverem připojení.
- Mapování je udržována v služby SignalR až klient nebo server odpojí.

V tomto okamžiku aplikační server přijímá události s informacemi z nového klienta. Logické připojení ke klientovi se vytvoří v aplikačním serveru. Datový kanál se naváže od klienta k serveru aplikace prostřednictvím služby SignalR.

Služby SignalR odesílá data z klienta na párovací aplikačního serveru. A odešlou se data z aplikačního serveru pro mapovanou klienty.

Jak je vidět, služby Azure SignalR je v podstatě logické přenosové vrstvy mezi aplikační server a klienty. Všechny trvalé připojení přebírá služby SignalR.
Aplikační server potřebuje pouze pro zpracování obchodní logiku v třídě centra, ale nemusíme se starat o připojení klienta.