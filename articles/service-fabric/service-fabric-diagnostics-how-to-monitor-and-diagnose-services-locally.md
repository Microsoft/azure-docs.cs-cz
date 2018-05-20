---
title: Ladění Azure mikroslužeb v systému Windows | Microsoft Docs
description: Zjistěte, jak sledovat a diagnostikovat vaše služby vytvořené pomocí Microsoft Azure Service Fabric na místním vývojovém počítači.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 93cf4985e94c0af480d9f4e2e38b792cffe4df6e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorování a Diagnostika služby v instalačním programu pro vývoj místním počítači
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitorování, zjišťování, Diagnostika a řešení potíží s povolit pro služby, chcete-li pokračovat s minimálním dopadem na činnost koncového uživatele. Při monitorování a Diagnostika jsou kritické v prostředí skutečné produkční nasazené, efektivitu bude záviset na přijetí podobné modelu během vývoje služby zajistěte, aby byl že při přesunutí instalace reálného asi nebudou fungovat. Service Fabric usnadňuje vývojářům implementovat diagnostiky, které může bezproblémově fungovat v jednom počítači místní vývoj nastavení a nastavení clusteru skutečné produkční služby.

## <a name="event-tracing-for-windows"></a>Trasování událostí pro Windows
[Trasování událostí pro systém Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) je technologie doporučené pro trasování zpráv v Service Fabric. Jsou některé výhody použití trasování událostí pro Windows:

* **Trasování událostí pro Windows je rychlá.** Byla vytvořena jako trasování technologie, která má minimální dopad na dobu provádění kódu.
* **Trasování událostí pro Windows funguje bezproblémově mezi místní vývojové prostředí a taky nastavení reálného clusteru.** To znamená, že nemáte přepsání trasování kódu, když budete chtít kód nasadit do clusteru s podporou skutečné.
* **Service Fabric systému kód také používá trasování událostí pro Windows pro interní trasování.** To vám umožní zobrazit vaše trasování aplikací prokládaný. pomocí trasování systému Service Fabric. Také umožňuje snadno pochopit pořadí a vzájemné vztahy mezi kódu aplikace a události v základním systému.
* **Není integrovanou podporu v nástroji Service Fabric Visual Studio zobrazovat události trasování událostí pro Windows.** Události trasování událostí se zobrazí v zobrazení diagnostických událostí sady Visual Studio, jakmile sady Visual Studio je správně nakonfigurován s Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Zobrazit události systému Service Fabric v sadě Visual Studio
Service Fabric vysílá události trasování událostí pro Windows, což vývojářům aplikací pochopit, co se děje v platformu. Pokud jste tak již neučinili, pokračujte a postupujte podle kroků v [vytvoření vaší první aplikace v sadě Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md). Tyto informace vám pomohou aplikace spuštěná Diagnostika prohlížeči událostí zobrazuje trasovací zprávy.

1. Pokud k diagnostice události okno nezobrazí automaticky, přejděte na **zobrazení** v sadě Visual Studio, zvolte **ostatní okna** a potom **prohlížeče diagnostických událostí**.
2. Každá událost má standardní metadata informaci o uzlu, aplikace a služby, kterou událost pochází z. Můžete také filtrovat seznam událostí pomocí **filtrování událostí** pole v horní části okna události. Například můžete filtrovat podle **název uzlu** nebo **název služby.** A když se díváte na podrobnosti události, je také možné pozastavit pomocí **pozastavit** tlačítka v horní části okna události a pokračovat později, aniž by došlo ke ztrátě událostí.
   
   ![Prohlížeč událostí diagnostiky Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Přidání vlastního vlastní trasování do kódu aplikace
Šablony projektů Service Fabric Visual Studio obsahovat ukázkový kód. Kód ukazuje, jak přidat vlastní aplikaci trasování ETW kódu, které se zobrazí v prohlížeči Visual Studio ETW společně systému trasování z Service Fabric. Výhodou této metody je, že metadata jsou automaticky přidány do trasování a Prohlížeč událostí diagnostické sady Visual Studio již byla konfigurována pro jejich zobrazení.

Pro projekty vytvořené z **šablony služby** (bezstavové nebo stateful) také vyhledat `RunAsync` implementace:

1. Volání `ServiceEventSource.Current.ServiceMessage` v `RunAsync` metoda ukazuje příklad vlastního trasování událostí pro Windows trasování z kódu aplikace.
2. V **ServiceEventSource.cs** souboru, zjistíte, přetížení pro `ServiceEventSource.ServiceMessage` metoda, která má být použit pro vysoká frekvence událostí z důvodů výkonu.

Pro projekty vytvořené z **šablony objektu actor** (bezstavové nebo stateful):

1. Otevřete **"ProjectName".cs** souboru kde *ProjectName* je název, který jste zvolili pro svůj projekt sady Visual Studio.  
2. Najít kód `ActorEventSource.Current.ActorMessage(this, "Doing Work");` v *DoWorkAsync* metoda.  Toto je příklad vlastního trasování ETW zapsána z kódu aplikace.  
3. V souboru **ActorEventSource.cs**, zjistíte, přetížení pro `ActorEventSource.ActorMessage` metoda, která má být použit pro vysoká frekvence událostí z důvodů výkonu.

Po přidání vlastní trasování událostí pro Windows trasování do kódu služby, můžete vytvořit, nasadit a spusťte aplikaci znovu zobrazíte vaší událostí v prohlížeči diagnostických událostí. Pokud při ladění aplikace s **F5**, se automaticky otevře prohlížeč diagnostických událostí.

## <a name="next-steps"></a>Další postup
Stejný kód trasování, které jste přidali do vaší aplikace výše pro místní diagnostiky pracovat s nástroji, které můžete použít k zobrazení tyto události při spuštění aplikace v clusteru služby Azure. Podívejte se na tyto články, které popisují různé možnosti pro nástroje a popisují, jak je můžete nastavit tak.

* [Postup shromažďování protokolů pomocí Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md)
* [Seskupení událostí a kolekce pomocí EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

