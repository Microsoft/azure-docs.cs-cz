---
title: "Postup containerize vaše Azure Service Fabric mikroslužeb (preview)"
description: "Azure Service Fabric přidal novou funkci pro containerize vaše mikroslužeb Service Fabric. Tato funkce je aktuálně ve verzi Preview."
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.openlocfilehash: e66e488d8e547e828c014b105a816a14726e5005
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Postup containerize vaší služby Fabric Reliable Services a Reliable Actors (Preview)

Service Fabric podporuje containerizing mikroslužeb Service Fabric (spolehlivé služeb a služeb na základě spolehlivého Actor). Další informace najdete v tématu [služby fabric kontejnery](service-fabric-containers-overview.md).

Tato funkce je ve verzi preview a tento článek obsahuje různé kroky k získání služby běží uvnitř kontejneru.  

> [!NOTE]
> Tato funkce je ve verzi preview a není podporována v produkčním prostředí. Tato funkce v současné době funkční pro systém Windows. Pokud chcete spustit kontejnery, musí být na Windows Server 2016 s kontejnery spuštěna clusteru.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Postup containerize aplikace Service Fabric

1. Otevřete aplikaci Service Fabric v sadě Visual Studio.

2. Přidání třídy [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) do projektu. Kód v této třídě je pomocné rutiny správně načíst binární soubory modulu runtime Service Fabric v aplikaci při spuštění v rámci kontejneru.

3. Pro každý balíček kódu, kterou jste chtěli containerize, inicializovat zavaděč v programu Vstupní bod. Přidání statického konstruktoru uvedené v následující fragment kódu do souboru programu Vstupní bod.

  ```csharp
  namespace MyApplication
  {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
  ```

4. Sestavení a [balíček](service-fabric-package-apps.md#Package-App) projektu. Sestavení a vytvoření balíčku, klikněte pravým tlačítkem na projekt aplikace v Průzkumníku řešení a zvolte **balíček** příkaz.

5. Pro každý balíček kódu je třeba containerize, spustit skript prostředí PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Využití je následující:
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  Tento skript vytvoří složku s artefakty Docker v $dockerPackageOutputDirectoryPath. Vygenerovaný soubor Docker vystavit žádné porty, spusťte instalační program skripty atd. na základě potřeb upravte.

6. Dále je třeba [sestavení](service-fabric-get-started-containers.md#Build-Containers) a [nabízené](service-fabric-get-started-containers.md#Push-Containers) vašeho balíčku kontejner Docker do úložiště.

7. Upravte ApplicationManifest.xml a ServiceManifest.xml Přidání bitové kopie kontejneru, informace o úložišti, registru ověřování a port hostitele mapování. Úprava manifesty, najdete v části [vytvoření kontejneru aplikace Azure Service Fabric](service-fabric-get-started-containers.md). Definice balíčku kódu v service manifest je nutné vyměnit bitovou kopii odpovídající kontejneru. Ujistěte se, že změnit na typ ContainerHost vstupní bod.

  ```xml
<!-- Code package is your service executable. -->
<CodePackage Name="Code" Version="1.0.0">
  <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
  </EntryPoint>
  <!-- Pass environment variables to your container: -->    
</CodePackage>
  ```

8. Přidáte mapování port hostitele pro replikátor a koncový bod služby. Vzhledem k tomu, že oba tyto porty jsou přiřazeny za běhu pomocí Service Fabric, ContainerPort je nastaven na hodnotu nula použití portu přiřazené pro mapování.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. K testování této aplikace, musíte ho nasadit do clusteru, který používá verzi 5.7 nebo vyšší. Kromě toho budete muset upravte a aktualizujte nastavení clusteru pro povolení této funkce ve verzi preview. Postupujte podle kroků v tomto [článku](service-fabric-cluster-fabric-settings.md) přidejte zobrazí další nastavení.
```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
```
10. Další [nasazení](service-fabric-deploy-remove-applications.md) upravená aplikace balíčku do tohoto clusteru.

Teď byste měli mít kontejnerizované aplikace Service Fabric spuštění clusteru.

## <a name="next-steps"></a>Další postup
* Další informace o spouštění [kontejnerů v Service Fabric](service-fabric-get-started-containers.md).
* Informace o [životním cyklu aplikace](service-fabric-application-lifecycle.md) Service Fabric.
