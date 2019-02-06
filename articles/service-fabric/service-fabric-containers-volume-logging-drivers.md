---
title: Service Fabric Azure, soubory ovladačů svazků (Preview) | Dokumentace Microsoftu
description: Service Fabric podporuje použití služby soubory Azure zálohovat svazky z vašeho kontejneru. Toto je momentálně ve verzi preview.
services: service-fabric
documentationcenter: other
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/10/2018
ms.author: twhitney, subramar
ms.openlocfilehash: f2636720f6f1faeffb9a63052efdf009668d806f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752071"
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Service Fabric soubory Azure svazku ovladače (Preview)
Je modul plug-in Azure Files svazku [modulu plug-in svazku Docker](https://docs.docker.com/engine/extend/plugins_volume/) poskytující [soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) na základě svazky pro kontejnery Dockeru. Tento modul plug-in Docker svazek je zabalený jako aplikace Service Fabric, který je možné nasadit do clusterů Service Fabric. Jeho účelem je poskytnout službě soubory Azure založené na svazky pro ostatní kontejnerové aplikace Service Fabric, které jsou nasazené na clusteru.

> [!NOTE]
> Verze 6.4.571.9494 modul plug-in Azure Files svazek je verze preview, která je k dispozici s tímto dokumentem. Jako verze preview, je **není** podporovaných pro použití v produkčním prostředí.
>

## <a name="prerequisites"></a>Požadavky
* Verze Windows modulu plug-in Azure Files svazku funguje na [Windows Server verze 1709](https://docs.microsoft.com/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 verze 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) nebo pouze novější operační systémy. Linux verze modulu plug-in svazku soubory Azure funguje ve všech verzích operačních systémů podporovaných Service Fabric.

* Modul plug-in Azure Files svazku funguje pouze na Service Fabric verze 6.2 a novější.

* Postupujte podle pokynů [dokumentace ke službě Azure Files](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) k vytvoření sdílené složky pro aplikaci Service Fabric container používat jako svazek.

* Budete potřebovat [prostředí Powershell s modulem Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) nebo [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) nainstalované.

* Pokud používáte kontejnery Hyper-v, je nutné přidat v části Nastavení fabricSettings šablony ARM (Azure clusteru) nebo ClusterConfig.json (samostatného clusteru) nebo ClusterManifest (místního clusteru) následující fragmenty kódu. Budete potřebovat název svazku a port, který svazek naslouchá na clusteru. 

V ClusterManifest následující potřeba do ní přidat v části Hosting. V tomto příkladu je název svazku **sfazurefile** a port, který naslouchá na clusteru je **19300**.  

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19300" />
</Section>
```

V části Nastavení fabricSettings šablony ARM (pro nasazení v Azure) nebo ClusterConfig.json (pro samostatné nasazení) je potřeba přidat následující fragment kódu. 

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19300"
      }
    ]
  }
]
```


## <a name="deploy-the-service-fabric-azure-files-application"></a>Nasazení aplikace Service Fabric Azure Files

Aplikace Service Fabric, která poskytuje pro vaše kontejnery svazků si můžete stáhnout z následující [odkaz](https://aka.ms/sfvolume6.4). Aplikace je nasadit do clusteru přes [PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications), [rozhraní příkazového řádku](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle-sfctl) nebo [rozhraní API FabricClient](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Pomocí příkazového řádku, změňte adresář na kořenovém adresáři stažený balíček aplikace.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Zkopírujte balíček aplikace do úložiště imagí s odpovídající hodnotou pro [ApplicationPackagePath] a [ImageStoreConnectionString] spuštěním následujícího příkazu:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Zaregistrujte typ aplikace

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Vytvoření aplikace v příkazu pro vytvoření aplikace níže si všimněte **ListenPort** parametr aplikace. Tato hodnota zadaná pro parametr této aplikace je port, na kterém modul plug-in Azure Files svazku čeká na požadavky z démona Dockeru. Je důležité zajistit, že port, který poskytuje k aplikaci není v konfliktu s jakýkoli jiný port, který cluster nebo vaše aplikace používat.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter nepodporuje připojení SMB mapování do kontejnerů ([, která je podporována pouze v systému Windows Server verze 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Toto omezení zabrání mapování sítě svazek a ovladače svazku soubory Azure na verze starší než 1709.
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Nasazení aplikace v místním vývojovém clusteru
Výchozí počet instancí služby soubory Azure aplikace modulu plug-in svazku se -1, což znamená, že instance služby nasazené na každém uzlu v clusteru. Při nasazování aplikace modulu plug-in Azure Files svazku na místní vývojový cluster, počet instancí služby by měla zadat jako 1. Můžete to udělat pomocí **InstanceCount** parametr aplikace. Proto je příkaz pro nasazení aplikace modulu plug-in Azure Files svazku v místním vývojovém clusteru:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurace vaší aplikace, aby používaly svazku
Následující fragment kódu ukazuje, jak lze zadat svazku soubory Azure na základě v manifestu aplikace vaší aplikace. Konkrétní elementu zájmu je **svazku** značky:

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
                <DriverOption Name="storageAccountFQDN" Value="" />
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

Název ovladače pro modul plug-in Azure Files svazek je **sfazurefile**. Tato hodnota je nastavena pro **ovladač** atribut **svazku** elementu v manifestu aplikace.

V **svazku** element ve výše uvedeném fragmentu, modul plug-in Azure Files svazku vyžaduje následující značky:
- **Zdroj** – jde o název svazku. Uživatele můžete vybrat libovolný název pro jejich svazek.
- **Určení** – tato značka je místo, svazek je namapována na v rámci spuštěný kontejner. Díky tomu se cíl nemůže být umístění, které již v kontejneru

Jak je znázorněno **DriverOption** elementy ve výše uvedeném fragmentu, modul plug-in svazku soubory Azure podporuje následující možnosti ovladače:
- **shareName** – název sdílené složky Azure Files, která poskytuje svazku pro kontejner.
- **storageAccountName** – název účtu úložiště Azure, která obsahuje soubor Azure Files sdílet.
- **storageAccountKey** -přístupový klíč pro účet úložiště Azure, který obsahuje sdílené soubory Azure.
- **storageAccountFQDN** – název domény přidružený k účtu úložiště. Pokud není zadán storageAccountFQDN, název domény bude vytvořen pomocí suffix(.file.core.windows.net) výchozí položka storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Použití vlastní svazek nebo ovladače protokolování
Service Fabric také umožní používat vlastní [svazku](https://docs.docker.com/engine/extend/plugins_volume/) nebo [protokolování](https://docs.docker.com/engine/admin/logging/overview/) ovladače. Pokud v clusteru není nainstalován ovladač Dockeru svazek/protokolování, můžete je nainstalovat ručně pomocí protokolů RDP/SSH. Instalace se prostřednictvím těchto protokolů můžete provádět [škálovací sady virtuálních počítačů spouštěcí skript](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) nebo [SetupEntryPoint skript](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model).

Příklad skriptu pro instalaci [svazku ovladač Dockeru pro Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) vypadá takto:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Ve svých aplikacích pomocí svazku nebo protokolování ovladače, které jste nainstalovali, je třeba zadejte odpovídající hodnoty v **svazku** a **LogConfig** elementů v rámci  **ContainerHostPolicies** v manifestu aplikace.

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

Při zadávání svazku modulu plug-in Service Fabric automaticky vytvoří svazek pomocí zadaných parametrů. **Zdroj** značky **svazku** elementu je název svazku a **ovladač** značka Určuje modul plug-in svazku ovladače. **Cílové** značka je umístění, která **zdroje** je namapována na v rámci spuštěný kontejner. Cíl nemůže být tedy umístění, které již existuje v rámci vašeho kontejneru. Parametry je možné zadat pomocí **DriverOption** označte následujícím způsobem:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Parametry aplikace jsou podporovány pro svazky, jak je znázorněno v předchozím fragmentu kódu manifestu (vyhledejte `MyStorageVar` Příkladem použití).

Pokud ovladač Dockeru protokolu není zadána, budete muset nasadit agenty (nebo kontejnery) pro zpracování protokolů v clusteru. **DriverOption** značky umožňuje určit možnosti pro ovladač protokolu.

## <a name="next-steps"></a>Další postup
* Pokud chcete zobrazit ukázky kontejnerů, včetně svazku ovladač, navštivte prosím [ukázky kontejnerů Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* K nasazení kontejnerů do clusteru Service Fabric, najdete v článku [nasadíte kontejner ve službě Service Fabric](service-fabric-deploy-container.md)
