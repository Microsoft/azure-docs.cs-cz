---
title: Ladění aplikací Azure Service Fabric ve Windows
description: Zjistěte, jak monitorovat a diagnostikovat vaše služby napsané pomocí microsoft azure service fabric na místním vývojovém počítači.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8435bb82afddd0070679768bb8d22ad9290f2279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258509"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorování a diagnostika služeb v nastavení místních počítačů pro vývoj
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitorování, zjišťování, diagnostika a řešení potíží umožňují, aby služby pokračovaly s minimálním narušením uživatelského prostředí. Zatímco monitorování a diagnostika jsou důležité ve skutečném nasazeném produkčním prostředí, efektivita bude záviset na přijetí podobného modelu během vývoje služeb, aby bylo zajištěno, že fungují, když přejdete do reálného nastavení. Service Fabric usnadňuje vývojářům služeb implementovat diagnostiku, která může bezproblémově fungovat jak v nastavení místního vývoje jednoho počítače, tak v reálných produkčních nastaveních clusteru.

## <a name="event-tracing-for-windows"></a>Trasování událostí pro Windows
[Trasování událostí pro Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) je doporučená technologie pro trasování zpráv v Service Fabric. Některé výhody používání ETW jsou:

* **ETW je rychlý.** Byl vytvořen jako technologie trasování, která má minimální dopad na dobu provádění kódu.
* **Trasování ETW funguje bez problémů v prostředích místního vývoje a také reálných nastavení clusterů.** To znamená, že není třeba přepsat trasovací kód, když jste připraveni nasadit kód do skutečného clusteru.
* **Kód systému Service Fabric také používá ETW pro interní trasování.** To umožňuje zobrazit trasování aplikace prokládáné trasování service fabric systému trasování. Také vám pomůže snadněji pochopit sekvence a vzájemné vztahy mezi kódem aplikace a událostmi v podkladovém systému.
* **K dispozici je integrovaná podpora v nástrojích Service Fabric Visual Studio pro zobrazení událostí ETW.** Události ETW se zobrazí v zobrazení události diagnostiky sady Visual Studio, jakmile je Visual Studio správně nakonfigurováno pomocí service fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Zobrazení systémových událostí Service Fabric v sadě Visual Studio
Service Fabric vydává události ETW, které vývojářům aplikací pomáhají pochopit, co se děje na platformě. Pokud jste tak ještě neučinili, pokračujte a [postupujte](service-fabric-tutorial-create-dotnet-app.md)podle pokynů v části Vytvoření první aplikace v sadě Visual Studio . Tyto informace vám pomohou zprovoznit aplikaci pomocí prohlížeče událostí diagnostiky zobrazujícího trasovací zprávy.

1. Pokud se okno události diagnostiky nezobrazuje automaticky, přejděte na kartu **Zobrazení** v sadě Visual Studio, zvolte **Jiný systém Windows** a potom prohlížeč **diagnostických událostí**.
2. Každá událost má standardní informace o metadatech, které vám sdělí uzel, aplikaci a službu, ze kterých událost přichází. Seznam událostí můžete také filtrovat pomocí pole **Filtrovat události** v horní části okna událostí. Můžete například filtrovat **název uzlu** nebo název **služby.** A když se díváte na podrobnosti o události, můžete také pozastavit pomocí tlačítka **Pozastavit** v horní části okna událostí a pokračovat později bez ztráty událostí.
   
   ![Prohlížeč událostí diagnostiky sady Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Přidání vlastních trasování do kódu aplikace
Šablony projektu Service Fabric Visual Studio obsahují ukázkový kód. Kód ukazuje, jak přidat vlastní kód aplikace ETW trasování, které se zobrazí v prohlížeči Visual Studio ETW spolu se sledováním systému z Service Fabric. Výhodou této metody je, že metadata jsou automaticky přidány do trasování a Prohlížeč diagnostických událostí sady Visual Studio je již nakonfigurován tak, aby je zobrazoval.

Pro projekty vytvořené ze **šablon služeb** (bezstavové nebo `RunAsync` stavové) stačí vyhledat implementaci:

1. Volání `ServiceEventSource.Current.ServiceMessage` v metodě `RunAsync` ukazuje příklad vlastní trasování ETW z kódu aplikace.
2. V **souboru ServiceEventSource.cs** najdete přetížení `ServiceEventSource.ServiceMessage` pro metodu, která by měla být použita pro vysokofrekvenční události z důvodů výkonu.

Pro projekty vytvořené ze **šablon objektu actor** (bezstavové nebo stavové):

1. Otevřete soubor **"Název_projektu".cs,** kde *název aplikace ProjectName* je název, který jste zvolili pro projekt sady Visual Studio.  
2. Najděte `ActorEventSource.Current.ActorMessage(this, "Doing Work");` kód v metodě *DoWorkAsync.*  Toto je příklad vlastního trasování ETW zapsaného z kódu aplikace.  
3. V **ActorEventSource.cs**souborů najdete přetížení `ActorEventSource.ActorMessage` pro metodu, která by měla být použita pro vysokofrekvenční události z důvodů výkonu.

Po přidání vlastní eTW trasování do kódu služby, můžete vytvořit, nasadit a spustit aplikaci znovu zobrazit události v prohlížeči diagnostických událostí. Pokud ladíte aplikaci pomocí **f5**, automaticky se otevře Prohlížeč diagnostických událostí.

## <a name="next-steps"></a>Další kroky
Stejný kód trasování, který jste přidali do výše uvedené aplikace pro místní diagnostiku, bude fungovat s nástroji, které můžete použít k zobrazení těchto událostí při spuštění aplikace v clusteru Azure. Podívejte se na tyto články, které popisují různé možnosti pro nástroje a popisují, jak je můžete nastavit.

* [Jak shromažďovat protokoly pomocí Diagnostika Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Agregace a kolekce událostí pomocí EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

