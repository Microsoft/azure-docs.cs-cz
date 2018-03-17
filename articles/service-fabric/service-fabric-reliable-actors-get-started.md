---
title: "Vytvoření vaší první na základě objektu actor Azure mikroslužbu v jazyce C# | Microsoft Docs"
description: "Tento kurz vás provede kroky při vytváření, ladění a nasazení jednoduchého službu založenou na objektu actor pomocí Service Fabric Reliable Actors."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: 20786522a9a25d84a32a53e5e111b4b542501287
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="getting-started-with-reliable-actors"></a>Začínáme s Reliable Actors
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-actors-get-started.md)
> * [Java v Linuxu](service-fabric-reliable-actors-get-started-java.md)

Tento článek vás provede vytváření a ladění jednoduchou aplikaci spolehlivé objektu Actor v sadě Visual Studio. Další informace o Reliable Actors najdete v tématu [Úvod do Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, abyste měli Service Fabric vývojového prostředí, včetně Visual Studio, nastavit na vašem počítači. Podrobnosti najdete v tématu [postup nastavení vývojového prostředí](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Vytvořte nový projekt v sadě Visual Studio

Spusťte sadu Visual Studio 2015 nebo novější jako správce a pak vytvořte nové **aplikace Service Fabric** projektu:

![Service Fabric nástrojů pro Visual Studio – nový projekt][1]

V dialogovém okně Další zvolte **služby objektu Actor** pod **základní rozhraní .net 2.0** a zadejte název pro službu.

![Šablony projektů Service Fabric][5]

Vytvořený projekt ukazuje následující strukturou:

![Struktura projektu Service Fabric][2]

## <a name="examine-the-solution"></a>Zkontrolujte řešení

Řešení obsahuje tří projektů:

* **Projekt aplikace (Moje_aplikace)**. Tento projekt balíčky všechny společně služby pro nasazení. Obsahuje *ApplicationManifest.xml* a skriptů prostředí PowerShell pro správu aplikace.

* **Rozhraní projektu (HelloWorld.Interfaces)**. Tento projekt obsahuje definici rozhraní objektu actor. Rozhraní objektu actor lze definovat v jakékoli projektu s libovolným názvem.  Rozhraní definuje kontrakt objektu actor, který sdílí objektu actor implementace a volání objektu actor klienty.  Protože projekty klienta může na něm závisí, obvykle má smysl definovat v sestavení, které jsou oddělené od objektu actor implementace.

* **Projekt služby objektu actor (HelloWorld)**. Tento projekt definuje služba Service Fabric, která bude hostitelem objektu actor. Obsahuje implementace objektu actor, *HellowWorld.cs*. Implementace objektu actor je třída, která je odvozena ze základního typu `Actor` a implementuje rozhraní definované v *MyActor.Interfaces* projektu. Třídu objektu actor musí také implementovat konstruktor, který přijímá `ActorService` instance a `ActorId` a předává je základní `Actor` třídy.
    
    Tento projekt obsahuje také *Program.cs*, čímž registruje třídy objektu actor s použitím modulu runtime Service Fabric `ActorRuntime.RegisterActorAsync<T>()`. `HelloWorld` Třída je již zaregistrován. Jakékoli další objektu actor implementace k projektu nepřidají musí být zaregistrovaná taky v `Main()` metoda.

## <a name="customize-the-helloworld-actor"></a>Přizpůsobení objektu actor HelloWorld

Definuje některé metody v šabloně projektů `IHelloWorld` rozhraní a je v implementuje `HelloWorld` objektu actor implementace.  Tyto metody nahraďte, aby služby objektu actor vrátí jednoduchým řetězcem "Hello World".

V *HelloWorld.Interfaces* v projektu *IHelloWorld.cs* souboru, nahraďte definici rozhraní takto:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

V **HelloWorld** projektu v **HelloWorld.cs**, nahraďte definici celou třídu následujícím způsobem:

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

Stiskněte klávesu **Ctrl-Shift-B** se projekt sestavil a zajistěte, aby vše zkompiluje.

## <a name="add-a-client"></a>Přidání klienta

Vytvořte jednoduché konzolové aplikace pro volání služby objektu actor.

1. Klikněte pravým tlačítkem na řešení v Průzkumníku řešení > **přidat** > **nový projekt...** .

2. V části **.NET Core** typy projektů, zvolte **konzolové aplikace (.NET Core)**.  Název projektu *ActorClient*.
    
    ![Přidat dialogové okno Nový projekt][6]    
    
    > [!NOTE]
    > Konzolové aplikace není typu aplikace, které se obvykle používají jako klient v Service Fabric, ale umožňuje vhodné například pro ladění a testování pomocí místní cluster Service Fabric.

3. Konzolové aplikace musí být 64bitové aplikaci k zachování kompatibility s rozhraní projektu a další závislosti.  V Průzkumníku řešení klikněte pravým tlačítkem myši **ActorClient** projektu a pak klikněte na **vlastnosti**.  Na **sestavení** nastavte **Cílová platforma** k **x64**.
    
    ![Vlastnosti sestavení][8]

4. Projektu klienta vyžaduje balíček NuGet spolehlivé aktéři.  Klikněte na **Nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.  V konzole Správce balíčků zadejte následující příkaz:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Balíček NuGet a všechny jeho závislé součásti jsou nainstalovány v ActorClient projektu.

5. Projektu klienta taky vyžaduje odkaz na projekt rozhraní.  V projektu ActorClient, klikněte pravým tlačítkem na **závislosti** a pak klikněte na tlačítko **přidat odkaz...** .  Vyberte **projekty > řešení** (Pokud není vybrána) a klikněte na zaškrtávací políčko vedle osové **HelloWorld.Interfaces**.  Klikněte na **OK**.
    
    ![Přidat odkaz na dialogové okno][7]

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

## <a name="running-and-debugging"></a>Spuštění a ladění

Stiskněte klávesu **F5** k vytvoření, nasazení a místní spuštění aplikace v clusteru vývoj Service Fabric.  Během procesu nasazení můžete sledovat průběh v **výstup** okno.

![Service Fabric ladicích výstup – okno][3]

Výstup obsahuje textu, *aplikace je připravena*, je možné otestovat pomocí ActorClient aplikace.  V Průzkumníku řešení klikněte pravým tlačítkem na **ActorClient** projektu a pak klikněte na **ladění** > **spustit novou instanci**.  Aplikace příkazového řádku by měl zobrazit výstup služby objektu actor.

![výstup aplikace][9]

> [!TIP]
> Modul runtime Service Fabric aktéři vysílá některé [události a čítače výkonu související s metody objektu actor](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Jsou užitečné v Diagnostika a sledování výkonu.

## <a name="next-steps"></a>Další postup
Další informace o [jak Reliable Actors pomocí platformy Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png