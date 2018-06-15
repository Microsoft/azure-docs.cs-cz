---
title: Ladění aplikace v sadě Visual Studio | Microsoft Docs
description: Vylepšit spolehlivost a výkon vašich služeb vývoji a ladění je v sadě Visual Studio na místní vývojový cluster.
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
ms.workload: na
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4582fd16d08ae8d51460dc8cabfd282e1f2cd43e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206556"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Ladění aplikace Service Fabric pomocí sady Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio nebo CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse nebo Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Ladění místní aplikace Service Fabric
Nasazení a ladění aplikace Azure Service Fabric v místním počítači vývoj clusteru můžete ušetřit čas a peníze. Visual Studio 2017 nebo Visual Studio 2015 můžete nasadit aplikaci k místnímu clusteru a automaticky připojit ladicí program na všechny instance aplikace.

1. Spuštění místního vývojového clusteru podle kroků v [nastavení vývojového prostředí Service Fabric](service-fabric-get-started.md).
2. Stiskněte klávesu **F5** nebo klikněte na tlačítko **ladění** > **spustit ladění**.
   
    ![Spuštění ladění aplikace][startdebugging]
3. Nastavte zarážky v kódu a krok prostřednictvím aplikace klepnutím na příkazy v **ladění** nabídky.
   
   > [!NOTE]
   > Visual Studio se připojí k všech instancí aplikace. Když jste krokování kódu, může získat zarážky více procesy, které jsou výsledkem souběžných relací. Zkuste, zákaz zarážce poté, že máte, tím, že každé zarážky podmíněného na ID vlákna nebo pomocí diagnostických událostí.
   > 
   > 
4. **Diagnostických událostí** okno se automaticky otevře, takže si můžete prohlédnout diagnostických událostí v reálném čase.
   
    ![Zobrazení diagnostických událostí v reálném čase][diagnosticevents]
5. Můžete také otevřít **diagnostických událostí** okno v Průzkumníku cloudu.  V části **Service Fabric**, klikněte pravým tlačítkem na libovolný uzel a vyberte možnost **zobrazení streamování trasování**.
   
    ![Otevřete okno diagnostických událostí][viewdiagnosticevents]
   
    Pokud chcete filtrovat vaší trasování pro konkrétní službu nebo aplikaci, jednoduše povolte streamování trasování na této konkrétní služby nebo aplikace.
6. Diagnostické události si můžete prohlédnout v automaticky generované **ServiceEventSource.cs** souborů a jsou volat z kódu aplikace.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. **Diagnostických událostí** okno podporuje filtrování, pozastavení a kontrole událostí v reálném čase.  Filtr je hledání jednoduchého řetězce událostí zprávy, včetně jeho obsah.
   
    ![Filtrovat, pozastavení a obnovení nebo zkontrolujte události v reálném čase][diagnosticeventsactions]
8. Ladění služeb je jako ladění žádnou jinou aplikaci. Bude obvykle nastavit zarážky pomocí sady Visual Studio pro snadné ladění. I když spolehlivé kolekce replikují mezi několika uzly, se stále implementovat rozhraní IEnumerable. To znamená, že můžete zobrazit výsledky v sadě Visual Studio při ladění najdete v článku jste uložený v. Jednoduše zarážku kdekoli v kódu.
   
    ![Spuštění ladění aplikace][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Ladění vzdáleného aplikace Service Fabric
Pokud vaše aplikace Service Fabric běží na clusteru Service Fabric v Azure, budete moci vzdáleně ladit tyto přímo ze sady Visual Studio.

> [!NOTE]
> Funkce vyžaduje [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) a [Azure SDK pro .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Vzdálené ladění je určená pro scénáře vývoje/testování a není určen k použití v produkčním prostředí, protože dopad na spuštěné aplikace.
> 
> 

1. Přejít na cluster v **Průzkumník cloudu**, klikněte pravým tlačítkem a vyberte možnost **povolit ladění**
   
    ![Povolit vzdálené ladění][enableremotedebugging]
   
    To bude ji proces povolení vzdáleného ladění rozšíření na vaše uzly clusteru, jakož i Konfigurace požadované sítě.
2. Klikněte pravým tlačítkem na uzel clusteru v **Průzkumník cloudu**a zvolte **připojit ladicí program**
   
    ![Připojit ladicí program][attachdebugger]
3. V **připojit k procesu** dialogové okno, zvolte proces chcete ladit a klikněte na tlačítko **připojit**
   
    ![Vyberte proces][chooseprocess]
   
    Název procesu, který chcete připojit, se rovná názvu název sestavení projektu služby.
   
    Připojí ladicí program se na všech uzlech spuštěním procesu.
   
   * Všechny instance služby na všech uzlech v případě, kde jsou ladění bezstavové služby, jsou součástí relace ladění.
   * Pokud ladíte stavové služby, bude pouze primární replika libovolného oddílu aktivní a proto zachycení pomocí ladicího programu. Pokud se během ladicí relace přesune primární replikou, zpracování této repliky bude stále součástí relace ladění.
   * Chcete-li pouze zachytit příslušné oddíly nebo instance dané služby, můžete v podmíněné zarážky pouze rozdělit na konkrétní oddíl nebo instance.
     
     ![Podmíněné zarážky][conditionalbreakpoint]
     
     > [!NOTE]
     > Ladění clusteru Service Fabric s více instancemi se stejným názvem spustitelného souboru služby aktuálně nepodporujeme.
     > 
     > 
4. Jakmile dokončíte ladění aplikace, můžete zakázat vzdáleného ladění rozšíření kliknutím pravým tlačítkem myši na cluster ve **Průzkumník cloudu** a zvolte **zakázat ladění**
   
    ![Zakázání vzdálené ladění][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streamování trasování z uzlu vzdáleného clusteru
Je také možné do datového proudu trasování přímo z uzlu vzdáleného clusteru k sadě Visual Studio. Tato funkce umožňuje události trasování ETW datového proudu, vytváří na uzlu clusteru Service Fabric.

> [!NOTE]
> Tato funkce vyžaduje [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) a [Azure SDK pro .NET 2.9](https://azure.microsoft.com/downloads/).
> Tato funkce podporuje pouze clustery spuštěná v Azure.
> 
> 

<!-- -->
> [!WARNING]
> Streamování trasování je určená pro scénáře vývoje/testování a není určen k použití v produkčním prostředí, protože dopad na spuštěné aplikace.
> V produkční scénář měli byste tedy spoléhat na předávání událostí pomocí diagnostiky Azure.
> 
> 

1. Přejít na cluster v **Průzkumník cloudu**, klikněte pravým tlačítkem a vyberte možnost **povolit trasování streamování**
   
    ![Povolit vzdálené streamování trasování][enablestreamingtraces]
   
    To bude ji proces povolení streamování rozšíření trasování na vaše uzly clusteru, jakož i Konfigurace požadované sítě.
2. Rozbalte **uzly** element v **Průzkumník cloudu**, klikněte pravým tlačítkem na uzel chcete trasování z datového proudu a zvolte **zobrazení streamování trasování**
   
    ![Zobrazení vzdáleného streamování trasování][viewremotestreamingtraces]
   
    Opakujte krok 2 pro libovolný počet uzlů, jak chcete zobrazit trasování z. Každý datový proud uzly se zobrazí v okně vyhrazené.
   
    Nyní jste moci zobrazit trasování vysílaných Service Fabric a služeb. Pokud chcete filtrovat události, které chcete zobrazit pouze na konkrétní aplikaci, jednoduše zadejte název aplikace ve filtru.
   
    ![Zobrazení, streamování trasování][viewingstreamingtraces]
3. Jakmile dokončíte streamování trasování z clusteru, můžete zakázat vzdálené streamování trasování, kliknutím pravým tlačítkem myši na cluster ve **Průzkumník cloudu** a zvolte **zakázat streamování trasování**
   
    ![Zakázání vzdálené streamování trasování][disablestreamingtraces]

## <a name="next-steps"></a>Další postup
* [Testování služby Service Fabric](service-fabric-testability-overview.md).
* [Spravujte svoje aplikace Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).

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
