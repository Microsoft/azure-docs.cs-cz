---
title: Ladění Azure Service Fabric aplikací ve Windows | Dokumentace Microsoftu
description: Informace o monitorování a Diagnostika služeb napsané s využitím Microsoft Azure Service Fabric na místním vývojovém počítači.
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
ms.openlocfilehash: b4c3bc21591e8472dc8d51309f7431cb5d4421fd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054165"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorování a Diagnostika služeb v nastavení vývojového místního počítače
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitorování, zjišťování, Diagnostika a řešení potíží s povolit pro služby, abyste mohli pokračovat s minimálním dopadem na uživatelské prostředí. Monitorování a Diagnostika jsou kritické skutečný nasazené produkčního prostředí, efektivitu bude záviset na přijetí modelu podobně jako při vývoji služeb Ujistěte se, že fungují při přesunutí instalace reálného světa. Service Fabric umožňuje vývojářům služby k implementaci diagnostiku, která můžete bez problémů fungují s jeden počítač místního vývojového nastavení i nastavení skutečné produkční cluster.

## <a name="event-tracing-for-windows"></a>Trasování událostí pro Windows
[Trasování událostí pro Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) je doporučené technologické pro trasovací zprávy v Service Fabric. Některé výhody použití trasování událostí pro Windows jsou:

* **Trasování událostí pro Windows je rychlá.** Byla vytvořena jako technologie trasování, který má minimální dopad na dobu spuštění kódu.
* **Trasování událostí pro Windows bez problémů funguje napříč místní vývojové prostředí a také nastavení clusteru reálného světa.** To znamená, že nemusíte přepisovat kód vaší trasování, až budete připraveni k nasazení kódu do skutečným clusterem.
* **Kód systému Service Fabric také používá trasování událostí pro Windows pro interní trasování.** To umožňuje zobrazit trasování vaší aplikace proloženy trasování systému Service Fabric. Také umožňuje snadněji pochopit, pořadí a vztahy mezi kódu aplikace a události v základního systému.
* **Je integrovaná podpora v Service Fabric Visual Studio tools zobrazovat události trasování událostí pro Windows.** Události trasování událostí pro Windows se zobrazí v okně diagnostické události aplikace Visual Studio po sady Visual Studio je správně nakonfigurovaný s využitím Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Zobrazit události systému Service Fabric v sadě Visual Studio
Service Fabric vysílá události trasování událostí pro Windows, což vývojářům aplikací umožňuje pochopit, co se děje na platformě. Pokud jste tak již neučinili, pokračujte a postupujte podle kroků v [vytvoření vaší první aplikace v sadě Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md). Tyto informace vám pomohou aplikace pracovat s prohlížeč diagnostických událostí, zobrazení trasovacích zpráv.

1. Pokud diagnostické události okno automaticky nezobrazuje, přejděte na **zobrazení** kartu v sadě Visual Studio, zvolte **ostatní Windows** a potom **prohlížeče diagnostických událostí**.
2. Každou událost má standardní metadat informaci o uzlu, aplikace a služby, kterou událost pochází. Můžete také filtrovat seznam událostí s využitím **filtrovat události** pole v horní části okna událostí. Například můžete filtrovat podle **název uzlu** nebo **název služby.** A když se podíváte na podrobnosti o události, je také možné pozastavit pomocí **pozastavit** tlačítko v horní části okna událostí a později obnovit bez ztráty událostí.
   
   ![Prohlížeč událostí diagnostiky sady Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Přidání vlastního trasování do kódu aplikace
Šablony projektů Service Fabric Visual Studio obsahuje ukázkový kód. Kód ukazuje, jak přidat vlastní aplikaci kód trasování událostí pro Windows trasování, které se zobrazí v prohlížeči trasování událostí pro Windows Visual Studio spolu s trasování systému ze Service Fabric. Výhodou této metody je, že do trasování se automaticky přidají metadata a Prohlížeč událostí diagnostiky sady Visual Studio již byla konfigurována pro jejich zobrazení.

Pro projekty vytvořené z **šablon služeb** (Bezstavová nebo stavová) také vyhledat `RunAsync` implementace:

1. Volání `ServiceEventSource.Current.ServiceMessage` v `RunAsync` metoda ukazuje příklad vlastního trasování událostí pro Windows trasování z kódu aplikace.
2. V **ServiceEventSource.cs** souboru, bude najít přetížení pro `ServiceEventSource.ServiceMessage` metody, který se má použít pro vysokou frekvencí události z důvodů výkonu.

Pro projekty vytvořené z **šablony objektu actor** (Bezstavová nebo stavová):

1. Otevřít **.cs "ProjectName"** souboru where *ProjectName* je název, který jste zvolili pro svůj projekt sady Visual Studio.  
2. Kód, který `ActorEventSource.Current.ActorMessage(this, "Doing Work");` v *DoWorkAsync* metody.  Toto je příklad vlastního trasování událostí pro Windows trasování zapsat od kódu aplikace.  
3. V souboru **ActorEventSource.cs**, zjistíte přetížení pro `ActorEventSource.ActorMessage` metody, který se má použít pro vysokou frekvencí události z důvodů výkonu.

Po přidání vlastního trasování událostí pro Windows do kódu služby, můžete vytvořit, nasadit a spustit aplikaci znovu, abyste viděli váš událostí v prohlížeči diagnostických událostí. Při ladění aplikace s **F5**, automaticky otevře prohlížeči diagnostických událostí.

## <a name="next-steps"></a>Další postup
Stejný kód trasování, který jste přidali do vaší aplikace výše pro místní diagnostiky bude fungovat s nástroji, které můžete použít k zobrazení těchto událostí při spuštění aplikace v clusteru Azure. Prohlédněte si tyto články, které popisují různé možnosti pro nástroje a popisují, jak můžete nastavit jejich.

* [Shromažďování protokolů pomocí Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md)
* [Agregace událostí a kolekce pomocí využitím EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

