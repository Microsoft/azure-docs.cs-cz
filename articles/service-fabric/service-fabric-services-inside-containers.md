---
title: Kontejnerizace služby Azure Service Fabric ve Windows
description: Naučte se kontejnerizace Service Fabric Reliable Services a Reliable Actors služby ve Windows.
ms.topic: conceptual
ms.date: 5/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e0c5f3ecf0334ad2190d8542e54388a2a25d30fb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999803"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Kontejnerizace služeb Reliable Actors a Reliable Services služby Service Fabric ve Windows

Service Fabric podporuje mikroslužby Service Fabric uzavření (Reliable Services a spolehlivé služby založené na objektu actor). Další informace najdete v tématu [kontejnery Service Fabric](service-fabric-containers-overview.md).

Tento dokument poskytuje pokyny k tomu, abyste mohli spustit službu v kontejneru Windows.

> [!NOTE]
> Tato funkce je v tuto chvíli funkční jenom pro Windows. Ke spuštění kontejnerů musí být cluster spuštěný v systému Windows Server 2016 s kontejnery.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Postup kontejnerizace vaší aplikace Service Fabric

1. Otevřete aplikaci Service Fabric v aplikaci Visual Studio.

2. Přidejte do projektu třídu [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) . Kód v této třídě je Pomocník pro správné načtení Service Fabric binárních souborů modulu runtime do aplikace při spuštění v rámci kontejneru.

3. Pro každý balíček kódu, který byste chtěli kontejnerizace, inicializujte zavaděč v vstupním bodě programu. Přidejte statický konstruktor zobrazený v následujícím fragmentu kódu do souboru vstupního bodu programu.

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

4. Sestavte a [zabalite](service-fabric-package-apps.md#Package-App) svůj projekt. Chcete-li sestavit a vytvořit balíček, klikněte pravým tlačítkem na projekt aplikace v Průzkumník řešení a vyberte příkaz **balíček** .

5. Pro každý balíček kódu, který potřebujete kontejnerizace, spusťte PowerShellový skript [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Použití je následující:

    Plné rozhraní .NET
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
      Skript vytvoří složku s artefakty Docker na $dockerPackageOutputDirectoryPath. Upravte vygenerovanou souboru Dockerfile na `expose` libovolných portech, spusťte instalační skripty a tak dále. podle vašich potřeb.

6. Dál je potřeba [sestavit](service-fabric-get-started-containers.md#Build-Containers) a [Vložit](service-fabric-get-started-containers.md#Push-Containers) balíček kontejneru Docker do úložiště.

7. Upravte ApplicationManifest.xml a ServiceManifest.xml a přidejte tak image kontejneru, informace o úložišti, ověřování v registru a mapování portů na hostitele. Informace o úpravách manifestů najdete v tématu [Vytvoření aplikace Azure Service Fabric Container](service-fabric-get-started-containers.md). Definice balíčku kódu v manifestu služby musí být nahrazena odpovídající imagí kontejneru. Ujistěte se, že jste vstupní bod změnili na ContainerHost typ.

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

8. Přidejte mapování portů na hostitele pro replikátor a koncový bod služby. Vzhledem k tomu, že oba tyto porty jsou přiřazeny za běhu pomocí Service Fabric, je ContainerPort na hodnotu nula pro použití přiřazeného portu pro mapování.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Informace o konfiguraci režimu izolace kontejnerů najdete v tématu [Konfigurace režimu izolace]( ./service-fabric-get-started-containers.md#configure-isolation-mode). Systém Windows podporuje pro kontejnery dva režimy izolace: procesy a Hyper-V. Následující fragmenty kódu ukazují, jak je určen režim izolace v souboru manifestu aplikace.

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
> Ve výchozím nastavení mají aplikace Service Fabric přístup k modulu runtime Service Fabric ve formě koncového bodu, který přijímá požadavky specifické pro aplikaci. Zvažte zakázání tohoto přístupu, pokud je aplikace hostitelem nedůvěryhodného kódu. Další informace najdete [v tématu osvědčené postupy zabezpečení v Service Fabric](service-fabric-best-practices-security.md#platform-isolation). Chcete-li zakázat přístup k modulu Service Fabric runtime, přidejte následující nastavení do oddílu zásady manifestu aplikace odpovídající importovanému manifestu služby následujícím způsobem:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Chcete-li otestovat tuto aplikaci, je nutné ji nasadit do clusteru se spuštěnou verzí 5,7 nebo vyšší. Pro modul runtime verze 6,1 nebo nižší je nutné upravit a aktualizovat nastavení clusteru, aby byla tato funkce ve verzi Preview povolena. Podle kroků v tomto [článku](service-fabric-cluster-fabric-settings.md) přidejte nastavení zobrazené dál.
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

11. Dále [Nasaďte](service-fabric-deploy-remove-applications.md) upravený balíček aplikace do tohoto clusteru.

Nyní byste měli mít kontejner Service Fabric aplikace, ve které je spuštěný cluster.

## <a name="next-steps"></a>Další kroky
* Další informace o spouštění [kontejnerů v Service Fabric](service-fabric-get-started-containers.md).
* Informace o [životním cyklu aplikace](service-fabric-application-lifecycle.md) Service Fabric.