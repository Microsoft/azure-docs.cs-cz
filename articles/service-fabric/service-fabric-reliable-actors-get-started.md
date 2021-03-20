---
title: Vytvoření služby založené na objektu actor v Azure Service Fabric
description: Naučte se vytvářet, ladit a nasazovat svou první službu založenou na objektech actor v jazyce C# pomocí Service Fabric Reliable Actors.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 225ccb67153a33ed47af68ebb1549dce37426278
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96573457"
---
# <a name="getting-started-with-reliable-actors"></a>Začínáme s Reliable Actors
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-actors-get-started.md)
> * [Java v Linuxu](./service-fabric-create-your-first-linux-application-with-java.md)

Tento článek vás provede vytvořením a laděním jednoduché aplikace typu Reliable actor v aplikaci Visual Studio. Další informace o Reliable Actors najdete v tématu [Úvod do Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že máte vývojové prostředí Service Fabric, včetně sady Visual Studio, a nastavte na svém počítači. Podrobnosti najdete v tématu [Postup nastavení vývojového prostředí](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Vytvoření nového projektu v aplikaci Visual Studio

Spusťte aplikaci Visual Studio 2019 nebo novější jako správce a pak vytvořte nový projekt **Service Fabric aplikace** :

![Service Fabric Tools for Visual Studio – nový projekt][1]

V dalším dialogovém okně vyberte v části **.NET Core 2,0** položku **Služba actor** a zadejte název služby.

![Service Fabric šablony projektů][5]

Vytvořený projekt zobrazuje následující strukturu:

![Service Fabric strukturu projektu][2]

## <a name="examine-the-solution"></a>Prověřte řešení

Řešení obsahuje tři projekty:

* **Projekt aplikace (MyApplication)**. Tento projekt zabalí všechny služby dohromady pro nasazení. Obsahuje *ApplicationManifest.xml* a skripty PowerShellu pro správu aplikace.

* **Projekt rozhraní (HelloWorld. Interfaces)**. Tento projekt obsahuje definici rozhraní objektu actor. Rozhraní objektu actor lze definovat v jakémkoli projektu s libovolným názvem.  Rozhraní definuje kontrakt objektu actor, který je sdílen implementací objektu actor, a klienty, kteří volají objekt actor.  Vzhledem k tomu, že klientské projekty mohou být na něm závislé, obvykle má smysl ho definovat v sestavení, které je odděleno od implementace objektu actor.

* **Projekt služby objektu actor (HelloWorld)**. Tento projekt definuje službu Service Fabric, která bude hostovat objekt actor. Obsahuje implementaci objektu actor, *HelloWorld. cs*. Implementace objektu actor je třída, která je odvozena od základního typu `Actor` a implementuje rozhraní definovaná v projektu *MyActor. Interfaces* . Třída objektu actor musí také implementovat konstruktor, který přijímá `ActorService` instanci a `ActorId` předává je do základní `Actor` třídy.
    
    Tento projekt také obsahuje *program. cs*, který registruje třídy objektu actor pomocí modulu runtime Service Fabric pomocí `ActorRuntime.RegisterActorAsync<T>()` . `HelloWorld`Třída je již zaregistrována. Všechny další implementace objektu actor přidané do projektu musí být také registrovány v `Main()` metodě.

## <a name="customize-the-helloworld-actor"></a>Přizpůsobení objektu actor Hello

Šablona projektu definuje některé metody v `IHelloWorld` rozhraní a implementuje je v `HelloWorld` implementaci objektu actor.  Nahraďte tyto metody, aby služba objektu actor vrátila jednoduchý řetězec "Hello World".

V projektu *HelloWorld. Interfaces* v souboru *IHelloWorld. cs* Nahraďte definici rozhraní následujícím způsobem:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

V projektu **HelloWorld** v souboru **HelloWorld. cs** nahraďte celou definici třídy následujícím způsobem:

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

Stisknutím **kombinace kláves CTRL + SHIFT + B** Sestavte projekt a ujistěte se, že vše kompiluje.

## <a name="add-a-client"></a>Přidání klienta

Vytvořte jednoduchou konzolovou aplikaci pro volání služby objektu actor.

1. Klikněte pravým tlačítkem na řešení v Průzkumník řešení > **Přidat**  >  **Nový projekt...**.

2. V části typy projektů **.NET Core** vyberte **Konzolová aplikace (.NET Core)**.  Pojmenujte projekt *ActorClient*.
    
    ![Dialogové okno Přidat nový projekt][6]    
    
    > [!NOTE]
    > Konzolová aplikace není typu aplikace, který byste obvykle používali jako klienta v Service Fabric, ale nabízí pohodlný příklad pro ladění a testování pomocí místního clusteru Service Fabric.

3. Konzolová aplikace musí být 64 aplikace, aby bylo možné zachovat kompatibilitu s projektem rozhraní a dalšími závislostmi.  V Průzkumník řešení klikněte pravým tlačítkem na projekt **ActorClient** a potom klikněte na **vlastnosti**.  Na kartě **sestavení** nastavte **cíl platformy** na hodnotu **x64**.
    
    ![Vlastnosti sestavení][8]

4. Klientský projekt vyžaduje balíček NuGet Reliable Actors.  Klikněte na **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.  V konzole správce balíčků zadejte následující příkaz:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Balíček NuGet a všechny jeho závislosti se nainstalují do projektu ActorClient.

5. Klientský projekt také vyžaduje odkaz na projekt rozhraní.  V projektu ActorClient klikněte pravým tlačítkem na **závislosti** a pak klikněte na **Přidat odkaz na projekt..**.  Vyberte **projekty > řešení** (Pokud ještě není vybrané) a potom zaškrtněte políčko vedle **HelloWorld. Interfaces**.  Klikněte na **OK**.
    
    ![Přidat odkaz – dialogové okno][7]

6. V projektu ActorClient nahraďte celý obsah *programu. cs* následujícím kódem:
    
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

Stisknutím klávesy **F5** Sestavte, nasaďte a spusťte aplikaci místně ve Service Fabric vývojovém clusteru.  Během procesu nasazení si můžete prohlédnout průběh v okně **výstup** .

![Okno výstupu ladění Service Fabric][3]

Když výstup obsahuje text, *je aplikace připravená*, je možné otestovat službu pomocí aplikace ActorClient.  V Průzkumník řešení klikněte pravým tlačítkem na projekt **ActorClient** a pak klikněte na **ladit**  >  **spustit novou instanci**.  Aplikace příkazového řádku by měla zobrazit výstup ze služby objektu actor.

![Výstup aplikace][9]

> [!TIP]
> Modul runtime Service Fabric actor generuje některé [události a čítače výkonu související s metodami objektu actor](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Jsou užitečné v monitorování diagnostiky a výkonu.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [tom, jak Reliable Actors používat platformu Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png
