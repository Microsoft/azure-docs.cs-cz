---
title: CodePackages inicializátoru v Service Fabric
description: Popisuje CodePackages inicializátoru v Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258976"
---
# <a name="initializer-codepackages"></a>Balíčky kódu inicializátoru

Počínaje verzí 7,1 Service Fabric podporuje **CodePackages inicializátorů** pro [kontejnery][containers-introduction-link] a [spustitelné aplikace hosta][guest-executables-introduction-link] . Inicializátor CodePackages poskytuje možnost provádět inicializace v oboru pro ServicePack ještě předtím, než ostatní CodePackages spustí provádění. Jejich vztah k ServicePack je podobný jako [SetupEntryPoint][setup-entry-point-link] pro CodePackage.

Než budete pokračovat v tomto článku, doporučujeme seznámit se s [Service Fabric aplikačním modelem][application-model-link] a [modelem Service Fabric hostování][hosting-model-link].

> [!NOTE]
> Inicializátor CodePackages se v tuto chvíli nepodporuje u služeb napsaných pomocí programovacího modelu [Reliable Services][reliable-services-link] .
 
## <a name="semantics"></a>Sémantika

Očekává se spuštění inicializátoru CodePackage pro **úspěšné dokončení (ukončovací kód 0)**. Neúspěšný inicializátor CodePackage se restartuje, dokud se úspěšně nedokončí. Více inicializátorů CodePackages je povoleno a jsou spouštěny do **úspěšného dokončení**v **zadaném pořadí** před jiným CodePackages ve službě ServicePack. **sequentially**

## <a name="specifying-initializer-codepackages"></a>Určení CodePackages inicializátoru
Můžete označit CodePackage jako inicializátor nastavením atributu **inicializátoru** na **hodnotu true** v ServiceManifest. Pokud existuje více inicializátorů CodePackages, jejich pořadí spuštění lze zadat prostřednictvím atributu **ExecOrder** . **ExecOrder** musí být nezáporné celé číslo a je platné pouze pro inicializátor CodePackages. Nejprve se spustí inicializátor CodePackages s nižšími hodnotami **ExecOrder** . Pokud není **ExecOrder** pro inicializátor CodePackage zadán, je použita výchozí hodnota 0. Relativní pořadí provádění inicializátoru CodePackages se stejnou hodnotou **ExecOrder** není určeno.

Následující fragment kódu ServiceManifest popisuje tři CodePackagesy, které jsou označeny jako Inicializátory. Když se aktivuje tato ServicePack, *InitCodePackage0* se spustí jako první, protože má nejnižší hodnotu **ExecOrder**. Po úspěšném dokončení (ukončovací kód 0) *InitCodePackage0*se spustí *InitCodePackage1* . Nakonec po úspěšném dokončení *InitCodePackage1*se spustí *WorkloadCodePackage* .

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Kompletní příklad použití inicializátoru CodePackages

Pojďme se podívat na úplný příklad pomocí inicializátoru CodePackages.

> [!IMPORTANT]
> Následující příklad předpokládá znalost vytváření [kontejnerových aplikací pro Windows pomocí Service Fabric a Docker][containers-getting-started-link].
>
> Tento příklad odkazuje na mcr.microsoft.com/windows/nanoserver:1809. Kontejnery Windows serveru nejsou kompatibilní napříč všemi verzemi hostitelského operačního systému. Další informace najdete v tématu [Kompatibilita verzí kontejnerů Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Následující ServiceManifest.xml jsou vytvořeny na fragmentu ServiceManifest popsané výše. *InitCodePackage0*, *InitCodePackage1* a *WorkloadCodePackage* jsou CodePackages, které představují kontejnery. Při aktivaci se *InitCodePackage0* spustí jako první. Zaznamená zprávu do souboru a ukončí se. V dalším kroku se spustí *InitCodePackage1* , který také zaznamená zprávu do souboru a ukončí. Nakonec *WorkloadCodePackage* zahájí provádění. Zaznamená také zprávu do souboru, vytvoří výstup obsahu souboru do **stdout** a pak se znovu otestuje.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Následující ApplicationManifest.xml popisuje aplikaci na základě ServiceManifest.xml popsaných výše. Všimněte si, že určuje stejné připojení **svazku** pro všechny kontejnery, tj. **C:\WorkspaceOnHost** je připojeno na **C:\WorkspaceOnContainer** na všech třech kontejnerech. Čistý efekt znamená, že všechny kontejnery zapisují do stejného souboru protokolu v pořadí, ve kterém jsou aktivované.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
Po úspěšné aktivaci programu ServicePack by měl být obsah **C:\WorkspaceOnHost\log.txt** následující.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>Další kroky

Související informace najdete v následujících článcích.

* [Service Fabric a kontejnery.][containers-introduction-link]
* [Service Fabric a spustitelné soubory hosta.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
