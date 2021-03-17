---
title: Ladění aplikací pro Azure Service Fabric v systému Windows
description: Naučte se monitorovat a diagnostikovat vaše služby napsané pomocí Microsoft Azure Service Fabric na místním vývojovém počítači.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 58319b47c78a85b4f06c2c834db20f6c42cc1939
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247417"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorování a diagnostika služeb v nastavení místních počítačů pro vývoj
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitorování, detekce, diagnostika a řešení potíží umožní službám pokračovat s minimálním přerušením uživatelského prostředí. I když jsou monitorování a diagnostika v samotném nasazeném provozním prostředí kritické, efektivita bude záviset na tom, jak při vývoji služeb používat podobný model, abyste zajistili, že budou fungovat při přesunu do reálného nastavení. Service Fabric usnadňuje vývojářům služeb implementovat diagnostiku, která dokáže hladce fungovat v rámci místních nastavení pro vývoj v jednom počítači a v reálných instalacích produkčních clusterů.

## <a name="event-tracing-for-windows"></a>Trasování událostí pro Windows
[Trasování událostí pro Windows](/windows/win32/etw/event-tracing-portal) (ETW) je doporučená technologie pro trasování zpráv v Service Fabric. Mezi výhody používání ETW patří:

* **Trasování událostí pro Windows je rychlé.** Byla sestavena jako technologie trasování, která má minimální dopad na doby spuštění kódu.
* **Trasování ETW funguje bez problémů v místních vývojových prostředích i v reálných nastaveních clusteru.** To znamená, že nemusíte přepisovat kód trasování, když jste připraveni nasadit kód do reálného clusteru.
* **Service Fabric systémový kód také používá ETW k internímu trasování.** To vám umožní zobrazit trasování vaší aplikace Service Fabric trasování systému. Pomůže vám také snadněji pochopit sekvence a vzájemné vztahy mezi kódem aplikace a událostmi v podkladovém systému.
* **V Service Fabricch nástrojích sady Visual Studio je integrovaná podpora pro zobrazení událostí ETW.** Události ETW se zobrazí v zobrazení diagnostické události v aplikaci Visual Studio, jakmile je Visual Studio správně nakonfigurováno pomocí Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Zobrazení Service Fabric systémových událostí v aplikaci Visual Studio
Service Fabric generuje události ETW, které vývojářům aplikací pomůžou pochopit, co se děje na platformě. Pokud jste to ještě neudělali, pokračujte podle kroků v části [Vytvoření první aplikace v aplikaci Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Tyto informace vám pomůžou zajistit, aby aplikace běžela v prohlížeči diagnostických událostí, které zobrazují zprávy trasování.

1. Pokud se okno diagnostické události nezobrazí automaticky, na kartě **zobrazení** v aplikaci Visual Studio zvolte **Další okna** a pak na **prohlížeč diagnostických událostí**.
2. Každá událost má standardní informace o metadatech, které vám sdělují uzel, aplikaci a službu, ze které pochází událost. Seznam událostí můžete filtrovat také pomocí pole **Filtrovat události** v horní části okna události. Můžete například filtrovat podle **názvu uzlu** nebo **názvu služby.** A když se díváte na podrobnosti události, můžete také pozastavit pomocí tlačítka **pozastavit** v horní části okna události a pokračovat později bez ztráty událostí.
   
   ![Prohlížeč událostí diagnostiky sady Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Přidat vlastní trasování do kódu aplikace
Šablony projektu Service Fabric sady Visual Studio obsahují vzorový kód. Kód ukazuje, jak přidat vlastní trasování ETW kódu aplikace, které se zobrazí v prohlížeči ETW sady Visual Studio spolu s trasováním systému z Service Fabric. Výhodou této metody je, že metadata jsou automaticky přidána do trasování a prohlížeč diagnostických událostí sady Visual Studio je již nakonfigurován k jejich zobrazení.

Pro projekty vytvořené ze **šablon služeb** (bezstavové nebo stavové) stačí vyhledat `RunAsync` implementaci:

1. Volání `ServiceEventSource.Current.ServiceMessage` v `RunAsync` metodě ukazuje příklad vlastního trasování ETW z kódu aplikace.
2. V souboru **ServiceEventSource.cs** naleznete přetížení pro `ServiceEventSource.ServiceMessage` metodu, která se má použít pro události s vysokou frekvencí z důvodů výkonu.

Pro projekty vytvořené z **šablon objektu actor** (Bezstavová nebo stavová):

1. Otevřete soubor **ProjectName". cs** , kde *ProjectName* je název, který jste zvolili pro projekt sady Visual Studio.  
2. Vyhledejte kód `ActorEventSource.Current.ActorMessage(this, "Doing Work");` v metodě *DoWorkAsync* .  Toto je příklad vlastního trasování ETW napsaného z kódu aplikace.  
3. V souboru **ActorEventSource.cs**naleznete přetížení pro `ActorEventSource.ActorMessage` metodu, která se má použít pro události s vysokou frekvencí z důvodů výkonu.

Po přidání vlastního trasování ETW do kódu služby můžete aplikaci sestavit, nasadit a spustit znovu, abyste viděli události v prohlížeči diagnostických událostí. Pokud ladění aplikace pomocí nástroje **F5**, otevře se prohlížeč diagnostických událostí automaticky.

## <a name="next-steps"></a>Další kroky
Stejný trasovací kód, který jste přidali do aplikace výše pro místní diagnostiku, bude fungovat s nástroji, které můžete použít k zobrazení těchto událostí při spuštění aplikace v clusteru Azure. Podívejte se na tyto články, které popisují různé možnosti nástrojů, a popište, jak je můžete nastavit.

* [Postup shromažďování protokolů pomocí Azure Diagnostics](./service-fabric-diagnostics-event-aggregation-wad.md)
* [Agregace a shromažďování událostí pomocí využitím eventflow](service-fabric-diagnostics-event-aggregation-eventflow.md)
