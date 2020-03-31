---
title: Vytvoření a publikování aplikace a.Net Core do vzdáleného clusteru Linux
description: Vytvoření a publikování aplikací .Net Core zaměřených na vzdálený cluster Linuxu z Visual Studia
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614345"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Použití sady Visual Studio k vytvoření a publikování aplikací .Net Core zaměřených na vzdálený cluster Linux Service Fabric
Pomocí nástrojů Visual Studio můžete vyvíjet a publikovat aplikace Service Fabric .Net Core zaměřené na cluster Linux Service Fabric. Verze sady SDK musí být 3.4 nebo vyšší, aby bylo možné nasadit aplikaci .Net Core, která cílí na clustery Linux Service Fabric z aplikace Visual Studio.

> [!Note]
> Visual Studio nepodporuje ladění service fabric aplikací, které cílí na Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Vytvoření aplikace Service Fabric zaměřené na .Net Core
1. Spusťte Visual Studio jako **správce**.
2. Vytvořte projekt pomocí **projektu >New->.**
3. V dialogovém okně **Nový projekt** zvolte **Cloud -> Service Fabric Application**.
![vytvořit-aplikace]
4. Pojmenujte aplikaci a klepněte na tlačítko **Ok**.
5. Na stránce **Nová služba Fabric vyberte** typ služby, kterou chcete vytvořit v **části .Net Core Section**.
![vytvořit službu]

## <a name="deploy-to-a-remote-linux-cluster"></a>Nasazení do vzdáleného clusteru Linuxu
1. V průzkumníku řešení klikněte pravým tlačítkem myši na aplikaci a vyberte **build**.
![sestavení aplikace]
2. Po dokončení procesu sestavení aplikace klikněte pravým tlačítkem myši na službu a vyberte upravit **soubor csproj**.
![edit-csproj]
3. Upravte vlastnost UpdateServiceFabricManifestEnabled z True na **False,** pokud je služba **typu projektu objektu actor**. Pokud vaše aplikace nemá službu objektu actor, přejděte ke kroku 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Nastavení updateServiceFabricManifestEnabled na false, zakáže aktualizace ServiceManifest.xml během sestavení. Žádné změny, jako je například přidání, odebrání nebo přejmenování služby, se neprojeví v souboru ServiceManifest.xml. Pokud jsou provedeny nějaké změny, musíte aktualizovat ServiceManifest ručně nebo dočasně nastavit UpdateServiceFabricManifestEnabled na true a vytvořit službu, která bude aktualizovat ServiceManifest.xml a potom ji vrátit zpět na false.
>

4. Aktualizujte runtimeindetifier z win7-x64 na cílovou platformu v projektu služby.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. V ServiceManifest aktualizujte entrypoint program odebrat .exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. V Průzkumníku řešení klikněte pravým tlačítkem myši na aplikaci a vyberte **publikovat**. Zobrazí se dialogové okno **Publikovat**.
7. V **aplikaci Connection Endpoint**vyberte koncový bod pro vzdálený cluster Service Fabric Linux, na který chcete cílit.
![publikovat-aplikace]

<!--Image references-->
[vytvořit-aplikace]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[vytvořit službu]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[sestavení aplikace]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publikovat-aplikace]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Další kroky
* Další informace o [začínáme s Service Fabric s .Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
