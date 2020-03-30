---
title: Ladění aplikace v sadě Visual Studio
description: Zlepšete spolehlivost a výkon svých služeb jejich vývojem a laděním v sadě Visual Studio v místním vývojovém clusteru.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fff8a19d5643f7ce866c9eb9c57486340b6f8a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624142"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Ladění aplikace Service Fabric pomocí Visual Studia
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Zatmění/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Ladění místní aplikace Service Fabric
Můžete ušetřit čas a peníze nasazením a laděním aplikace Azure Service Fabric v clusteru pro vývoj místních počítačů. Visual Studio 2019 nebo 2015 můžete nasadit aplikaci do místního clusteru a automaticky připojit ladicí program ke všem instancím vaší aplikace. Visual Studio musí být spuštěnjako správce pro připojení ladicího programu.

1. Spusťte cluster místního vývoje podle kroků v [části Nastavení vývojového prostředí Service Fabric](service-fabric-get-started.md).
2. Stiskněte **klávesu F5** nebo klepněte na tlačítko **Ladění** > **zahájit ladění**.
   
    ![Spuštění ladění aplikace][startdebugging]
3. Nastavte zarážky v kódu a krokovat aplikace klepnutím na příkazy v nabídce **ladění.**
   
   > [!NOTE]
   > Visual Studio připojí všechny instance aplikace. Při krokování kódu, zarážky může získat přístup ů více procesů, což má za následek souběžné relace. Zkuste zakázat zarážky po jejich přístupu, tím, že každý zarážka podmíněné ID vlákna nebo pomocí diagnostických událostí.
   > 
   > 
4. Okno **Diagnostické události** se automaticky otevře, takže můžete zobrazit diagnostické události v reálném čase.
   
    ![Zobrazení diagnostických událostí v reálném čase][diagnosticevents]
5. Okno **Diagnostické události** můžete také otevřít v Průzkumníkovi cloudu.  V části **Service Fabric**klepněte pravým tlačítkem myši na libovolný uzel a zvolte **Zobrazit trasování datových proudů**.
   
    ![Otevření okna diagnostických událostí][viewdiagnosticevents]
   
    Pokud chcete filtrovat trasování na konkrétní službu nebo aplikaci, povolte trasování datových proudů v této konkrétní službě nebo aplikaci.
6. Diagnostické události lze vidět v automaticky **generovanéServiceEventSource.cs** souboru a jsou volány z kódu aplikace.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Okno **Diagnostické události** podporuje filtrování, pozastavení a kontrolu událostí v reálném čase.  Filtr je jednoduché hledání řetězce zprávy události, včetně jejího obsahu.
   
    ![Filtrování, pozastavení a pokračování nebo kontrola událostí v reálném čase][diagnosticeventsactions]
8. Ladění služby je jako ladění jakékoli jiné aplikace. Obvykle nastavíte zarážky prostřednictvím sady Visual Studio pro snadné ladění. I když spolehlivé kolekce replikovat přes více uzlů, které stále implementují IEnumerable. Tato implementace znamená, že můžete použít zobrazení výsledků v sadě Visual Studio při ladění vidět, co jste uložili uvnitř. Chcete-li tak učinit, nastavte zarážku kdekoli v kódu.
   
    ![Spuštění ladění aplikace][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Spuštění skriptu jako součásti ladění
V některých případech může být nutné spustit skript jako součást spuštění relace ladění (například pokud nepoužíváte výchozí služby).

V sadě Visual Studio můžete přidat soubor s názvem **Start-Service.ps1** ve složce **Skripty** projektu Service Fabric Application (.sfproj). Tento skript bude vyvolán po vytvoření aplikace v místním clusteru.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Ladění vzdálené aplikace Service Fabric
Pokud vaše aplikace Service Fabric běží v clusteru Service Fabric v Azure, můžete vzdáleně ladit tyto aplikace, přímo z Visual Studia.

> [!NOTE]
> Tato funkce vyžaduje [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) a [Azure SDK pro .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> Vzdálené ladění je určen pro scénáře vývoj a testování a není použít v produkčním prostředí, z důvodu dopadu na spuštěné aplikace.

1. Přejděte do clusteru v **Cloud Exploreru**. Klikněte pravým tlačítkem myši a zvolte **Povolit ladění.**
   
    ![Povolit vzdálené ladění][enableremotedebugging]
   
    Tato akce zahájí proces povolení rozšíření vzdáleného ladění v uzlech clusteru a požadovaných konfigurací sítě.
2. Klikněte pravým tlačítkem myši na uzel clusteru v **Průzkumníkovi Cloudu**a zvolte **Připojit ladicí program.**
   
    ![Připojit ladicí program][attachdebugger]
3. V dialogovém okně **Připojit k procesu** zvolte proces, který chcete ladit, a klepněte na **Připojit**
   
    ![Zvolte proces][chooseprocess]
   
    Název procesu, ke kterému chcete připojit, se rovná názvu názvu sestavení projektu servisu.
   
    Ladicí program se připojí ke všem uzlům, které proces spouštějí.
   
   * V případě, že ladíte bezstavovou službu, jsou všechny instance služby ve všech uzlech součástí relace ladění.
   * Pokud ladíte stavovou službu, bude aktivní pouze primární replika libovolného oddílu, a proto bude ladicím programem zachycena. Pokud se primární replika přesune během relace ladění, zpracování této repliky bude stále součástí relace ladění.
   * Chcete-li zachytit pouze příslušné oddíly nebo instance dané služby, můžete použít podmíněné zarážky pouze přerušit konkrétní oddíl nebo instanci.
     
     ![Podmíněná zarážka][conditionalbreakpoint]
     
     > [!NOTE]
     > V současné době nepodporujeme ladění clusteru Service Fabric s více instancemi se stejným názvem spustitelného souboru služby.
     > 
     > 
4. Po dokončení ladění aplikace můžete zakázat rozšíření vzdáleného ladění kliknutím pravým tlačítkem myši na cluster v **Průzkumníku cloudu** a zvolte **Zakázat ladění.**
   
    ![Zakázání vzdáleného ladění][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Trasování datových proudů ze vzdáleného uzlu clusteru
Můžete také streamovat trasování přímo ze vzdáleného uzlu clusteru do sady Visual Studio. Tato funkce umožňuje streamovat události trasování ETW vytvořené v uzlu clusteru Service Fabric.

> [!NOTE]
> Tato funkce vyžaduje [service fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) a [Azure SDK pro .NET 2.9](https://azure.microsoft.com/downloads/).
> Tato funkce podporuje jenom clustery spuštěné v Azure.
> 
> 

<!-- -->
> [!WARNING]
> Trasování datových proudů je určena pro scénáře vývoj a testování a nelze je použít v produkčním prostředí z důvodu dopadu na spuštěné aplikace.
> Ve scénáři produkčního prostředí byste měli spoléhat na předávání událostí pomocí Diagnostika Azure.

1. Přejděte do clusteru v **Cloud Exploreru**. Klikněte pravým tlačítkem myši a zvolte **Povolit trasování streamování**
   
    ![Povolení trasování vzdáleného streamování][enablestreamingtraces]
   
    Tato akce zahájí proces povolení rozšíření trasování datových proudů v uzlech clusteru a také požadované konfigurace sítě.
2. Rozbalte prvek **Uzly** v **Průzkumníkovi cloudu**, klikněte pravým tlačítkem myši na uzel, ze kterého chcete streamovat trasování, a zvolte **Zobrazit trasování datových proudů.**
   
    ![Zobrazení trasování vzdáleného streamování][viewremotestreamingtraces]
   
    Opakujte krok 2 pro tolik uzlů, kolik chcete zobrazit trasování z. Každý datový proud uzlů se zobrazí ve vyhrazeném okně.
   
    Nyní můžete zobrazit trasování vyzařované Service Fabric a vaše služby. Pokud chcete filtrovat události tak, aby zobrazovala pouze určitou aplikaci, jednoduše zadejte název aplikace do filtru.
   
    ![Zobrazení trasování streamování][viewingstreamingtraces]
3. Po dokončení trasování datových proudů z clusteru můžete zakázat trasování vzdáleného streamování kliknutím pravým tlačítkem myši na cluster v **aplikaci Cloud Explorer** a zvolte **Zakázat trasování datových proudů.**
   
    ![Zakázání trasování vzdáleného streamování][disablestreamingtraces]

## <a name="next-steps"></a>Další kroky
* [Otestujte službu Service Fabric](service-fabric-testability-overview.md).
* [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
