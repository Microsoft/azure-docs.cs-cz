---
title: Vytvoření a publikování aplikace a.Net Core do vzdáleného clusteru se systémem Linux
description: Vytváření a publikování aplikací .Net Core cílících na vzdálený cluster se systémem Linux ze sady Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75614345"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Použití sady Visual Studio k vytváření a publikování aplikací .Net Core cílících na vzdálený Service Fabric clusteru se systémem Linux
Pomocí nástrojů sady Visual Studio můžete vyvíjet a publikovat Service Fabric .Net Core aplikace cílící na cluster se systémem Linux Service Fabric. Verze sady SDK musí být 3,4 nebo vyšší, aby bylo možné nasadit .Net Core aplikace cílené na clustery se systémem Linux Service Fabric ze sady Visual Studio.

> [!Note]
> Visual Studio nepodporuje ladění Service Fabricch aplikací, které jsou určené pro Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Vytvoření cílení Service Fabric aplikace .Net Core
1. Spusťte Visual Studio jako **správce**.
2. Vytvořte projekt pomocí **>projektu New->**.
3. V dialogovém okně **Nový projekt** vyberte **Service Fabric aplikace Cloud->**.
![vytvořit aplikaci]
4. Pojmenujte aplikaci a klikněte na **OK**.
5. Na stránce **Nová služba Service Fabric** v **části .NET Core** vyberte typ služby, kterou chcete vytvořit.
![vytvořit službu]

## <a name="deploy-to-a-remote-linux-cluster"></a>Nasazení na vzdálený cluster se systémem Linux
1. V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci a vyberte **sestavení**.
![sestavení-aplikace]
2. Po dokončení procesu sestavení pro aplikaci klikněte pravým tlačítkem na službu a vyberte upravit **soubor CSPROJ**.
![Upravit – csproj]
3. Pokud je služba **typem projektu actor**, upravte vlastnost UpdateServiceFabricManifestEnabled z true na **false** . Pokud vaše aplikace neobsahuje službu objektu actor, přejděte ke kroku 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Nastavením UpdateServiceFabricManifestEnabled na hodnotu false dojde k zakázání aktualizací ServiceManifest.xml během sestavení. Jakékoli změny, jako je například přidání, odebrání nebo přejmenování služby, se v ServiceManifest.xml neprojeví. Pokud jsou provedeny nějaké změny, musíte buď aktualizovat ServiceManifest ručně, nebo dočasně nastavit UpdateServiceFabricManifestEnabled na hodnotu true, a sestavit službu, která aktualizuje ServiceManifest.xml a pak ji vrátit zpět na false.
>

4. Aktualizujte RuntimeIndetifier z Win7-x64 na cílovou platformu v projektu služby.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. V ServiceManifest aktualizujte program EntryPoint na Remove. exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. V Průzkumník řešení klikněte pravým tlačítkem na aplikaci a vyberte **publikovat**. Zobrazí se dialogové okno **Publikovat**.
7. V části **koncový bod připojení** vyberte koncový bod pro cluster vzdáleného Service Fabric Linux, na který chcete cílit.
![publikování – aplikace]

<!--Image references-->
[vytvořit aplikaci]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[vytvořit službu]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[sestavení-aplikace]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[Upravit – csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publikování – aplikace]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Další kroky
* Přečtěte si informace o tom, jak [začít s Service Fabric s .NET Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
