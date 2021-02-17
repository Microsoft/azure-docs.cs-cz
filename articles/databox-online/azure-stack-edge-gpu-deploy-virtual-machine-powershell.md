---
title: Nasazení virtuálních počítačů na zařízení Azure Stack Edge přes Azure PowerShell
description: Popisuje, jak vytvořit a spravovat virtuální počítače na Azure Stack hraničním zařízení pomocí Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: d4a4a2e6e04f8f6247df663aba033d387e66c437
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546886"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Nasazení virtuálních počítačů na zařízení Azure Stack Edge přes Azure PowerShell

Tento článek popisuje, jak vytvořit a spravovat virtuální počítač na zařízení Azure Stack Edge pomocí Azure PowerShell. Tento článek se týká Azure Stackch PROCESORů Edge pro, Azure Stack Edge pro R a Azure Stack hraničních zařízení R.

## <a name="vm-deployment-workflow"></a>Pracovní postup nasazení virtuálních počítačů

Postup nasazení vypadá následovně:

![Diagram pracovního postupu nasazení virtuálních počítačů](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-built-in-subscription-on-the-device"></a>Dotaz na integrované předplatné na zařízení

Pro Azure Resource Manager se podporuje jenom jedno pevné předplatné, které je uživatelsky viditelné. Toto předplatné je jedinečné pro každé zařízení a název předplatného nebo ID předplatného se nedá změnit.

Toto předplatné obsahuje všechny prostředky, které jsou vytvořené pro vytváření virtuálních počítačů. 

> [!IMPORTANT]
> Toto předplatné se vytvoří, když povolíte virtuální počítače z Azure Portal a místně funguje na vašem zařízení.

Toto předplatné se používá k nasazení virtuálních počítačů.

1.  Pokud chcete zobrazit seznam tohoto předplatného, zadejte:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Zde je ukázkový výstup:

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. Získá seznam registrovaných poskytovatelů prostředků spuštěných v zařízení. Tento seznam obvykle zahrnuje výpočetní výkon, síť a úložiště.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Poskytovatelé prostředků jsou předregistrováni a nelze je upravit ani změnit.
    
    Zde je ukázkový výstup:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure, jako je například účet úložiště, disk a spravovaný disk.

> [!IMPORTANT]
> Všechny prostředky se vytvoří ve stejném umístění jako zařízení a umístění je nastavené na **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Zde je ukázkový výstup:

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Vytvořte nový účet úložiště pomocí skupiny prostředků vytvořené v předchozím kroku. Toto je místní účet úložiště, který slouží k nahrání image virtuálního disku pro virtuální počítač.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Pomocí Azure Resource Manager můžete vytvářet jenom místní účty úložiště, jako je třeba místně redundantní úložiště (Standard nebo Premium). Informace o vytváření vrstvených účtů úložiště najdete v tématu [kurz: přenos dat prostřednictvím účtů úložiště s grafickým procesorem Azure Stack Edge pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Zde je ukázkový výstup:

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Pokud chcete získat klíč účtu úložiště, spusťte `Get-AzureRmStorageAccountKey` příkaz. Zde je ukázkový výstup tohoto příkazu:

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-the-blob-uri-to-the-host-file"></a>Přidání identifikátoru URI objektu blob do souboru hostitele

Do souboru hostitelů jste už přidali identifikátor URI objektu BLOB pro klienta, kterého používáte pro připojení k Azure Blob Storage v části [Úprava souboru hostitele pro překlad názvů koncových bodů](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Tato položka se použila k přidání identifikátoru URI objektu BLOB:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> ..\<dnsdomain\>

## <a name="install-certificates"></a>Instalace certifikátů

Pokud používáte *protokol HTTPS*, musíte na svém zařízení nainstalovat vhodné certifikáty. V takovém případě nainstalujte certifikát koncového bodu objektu BLOB. Další informace najdete v tématu Vytvoření a nahrání certifikátů v části [použití certifikátů pomocí zařízení s grafickým procesorem Azure Stack Edge pro](azure-stack-edge-gpu-manage-certificates.md).

## <a name="upload-a-vhd"></a>Nahrání virtuálního pevného disku

Zkopírujte všechny bitové kopie disků, které se mají použít, do objektů blob stránky v místním účtu úložiště, který jste vytvořili v předchozích krocích. Pomocí nástroje, jako je [AzCopy](../storage/common/storage-use-azcopy-v10.md) , můžete nahrát virtuální pevný disk do účtu úložiště. 

<!--Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge Pro device.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Set `BlobType` to `page` for creating a managed disk out of VHD. Set `BlobType` to `block` when you're writing to tiered storage accounts by using AzCopy.

You can download the disk images from Azure Marketplace. For detailed steps, see [Get the virtual disk image from Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Here's a sample output using AzCopy 7.3. For more information on this command, see [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->
Použijte následující příkazy s AzCopy 10:  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

Tady je příklad výstupu: 

```powershell
$ContainerName = <ContainerName>
$ResourceGroupName = <ResourceGroupName>
$StorageAccountName = <StorageAccountName>
$VHDPath = "Full VHD Path"
$VHDFile = <VHDFileName>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```

## <a name="create-managed-disks-from-the-vhd"></a>Vytvoření spravovaných disků z VHD

Z nahraného virtuálního pevného disku vytvořte spravovaný disk.

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Zde je ukázkový výstup: 

<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Zde je ukázkový výstup. Další informace o této rutině najdete na webu [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Vytvoření image virtuálního počítače z disku spravovaného imagí

Pomocí následujícího příkazu vytvořte image virtuálního počítače ze spravovaného disku. Nahraďte hodnoty v rámci \< \> zvolených názvů.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Zde je ukázkový výstup. Další informace o této rutině najdete na webu [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-vm-with-previously-created-resources"></a>Vytvoření virtuálního počítače s dříve vytvořenými prostředky

Před vytvořením a nasazením virtuálního počítače musíte vytvořit jednu virtuální síť a přidružit ji k virtuálnímu síťovému rozhraní.

> [!IMPORTANT]
> Platí následující pravidla:
> - Můžete vytvořit jenom jednu virtuální síť, a to i přes skupiny prostředků. Virtuální síť musí mít přesně stejný adresní prostor jako logická síť.
> - Virtuální síť může mít jenom jednu podsíť. Podsíť musí mít přesně stejný adresní prostor jako virtuální síť.
> - Když vytváříte kartu virtuálního síťového rozhraní, můžete použít pouze metodu statického přidělení. Uživatel musí zadat privátní IP adresu.

### <a name="query-the-automatically-created-virtual-network"></a>Dotaz na automaticky vytvořenou virtuální síť

Pokud povolíte výpočetní prostředky z místního uživatelského rozhraní vašeho zařízení, `ASEVNET` ve skupině prostředků se automaticky vytvoří virtuální síť s názvem `ASERG` . K dotazování existující virtuální sítě použijte následující příkaz:

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

### <a name="create-a-virtual-network-interface-card"></a>Vytvoření virtuální síťové karty

Tady je příkaz k vytvoření virtuální síťové karty pomocí ID podsítě virtuální sítě:

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

Tady je ukázkový výstup těchto příkazů:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Při vytváření karty virtuálního síťového rozhraní pro virtuální počítač můžete volitelně předat veřejnou IP adresu. V této instanci veřejná IP adresa vrátí privátní IP adresu. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Image virtuálního počítače teď můžete použít k vytvoření virtuálního počítače a připojit ho k virtuální síti, kterou jste vytvořili dříve.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>Připojení k virtuálnímu počítači

Postup pro připojení se může lišit v závislosti na tom, jestli jste vytvořili virtuální počítač se systémem Windows nebo Linux.

### <a name="connect-to-linux-vm"></a>Připojení k virtuálnímu počítači se systémem Linux

Pomocí těchto kroků se připojte k virtuálnímu počítači se systémem Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Připojení k virtuálnímu počítači s Windows

Pomocí těchto kroků se připojte k virtuálnímu počítači s Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM by using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When you're prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command:

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this case is the same as the private IP that you passed during the virtual network interface creation.-->


## <a name="manage-the-vm"></a>Správa virtuálního počítače

Následující části popisují některé běžné operace, které můžete vytvořit na zařízení Azure Stack Edge pro.

### <a name="list-vms-running-on-the-device"></a>Výpis virtuálních počítačů spuštěných v zařízení

Pokud chcete vrátit seznam všech virtuálních počítačů, které běží na vašem zařízení Azure Stack Edge, spusťte tento příkaz:


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>Zapnout virtuální počítač

Spuštěním následující rutiny zapněte virtuální počítač, který běží na vašem zařízení:


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`

Další informace o této rutině získáte v nabídce [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="suspend-or-shut-down-the-vm"></a>Pozastavit nebo vypnout virtuální počítač

Spuštěním následující rutiny zastavte nebo vypněte virtuální počítač, který běží na vašem zařízení:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

Další informace o této rutině najdete na webu [rutiny Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true).

### <a name="add-a-data-disk"></a>Přidat datový disk

Pokud se požadavky na váš virtuální počítač zvyšují, možná budete muset přidat datový disk.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Spuštěním následující rutiny odeberte virtuální počítač z vašeho zařízení:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Další informace o této rutině najdete v [rutině Remove-AzureRmVm](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true).

## <a name="next-steps"></a>Další kroky

[Rutiny Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
