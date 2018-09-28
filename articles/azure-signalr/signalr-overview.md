---
title: Co je Azure SignalR | Microsoft Docs
description: Přehled služby Azure SignalR
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: a159833936ec4762213f063e235fa4f9237af95b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951096"
---
# <a name="what-is-azure-signalr-service"></a>Co je služba Azure SignalR

Služba Azure SignalR zjednodušuje proces přidávání webových funkcí pracujících v reálném čase do aplikací přes HTTP. Tato funkce v reálném čase umožňuje službě nabízet aktualizace obsahu do připojených klientů, například jednu webovou stránku nebo mobilní aplikaci. Klienti díky tomu můžou získávat aktualizace, aniž by museli zadávat dotazy na server nebo odesílat nové požadavky HTTP na aktualizace.

Tento článek obsahuje přehled služby Azure SignalR.

## <a name="what-is-azure-signalr-service-used-for"></a>K čemu slouží služba Azure SignalR? 

Existuje řada typů aplikací, které vyžadují aktualizace obsahu v reálném čase. Použití služby Azure SignalR je vhodné v následujících příkladech:

* Aplikace, které vyžadují aktualizace ze serveru s vysokou frekvencí. Jako příklad je možné uvést hry, hlasování, aukce, mapy a aplikace využívající GPS.
* Řídicí panely a aplikace pro monitorování. Jako příklady je možné uvést řídicí panely společností a okamžité aktualizace informací o prodeji.
* Aplikace podporující spolupráci. Příklady aplikací podporujících spolupráci jsou aplikace tabulí a software pro týmové schůzky.
* Aplikace, které vyžadují oznámení. Oznámení využívají sociální sítě, e-mailové aplikace, chaty, hry, upozornění pro cestující a řada dalších aplikací.

Ve službě SignalR jsou k dispozici abstrakce pro řadu technik používaných k vytváření webových aplikací pracujících v reálném čase. Optimální pro přenos jsou protokoly [WebSocket](https://wikipedia.org/wiki/WebSocket), ale v případě, že nejsou dostupné jiné možnosti, se používají i jiné techniky, jako jsou [události na straně serveru (SSE)](https://wikipedia.org/wiki/Server-sent_events) a dlouhé intervaly dotazování. SignalR automaticky detekuje funkce, které server a klient podporují, a podle toho inicializuje odpovídající přenos.

Kromě toho je ve službě SignalR k dispozici programovací model pro aplikace pracující v reálném čase, který umožňuje serveru posílat zprávy do všech připojení nebo do podmnožiny připojení, která patří konkrétnímu uživateli nebo byla umístěna v dohodnuté skupině.

## <a name="how-to-use-azure-signalr-service"></a>Prozkoumání služby Azure SignalR

Aktuálně existují tři způsoby použití služby Azure SignalR:

- **[Škálování aplikace ASP.NET Core SignalR](signalr-overview-scale-aspnet-core.md)** – integrace služby Azure SignalR s aplikací ASP.NET Core SignalR pro horizontální navýšení kapacity na stovky tisíc připojení.
- **[Vytváření aplikací bez serveru pracujících v reálném čase](signalr-overview-azure-functions.md)** – integrace funkcí Azure se službou Azure SignalR pro účely vytváření aplikací bez serveru pracujících v reálném čase v jazycích, jako je JavaScript, C# nebo Java.
- **[Odesílání zpráv ze serveru do klientů přes rozhraní REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – ve službě Azure SignalR je k dispozici rozhraní REST API, které umožňuje posílat z aplikací zprávy do klientů spojených se službou SignalR v kterémkoli programovacím jazyce podporujícím rozhraní REST.

