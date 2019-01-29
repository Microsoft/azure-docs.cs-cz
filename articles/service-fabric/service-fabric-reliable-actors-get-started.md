---
title: Vytvoření služby objektu actor založené na platformě Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet, ladit a nasadit první službu založenou na objektu actor v C# s použitím Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: f92fe2432051b148bf0b35fccc3fa33db9b66a14
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093140"
---
# <a name="getting-started-with-reliable-actors"></a>Začínáme s Reliable Actors
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-actors-get-started.md)
> * [Java v Linuxu](service-fabric-reliable-actors-get-started-java.md)

Tento článek vás provede vytvořením a ladění jednoduché aplikace Reliable Actors v sadě Visual Studio. Další informace o Reliable Actors, naleznete v tématu [Úvod do Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte vývojového prostředí Service Fabric, včetně sady Visual Studio na svém počítači. Podrobnosti najdete v tématu [jak nastavit vývojové prostředí](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Vytvoření nového projektu v sadě Visual Studio

Spusťte sadu Visual Studio 2015 nebo vyšší jako správce a pak vytvořte nový **aplikace Service Fabric** projektu:

![Service Fabric tools pro Visual Studio – nový projekt][1]

V dalším dialogovém okně zvolte **služba objektu Actor** pod **.Net Core 2.0** a zadejte název pro službu.

![Šablony projektu Service Fabric][5]

Vytvořený projekt zobrazuje následující strukturu:

![Struktura projektu Service Fabric][2]

## <a name="examine-the-solution"></a>Prozkoumat řešení

Řešení obsahuje tři projekty:

* **Projekt aplikace (MyApplication)**. Tento projekt balíčky všechny služby najednou pro nasazení. Obsahuje *ApplicationManifest.xml* a skriptů prostředí PowerShell pro správu aplikace.

* **Projekt rozhraní (HelloWorld.Interfaces)**. Tento projekt obsahuje definici rozhraní pro objekt actor. Rozhraní objektu actor lze definovat v každém projektu s názvem.  Rozhraní definuje kontrakt objektu actor sdílený mezi implementací objektu actor a klienty objekt actor volají.  Protože klientské projekty mohou na něm závisí, je obvykle vhodné jej definovat na sestavení, která je oddělená od implementace objektu actor.

* **Projekt služby objektu actor (HelloWorld)**. Tento projekt definuje službu Service Fabric, který bude hostovat objekt actor. Obsahuje implementace objektu actor, *HelloWorld.cs*. Implementace objektu actor je třída, která se odvozuje od základního typu `Actor` a implementuje rozhraní definované v *MyActor.Interfaces* projektu. Třídu objektu actor musí také implementovat konstruktor, který přijímá `ActorService` instance a `ActorId` a předává je do základní `Actor` třídy.
    
    Tento projekt obsahuje také *Program.cs*, který zaregistruje třídy actor pomocí modulu runtime Service Fabric `ActorRuntime.RegisterActorAsync<T>()`. `HelloWorld` Třída je již registrována. Jakékoli další objekt actor implementace přidány do projektu musí být zaregistrovaný také ve `Main()` metody.

## <a name="customize-the-helloworld-actor"></a>Přizpůsobení HelloWorld objektu actor

Definuje některé metody v šabloně projektů `IHelloWorld` rozhraní a je v implementuje `HelloWorld` implementace objektu actor.  Nahraďte tyto metody tak, aby služba objektu actor jednoduchým řetězcem "Hello World".

V *HelloWorld.Interfaces* v projektu *IHelloWorld.cs* souboru, nahraďte definici rozhraní následujícím způsobem:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

V **HelloWorld** projektu v **HelloWorld.cs**, nahraďte definici celá třída následujícím způsobem:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Stisknutím klávesy **Ctrl-Shift-B** se projekt sestavil a ujistěte se, že všechno, co zkompiluje.

## <a name="add-a-client"></a>Přidání klienta

Vytvořte jednoduchou konzolovou aplikaci k volání služby objektu actor.

1. Klikněte pravým tlačítkem na řešení v Průzkumníku řešení > **přidat** > **nový projekt...** .

2. V části **.NET Core** typy projektů, zvolte **Konzolová aplikace (.NET Core)**.  Pojmenujte projekt *ActorClient*.
    
    ![Přidání dialogového okna Nový projekt][6]    
    
    > [!NOTE]
    > Konzolová aplikace není typu aplikace, které obvykle použijete jako klient v Service Fabric, ale je vhodné například pro ladění a testování s využitím místního clusteru Service Fabric.

3. Konzolová aplikace musí být 64bitové aplikaci k udržovat kompatibilitu s projekty rozhraní a další závislosti.  V Průzkumníku řešení klikněte pravým tlačítkem myši **ActorClient** projektu a pak klikněte na tlačítko **vlastnosti**.  Na **sestavení** kartu, nastavte **Cílová platforma** k **x64**.
    
    ![Vlastnosti sestavení][8]

4. Klientský projekt vyžaduje balíček NuGet reliable actors.  Klikněte na **Nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.  V konzole Správce balíčků zadejte následující příkaz:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Balíček NuGet a všechny jeho závislosti jsou nainstalovány v projektu ActorClient.

5. Klientský projekt také vyžaduje odkaz na projekt rozhraní.  V projektu ActorClient klikněte pravým tlačítkem na **závislosti** a potom klikněte na tlačítko **přidat odkaz...** .  Vyberte **projektů > řešení** (Pokud není již vybrána) a potom zaškrtněte políčko vedle položky **HelloWorld.Interfaces**.  Klikněte na **OK**.
    
    ![Přidat odkaz na – dialogové okno][7]

6. V projektu ActorClient nahradit celý obsah *Program.cs* následujícím kódem:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Spouštění a ladění

Stisknutím klávesy **F5** Pokud chcete sestavit, nasadit a spustit aplikaci místně v vývojový cluster Service Fabric.  Během procesu nasazení můžete zobrazit průběh na **výstup** okna.

![Service Fabric ladění okna výstup][3]

Pokud výstup obsahuje text, *aplikace je připravena*, je možné k otestování služby pomocí ActorClient aplikace.  V Průzkumníku řešení klikněte pravým tlačítkem myši na **ActorClient** projektu a pak klikněte na **ladění** > **zahájit novou instanci**.  Aplikace příkazového řádku by se zobrazit výstup služby actor.

![Výstup aplikace][9]

> [!TIP]
> Modul runtime Service Fabric Actors vysílá některé [událostí a čítačů výkonu související s metody objektu actor](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Jsou užitečné pro diagnostiku a sledování výkonu.

## <a name="next-steps"></a>Další postup
Další informace o [jak objekty Reliable Actors využívají platformu Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png