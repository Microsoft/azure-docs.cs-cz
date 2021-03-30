---
title: Co je služba Azure SignalR Service?
description: Lépe pochopíte typické scénáře použití pro používání služby Azure Signal a Naučte se klíčové výhody služby Azure Signal.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 92da400616ace9f24da121d81798d5ebd9222e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88921881"
---
# <a name="what-is-azure-signalr-service"></a>Co je služba Azure SignalR Service?

Služba Azure SignalR zjednodušuje proces přidávání webových funkcí pracujících v reálném čase do aplikací přes HTTP. Tato funkce v reálném čase umožňuje službě nabízet aktualizace obsahu do připojených klientů, například jednu webovou stránku nebo mobilní aplikaci. Klienti díky tomu můžou získávat aktualizace, aniž by museli zadávat dotazy na server nebo odesílat nové požadavky HTTP na aktualizace.


Tento článek obsahuje přehled služby Azure SignalR.

## <a name="what-is-azure-signalr-service-used-for"></a>K čemu slouží služba Azure SignalR?

Libovolný scénář, který vyžaduje vložení dat ze serveru do klienta v reálném čase, může používat službu Azure Signal Service.

Pro tradiční funkce v reálném čase, které často vyžadují dotazování ze serveru, může také používat službu Azure Signal Service.

Služba signalizace Azure se použila v nejrůznějších odvětvích, a to u libovolného typu aplikace, který vyžaduje aktualizace obsahu v reálném čase. Uvádíme několik příkladů, které jsou vhodné pro použití služby signalizace Azure:

* **Aktualizace dat s vysokou frekvencí:** hraní her, hlasování, dotazování, aukce.
* **Řídicí panely a monitorování:** řídicí panel společnosti, data finančního trhu, aktualizace rychlých prodejů, vedoucí hry pro více hráčů a sledování IoT.
* **Chat:** živé chatovací místnosti, chat robota, online podpora zákazníků, Pomocník pro nakupování v reálném čase, Messenger, konverzace v herním zařízení atd.
* **Umístění v reálném čase na mapě:** logistické sledování, sledování stavu doručování, aktualizace stavu přenosu, aplikace GPS.
* **Cílené reklamy v reálném čase:** individuální inzeráty a nabídky nabízených oznámení v reálném čase, interaktivní reklamy.
* **Aplikace pro spolupráci:** spoluvytváření, aplikace tabule a software schůzky týmu.
* **Nabízená oznámení:** sociální síť, e-mail, hra, oznámení o cestování.
* **Všesměrové vysílání v reálném čase:** živé vysílání zvuku a videa, živé titulky, překlady, události a šíření zpráv.
* **IoT a připojená zařízení:** metriky IoT v reálném čase, vzdálené řízení, stav v reálném čase a sledování polohy.
* **Automatizace:** aktivační událost v reálném čase z nadřazených událostí.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Jaké výhody využívá služba Azure Signaler?

**Založené na standardu:**

Ve službě SignalR jsou k dispozici abstrakce pro řadu technik používaných k vytváření webových aplikací pracujících v reálném čase. Optimální pro přenos jsou protokoly [WebSocket](https://wikipedia.org/wiki/WebSocket), ale v případě, že nejsou dostupné jiné možnosti, se používají i jiné techniky, jako jsou [události na straně serveru (SSE)](https://wikipedia.org/wiki/Server-sent_events) a dlouhé intervaly dotazování. SignalR automaticky detekuje funkce, které server a klient podporují, a podle toho inicializuje odpovídající přenos.

**Podpora nativního ASP.NET Core:**

Služba signalizace poskytuje nativní prostředí pro programování ASP.NET Core a ASP.NET. Vývoj nové aplikace pro signalizaci pomocí služby signalizace nebo migrace z existující aplikace založené na signalizaci na službu Signal vyžaduje minimální úsilí.
Služba signalizace podporuje také nové funkce ASP.NET Core Blazor na straně serveru.

**Široká podpora klientů:**

Služba signalizace spolupracuje s širokou škálou klientů, jako jsou webové a mobilní prohlížeče, desktopové aplikace, mobilní aplikace, serverový proces, zařízení IoT a herní konzoly. Služba signalizace nabízí sady SDK v různých jazycích. Kromě nativních ASP.NET Core nebo ASP.NETch sad C# SDK služba Signal Service také poskytuje klientskou sadu SDK pro JavaScript, pro povolení webových klientů a mnoho rozhraní JavaScript. Klientská sada SDK pro Java je podporována také pro aplikace v jazyce Java, včetně nativních aplikací pro Android. Služba signalizace podporuje REST API a bez serveru prostřednictvím integrace s Azure Functions a Event Grid.

**Zpracování rozsáhlých připojení klientů:**

Služba signalizace je navržená pro aplikace v reálném čase ve velkém měřítku. Služba signalizace umožňuje více instancím spolupracovat pro škálování na miliony připojení klientů. Služba také podporuje více globálních oblastí pro účely horizontálního dělení, vysoké dostupnosti nebo zotavení po havárii.

**Odstraňte zátěž pro samoobslužného hostitele:**

V porovnání s aplikacemi signalizace v místním prostředí, přepnutí na službu Signaler odebere nutnost spravovat zpětnou rovinu, která bude zpracovávat škály a připojení klientů. Plně spravovaná služba také zjednodušuje webové aplikace a ukládá náklady na hostování. Služba signalizace nabízí globální dosažitelné datové centrum a síť světové úrovně, škáluje se s miliony připojení, garantuje smlouvu SLA a současně zajišťuje veškeré dodržování předpisů a zabezpečení ve službě Azure Standard.

![Spravovaná služba signálu](./media/signalr-overview/managed-signalr-service.png)

**Nabízí bohatá rozhraní API pro různé vzory zasílání zpráv:**

Služba signalizace umožňuje serveru posílat zprávy do konkrétního připojení, všechna připojení nebo podmnožinu připojení, která patří konkrétnímu uživateli, nebo být umístěná v libovolné skupině.

## <a name="how-to-use-azure-signalr-service"></a>Prozkoumání služby Azure SignalR

Existuje mnoho různých způsobů, jak programovat se službou Azure Signal Service, jako některé z uvedených ukázek:

- **[Škálování aplikace ASP.NET Core SignalR](signalr-concept-scale-aspnet-core.md)** – integrace služby Azure SignalR s aplikací ASP.NET Core SignalR pro horizontální navýšení kapacity na stovky tisíc připojení.
- **[Vytváření aplikací bez serveru pracujících v reálném čase](signalr-concept-azure-functions.md)** – integrace funkcí Azure se službou Azure SignalR pro účely vytváření aplikací bez serveru pracujících v reálném čase v jazycích, jako je JavaScript, C# nebo Java.
- **[Odesílání zpráv ze serveru do klientů přes rozhraní REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – ve službě Azure SignalR je k dispozici rozhraní REST API, které umožňuje posílat z aplikací zprávy do klientů spojených se službou SignalR v kterémkoli programovacím jazyce podporujícím rozhraní REST.
