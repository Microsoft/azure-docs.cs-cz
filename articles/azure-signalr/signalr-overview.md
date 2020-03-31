---
title: Co je služba Azure SignalR?
description: Lépe pochopit, jaké scénáře typické použití případu používat Azure SignalR a naučit se klíčové výhody Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 31cb4570dd0952f60fbff2fa1131b3af36c278cb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74157588"
---
# <a name="what-is-azure-signalr-service"></a>Co je služba Azure SignalR?

Služba Azure SignalR zjednodušuje proces přidávání webových funkcí pracujících v reálném čase do aplikací přes HTTP. Tato funkce v reálném čase umožňuje službě nabízet aktualizace obsahu do připojených klientů, například jednu webovou stránku nebo mobilní aplikaci. Klienti díky tomu můžou získávat aktualizace, aniž by museli zadávat dotazy na server nebo odesílat nové požadavky HTTP na aktualizace.


Tento článek obsahuje přehled služby Azure SignalR.

## <a name="what-is-azure-signalr-service-used-for"></a>K čemu slouží služba Azure SignalR?

Jakýkoli scénář, který vyžaduje odesílání dat ze serveru do klienta v reálném čase, můžete použít službu Azure SignalR.

Tradiční funkce v reálném čase, které často vyžadují dotazování ze serveru, můžete také použít službu Azure SignalR.

Služba Azure SignalR se používá v široké škále odvětví pro všechny typy aplikací, které vyžadují aktualizace obsahu v reálném čase. Uvádíme některé příklady, které je vhodné používat službu Azure SignalR:

* **Aktualizace vysokofrekvenčních dat:** hraní her, hlasování, hlasování, aukce.
* **Řídicí panely a monitorování:** řídicí panel společnosti, data finančního trhu, okamžitá aktualizace prodeje, žebříček her pro více hráčů a monitorování IoT.
* **Chat:** live chat ovací místnost, chat bot, on-line zákaznická podpora, nákupní asistent v reálném čase, messenger, herní chat a tak dále.
* **Poloha v reálném čase na mapě:** sledování logistiky, sledování stavu doručení, aktualizace stavu přepravy, aplikace GPS.
* **Cílené reklamy v reálném čase:** personalizované reklamy a nabídky v reálném čase, interaktivní reklamy.
* **Aplikace pro spolupráci:** spoluvytváření, aplikace tabule a software pro týmové schůzky.
* **Push oznámení:** sociální síť, e-mail, hra, cestovní upozornění.
* **Vysílání v reálném čase:** živé audio/video vysílání, živé titulky, překlady, události/zpravodajství.
* **IoT a připojená zařízení:** metriky IoT v reálném čase, dálkové ovládání, stav v reálném čase a sledování polohy.
* **Automatizace:** aktivační událost v reálném čase z událostí upstream.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Jaké jsou výhody využívající službu Azure SignalR?

**Standardní bázi:**

Ve službě SignalR jsou k dispozici abstrakce pro řadu technik používaných k vytváření webových aplikací pracujících v reálném čase. Optimální pro přenos jsou protokoly [WebSocket](https://wikipedia.org/wiki/WebSocket), ale v případě, že nejsou dostupné jiné možnosti, se používají i jiné techniky, jako jsou [události na straně serveru (SSE)](https://wikipedia.org/wiki/Server-sent_events) a dlouhé intervaly dotazování. SignalR automaticky detekuje funkce, které server a klient podporují, a podle toho inicializuje odpovídající přenos.

**Podpora jádra nativní ASP.NET:**

SignalR Service poskytuje nativní programovací zkušenosti s ASP.NET Core a ASP.NET. Vývoj nové aplikace SignalR se službou SignalR nebo migrace z existující aplikace založené na SignalR na službu SignalR vyžaduje minimální úsilí.
SignalR Service také podporuje ASP.NET nové funkce Core, Server-side Blazor.

**Široká klientská podpora:**

Služba SignalR spolupracuje se širokou škálou klientů, jako jsou webové a mobilní prohlížeče, desktopové aplikace, mobilní aplikace, serverový proces, zařízení IoT a herní konzole. SignalR Service nabízí sady SDK v různých jazycích. Kromě nativníASP.NET jádra nebo ASP.NET sady SDK C# poskytuje služba SignalR také sadu JavaScript client SDK, která umožňuje webové klienty a mnoho rozhraní JavaScript. Sada Java client SDK je podporována také pro java aplikace, včetně nativních aplikací pro Android. Služba SignalR podporuje rozhraní REST API a bez serveru prostřednictvím integrace s funkcemi Azure a gridem událostí.

**Zpracování připojení velkých klientů:**

SignalR Service je určena pro rozsáhlé aplikace v reálném čase. Služba SignalR umožňuje více instancí spolupracovat škálovat na miliony připojení klientů. Služba také podporuje více globálních oblastí pro účely sytí, vysoké dostupnosti nebo zotavení po havárii.

**Odstraňte zátěž pro samoobslužný signalr:**

Ve srovnání s aplikacemi SignalR, které jsou hostovány samostatně, přepnutí na službu SignalR odstraní potřebu spravovat zadní roviny, které zpracovávají váhy a připojení klientů. Plně spravovaná služba také zjednodušuje webové aplikace a šetří náklady na hostování. Služba SignalR nabízí globální dosah a datové centrum a síť světové úrovně, škáluje na miliony připojení, zaručuje sla a zároveň poskytuje veškeré dodržování předpisů a zabezpečení na standardu Azure.

![Spravovanou službu SignalR](./media/signalr-overview/managed-signalr-service.png)

**Nabídněte bohatá api pro různé vzory zasílání zpráv:**

Služba SignalR umožňuje serveru odesílat zprávy určitému připojení, všem připojením nebo podmnožině připojení, která patří určitému uživateli nebo byla umístěna do libovolné skupiny.

## <a name="how-to-use-azure-signalr-service"></a>Prozkoumání služby Azure SignalR

Existuje mnoho různých způsobů, jak programovat pomocí služby Azure SignalR, protože některé ukázky uvedené zde:

- **[Škálování aplikace ASP.NET Core SignalR](signalr-concept-scale-aspnet-core.md)** – integrace služby Azure SignalR s aplikací ASP.NET Core SignalR pro horizontální navýšení kapacity na stovky tisíc připojení.
- **[Vytváření aplikací bez serveru pracujících v reálném čase](signalr-concept-azure-functions.md)** – integrace funkcí Azure se službou Azure SignalR pro účely vytváření aplikací bez serveru pracujících v reálném čase v jazycích, jako je JavaScript, C# nebo Java.
- **[Odesílání zpráv ze serveru do klientů přes rozhraní REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – ve službě Azure SignalR je k dispozici rozhraní REST API, které umožňuje posílat z aplikací zprávy do klientů spojených se službou SignalR v kterémkoli programovacím jazyce podporujícím rozhraní REST.
