---
title: Ladění aplikace v sadě Visual Studio | Dokumentace Microsoftu
description: Zlepšení spolehlivosti a výkonu služeb ve vývoji a ladění je v sadě Visual Studio v místním vývojovém clusteru.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 06db540600be323b3129d64d18739582f6d9f2d0
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412637"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Ladění aplikace Service Fabric pomocí sady Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Ladění místní aplikace Service Fabric
Nasazení a ladění aplikace Azure Service Fabric v místním počítači vývojový cluster můžete ušetřit čas a peníze. Visual Studio 2017 nebo Visual Studio 2015 můžete nasadit aplikace do místního clusteru a automaticky připojit ladicí program na všechny instance aplikace; Visual Studio musíte spustit jako správce pro připojení ladicího programu.

1. Spusťte místní vývojový cluster podle pokynů v [nastavení vývojového prostředí Service Fabric](service-fabric-get-started.md).
2. Stisknutím klávesy **F5** nebo klikněte na tlačítko **ladění** > **spustit ladění**.
   
    ![Spusťte ladění aplikace][startdebugging]
3. Nastavte zarážky v kódu a krokovat aplikaci kliknutím příkazy v **ladění** nabídky.
   
   > [!NOTE]
   > Připojí se k všechny instance aplikace Visual Studio. Zatímco jste krokování kódem, může získat zarážky pomocí více procesů, které jsou výsledkem souběžných relací. Zkuste zakázat body přerušení, poté, že máte, tím, že každý zarážku na ID vlákna nebo pomocí diagnostických událostí.
   > 
   > 
4. **Diagnostické události** okno se automaticky otevře, abyste mohli zobrazit diagnostické události v reálném čase.
   
    ![Zobrazení diagnostických událostí v reálném čase][diagnosticevents]
5. Můžete také otevřít **diagnostické události** okno v Průzkumníku cloudu.  V části **Service Fabric**, klikněte pravým tlačítkem na libovolný uzel a vyberte **trasování streamování zobrazení**.
   
    ![Otevřete okno diagnostické události][viewdiagnosticevents]
   
    Pokud chcete filtrovat trasování pro konkrétní službu nebo aplikaci, prostě povolte trasy streamování pro daný konkrétní službu nebo aplikaci.
6. Můžete zobrazit diagnostické události v automaticky generované **ServiceEventSource.cs** souborů a jsou volány z kódu aplikace.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. **Diagnostické události** okna podporuje filtrování, pozastavení a kontrola událostí v reálném čase.  Tento filtr je jednoduchým řetězcem vyhledávání událostí zprávy, včetně jejího obsahu.
   
    ![Filtrovat, pozastavení a obnovení nebo kontrola událostí v reálném čase][diagnosticeventsactions]
8. Ladění služeb je jako ladění jiných aplikací. Obvykle se nastavit zarážky pomocí sady Visual Studio pro snadné ladění. Přestože Reliable Collections replikovat napříč několika uzly, je stále implementovat rozhraní IEnumerable. To znamená, že vám pomůže zobrazení výsledků v sadě Visual Studio během ladění naleznete v tématu jsme uložený v. Stačí nastavte zarážky kdekoli ve vašem kódu.
   
    ![Spusťte ladění aplikace][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Ladění vzdálené aplikace Service Fabric
Pokud vaše aplikace Service Fabric jsou spuštěné v clusteru Service Fabric v Azure, budete moci vzdáleně ladit tyto přímo ze sady Visual Studio.

> [!NOTE]
> Tato funkce vyžaduje [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) a [sady Azure SDK pro .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Vzdálené ladění je určená pro scénáře vývoje/testování a není určen k použití v produkčním prostředí kvůli dopadu na spuštěné aplikace.
> 
> 

1. Přejděte do svého clusteru v **Průzkumníka cloudu**klikněte pravým tlačítkem a zvolte **povolit ladění**
   
    ![Povolení vzdáleného ladění][enableremotedebugging]
   
    Tím se spustí řízený proces povolení vzdáleného ladění rozšíření na vaše uzly clusteru, a také požadovaná síťová konfigurace.
2. Klikněte pravým tlačítkem na uzel clusteru v **Průzkumníka cloudu**a zvolte **připojit ladicí program**
   
    ![Připojit ladicí program][attachdebugger]
3. V **připojit k procesu** dialogového okna, vyberte proces, kterému chcete ladit, a klikněte na **připojit**
   
    ![Vyberte proces][chooseprocess]
   
    Název procesu, který chcete připojit, rovná se název název sestavení projektu vaší služby.
   
    Ladicí program se připojit ke všem uzlům spuštění procesu.
   
   * Všechny instance služby na všech uzlech v případě, pokud ladíte bezstavovou službu, jsou součástí relace ladění.
   * Pokud ladíte stavové služby, bude pouze primární replika žádný oddíl aktivní a proto zachycené ladicím programem. Pokud se primární replika přesune během relace ladění, zpracování této repliky bude stále součástí relace ladění.
   * Aby bylo možné zachytit pouze relevantní oddíly nebo instance dané služby, můžete použít podmíněné zarážky se přerušit pouze konkrétní oddíl nebo instance.
     
     ![Podmíněné zarážky][conditionalbreakpoint]
     
     > [!NOTE]
     > Ladění clusteru Service Fabric s více instancemi se stejným názvem spustitelného souboru služby aktuálně nepodporujeme.
     > 
     > 
4. Po dokončení ladění vaší aplikace, můžete zakázat rozšíření vzdálené ladění kliknutím pravým tlačítkem cluster v **Průzkumníka cloudu** a zvolte **zakázat ladění**
   
    ![Zakázání vzdálené ladění][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Trasy streamování ze vzdáleného clusteru
Máte také možnost do datového proudu trasování přímo z uzlu vzdáleného clusteru se sadou Visual Studio. Tato funkce umožňuje trasování událostí trasování událostí pro Windows datového proudu, vytvořený v uzlu clusteru Service Fabric.

> [!NOTE]
> Tato funkce vyžaduje [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) a [sady Azure SDK pro .NET 2.9](https://azure.microsoft.com/downloads/).
> Tato funkce podporuje pouze clustery spuštěné v Azure.
> 
> 

<!-- -->
> [!WARNING]
> Streamování trasování je určená pro scénáře vývoje/testování a není určen k použití v produkčním prostředí kvůli dopadu na spuštěné aplikace.
> V případě produkčního prostředí by se neměla spoléhat na předávání událostí pomocí Azure Diagnostics.
> 
> 

1. Přejděte do svého clusteru v **Průzkumníka cloudu**klikněte pravým tlačítkem a zvolte **povolit trasování streamování**
   
    ![Povolit vzdálené trasy streamování][enablestreamingtraces]
   
    Tím se spustí řízený proces povolování rozšíření trasování streamování na vaše uzly clusteru, a také požadovaná síťová konfigurace.
2. Rozbalte **uzly** element v **Průzkumníka cloudu**, klikněte pravým tlačítkem na uzel, který chcete trasování z datového proudu a zvolte **trasování streamování zobrazení**
   
    ![Zobrazit vzdálené trasování streamování][viewremotestreamingtraces]
   
    Krok 2 opakujte pro naleznete v tématu trasování z počtu uzlů. Každý datový proud uzly se zobrazí v okně vyhrazené.
   
    Nyní budete moci zobrazit trasování, protože ho vygeneroval Service Fabric a služeb. Pokud chcete filtrovat události, které chcete zobrazit jen určité aplikace, jednoduše zadejte název aplikace ve filtru.
   
    ![Zobrazení trasování streamování][viewingstreamingtraces]
3. Po dokončení trasy streamování z vašeho clusteru, můžete zakázat trasy streamování vzdálené, kliknutím pravým tlačítkem cluster v **Průzkumníka cloudu** a zvolte **zakázat trasování streamování**
   
    ![Zakázání vzdálené trasy streamování][disablestreamingtraces]

## <a name="next-steps"></a>Další postup
* [Testovat službu Service Fabric](service-fabric-testability-overview.md).
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
