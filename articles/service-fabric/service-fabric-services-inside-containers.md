---
title: Kontejnerizujte služby Azure Service Fabric ve Windows
description: Zjistěte, jak kontejnerizovat spolehlivé služby Service Fabric a služby Reliable Actors v systému Windows.
ms.topic: conceptual
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: 9fe5980c13f655f8f30cc42771971a5015460420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466183"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Kontejnerizace služeb Reliable Actors a Reliable Services služby Service Fabric ve Windows

Service Fabric podporuje kontejnerizace Service Fabric mikroslužeb (spolehlivé služby a spolehlivé actor založené služby). Další informace naleznete v tématu [kontejnery service fabric](service-fabric-containers-overview.md).

Tento dokument obsahuje pokyny k tomu, aby vaše služba běžela uvnitř kontejneru systému Windows.

> [!NOTE]
> V současné době tato funkce funguje pouze pro Systém Windows. Chcete-li spustit kontejnery, musí být cluster spuštěn v systému Windows Server 2016 s kontejnery.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Postup kontejnerize aplikace Service Fabric

1. Otevřete aplikaci Service Fabric v sadě Visual Studio.

2. Přidejte do projektu [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) tříd. Kód v této třídě je pomocníksprávně načíst service fabric runtime binárnísoubory uvnitř aplikace při spuštění uvnitř kontejneru.

3. Pro každý balíček kódu, který chcete kontejnerizovat, inicializovat zavaděč v vstupním bodě programu. Přidejte statický konstruktor zobrazený v následujícím fragmentu kódu do souboru vstupního bodu programu.

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

4. Sestavte a [zabalte](service-fabric-package-apps.md#Package-App) svůj projekt. Chcete-li vytvořit a vytvořit balíček, klepněte pravým tlačítkem myši na projekt aplikace v Průzkumníku řešení a zvolte příkaz **Balíček.**

5. Pro každý balíček kódu, který potřebujete k kontejnerizaci, spusťte skript Prostředí PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Použití je následující:

    Úplná .NET
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
      Skript vytvoří složku s artefakty Dockeru v $dockerPackageOutputDirectoryPath. Upravte generovaný `expose` dockerfile na libovolné porty, spusťte instalační skripty a tak dále. na základě vašich potřeb.

6. Dále je potřeba [vytvořit](service-fabric-get-started-containers.md#Build-Containers) a [odsunout](service-fabric-get-started-containers.md#Push-Containers) balíček kontejnerů Dockeru do úložiště.

7. Upravte applicationManifest.xml a ServiceManifest.xml a přidejte bitovou kopii kontejneru, informace o úložišti, ověřování registru a mapování mezi porty na hostitele. Úpravy manifestů najdete v [tématu Vytvoření aplikace kontejneru Azure Service Fabric](service-fabric-get-started-containers.md). Definice balíčku kódu v manifestu služby musí být nahrazena odpovídající image kontejneru. Nezapomeňte změnit EntryPoint na typ ContainerHost.

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

8. Přidejte mapování port-to-host pro replikátor a koncový bod služby. Vzhledem k tomu, že oba tyto porty jsou přiřazeny za běhu service fabric, ContainerPort je nastavena na nulu použít přiřazený port pro mapování.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Informace o konfiguraci režimu izolace kontejnerů naleznete v [tématu Konfigurace izolovaného režimu]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). Systém Windows podporuje pro kontejnery dva režimy izolace: procesy a Hyper-V. Následující úryvky ukazují, jak je v souboru manifestu aplikace určen režim izolace.

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

> [!NOTE] 
> Ve výchozím nastavení service fabric aplikace mají přístup k service fabric runtime, ve formě koncového bodu přijetí požadavků specifických pro aplikaci. Zvažte zakázání tohoto přístupu, pokud aplikace hostuje nedůvěryhodný kód. Další informace naleznete [v doporučených postupech zabezpečení v service fabric .](service-fabric-best-practices-security.md#platform-isolation) Chcete-li zakázat přístup k runtime Service Fabric, přidejte následující nastavení v části Zásady manifestu aplikace odpovídající manifestu importované služby, a to následovně:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Chcete-li tuto aplikaci otestovat, musíte ji nasadit do clusteru se spuštěnou verzí 5.7 nebo vyšší. Pro runtime verze 6.1 nebo nižší je třeba upravit a aktualizovat nastavení clusteru, aby byla tato funkce náhledu povolena. Podle pokynů v tomto [článku](service-fabric-cluster-fabric-settings.md) přidejte nastavení zobrazené dále.
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

11. Další [nasazení](service-fabric-deploy-remove-applications.md) upraveného balíčku aplikace do tohoto clusteru.

Nyní byste měli mít kontejnerizované service fabric aplikace spuštěna clusteru.

## <a name="next-steps"></a>Další kroky
* Další informace o spouštění [kontejnerů v Service Fabric](service-fabric-get-started-containers.md).
* Informace o [životním cyklu aplikace](service-fabric-application-lifecycle.md) Service Fabric.
