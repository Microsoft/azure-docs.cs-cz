---
title: Vytvoření služby založené na objektu actor na Azure Service Fabric
description: Naučte se, jak vytvořit, ladit a nasadit první službu založenou na actor v C# pomocí service fabric reliable actors.
author: vturecek
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: a6e4fb48653572139463738c82de632ff7d55074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466248"
---
# <a name="getting-started-with-reliable-actors"></a>Začínáme se spolehlivými herci
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-actors-get-started.md)
> * [Java v Linuxu](service-fabric-reliable-actors-get-started-java.md)

Tento článek vás provede vytváření a ladění jednoduché aplikace reliable actor v sadě Visual Studio. Další informace o spolehlivé objekty actor, naleznete [v tématu Úvod do service fabric spolehlivé objekty](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte ve vašem počítači nastavené vývojové prostředí Service Fabric, včetně sady Visual Studio. Podrobnosti naleznete [v tématu nastavení vývojového prostředí](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Vytvoření nového projektu v sadě Visual Studio

Spusťte Visual Studio 2019 nebo novější jako správce a pak vytvořte nový projekt **aplikace Service Fabric:**

![Nástroje Service Fabric pro Visual Studio - nový projekt][1]

V dalším dialogovém okně zvolte **službu actor service** pod **.NET Core 2.0** a zadejte název služby.

![Šablony projektů Service Fabric][5]

Vytvořený projekt zobrazuje následující strukturu:

![Struktura projektu Service Fabric][2]

## <a name="examine-the-solution"></a>Zkontrolujte řešení

Řešení obsahuje tři projekty:

* **Projekt aplikace (MyApplication)**. Tento projekt sbalí všechny služby společně pro nasazení. Obsahuje skripty *ApplicationManifest.xml* a PowerShell pro správu aplikace.

* **Projekt rozhraní (HelloWorld.Interfaces)**. Tento projekt obsahuje definici rozhraní pro objekt actor. Rozhraní objektu actor lze definovat v libovolném projektu s libovolným názvem.  Rozhraní definuje kontrakt objektu actor, který je sdílen implementací objektu actor a klienty volajícíobjekt actor.  Vzhledem k tomu, že klientské projekty na něm mohou záviset, má obvykle smysl definovat v sestavení, které je oddělené od implementace objektu actor.

* **Projekt služby objektu actor (HelloWorld)**. Tento projekt definuje službu Service Fabric, která bude hostitelem objektu actor. Obsahuje implementaci objektu actor, *HelloWorld.cs*. Implementace objektu actor je třída, která `Actor` je odvozena od základního typu a implementuje rozhraní definovaná v projektu *MyActor.Interfaces.* Třída actor musí také implementovat konstruktor, který přijímá `ActorService` instanci `ActorId` a a předá je základní `Actor` třídě.
    
    Tento projekt také obsahuje *Program.cs*, který registruje třídy objektu actor pomocí runtime Service Fabric pomocí `ActorRuntime.RegisterActorAsync<T>()`. Třída `HelloWorld` je již zaregistrována. Všechny další objektactor implementace přidané do projektu `Main()` musí být také registrovány v metodě.

## <a name="customize-the-helloworld-actor"></a>Přizpůsobení objektu HelloWorld

Šablona projektu definuje některé `IHelloWorld` metody v rozhraní a `HelloWorld` implementuje je v implementaci objektu actor.  Nahradit tyto metody tak, aby služba objektu actor vrátí jednoduchý řetězec "Hello World".

V projektu *HelloWorld.Interfaces* v *souboru IHelloWorld.cs* nahraďte definici rozhraní následujícím způsobem:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

V projektu **HelloWorld** v **HelloWorld.cs**nahraďte celou definici třídy takto:

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

Stisknutím **kombinace kláves Ctrl-Shift-B** vytvořte projekt a ujistěte se, že se vše zkompiluje.

## <a name="add-a-client"></a>Přidání klienta

Vytvořte jednoduchou konzolovou aplikaci pro volání služby objektu actor.

1. Klikněte pravým tlačítkem myši na řešení v Průzkumníku řešení > **Přidat** > **nový projekt...**.

2. V části typy projektu **.NET Core** zvolte **Console App (.NET Core)**.  Pojmenujte projekt *ActorClient*.
    
    ![Dialogové okno Přidat nový projekt][6]    
    
    > [!NOTE]
    > Konzolová aplikace není typ aplikace, kterou byste obvykle používali jako klient v Service Fabric, ale vytváří pohodlný příklad pro ladění a testování pomocí místního clusteru Service Fabric.

3. Konzolová aplikace musí být 64bitová aplikace, aby byla zachována kompatibilita s projektem rozhraní a dalšími závislostmi.  V Průzkumníku řešení klepněte pravým tlačítkem myši na projekt **ActorClient** a potom klepněte na příkaz **Vlastnosti**.  Na kartě **Sestavení** nastavte **cíl platformy** na **x64**.
    
    ![Vlastnosti sestavení][8]

4. Projekt klienta vyžaduje spolehlivé objekty actor NuGet balíček.  Klepněte na **položku Nástroje** > **Aplikace NuGet , konzola** > **správce balíčků správce balíčků**.  V konzole Správce balíčků zadejte následující příkaz:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Balíček NuGet a všechny jeho závislosti jsou nainstalovány v projektu ActorClient.

5. Projekt klienta také vyžaduje odkaz na projekt rozhraní.  V projektu ActorClient klepněte pravým tlačítkem myši na **položku Závislosti** a potom klepněte na příkaz **Přidat odkaz...**.  Vyberte **Možnost Projekty > řešení** (pokud ještě není zaškrtnuto) a zaškrtněte políčko vedle **položky HelloWorld.Interfaces**.  Klikněte na tlačítko **OK**.
    
    ![Dialogové okno Přidat odkaz][7]

6. V projektu ActorClient nahraďte celý obsah *Program.cs* následujícím kódem:
    
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

## <a name="running-and-debugging"></a>Běh a ladění

Stisknutím **klávesy F5** sestavíte, nasazujete a spusťte aplikaci místně ve vývojovém clusteru Service Fabric.  Během procesu nasazení můžete vidět průběh v okně **Výstup.**

![Výstupní okno ladění prostředků služby Fabric][3]

Pokud výstup obsahuje text, *Aplikace je připravena*, je možné otestovat službu pomocí aplikace ActorClient.  V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt **ActorClient** a potom klikněte na **příkaz Ladění** > **spustit novou instanci**.  Aplikace příkazového řádku by měla zobrazit výstup ze služby objektu actor.

![Výstup aplikace][9]

> [!TIP]
> Objekty runtime objekty Actor služby vyzařuje některé [události a čítače výkonu související s objektem actor metody](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Jsou užitečné při diagnostice a sledování výkonu.

## <a name="next-steps"></a>Další kroky
Další informace o [tom, jak spolehliví aktéři používají platformu Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png