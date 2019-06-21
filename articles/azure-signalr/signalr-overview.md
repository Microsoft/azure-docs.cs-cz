---
title: Novinky služby Azure SignalR
description: Přehled služby Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 06/20/2019
ms.author: zhshang
ms.openlocfilehash: dc7ba3585ec49921c0a0e66185fc5550d3d4a006
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303626"
---
# <a name="what-is-azure-signalr-service"></a>Novinky služby Azure SignalR

Služba Azure SignalR zjednodušuje proces přidávání webových funkcí pracujících v reálném čase do aplikací přes HTTP. Tato funkce v reálném čase umožňuje službě nabízet aktualizace obsahu do připojených klientů, například jednu webovou stránku nebo mobilní aplikaci. Klienti díky tomu můžou získávat aktualizace, aniž by museli zadávat dotazy na server nebo odesílat nové požadavky HTTP na aktualizace.


Tento článek obsahuje přehled služby Azure SignalR.

## <a name="what-is-azure-signalr-service-used-for"></a>K čemu slouží služba Azure SignalR?

Jakýkoli scénář, který vyžaduje předání dat ze serveru klientovi v reálném čase pomocí služby Azure SignalR.

Tradiční funkcemi v reálném čase, které často vyžadují dotazování ze serveru, můžete také použít služby Azure SignalR.

Službě Azure SignalR byla použita v široké paletě oborů, pro libovolný typ aplikace, který vyžaduje aktualizace obsahu v reálném čase. Uvádíme několik příkladů, které jsou vhodné pro použití služby Azure SignalR:

* **Vysoká frekvence aktualizace dat:** hraní her, hlasování, dotazování, aukce.
* **Monitorování a řídicí panely:** firemní řídicí panel, finanční trhy. data, okamžité aktualizace prodeje, více hráčů her tabulky výsledků a sledování IoT.
* **Konverzace:** live chatovací místnosti, chatovací robot, online zákaznickou podporu, v reálném čase nákupní Pomocníka s nastavením, messenger, konverzace v rámci her a tak dále.
* **Umístění v reálném čase na mapě:** logistické sledování, sledování stavu doručování, aktualizace stavu dopravy, GPS aplikace.
* **Reálném čase cílené reklamy:** přizpůsobené reklamy čtení čas nabízených oznámení a nabídky, interaktivní reklamy.
* **Aplikace pro spolupráci:** spoluvytváření Tabule aplikace a tým splnění softwaru.
* **Nabízená oznámení:** sociálních sítí, e-mailu, hry, cestování upozornění.
* **Všesměrové vysílání v reálném čase:** živé vysílání zvuku a videa, živé vysílání titulků, překladu, události a zprávy.
* **IoT a připojená zařízení:** metrik v reálném čase IoT, vzdálené řízení, stav v reálném čase a sledování polohy.
* **Automatizace:** z odesílání dat události v reálném čase triggeru.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Jaké jsou výhody používání služby Azure SignalR?

**Standard, na základě:**

Ve službě SignalR jsou k dispozici abstrakce pro řadu technik používaných k vytváření webových aplikací pracujících v reálném čase. Optimální pro přenos jsou protokoly [WebSocket](https://wikipedia.org/wiki/WebSocket), ale v případě, že nejsou dostupné jiné možnosti, se používají i jiné techniky, jako jsou [události na straně serveru (SSE)](https://wikipedia.org/wiki/Server-sent_events) a dlouhé intervaly dotazování. SignalR automaticky detekuje funkce, které server a klient podporují, a podle toho inicializuje odpovídající přenos.

**Nativní podpora ASP.NET Core:**

Služby SignalR poskytuje nativní programovací prostředí s ASP.NET Core a ASP.NET. Vývoj nové aplikace SignalR službou SignalR nebo migraci z existující SignalR na základě aplikace pro služby SignalR vyžaduje minimálním úsilí.
Služby SignalR také podporuje novou funkci ASP.NET Core, Blazor na straně serveru.

**Podpora širokého klientů:**

Služby SignalR funguje se širokou škálu klientů, jako jsou webové a mobilní prohlížeče, aplikací klasické pracovní plochy, mobilních aplikací, proces serveru, zařízení IoT a herní konzoly. Sady SDK pro služby SignalR nabízí v různých jazycích. Kromě nativní ASP.NET Core nebo ASP.NET C# sad SDK, SignalR Service navíc poskytuje JavaScript Klientská sada SDK webových klientů a mnoho rozhraní JavaScript povolit. Klientskou sadou Java SDK je také podporována pro aplikace v Javě, včetně nativních aplikací pro Android. Podporuje rozhraní REST API služby SignalR a bez serveru prostřednictvím integrace s Azure Functions a Event Grid.

**Zpracování rozsáhlých klientských připojení:**

Služby SignalR je určená pro aplikace ve velkém měřítku v reálném čase. Služby SignalR umožňuje více instancí, aby spolupracovaly na škálování na miliony připojení klientů. Služba také podporuje více globálních oblastí pro horizontální dělení, vysokou dostupnost a zotavení po havárii.

**Odebrání zatížení k samoobslužnému hostování SignalR:**

Ve srovnání s místním prostředí aplikace SignalR, přepínání pro služby SignalR bude zbavují uživatele nutnosti provádět správu back plochy, které zpracovávají měřítek a připojení klientů. Plně spravovaná služba taky zjednodušuje webových aplikací a šetří náklady na hostování. Služby SignalR nabízí globálním dosahem a špičkové datové centrum a síť, škáluje milionům připojení, zaručuje smlouvu SLA, poskytuje dodržování předpisů a zabezpečení v Azure standard.

![Spravované služby SignalR](./media/signalr-overview/managed-signalr-service.png)

**Nabídka bohatá rozhraní API pro různé způsoby přenosu zpráv:**

Služby SignalR umožňuje serveru odesílat zprávy pro konkrétní připojení, všechna připojení nebo podmnožinu připojení, která patří do konkrétního uživatele, nebo byly umístěny v libovolné skupině.

## <a name="how-to-use-azure-signalr-service"></a>Prozkoumání služby Azure SignalR

Do aplikace SignalR službou Azure, jako některé z ukázek, které jsou tady uvedené mnoha různými způsoby:

- **[Škálování aplikace ASP.NET Core SignalR](signalr-concept-scale-aspnet-core.md)** – integrace služby Azure SignalR s aplikací ASP.NET Core SignalR pro horizontální navýšení kapacity na stovky tisíc připojení.
- **[Vytváření aplikací bez serveru pracujících v reálném čase](signalr-concept-azure-functions.md)** – integrace funkcí Azure se službou Azure SignalR pro účely vytváření aplikací bez serveru pracujících v reálném čase v jazycích, jako je JavaScript, C# nebo Java.
- **[Odesílání zpráv ze serveru do klientů přes rozhraní REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – ve službě Azure SignalR je k dispozici rozhraní REST API, které umožňuje posílat z aplikací zprávy do klientů spojených se službou SignalR v kterémkoli programovacím jazyce podporujícím rozhraní REST.