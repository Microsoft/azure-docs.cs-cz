---
title: Co je Azure SignalR | Microsoft Docs
description: Přehled služby Azure SignalR
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: bc144fb1d7db9251871e7e181b012417a32de7e6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868105"
---
# <a name="what-is-azure-signalr-service"></a>Co je služba Azure SignalR

Služba Microsoft Azure SignalR je momentálně ve verzi [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure SignalR je služba Azure založená na [SignalR pro ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/introduction). SignalR pro ASP.NET Core je [open source knihovna](https://github.com/aspnet/signalr), která zjednodušuje proces přidávání webových funkcí pracujících v reálném čase do aplikací přes HTTP. Tato funkce pracující v reálném čase umožňuje webovému serveru nabízet aktualizace obsahu do připojených klientů. Klienti díky tomu můžou získávat aktualizace, aniž by museli zadávat dotazy na server nebo odesílat nové požadavky HTTP na aktualizace.

Tento článek obsahuje přehled služby Azure SignalR. Pokud chcete začít, přečtěte si [rychlý start pro ASP.NET Core](signalr-quickstart-dotnet-core.md).

## <a name="what-is-signalr-service-used-for"></a>K čemu se služba SignalR používá? 

Existuje řada typů aplikací, které vyžadují aktualizace obsahu v reálném čase. Vhodnými kandidáty pro použití služby Azure SignalR jsou následující příklady typů aplikací:

* Aplikace, které vyžadují aktualizace ze serveru s vysokou frekvencí. Příkladem jsou hry, sociální sítě, hlasování, aukce, mapy a aplikace využívající GPS.
* Řídicí panely a aplikace pro monitorování. Mezi příklady patří řídicí panely společností, okamžité a aktuální informace o prodeji nebo upozornění pro cestující.
* Aplikace podporující spolupráci. Příklady aplikací podporujících spolupráci jsou aplikace tabulí a software pro týmové schůzky.
* Aplikace, které vyžadují oznámení. Oznámení využívají sociální sítě, e-mailové aplikace, chaty, hry, upozornění pro cestující a řada dalších aplikací.

Vnitřně je služba SignalR abstrakcí nad celou řadou technik, které se používají k vytváření webových aplikací pracujících v reálném čase. Optimální pro přenos jsou protokoly [WebSocket](https://wikipedia.org/wiki/WebSocket), ale v případě, že nejsou dostupné jiné možnosti, se používají i jiné techniky, jako jsou [události na straně serveru (SSE)](https://wikipedia.org/wiki/Server-sent_events) a dlouhé intervaly dotazování. SignalR automaticky detekuje funkce, které server a klient podporují, a podle toho inicializuje odpovídající přenos.

## <a name="developing-signalr-apps"></a>Vývoj aplikací SignalR

V současnosti existují dvě verze SignalR, které můžete ve webových aplikacích použít: SignalR pro ASP.NET a novější verze SignalR pro ASP.NET Core. Služba Azure SignalR je služba spravovaná v Azure a založená na SignalR pro ASP.NET Core. 

SignalR pro ASP.NET Core je přepsaná předchozí verze. To znamená, že SignalR pro ASP.NET Core není zpětně kompatibilní se starší verzí SignalR. Rozhraní API a chování se liší. Sada SDK služby SignalR pro ASP.NET Core je napsaná v .NET Standard, takže ji stále můžete používat s rozhraním .NET Framework. Místo starých rozhraní API však musíte použít nová rozhraní API. Pokud používáte SignalR a chcete přejít na SignalR pro ASP.NET Core neboli službu Azure SignalR, budete muset změnit svůj kód s ohledem na rozdíly v rozhraních API.

V případě služby Azure SignalR je serverová komponenta SignalR pro ASP.NET Core hostovaná v Azure. Vzhledem k tomu, že tato technologie využívá ASP.NET Core, však budete moct spouštět vlastní webové aplikace na několika platformách (Windows, Linux a MacOS), zatímco k hostování můžete použít [Azure App Service](../app-service/app-service-web-overview.md), [službu IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [NGINX](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) nebo [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). Ve vlastním procesu můžete využít také samoobslužné hostování.

Pokud mezi cíle vaší aplikace patří podpora nejnovějších funkcí pro aktualizace obsahu webových klientů v reálném čase, provoz na několika platformách (Azure, Windows, Linux a MacOS) a hostování v různých prostředích, může být nejlepší volbou využít službu Azure SignalR.


## <a name="why-not-deploy-signalr-myself"></a>Proč nepoužít vlastní nasazení SignalR?

Stále můžete nasadit vlastní webovou aplikaci podporující SignalR pro ASP.NET Core jako back-endovou součást celkové webové aplikace.

Jedním z hlavních důvodů, proč použít službu Azure SignalR, je jednoduchost. Díky službě Azure SignalR nemusíte řešit problémy s výkonem, škálovatelností ani dostupností. Tyto problémy za vás řeší smlouva o úrovni služeb (SLA) zajišťující 99,9% dostupnost.

Kromě toho se pro zajištění podpory aktualizací obsahu v reálném čase obvykle upřednostňují protokoly WebSocket. Při škálování se však může z vyrovnávání zatížení velkého počtu trvalých připojení WebSocket stát složitý problém, který budete muset řešit. Běžná řešení využívají vyrovnávání zatížení DNS, hardwarové nástroje pro vyrovnávání zatížení a softwarové vyrovnávání zatížení. Služba Azure SignalR řeší tento problém za vás.

Dalším důvodem může být, že webovou aplikaci vlastně vůbec nepotřebujete hostovat. Logika vaší webové aplikace může využívat [architekturu bez serverů](https://azure.microsoft.com/overview/serverless-computing/). Váš kód například může být hostovaný a spouštěný pouze na vyžádání pomocí triggerů [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Tento scénář může být složitý, protože se váš kód spouští pouze na vyžádání a neudržuje dlouhé spojení s klienty. Služba Azure SignalR dokáže tuto situaci řešit díky tomu, že už za vás spravuje připojení.

## <a name="how-does-it-scale"></a>Jak se škáluje?

SignalR se běžně škáluje s využitím SQL Serveru, služby Azure Service Bus nebo Redis Cache. Služba Azure SignalR se stará o postup škálování za vás. Výkon a náklady jsou srovnatelné s těmito postupy, ale bez složité správy těchto dalších služeb. Stačí pouze aktualizovat počet jednotek pro vaši službu. Každá jednotka služby podporuje až 1 000 klientských připojení.

## <a name="next-steps"></a>Další kroky
* [Rychlý start: Vytvoření chatovací místnosti s využitím služby Azure SignalR](signalr-quickstart-dotnet-core.md)  
  

