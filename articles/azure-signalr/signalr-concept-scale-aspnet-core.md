---
title: Škálování signálu ASP.NET Core pomocí služby Azure Signal
description: Přehled používání služby Azure SignalR ke škálování aplikací ASP.NET Core SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 90853b5ff769b710c6c95e4f6e62b3a4aa19fadf
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151071"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Škálování aplikací ASP.NET Core SignalR pomocí služby Azure SignalR

## <a name="developing-signalr-apps"></a>Vývoj aplikací SignalR

V současné době existují [dvě verze](/aspnet/core/signalr/version-differences) nástroje Signal, které můžete používat s vašimi webovými aplikacemi: signaler pro ASP.NET a signál ASP.NET Core, což je nejnovější verze. Služba Azure SignalR je služba spravovaná v Azure a založená na SignalR pro ASP.NET Core.

SignalR pro ASP.NET Core je přepsaná předchozí verze. To znamená, že SignalR pro ASP.NET Core není zpětně kompatibilní se starší verzí SignalR. Rozhraní API a chování se liší. Sada SDK služby SignalR pro ASP.NET Core cílí na .NET Standard, takže ji stále můžete používat s rozhraním .NET Framework. Místo starých rozhraní API však musíte použít nová rozhraní API. Pokud používáte SignalR a chcete přejít na SignalR pro ASP.NET Core neboli službu Azure SignalR, budete muset změnit svůj kód s ohledem na rozdíly v rozhraních API.

V případě služby Azure SignalR je serverová komponenta SignalR pro ASP.NET Core hostovaná v Azure. Vzhledem k tomu, že tato technologie využívá ASP.NET Core, však budete moct spouštět vlastní webové aplikace na několika platformách (Windows, Linux a MacOS), zatímco k hostování můžete použít [Azure App Service](../app-service/overview.md), [službu IIS](/aspnet/core/host-and-deploy/iis/index), [NGINX](/aspnet/core/host-and-deploy/linux-nginx), [Apache](/aspnet/core/host-and-deploy/linux-apache) nebo [Docker](/aspnet/core/host-and-deploy/docker/index). Ve vlastním procesu můžete využít také samoobslužné hostování.

Pokud mezi cíle vaší aplikace patří podpora nejnovějších funkcí pro aktualizace obsahu webových klientů v reálném čase, provoz na několika platformách (Azure, Windows, Linux a macOS) a hostování v různých prostředích, může být nejlepší volbou využít službu Azure SignalR.

## <a name="why-not-deploy-signalr-myself"></a>Proč nepoužít vlastní nasazení SignalR?

Stále můžete nasadit vlastní webovou aplikaci podporující SignalR pro ASP.NET Core jako back-endovou součást celkové webové aplikace.

Jedním z hlavních důvodů, proč použít službu Azure SignalR, je jednoduchost. Díky službě Azure SignalR nemusíte řešit problémy s výkonem, škálovatelností ani dostupností. Tyto problémy za vás řeší smlouva o úrovni služeb (SLA) zajišťující 99,9% dostupnost.

Kromě toho se pro zajištění podpory aktualizací obsahu v reálném čase obvykle upřednostňují protokoly WebSocket. Při škálování se však může z vyrovnávání zatížení velkého počtu trvalých připojení WebSocket stát složitý problém, který budete muset řešit. Běžná řešení využívají vyrovnávání zatížení DNS, hardwarové nástroje pro vyrovnávání zatížení a softwarové vyrovnávání zatížení. Služba Azure SignalR řeší tento problém za vás.

Dalším důvodem může být, že webovou aplikaci vlastně vůbec nepotřebujete hostovat. Logika vaší webové aplikace může využívat [architekturu bez serverů](https://azure.microsoft.com/overview/serverless-computing/). Váš kód například může být hostovaný a spouštěný pouze na vyžádání pomocí triggerů [Azure Functions](../azure-functions/index.yml). Tento scénář může být složitý, protože se váš kód spouští pouze na vyžádání a neudržuje dlouhé spojení s klienty. Služba Azure SignalR dokáže tuto situaci řešit díky tomu, že už za vás spravuje připojení. Podrobnosti najdete v [přehledu, jak používat službu SignalR s Azure Functions](signalr-concept-azure-functions.md).

## <a name="how-does-it-scale"></a>Jak se škáluje?

Je běžné škálovat signál pomocí SQL Server, Azure Service Bus nebo Azure cache pro Redis. Služba Azure SignalR se stará o postup škálování za vás. Výkon a náklady jsou srovnatelné s těmito postupy, ale bez složité správy těchto dalších služeb. Stačí pouze aktualizovat počet jednotek pro vaši službu. Každá jednotka podporuje až 1000 klientských připojení.

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření chatovací místnosti s využitím služby Azure SignalR](signalr-quickstart-dotnet-core.md)