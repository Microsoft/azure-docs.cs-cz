---
title: Novinky služby Azure SignalR
description: Přehled služby Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 198eb0ff6c9f8de311cc2d39ba8fb7c8b6ed3a11
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552077"
---
# <a name="what-is-azure-signalr-service"></a>Novinky služby Azure SignalR

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

- **[Škálování aplikace ASP.NET Core SignalR](signalr-concept-scale-aspnet-core.md)** – integrace služby Azure SignalR s aplikací ASP.NET Core SignalR pro horizontální navýšení kapacity na stovky tisíc připojení.
- **[Vytváření aplikací bez serveru pracujících v reálném čase](signalr-concept-azure-functions.md)** – integrace funkcí Azure se službou Azure SignalR pro účely vytváření aplikací bez serveru pracujících v reálném čase v jazycích, jako je JavaScript, C# nebo Java.
- **[Odesílání zpráv ze serveru do klientů přes rozhraní REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – ve službě Azure SignalR je k dispozici rozhraní REST API, které umožňuje posílat z aplikací zprávy do klientů spojených se službou SignalR v kterémkoli programovacím jazyce podporujícím rozhraní REST.