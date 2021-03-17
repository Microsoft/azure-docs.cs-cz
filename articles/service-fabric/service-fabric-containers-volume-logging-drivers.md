---
title: Ovladač svazku souborů Azure pro Service Fabric
description: Service Fabric podporuje použití souborů Azure k zálohování svazků z vašeho kontejneru.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: a5125dbd88a2fe236196c427244f1311d9b73b9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247689"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Ovladač svazku souborů Azure pro Service Fabric

Ovladač svazku souborů Azure je [modul plug-in Docker Volume](https://docs.docker.com/engine/extend/plugins_volume/) , který poskytuje svazky na bázi [Azure Files](../storage/files/storage-files-introduction.md) pro kontejnery Docker. Je zabalen jako aplikace Service Fabric, která se dá nasadit do clusteru Service Fabric, aby poskytovala svazky pro jiné Service Fabric aplikace kontejneru v clusteru.

> [!NOTE]
> Pro obecnou dostupnost byla vydaná verze 6.5.661.9590 modulu plug-in svazku souborů Azure.
>

## <a name="prerequisites"></a>Požadavky
* Verze Windows pro modul plug-in Volume Files ve Windows funguje v [systému Windows Server verze 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [windows 10 verze 1709](/windows/whats-new/whats-new-windows-10-version-1709) nebo novější operační systémy.

* Verze Linux modulu plug-in Volume Files pro Linux funguje na všech verzích operačních systémů podporovaných nástrojem Service Fabric.

* Modul plug-in svazku soubory Azure funguje jenom na Service Fabric verze 6,2 a novější.

* Postupujte podle pokynů v [dokumentaci k souborům Azure](../storage/files/storage-how-to-create-file-share.md) a vytvořte sdílenou složku pro Service Fabric aplikaci typu kontejner, kterou chcete použít jako svazek.

* Budete potřebovat [PowerShell s nainstalovaným modulem Service Fabric](./service-fabric-get-started.md) nebo [SFCTL](./service-fabric-cli.md) .

* Pokud používáte kontejnery Hyper-V, je třeba přidat následující fragmenty kódu do manifestem clusteru (místní cluster) nebo fabricSettings v šabloně Azure Resource Manager (cluster Azure) nebo ClusterConfig.jsv (samostatném clusteru).

V manifestem clusteru je nutné přidat následující v části hosting. V tomto příkladu je název svazku **sfazurefile** a port, na který naslouchá na clusteru, je **19100**. Nahraďte je správnými hodnotami pro váš cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

V části fabricSettings v šabloně Azure Resource Manager (pro nasazení Azure) nebo ClusterConfig.jsv (pro samostatná nasazení) je potřeba přidat následující fragment kódu. Znovu nahraďte hodnoty název svazku a port vlastními.

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

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Nasazení ukázkové aplikace pomocí Service Fabric ovladače svazku souborů Azure

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Použití Azure Resource Manager prostřednictvím poskytnutého skriptu PowerShellu (doporučeno)

Pokud je váš cluster založený na Azure, doporučujeme do něj nasadit aplikace pomocí modelu prostředku Azure Resource Manager aplikace, aby se usnadnilo použití, a mohl by se přecházet k modelu údržby infrastruktury jako kódu. Tento přístup eliminuje nutnost sledovat verzi aplikace pro ovladač svazku souborů Azure. Umožňuje také udržovat samostatné šablony Azure Resource Manager pro každý podporovaný operační systém. Skript předpokládá, že nasazujete nejnovější verzi aplikace soubory Azure a použijete parametry pro typ operačního systému, ID předplatného clusteru a skupinu prostředků. Skript si můžete stáhnout z [webu Service Fabric stáhnout](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Všimněte si, že tato funkce automaticky nastaví adresu ListenPort, což je port, na kterém modul plug-in svazku souborů Azure vystavuje požadavky z démona Docker na 19100. Můžete ji změnit přidáním parametru s názvem listenPort. Ujistěte se, že port není v konfliktu s žádným jiným portem, který používá cluster nebo aplikace.
 

Příkaz nasazení Azure Resource Manager pro Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Příkaz nasazení Azure Resource Manager pro Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Po úspěšném spuštění skriptu můžete přejít na [oddíl konfigurace aplikace.](#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Ruční nasazení pro samostatné clustery

Aplikace Service Fabric, která poskytuje svazky pro vaše kontejnery, se dá stáhnout z [webu Service Fabric pro stažení](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip). Aplikaci můžete nasadit do clusteru prostřednictvím [rozhraní API](./service-fabric-deploy-remove-applications-fabricclient.md) [PowerShellu](./service-fabric-deploy-remove-applications.md), [CLI](./service-fabric-application-lifecycle-sfctl.md) nebo FabricClient.

1. Pomocí příkazového řádku změňte adresář do kořenového adresáře staženého balíčku aplikace.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Dále zkopírujte balíček aplikace do úložiště imagí s příslušnými hodnotami pro [ApplicationPackagePath] a [ImageStoreConnectionString]:

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

4. Vytvořte aplikaci a Zaměřte se na hodnotu parametru aplikace **ListenPort** . Tato hodnota je port, na kterém modul plug-in svazku souborů Azure naslouchá žádostem z démona Docker. Ujistěte se, že port poskytnutý aplikaci odpovídá VolumePluginPorts v manifestem clusteru a není v konfliktu s žádným jiným portem, který používá cluster nebo aplikace.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter nepodporuje mapování přípojných připojení SMB na kontejnery ([které jsou podporované jenom ve Windows serveru verze 1709](/virtualization/windowscontainers/manage-containers/container-storage)). Toto omezení zabrání mapování svazků sítě a ovladačů svazků služby soubory Azure ve verzích starších než 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Nasazení aplikace do místního vývojového clusteru
Postupujte podle kroků 1-3 [uvedených výše.](#manual-deployment-for-standalone-clusters)

 Výchozí počet instancí služby pro aplikaci modulu plug-in svazku souborů Azure je-1, což znamená, že je v clusteru nasazena instance služby. Při nasazování aplikace modulu plug-in svazku soubory Azure do místního vývojového clusteru se ale musí zadat počet instancí služby jako 1. To lze provést prostřednictvím parametru aplikace **InstanceCount** . Proto příkaz pro vytvoření aplikace modulu plug-in svazku souborů Azure v místním vývojovém clusteru je:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurace aplikací pro použití svazku
Následující fragment kódu ukazuje, jak lze zadat svazek založený na souborech Azure v souboru manifestu aplikace vaší aplikace. Konkrétním prvkem zájmu je značka **svazku** :

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

Název ovladače pro modul plug-in svazku souborů Azure se **sfazurefile**. Tato hodnota je nastavena pro atribut **ovladače** elementu **Volume** tag v manifestu aplikace.

V příznaku **svazku** ve fragmentu výše vyžaduje modul plug-in svazku soubory Azure následující atributy:
- **Source** – jedná se o název svazku. Uživatel může pro svůj svazek vybrat libovolný název.
- **Cíl** – tento atribut je umístění, na které je svazek mapován v rámci běžícího kontejneru. Proto vaše cílové umístění nemůže být umístěním, které již existuje ve vašem kontejneru.

Jak je znázorněno v **DriverOption** prvcích ve fragmentu, modul plug-in svazku souborů Azure podporuje následující možnosti ovladače:
- **název_sdílené_položky** – název sdílené složky souborů Azure, která poskytuje svazek pro kontejner.
- **storageAccountName** – název účtu úložiště Azure, který obsahuje sdílenou složku souborů Azure.
- **storageAccountKey** – přístupový klíč pro účet služby Azure Storage, který obsahuje sdílenou složku souborů Azure File.
- **storageAccountFQDN** – název domény přidružený k účtu úložiště. Pokud není zadán parametr storageAccountFQDN, bude název domény vytvořen pomocí výchozí přípony (. File. Core. Windows. NET) s parametrem storageAccountName.  

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

## <a name="using-your-own-volume-or-logging-driver"></a>Používání vlastního ovladače svazku nebo protokolování
Service Fabric také umožňuje použití vlastních ovladačů [svazků](https://docs.docker.com/engine/extend/plugins_volume/) nebo [protokolování](https://docs.docker.com/engine/admin/logging/overview/) . Pokud ovladač svazků nebo protokolování Docker není v clusteru nainstalovaný, můžete ho ručně nainstalovat pomocí protokolů RDP/SSH. Pomocí těchto protokolů můžete provést instalaci prostřednictvím [spouštěcího skriptu sady škálování virtuálního počítače](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) nebo [skriptu SetupEntryPoint](./service-fabric-application-model.md).

Příkladem skriptu pro instalaci [ovladače svazku Docker pro Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) je následující:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Chcete-li v aplikacích použít svazek nebo ovladač protokolování, který jste nainstalovali, je nutné zadat příslušné hodnoty v části **Volume** a **LogConfig** elementy v manifestu aplikace **ContainerHostPolicies** .

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

Při zadávání modulu plug-in svazku Service Fabric automaticky vytvoří svazek pomocí zadaných parametrů. **Zdrojovou** značkou prvku **svazek** je název svazku a značka **ovladače** určuje modul plug-in ovladače svazku. **Cílová** značka je umístění, na které je **zdroj** namapován v rámci běžícího kontejneru. Proto vaše cílové umístění nemůže být umístěním, které již existuje ve vašem kontejneru. Možnosti lze zadat pomocí značky **DriverOption** následujícím způsobem:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Parametry aplikace jsou podporovány pro svazky, jak je znázorněno v předchozím fragmentu manifestu (hledejte `MyStorageVar` pro příklad použití).

Pokud je zadán ovladač protokolu Docker, je nutné nasadit agenty (nebo kontejnery) pro zpracování protokolů v clusteru. Pomocí značky **DriverOption** lze zadat možnosti pro ovladač protokolu.

## <a name="next-steps"></a>Další kroky
* Pokud chcete zobrazit ukázky kontejnerů včetně ovladače svazku, navštivte prosím [ukázky Service Fabric kontejneru](https://github.com/Azure-Samples/service-fabric-containers) .
* Chcete-li nasadit kontejnery do clusteru Service Fabric, přečtěte si článek [nasazení kontejneru v Service Fabric](./service-fabric-get-started-containers.md)
