---
title: Interní informace služby Azure SignalR Service
description: Přečtěte si o interních datech služby signalizace Azure, architektuře, připojení a způsobu přenosu dat.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3fc6971c66d06ae9f25584f5be28b051075bfa49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88921966"
---
# <a name="azure-signalr-service-internals"></a>Interní informace služby Azure SignalR Service

Služba signalizace Azure je postavená na rozhraní ASP.NET Core Signale. Podporuje také signál ASP.NET tím, že znovu implementuje datový protokol ASP.NET signálu na začátku ASP.NET Core Frameworku.

K práci se službou signalizace můžete snadno migrovat aplikaci pro místní ASP.NET Core, nebo aplikaci signalizace ASP.NET, a to s několika řádky změny kódu.

Diagram níže popisuje typickou architekturu při použití služby signalizace spolu s aplikačním serverem.

Pojednává také o rozdílech od aplikace Signal-Hosted ASP.NET Core.

![Architektura](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Připojení k serveru

Aplikační server ASP.NET Coreového signálu v místním prostředí naslouchá a připojuje klienty přímo.

Pomocí služby signalizace už aplikační server nepřijímá trvalá připojení klientů, místo toho:

1. `negotiate`Sada SDK služby Azure signaler zveřejňuje koncový bod pro každé centrum.
1. Tento koncový bod bude odpovídat na požadavky na vyjednávání klienta a přesměrování klientů na službu Signal.
1. Nakonec budou klienti připojení ke službě signalizace.

Další informace najdete v tématu [připojení klientů](#client-connections).

Po spuštění aplikačního serveru, 
- V případě nástroje ASP.NET Core Signal služba Azure Signal Service SDK otevírá 5 připojení protokolu WebSocket na rozbočovač ke službě signalizace. 
- Pro signál ASP.NET se v sadě SDK služby Azure Signal Service otevírá 5 připojení protokolu WebSocket podle rozbočovače do služby signalizace a jedno pro každé připojení k webovému soketu aplikace.

5 připojení WebSocket je výchozí hodnota, kterou je možné změnit v [konfiguraci](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount).

Zprávy klientům a z nich budou do těchto připojení zamultiplexované.

Tato připojení budou zůstat připojená ke službě signalizace po celou dobu. Pokud je připojení serveru odpojené kvůli problému v síti,
- Všichni klienti obsluhující toto připojení k serveru (Další informace najdete v tématu [přenos dat mezi klientem a serverem](#data-transmit-between-client-and-server));
- připojení k serveru se znovu spustí automaticky.

## <a name="client-connections"></a>Připojení klientů

Při použití služby signalizace se klienti připojují ke službě signalizace namísto aplikačního serveru.
Existují dva kroky k navázání trvalých připojení mezi klientem a službou Signal.

1. Klient odešle aplikačnímu serveru požadavek Negotiate. Pomocí sady SDK služby Azure Signaler vrátí aplikační server odpověď přesměrování pomocí adresy URL a přístupového tokenu služby signalizace.

- V případě ASP.NET Coreového signalizace vypadá typická reakce na přesměrování:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- V případě ASP.NET signalizace vypadá typická reakce na přesměrování:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Po přijetí odpovědi přesměrování použije klient novou adresu URL a přístupový token ke spuštění normálního procesu pro připojení ke službě signalizace.

Přečtěte si další informace o [přenosových protokolech](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md)ASP.NET Coreového signálu.

## <a name="data-transmit-between-client-and-server"></a>Přenos dat mezi klientem a serverem

Když je klient připojen ke službě signalizace, modul runtime služby zjistí připojení serveru, které bude obsluhovat tohoto klienta.
- Tento krok probíhá pouze jednou a jedná se o mapování 1:1 mezi klientem a serverem.
- Mapování se udržuje ve službě signalizace, dokud se klient nebo server odpojí.

V tomto okamžiku aplikační server obdrží událost s informacemi z nového klienta. Logické připojení ke klientovi je vytvořeno na aplikačním serveru. Datový kanál se naváže z klienta na aplikační server prostřednictvím služby signalizace.

Služba signalizace přenáší data z klienta na server spárované aplikace. A data z aplikačního serveru budou odeslána mapovaným klientům.

Služba signalizace neukládá ani neukládá zákaznická data, všechna přijatá zákaznická data se přenáší do cílového serveru nebo klientů v reálném čase.

Jak vidíte, služba signalizace Azure je v podstatě logickou přenosovou vrstvou mezi aplikačním serverem a klienty. Všechna trvalá připojení se přesměrují na službu Signal.
Aplikační server potřebuje zpracovat obchodní logiku jenom ve třídě centra, aniž by se museli starat o připojení klientů.