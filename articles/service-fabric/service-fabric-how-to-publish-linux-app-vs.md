---
title: Další informace o vytváření a publikování v.Net Core aplikací do vzdáleného clusteru Azure Service Fabric s Linuxem | Dokumentace Microsoftu
description: Vytvoření a publikování.Net Core aplikace zaměřené na vzdálený cluster s Linuxem ze sady Visual Studio
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078661"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Vytvoření a publikování.Net Core pomocí sady Visual Studio aplikace cílené na vzdálený cluster Service Fabric s Linuxem
Pomocí sady Visual Studio nástroje můžete vyvíjet a publikovat.Net Core pro Service Fabric aplikace cílené na clusteru Service Fabric s Linuxem. Verze sady SDK musí být 3.4 nebo vyšší pro nasazení.Net Core clusterů aplikaci určenou pro Service Fabric s Linuxem ze sady Visual Studio.

> [!Note]
> Visual Studio nepodporuje ladění aplikace Service Fabric, které jsou určené pro Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Vytvoření aplikace Service Fabric cílí na.Net Core
1. Spusťte sadu Visual Studio jako **správce**.
2. Vytvořte projekt pomocí **soubor -> Nový -> projekt**.
3. V **nový projekt** dialogovém okně zvolte **Cloud -> aplikace Service Fabric**.
![create-application]
4. Pojmenujte aplikaci a klikněte na tlačítko **Ok**.
5. Na **nová služba Service Fabric** vyberte typ služby, které chcete vytvořit v části **.Net Core části**.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>Nasazení na vzdálený cluster s Linuxem
1. V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci a vyberte **sestavení**.
![sestavení aplikace]
2. Po dokončení procesu sestavení pro aplikaci, klikněte pravým tlačítkem na službu a vyberte možnost Upravit **souboru csproj**.
![edit-csproj]
3. Upravit vlastnost UpdateServiceFabricManifestEnabled z True na **False** Pokud služba je **typu projektu objektu actor**. Pokud aplikace nemá žádné služby objektu actor, přejděte ke kroku 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> UpdateServiceFabricManifestEnabled nastavení na hodnotu false, bude zakázat aktualizace ServiceManifest.xml během sestavení. Všechny změny těchto jak přidat, odebrat nebo přejmenovat ke službě se neprojeví v ServiceManifest.xml. Pokud jsou provedeny nějaké změny, které musí aktualizovat souboru ServiceManifest ručně nebo dočasně nastavena na hodnotu true a vytvořit službu, která provede aktualizaci ServiceManifest.xml a pak obnovte UpdateServiceFabricManifestEnabled zpět na hodnotu false.
>

4. Aktualizujte RuntimeIndetifier z win7 x64 na cílovou platformu v projektu služby.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. V souboru ServiceManifest aktualizujte vstupní bod programu .exe odebrat. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci a vyberte **publikovat**. Zobrazí se dialogové okno **Publikovat**.
7. V **koncový bod připojení**, vyberte koncový bod pro vzdálený cluster Service Fabric s Linuxem, který chcete cílit.
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[sestavení aplikace]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Další postup
* Další informace o [Začínáme s platformou Service Fabric s.Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
