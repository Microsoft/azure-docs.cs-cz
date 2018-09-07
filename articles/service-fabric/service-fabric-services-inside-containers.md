---
title: Kontejnerizace služby Azure Service Fabric na Windows
description: Zjistěte, jak kontejnerizovat služby Service Fabric Reliable Services a Reliable Actors ve Windows.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: d3ed1ff46bf4c82a172954828ec74bae80241288
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057036"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Kontejnerizace Service Fabric Reliable Services a Reliable Actors ve Windows

Service Fabric podporuje Service Fabric mikroslužeb uzavření do kontejneru (Reliable Services a Reliable Actors podle služby). Další informace najdete v tématu [service fabric kontejnery](service-fabric-containers-overview.md).

Tento dokument obsahuje pokyny k získání vaší služby běžící uvnitř kontejneru Windows.

> [!NOTE]
> Aktuálně tato funkce funguje pouze pro Windows. Pro spouštění kontejnerů clusteru musí běžet na Windows serveru 2016 s kontejnery.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Postup kontejnerizujte své aplikace Service Fabric

1. Otevřete aplikaci Service Fabric v sadě Visual Studio.

2. Přidejte třídu [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) do projektu. Kód v této třídě je pomocná správně načíst binární soubory modulu runtime Service Fabric do své aplikace při spuštění v rámci kontejneru.

3. Pro každý balíček kódu, který byste chtěli kontejnerizace, inicializovat zavaděč v položce program bodu. Přidání statického konstruktoru je znázorněno v následujícím fragmentu kódu do souboru vstupní bod programu.

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

4. Sestavení a [balíčku](service-fabric-package-apps.md#Package-App) váš projekt. Pro sestavování a vytváření balíčku, klikněte pravým tlačítkem na projekt aplikace v Průzkumníku řešení a zvolte **balíčku** příkazu.

5. Pro každý balíček kódu je potřeba kontejnerizace, spusťte skript prostředí PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Použití je následujícím způsobem:

    Úplné rozhraní .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      Tento skript vytvoří složku s artefakty Dockeru na $dockerPackageOutputDirectoryPath. Upravte vygenerovaný soubor Dockerfile k `expose` žádné porty, spusťte instalační program skripty a tak dále. na základě vašich potřeb.

6. Dál je potřeba [sestavení](service-fabric-get-started-containers.md#Build-Containers) a [nabízených](service-fabric-get-started-containers.md#Push-Containers) balíčku kontejneru Dockeru do úložiště.

7. Upravte ServiceManifest.xml a ApplicationManifest.xml přidání image kontejneru, informace o úložišti, ověřování registru a mapování portů na hostitele. Úpravy manifestů, naleznete v tématu [vytvoření aplikace Azure Service Fabric typu kontejner](service-fabric-get-started-containers.md). Balíček definice kódu v manifestu služby je potřeba nahradit odpovídající image kontejneru. Ujistěte se, že chcete-li změnit na typ ContainerHost vstupnímu bodu.

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

8. Přidáte mapování portů na hostitele pro replikátor a koncový bod služby. Vzhledem k tomu, že oba tyto porty jsou přiřazeny, na modul runtime Service Fabric, ContainerPort nastavená na nula, pokud chcete použít pro mapování přidělenému portu.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. Konfigurace režimu izolace kontejneru, naleznete v tématu [konfigurace režimu izolace]( https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). Systém Windows podporuje pro kontejnery dva režimy izolace: procesy a Hyper-V. Následující fragmenty kódu ukazují, jak je režim izolace určený v souboru manifestu aplikace.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
  ...
  </ContainerHostPolicies>
</Policies>
 ```
  ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
  ...
  </ContainerHostPolicies>
</Policies>
 ```

10. K otestování této aplikace, musíte ji nasadit do clusteru, který používá verzi 5.7 nebo novější. Pro modul runtime verze 6.1 nebo nižší budete muset upravit a aktualizovat nastavení clusteru povolit tuto funkci ve verzi preview. Postupujte podle kroků v tomto [článku](service-fabric-cluster-fabric-settings.md) přidat nastavení je ukázáno dále.
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

11. Další [nasazení](service-fabric-deploy-remove-applications.md) upravených aplikace balíčku do tohoto clusteru.

Teď byste měli mít kontejnerizované aplikace Service Fabric spuštěná vašeho clusteru.

## <a name="next-steps"></a>Další postup
* Další informace o spouštění [kontejnerů v Service Fabric](service-fabric-get-started-containers.md).
* Informace o [životním cyklu aplikace](service-fabric-application-lifecycle.md) Service Fabric.
