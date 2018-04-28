---
title: Azure Service Fabric Docker Compose (Preview) | Microsoft Docs
description: Azure Service Fabric přijme formát Docker Compose usnadnění orchestraci existující kontejnery pomocí Service Fabric. Podpora pro Docker Compose je aktuálně ve verzi preview.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 72ff8e99453d3067492d50c3e35396dba9f23c12
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>Pomocí Docker svazku zásuvné moduly a protokolování ovladače v vašeho kontejneru
Azure Service Fabric podporuje určení [Docker svazku zásuvné moduly](https://docs.docker.com/engine/extend/plugins_volume/) a [Docker protokolování ovladače](https://docs.docker.com/engine/admin/logging/overview/) pro vaši službu kontejneru. Může uchovávat vaše data v [Azure Files](https://azure.microsoft.com/services/storage/files/) při vašeho kontejneru je přesunout nebo restartováním jiného hostitele.

Aktuálně jsou podporovány pouze svazku ovladače pro kontejnery Linux. Pokud používáte Windows kontejnery, můžete namapovat svazek Azure Files [SMB3 sdílení](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) bez ovladač svazku. Aby bylo toto mapování aktualizujte virtuální počítače (VM) v clusteru na nejnovější verzi. 1709 Windows serveru.


## <a name="install-the-docker-volumelogging-driver"></a>Nainstalujte ovladač svazku nebo protokolování Docker

Pokud ovladač svazku nebo protokolování Docker není nainstalovaný na počítači, můžete je nainstalovat ručně pomocí protokolu RDP/SSH. Můžete provést instalaci těchto protokolech prostřednictvím [škálovací sady virtuálních počítačů spuštění skriptu](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) nebo [SetupEntryPoint skriptu](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Příklad skriptu k instalaci [Docker svazku ovladač pro Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) vypadá takto:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

> [!NOTE]
> Windows Server 2016 Datacenter nepodporuje mapování SMB připojení zařízení k kontejnery ([, je podporována pouze v systému Windows Server verze. 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Pomocí tohoto omezení zabrání mapování sítě svazku a Azure Files svazku ovladačů ve verzích, které jsou starší než. 1709. 
>   


## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>Zadejte modul plug-in nebo ovladač v manifestu
Moduly plug-in jsou určené v manifestu aplikace takto:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

**Zdroj** značka pro **svazku** element odkazuje do zdrojové složky. Zdrojová složka může být do složky ve virtuálním počítači, který je hostitelem kontejnery nebo trvalé vzdálené úložiště. **Cílové** značka je umístění, **zdroj** je namapována na spuštěné kontejneru. Cíl proto nemůže být umístění, které již existuje v rámci vašeho kontejneru.

Parametry aplikačního jsou podporovány pro svazky, jak je znázorněno v předchozím fragmentu kódu manifestu (vyhledejte `MyStoreVar` příklad použít).

Při zadávání svazek modul plug-in, Service Fabric automaticky vytvoří svazek pomocí zadaných parametrů. **Zdroj** značka je název svazku a **ovladač** značky určuje modulu plug-in svazku ovladačů. Možnosti lze zadat pomocí **DriverOption** značky následujícím způsobem:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Pokud je zadaný ovladač Docker protokolu, budete muset nasazení agentů (nebo kontejnery) pro zpracování protokoly v clusteru. **DriverOption** značku lze použít k určení možností pro ovladač protokolu.

## <a name="next-steps"></a>Další postup
K nasazení kontejnerů do clusteru Service Fabric, najdete v článku [nasazení kontejneru v Service Fabric](service-fabric-deploy-container.md).
