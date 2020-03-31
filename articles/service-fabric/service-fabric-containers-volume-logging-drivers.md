---
title: Ovladač svazku souborů Azure pro service fabric
description: Service Fabric podporuje použití souborů Azure k zálohování svazků z vašeho kontejneru.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: 514a0cb12359d58e38ebc30ae12cdb277757f2b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750038"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Ovladač svazku souborů Azure pro service fabric

Ovladač svazku Soubory Azure je [modul plugin svazek Dockeru,](https://docs.docker.com/engine/extend/plugins_volume/) který poskytuje svazky založené na [Azure Files](/azure/storage/files/storage-files-introduction) pro kontejnery Dockeru. Je zabalen jako aplikace Service Fabric, kterou lze nasadit do clusteru Service Fabric a poskytovat svazky pro jiné aplikace kontejnerů Service Fabric v rámci clusteru.

> [!NOTE]
> Verze 6.5.661.9590 modulu plug-in svazku souborů Azure byla vydána pro obecnou dostupnost.
>

## <a name="prerequisites"></a>Požadavky
* Verze modulu plug-in svazku Souborů Azure funguje pouze v operačních systémech [Windows Server verze 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 verze 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) nebo novějších.

* Linuxová verze modulu plug-in svazku souborů Azure funguje ve všech verzích operačního systému podporovaných service fabricem.

* Modul plug-in svazku souborů Azure funguje jenom na Service Fabric verze 6.2 a novější.

* Podle pokynů v [dokumentaci k souborům Azure](/azure/storage/files/storage-how-to-create-file-share) vytvořte sdílenou složku pro aplikaci kontejneru Service Fabric, která se použije jako svazek.

* Budete potřebovat [Powershell s modulem Service Fabric](/azure/service-fabric/service-fabric-get-started) nebo [nainstalovaným SFCTL.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)

* Pokud používáte hyper-v kontejnery, je třeba přidat následující úryvky v clusteruManifest (místní cluster) nebo fabricSettings části v šabloně Správce prostředků Azure (cluster Azure) nebo ClusterConfig.json (samostatný cluster).

V ClusterManifest následující musí být přidány v části Hostování. V tomto příkladu je název svazku **sfazurefile** a port, který naslouchá v clusteru **je 19100**. Nahraďte je správnými hodnotami pro váš cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

V části fabricSettings v šabloně Azure Resource Manager (pro nasazení Azure) nebo ClusterConfig.json (pro samostatná nasazení) je potřeba přidat následující fragment. Opět nahraďte název svazku a hodnoty portů vlastními.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Nasazení ukázkové aplikace pomocí ovladače svazku Service Fabric Azure Files

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Použití Správce prostředků Azure prostřednictvím poskytnutého skriptu Powershellu (doporučeno)

Pokud váš cluster je založen v Azure, doporučujeme nasadit aplikace do něj pomocí modelu prostředků Azure Resource Manager pro snadné použití a pomoci přejít k modelu údržby infrastruktury jako kódu. Tento přístup eliminuje potřebu sledovat verzi aplikace pro ovladač svazku Azure Files. Umožňuje také udržovat samostatné šablony Azure Resource Manager u každého podporovaného operačního systému. Skript předpokládá, že nasazujete nejnovější verzi aplikace Azure Files a přebírá parametry pro typ operačního systému, ID předplatného clusteru a skupinu prostředků. Skript si můžete stáhnout z [webu pro stahování Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Všimněte si, že to automaticky nastaví ListenPort, což je port, na kterém modul plug-in svazku Soubory Azure naslouchá žádosti od daemon Dockeru, na 19100. Můžete jej změnit přidáním parametru s názvem "listenPort". Ujistěte se, že port není v konfliktu s jiným portem, který cluster nebo vaše aplikace používají.
 

Příkaz nasazení Azure Resource Manageru pro Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Příkaz nasazení Azure Resource Managerpro Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Po úspěšném spuštění skriptu můžete přeskočit na [konfiguraci části aplikace.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Ruční nasazení pro samostatné clustery

Aplikace Service Fabric, která poskytuje svazky pro vaše kontejnery lze stáhnout z [webu pro stahování Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip). Aplikaci lze nasadit do clusteru prostřednictvím [powershellových,](./service-fabric-deploy-remove-applications.md) [cli](./service-fabric-application-lifecycle-sfctl.md) nebo [infrastrukturových klientských api](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Pomocí příkazového řádku změňte adresář na kořenový adresář staženého balíčku aplikace.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Dále zkopírujte balíček aplikace do úložiště obrázků s příslušnými hodnotami pro [ApplicationPackagePath] a [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Registrace typu přihlášky

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Vytvořte aplikaci a věnujte velkou pozornost hodnotě parametru aplikace **ListenPort.** Tato hodnota je port, na kterém modul plug-in svazku Soubory Azure naslouchá požadavkům od daemon dockeru. Ujistěte se, že port poskytnutý aplikaci odpovídá VolumePluginPorts v ClusterManifest a není v konfliktu s jiným portem, který používá cluster nebo vaše aplikace.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter nepodporuje mapování připojení SMB na kontejnery ([To je podporováno pouze v systému Windows Server verze 1709](/virtualization/windowscontainers/manage-containers/container-storage)). Toto omezení zabraňuje mapování svazků sítě a ovladače svazku Soubory Azure ve verzích starších než 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Nasazení aplikace v místním vývojovém clusteru
Postupujte podle kroků 1-3 z [výše uvedeného.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 Výchozí počet instancí služby pro aplikaci modulů plug-in svazku Azure Files je -1, což znamená, že existuje instance služby nasazené do každého uzlu v clusteru. Však při nasazování aplikace modulů plug-up svazku Souborů Azure v clusteru místního vývoje, počet instancí služby by měl být určen jako 1. To lze provést pomocí parametru aplikace **InstanceCount.** Proto příkaz pro vytvoření aplikace modulu plug-in svazku Souborů Azure v clusteru místního vývoje je:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurace aplikací tak, aby používaly svazek
Následující úryvek ukazuje, jak lze zadat svazek založený na souboru manifestu aplikace vaší aplikace. Specifickým prvkem zájmu je značka **Volume:**

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

Název ovladače pro modul plug-in svazku Souborů Azure je **sfazurefile**. Tato hodnota je nastavena pro atribut **Driver** prvku **tag volume** v manifestu aplikace.

Ve značce **Svazek** ve výstřižku výše vyžaduje modul plug-in svazku Souborů Azure následující atributy:
- **Zdroj** - Toto je název svazku. Uživatel si může vybrat libovolné jméno pro svůj svazek.
- **Cíl** - Tento atribut je umístění, které je namapováno na svazek v rámci spuštěného kontejneru. Cíl tedy nemůže být místem, které již v kontejneru existuje.

Jak je znázorněno v **DriverOption** prvky ve výstřižku výše, modul plug-in svazku Soubory Azure podporuje následující možnosti ovladače:
- **shareName** – název sdílené složky soubory Azure Files, která poskytuje svazek pro kontejner.
- **storageAccountName** – název účtu úložiště Azure, který obsahuje sdílenou složku Soubory Azure.
- **storageAccountKey** – přístupový klíč pro účet úložiště Azure, který obsahuje sdílenou složku Souborů Azure.
- **storageAccountFQDN** - Název domény přidružený k účtu úložiště. Pokud není zadán storageAccountFQDN, bude název domény vytvořen pomocí výchozí přípony(.file.core.windows.net) s storageAccountName.  

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

## <a name="using-your-own-volume-or-logging-driver"></a>Použití vlastního ovladače svazku nebo protokolování
Service Fabric také umožňuje použití vlastní [chod nebo](https://docs.docker.com/engine/extend/plugins_volume/) [ovladače protokolování.](https://docs.docker.com/engine/admin/logging/overview/) Pokud ovladač svazku/protokolování dockeru není v clusteru nainstalován, můžete jej nainstalovat ručně pomocí protokolů RDP/SSH. Instalaci s těmito protokoly můžete provést pomocí [skriptu pro spuštění sady škálování virtuálních strojů](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) nebo [skriptu SetupEntryPoint](/azure/service-fabric/service-fabric-application-model).

Příklad skriptu pro instalaci [ovladače svazku Dockeru pro Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) je následující:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Chcete-li ve svých aplikacích použít nainstalovaný ovladač svazku nebo protokolování, je nutné zadat příslušné hodnoty v elementech **Volume** a **LogConfig** v části **ContainerHostPolicies** v manifestu aplikace.

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

Při zadávání modulu plug-in svazku service fabric automaticky vytvoří svazek pomocí zadaných parametrů. **Zdrojová** značka elementu **Volume** je název svazku a značka **Ovladač** určuje modul plug-in ovladače svazku. **Cílová** značka je umístění, na které je **zdroj** mapován v rámci spuštěného kontejneru. Cíl tedy nemůže být umístění, které již v kontejneru existuje. Možnosti lze zadat pomocí značky **DriverOption** následujícím způsobem:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Parametry aplikace jsou podporovány pro svazky, jak je znázorněno `MyStorageVar` v předchozím fragmentu manifestu (vyhledejte příklad použití).

Pokud je zadán ovladač protokolu Dockeru, je třeba nasadit agenty (nebo kontejnery) pro zpracování protokolů v clusteru. Značku **DriverOption** lze použít k určení možností ovladače protokolu.

## <a name="next-steps"></a>Další kroky
* Chcete-li zobrazit vzorky kontejnerů, včetně ovladače svazku, navštivte [vzorky kontejneru Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Chcete-li nasadit kontejnery do clusteru Service Fabric, přečtěte si článek [Nasazení kontejneru na Service Fabric](service-fabric-deploy-container.md)
