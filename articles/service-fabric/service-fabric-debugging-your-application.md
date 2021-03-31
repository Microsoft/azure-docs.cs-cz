---
title: Ladění aplikace v aplikaci Visual Studio
description: Vylepšete spolehlivost a výkon vašich služeb tím, že je vyvíjíte a ladíte v aplikaci Visual Studio v místním vývojovém clusteru.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b7d08d610c883240abedc66c55abba64a74c8e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576311"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Ladění aplikace Service Fabric pomocí Visual Studia
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Zatmění/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Ladění místní aplikace Service Fabric
Můžete ušetřit čas a peníze nasazením a laděním aplikace Azure Service Fabric v clusteru pro vývoj místních počítačů. Sady Visual Studio 2019 nebo 2015 mohou nasadit aplikaci do místního clusteru a automaticky připojit ladicí program ke všem instancím aplikace. Pro připojení ladicího programu je třeba spustit aplikaci Visual Studio jako správce.

1. Spusťte místní vývojový cluster podle kroků uvedených v části [Nastavení vývojového prostředí Service Fabric](service-fabric-get-started.md).
2. Stiskněte klávesu **F5** nebo klikněte na **ladění**  >  **Spustit ladění**.
   
    ![Snímek obrazovky, který zobrazuje nabídku ladění.][startdebugging]
3. Nastavte zarážky v kódu a proveďte krokování aplikace kliknutím na příkazy v nabídce **ladění** .
   
   > [!NOTE]
   > Visual Studio se připojí ke všem instancím aplikace. Zatímco probíhajíte prostřednictvím kódu, zarážky mohou dosáhnout více procesů, které mají za následek souběžné relace. Zkuste zakázat zarážky po jejich dosažení tím, že každou zarážku nastavíte na ID vlákna nebo pomocí diagnostických událostí.
   > 
   > 
4. Okno **diagnostické události** se automaticky otevře, abyste mohli zobrazit diagnostické události v reálném čase.
   
    ![Zobrazit diagnostické události v reálném čase][diagnosticevents]
5. V Průzkumníku cloudu můžete také otevřít okno **diagnostické události** .  V části **Service Fabric** klikněte pravým tlačítkem na libovolný uzel a vyberte **Zobrazit trasování streamování**.
   
    ![Otevření okna diagnostické události][viewdiagnosticevents]
   
    Pokud chcete filtrovat trasování na určitou službu nebo aplikaci, povolte trasování streamování na konkrétní službě nebo aplikaci.
6. Diagnostické události lze zobrazit v automaticky generovaném souboru **ServiceEventSource. cs** a jsou volány z kódu aplikace.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Okno **diagnostické události** podporuje filtrování, pozastavování a kontrolu událostí v reálném čase.  Filtr je jednoduché hledání řetězců zprávy události, včetně jejího obsahu.
   
    ![Filtrování, pozastavení a obnovení nebo kontrola událostí v reálném čase][diagnosticeventsactions]
8. Služby ladění jsou jako ladění jakékoli jiné aplikace. V sadě Visual Studio budete normálně nastavovat zarážky pro snadné ladění. I když jsou spolehlivé kolekce replikovány napříč více uzly, stále implementují rozhraní IEnumerable. Tato implementace znamená, že při ladění můžete použít zobrazení výsledků v aplikaci Visual Studio, abyste viděli, co jste uložili v rámci. Provedete to tak, že nastavíte zarážku kdekoli v kódu.
   
    ![Spuštění ladění aplikace][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Spuštění skriptu jako součást ladění
V některých scénářích může být nutné spustit skript jako součást spuštění relace ladění (např. Pokud nepoužíváte výchozí služby).

V aplikaci Visual Studio můžete přidat soubor s názvem **Start-Service.ps1** ve složce **Scripts** projektu aplikace Service Fabric (. sfproj). Tento skript bude vyvolán po vytvoření aplikace v místním clusteru.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Ladění aplikace vzdálené Service Fabric
Pokud vaše aplikace Service Fabric běží na clusteru Service Fabric v Azure, můžete tyto aplikace vzdáleně ladit přímo ze sady Visual Studio.

> [!NOTE]
> Tato funkce vyžaduje [sadu Service Fabric sdk 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) a [sadu Azure sdk pro .NET 2,9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> Vzdálené ladění je určeno pro scénáře vývoje a testování a nepoužívá se v produkčních prostředích z důvodu dopadu na běžící aplikace.

1. V **Průzkumníku cloudu** přejděte do clusteru. Klikněte pravým tlačítkem a vyberte **Povolit ladění** .
   
    ![Povolit vzdálené ladění][enableremotedebugging]
   
    Tato akce zahájí proces povolení rozšíření vzdáleného ladění na uzlech clusteru a požadované konfigurace sítě.
2. Klikněte pravým tlačítkem na uzel clusteru v **Průzkumníku cloudu** a vyberte **připojit ladicí program** .
   
    ![Připojit ladicí program][attachdebugger]
3. V dialogovém okně **připojit k procesu** vyberte proces, který chcete ladit, a klikněte na tlačítko **připojit** .
   
    ![Zvolit proces][chooseprocess]
   
    Název procesu, ke kterému se chcete připojit, se rovná názvu vašeho názvu sestavení projektu služby.
   
    Ladicí program se připojí ke všem uzlům, které proces spouští.
   
   * V případě, že ladíte bezstavovou službu, jsou všechny instance služby na všech uzlech součástí relace ladění.
   * Pokud ladíte stavovou službu, bude aktivní pouze primární replika jakéhokoli oddílu, a proto bude zachycena ladicím programem. Pokud se primární replika přesune během relace ladění, bude zpracování této repliky stále součástí ladicí relace.
   * Chcete-li zachytit pouze relevantní oddíly nebo instance dané služby, můžete použít podmíněné zarážky pouze k přerušení konkrétního oddílu nebo instance.
     
     ![Podmíněná zarážka][conditionalbreakpoint]
     
     > [!NOTE]
     > V současné době nepodporujeme ladění Service Fabricho clusteru s více instancemi stejného názvu spustitelného souboru služby.
     > 
     > 
4. Po dokončení ladění aplikace můžete rozšíření vzdáleného ladění zakázat kliknutím pravým tlačítkem myši na cluster v **Průzkumníku cloudu** a zvolením možnosti **Zakázat ladění** .
   
    ![Zakázat vzdálené ladění][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streamování trasování ze vzdáleného uzlu clusteru
Je také možné streamovat trasování přímo ze vzdáleného uzlu clusteru do sady Visual Studio. Tato funkce umožňuje streamovat události trasování ETW, které se vytvářejí na uzlu clusteru Service Fabric.

> [!NOTE]
> Tato funkce vyžaduje [sadu Service Fabric sdk 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) a [sadu Azure sdk pro .NET 2,9](https://azure.microsoft.com/downloads/).
> Tato funkce podporuje jenom clustery běžící v Azure.
> 
> 

<!-- -->
> [!WARNING]
> Trasování streamování je určené pro scénáře pro vývoj a testování a ne pro použití v produkčních prostředích z důvodu dopadu na běžící aplikace.
> V produkčním scénáři byste měli spoléhat na události předávání pomocí Azure Diagnostics.

1. V **Průzkumníku cloudu** přejděte do clusteru. Klikněte pravým tlačítkem a vyberte **Povolit trasování streamování** .
   
    ![Povolit trasování vzdáleného streamování][enablestreamingtraces]
   
    Tato akce zahájí proces povolení rozšíření trasování datových proudů na uzlech clusteru a také požadované konfigurace sítě.
2. Rozbalte prvek **uzly** v **Průzkumníku cloudu**, klikněte pravým tlačítkem na uzel, ze kterého chcete streamovat trasování, a vyberte **Zobrazit trasování streamování.**
   
    ![Zobrazit trasování vzdáleného streamování][viewremotestreamingtraces]
   
    Opakujte krok 2 pro libovolný počet uzlů, ze kterých chcete zobrazit trasování. Každý datový proud uzlů se zobrazí ve vyhrazeném okně.
   
    Teď můžete vidět trasování vysílaná Service Fabric a vašimi službami. Chcete-li filtrovat události tak, aby zobrazovaly pouze konkrétní aplikaci, stačí do filtru zadat název aplikace.
   
    ![Zobrazení trasování streamování][viewingstreamingtraces]
3. Po skončení streamování trasování z clusteru můžete zakázat trasování vzdáleného streamování kliknutím pravým tlačítkem myši na cluster v **Průzkumníku cloudu** a výběrem možnosti **zakázat trasování streamování** .
   
    ![Zakázat trasování vzdáleného streamování][disablestreamingtraces]

## <a name="next-steps"></a>Další kroky
* [Otestujte službu Service Fabric](service-fabric-testability-overview.md).
* [Spravujte své aplikace Service Fabric v aplikaci Visual Studio](service-fabric-manage-application-in-visual-studio.md).

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
