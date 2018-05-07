---
title: Service Fabric Azure soubory ovladačů svazek (Preview) | Microsoft Docs
description: Service Fabric podporuje používání Azure Files zálohovat svazky z vašeho kontejneru. Toto je momentálně ve verzi preview.
services: service-fabric
documentationcenter: other
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/30/2018
ms.author: subramar
ms.openlocfilehash: d4751182cac9b5b952ef9a9dd125408267c1f8d0
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Služba Fabric soubory Azure svazku ovladačů (Preview)
Tento modul plug-in Azure Files svazek je [modulu plug-in svazku Docker](https://docs.docker.com/engine/extend/plugins_volume/) , který poskytuje [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) na základě svazků pro Docker kontejnery. Tento modul plug-in Docker svazek je zabalené jako aplikace Service Fabric, které můžou být nasazené do clusterů Service Fabric. Jeho účelem je poskytnout že Azure soubory na základě svazků pro jiné aplikace Service Fabric kontejneru, které jsou nasazeny do clusteru.

> [!NOTE]
> Verze 6.255.389.9494 modul plug-in Azure Files svazek je verze preview, který je k dispozici v tomto dokumentu. Jako verze preview, je **není** podporovaných pro použití v produkčním prostředí.
>

## <a name="prerequisites"></a>Požadavky
* Verze Windows modul plug-in Azure Files svazku funguje na [Windows Server verze. 1709](https://docs.microsoft.com/en-us/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 verze. 1709](https://docs.microsoft.com/en-us/windows/whats-new/whats-new-windows-10-version-1709) nebo pouze novější operační systémy. Linux verze modulu plug-in svazku Azure Files funguje ve všech verzích operačního systému nepodporuje Service Fabric.

* Postupujte podle pokynů [dokumentace k Azure Files](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-create-file-share) vytvoření sdílené složky pro aplikace Service Fabric kontejner sloužící jako svazek.

* Budete potřebovat [prostředí Powershell s modulem Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started) nebo [SFCTL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli) nainstalována.

## <a name="deploy-the-service-fabric-azure-files-application"></a>Nasazení aplikace soubory Azure Service Fabric

Aplikace Service Fabric, která poskytuje pro vaše kontejnery svazků lze stáhnout z následujícího [odkaz](https://aka.ms/sfvolume). Aplikace se dá nasadit na clusteru přes [prostředí PowerShell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications), [rozhraní příkazového řádku](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-lifecycle-sfctl) nebo [rozhraní API FabricClient](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Pomocí příkazového řádku, změňte adresář na kořenovém adresáři stažený balíček aplikace. 

    ```powershell 
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Zkopírujte balíček aplikace do úložiště bitové kopie, spusťte příkaz níže s odpovídající hodnotou pro [ApplicationPackagePath] a [parametr ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Registrace typu aplikace

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Vytvoření aplikace v příkazu k vytvoření aplikace níže si všimněte, **ListenPort** parametr aplikace. Tato hodnota zadaná pro parametr této aplikace je port, na kterém naslouchá modul plug-in Azure Files svazku pro žádosti od démon Docker. Je důležité zajistit, že zadaný port do aplikace není v konfliktu s další port, který clusteru nebo aplikace použít.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter nepodporuje mapování SMB připojení zařízení k kontejnery ([, je podporována pouze v systému Windows Server verze. 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Pomocí tohoto omezení zabrání mapování sítě svazku a Azure Files svazku ovladačů ve verzích, které jsou starší než. 1709. 
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Nasazení aplikace na místní vývojový cluster
Výchozí počet instancí služby pro aplikaci Azure soubory modulu plug-in svazek je -1, což znamená, že se o instanci služby nasazené na každém uzlu v clusteru. Při nasazování aplikace modulu plug-in Azure Files svazku na místní vývojový cluster, počet instancí služby by měl zadat jako 1. To lze provést pomocí **InstanceCount** parametr aplikace. Proto je příkaz pro nasazení aplikace modulu plug-in Azure Files svazku na místního vývojového clusteru:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurace vaší aplikace na používání svazku
Následující fragment kódu ukazuje, jak lze zadat svazek Azure soubory na základě v manifestu aplikace vaší aplikace. Konkrétní element týkající se nachází **svazku** značky:

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
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
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

Název ovladače pro tento modul plug-in Azure Files svazku je **sfazurefile**. Tato hodnota nastavená pro **ovladač** atribut **svazku** element v manifestu aplikace.

V **svazku** prvek ve výše uvedeném fragmentu, modul plug-in Azure Files svazku vyžaduje následující značky: 
- **Zdroj** -vztahuje se k zadané zdrojové složce, která může být složka ve virtuálním počítači, který je hostitelem kontejnery nebo trvalé vzdáleného úložiště
- **Cílový** – tato značka je umístění, **zdroj** je namapována na spuštěné kontejneru. Proto nemůže být váš cíl umístění, které již existuje v rámci vašeho kontejneru

Jak je znázorněno **DriverOption** elementy ve výše uvedeném fragmentu, modul plug-in svazku soubory Azure podporuje následující možnosti ovladače:

- **shareName** – název sdílené složky souborů Azure, která poskytuje svazku pro kontejner
- **storageAccountName** – název účtu úložiště Azure, který obsahuje soubory Azure soubor sdílet
- **storageAccountKey** -přístupový klíč pro účet úložiště Azure, který obsahuje sdílené složky Azure Files

Všechny výše uvedených možností ovladače jsou povinné. 

## <a name="using-your-own-volume-or-logging-driver"></a>Pomocí vlastního svazku nebo ovladač protokolování
Service Fabric také umožňuje použití vlastního svazku nebo ovladače protokolování. Pokud ovladač svazku nebo protokolování Docker není nainstalovaný v clusteru, můžete je nainstalovat ručně pomocí protokolu RDP/SSH. Můžete provést instalaci těchto protokolech prostřednictvím [škálovací sady virtuálních počítačů spuštění skriptu](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) nebo [SetupEntryPoint skriptu](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Příklad skriptu k instalaci [Docker svazku ovladač pro Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) vypadá takto:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

V aplikacích, používat svazek nebo protokolování ovladače, které jste nainstalovali, museli byste zadejte odpovídající hodnoty v **svazku** a **LogConfig** elementů v rámci  **ContainerHostPolicies** v manifestu aplikace. 

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

## <a name="next-steps"></a>Další postup
* Chcete-li najdete v části Ukázky kontejneru, včetně svazku ovladač, navštivte [ukázky kontejneru Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* K nasazení kontejnerů do clusteru Service Fabric, najdete v článku [nasazení kontejneru v Service Fabric](service-fabric-deploy-container.md)


