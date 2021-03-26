---
title: Nasazení virtuálních počítačů na zařízení Azure Stack Edge pro prostřednictvím šablon
description: Popisuje postup vytvoření a správy virtuálních počítačů na zařízení Azure Stack Edge pro pomocí šablon.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: cf236396f080af9676f211c42178ddda6a794420
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568337"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Nasazení virtuálních počítačů na zařízení GPU Azure Stack Edge pro prostřednictvím šablon

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

V tomto kurzu se dozvíte, jak vytvořit a spravovat virtuální počítač na zařízení Azure Stack Edge pro pomocí šablon. Tyto šablony jsou soubory JavaScript Object Notation (JSON), které definují infrastrukturu a konfiguraci pro váš virtuální počítač. V těchto šablonách určíte prostředky, které chcete nasadit, a vlastnosti těchto prostředků.

Šablony jsou flexibilní v různých prostředích, protože mohou přijímat parametry jako vstup za běhu ze souboru. Standardní struktura názvů je určena `TemplateName.json` pro šablonu a `TemplateName.parameters.json` soubor parametrů. Další informace o šablonách ARM najdete v [Azure Resource Manager šablonách?](../azure-resource-manager/templates/overview.md).

V tomto kurzu použijeme předem napsané ukázkové šablony pro vytváření prostředků. Nebudete muset upravovat soubor šablony a upravovat jenom `.parameters.json` soubory, abyste mohli nasazení přizpůsobit na svém počítači. 

## <a name="vm-deployment-workflow"></a>Pracovní postup nasazení virtuálních počítačů

Pokud chcete nasadit Azure Stack hraničních počítačů pro celou řadu zařízení, můžete použít jeden virtuální pevný disk nástroje Sysprep pro celý loďstvo, stejnou šablonu pro nasazení a jednoduše udělat drobné změny parametrů v této šabloně pro každé umístění nasazení (tyto změny se můžou provádět ručně, jak je to tady, nebo program). 

Shrnutí vysoké úrovně pracovního postupu nasazení pomocí šablon je následující:

1. **Nakonfigurovat požadavky** – existují tři typy požadavků: zařízení, klient a virtuální počítač.

    1. **Požadavky na zařízení**

        1. Umožňuje připojení k Azure Resource Manager na zařízení.
        2. Povolte výpočetní prostředky prostřednictvím místního uživatelského rozhraní.

    2. **Požadavky klienta**

        1. Stažení šablon virtuálních počítačů a přidružených souborů na klienta.
        1. Volitelně nakonfigurujte TLS 1,2 na klientovi.
        1. [Stáhněte a nainstalujte Průzkumník služby Microsoft Azure Storage](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) do svého klienta.

    3. **Požadavky na virtuální počítač**

        1. Vytvořte v umístění zařízení skupinu prostředků, která bude obsahovat všechny prostředky virtuálních počítačů.
        1. Vytvořte účet úložiště, který odešle virtuální pevný disk použitý k vytvoření image virtuálního počítače.
        1. Přidejte identifikátor URI místního účtu úložiště do souboru DNS nebo hostitelů na klientském počítači, který přistupuje k vašemu zařízení.
        1. Nainstalujte do zařízení certifikát BLOB Storage a v místním klientovi Přihlaste se k vašemu zařízení. Volitelně nainstalujte certifikát BLOB Storage do Průzkumník služby Storage.
        1. Vytvořte a nahrajte virtuální pevný disk do účtu úložiště, který jste vytvořili dříve.

2. **Vytvoření virtuálního počítače ze šablon**

    1. Vytvořte image virtuálního počítače pomocí `CreateImage.parameters.json` souboru parametrů a `CreateImage.json` šablony nasazení.
    1. Vytvořte virtuální počítač s dříve vytvořenými prostředky pomocí `CreateVM.parameters.json` souboru parametrů a  `CreateVM.json` šablony nasazení.

## <a name="device-prerequisites"></a>Požadavky na zařízení

Nakonfigurujte tyto požadavky na zařízení Azure Stack Edge pro.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Požadavky klienta

Nakonfigurujte tyto požadavky na klienta, který se použije pro přístup k zařízení Azure Stack Edge pro.

1. [Stáhněte si Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/) , pokud ho používáte k nahrání VHD. Alternativně můžete stáhnout AzCopy a nahrát VHD. Pokud používáte starší verze AzCopy, možná budete muset nakonfigurovat TLS 1,2 na klientském počítači. 
1. [Stáhněte si šablony virtuálních počítačů a soubory parametrů](https://aka.ms/ase-vm-templates) do klientského počítače. Rozbalte ho do adresáře, který použijete jako pracovní adresář.


## <a name="vm-prerequisites"></a>Požadavky na virtuální počítač

Nakonfigurujte tyto požadavky pro vytvoření prostředků potřebných k vytvoření virtuálního počítače. 

    
### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure, jako je například účet úložiště, disk a spravovaný disk.

> [!IMPORTANT]
> Všechny prostředky se vytvoří ve stejném umístění jako zařízení a umístění je nastavené na **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Ukázkový výstup najdete níž.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Vytvořte nový účet úložiště pomocí skupiny prostředků vytvořené v předchozím kroku. Tento účet je **místní účet úložiště** , který se použije k nahrání image virtuálního disku pro virtuální počítač.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Pomocí Azure Resource Manager lze vytvořit pouze účty místního úložiště, jako je například místně redundantní úložiště (Standard_LRS nebo Premium_LRS). Pokud chcete vytvořit vrstvené účty úložiště, přečtěte si postup v tématu [Přidání, připojení k účtům úložiště na Azure Stack Edge pro](./azure-stack-edge-gpu-deploy-add-storage-accounts.md).

Ukázkový výstup najdete níž.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Pokud chcete získat klíč účtu úložiště, spusťte `Get-AzureRmStorageAccountKey` příkaz. Zde je zobrazen ukázkový výstup tohoto příkazu.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Přidat do souboru hostitelů identifikátor URI objektu BLOB

Ujistěte se, že jste již přidali identifikátor URI objektu blob do souboru hostitelů pro klienta, který používáte pro připojení k úložišti objektů BLOB. **Spusťte Poznámkový blok jako správce** a přidejte následující položku pro identifikátor URI objektu BLOB v `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

V typickém prostředí byste měli nakonfigurovat DNS tak, aby všechny účty úložiště odkazovaly na zařízení Azure Stack Edge pro s `*.blob.devicename.domainname.com` položkou.

### <a name="optional-install-certificates"></a>Volitelné Instalace certifikátů

Pokud se připojíte přes Průzkumník služby Storage pomocí *protokolu HTTP*, tento krok přeskočte. Pokud používáte *protokol HTTPS*, musíte v Průzkumník služby Storage nainstalovat příslušné certifikáty. V takovém případě nainstalujte certifikát koncového bodu objektu BLOB. Další informace najdete v tématu Vytvoření a nahrání certifikátů v tématu [Správa certifikátů](azure-stack-edge-gpu-manage-certificates.md). 

### <a name="create-and-upload-a-vhd"></a>Vytvoření a nahrání virtuálního pevného disku

Ujistěte se, že máte image virtuálního disku, kterou můžete použít k nahrání v pozdějším kroku. Postupujte podle kroků v části [Vytvoření image virtuálního počítače](azure-stack-edge-gpu-create-virtual-machine-image.md). 

Zkopírujte všechny bitové kopie disků, které se mají použít, do objektů blob stránky v místním účtu úložiště, který jste vytvořili v předchozích krocích. Pomocí nástroje, jako je [Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/) nebo AzCopy, můžete [nahrát virtuální pevný disk do účtu úložiště](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) , který jste vytvořili v předchozích krocích. 

### <a name="use-storage-explorer-for-upload"></a>Použití Průzkumník služby Storage k nahrání

1. Otevřete Průzkumníka služby Storage. Přejdete na **Upravit** a ujistěte se, že je aplikace nastavená na **cílové rozhraní Azure Stack API**.

    ![Nastavit cíl na Azure Stack rozhraní API](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Nainstalujte certifikát klienta ve formátu PEM. Pokračujte v **úpravách > certifikáty SSL > import certifikátů**. Najeďte na certifikát klienta.

    ![Importovat certifikát koncového bodu služby Blob Storage](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Pokud používáte certifikáty vygenerované zařízením, Stáhněte a převeďte certifikát koncového bodu služby Blob Storage `.cer` do `.pem` formátu. Spusťte následující příkaz. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Pokud přidáváte vlastní certifikát, použijte kořenový certifikát podpisového řetězce ve `.pem` formátu.

3. Po dokončení importu certifikátu restartujte Průzkumník služby Storage, aby se změny projevily.

    ![Restartovat Průzkumník služby Storage](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. V levém podokně klikněte pravým tlačítkem na **účty úložiště** a vyberte **připojit k Azure Storage**. 

    ![Připojit k Azure Storage 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Vyberte **Use a storage account name and key** (Použití názvu a klíče účtu úložiště). Vyberte **Další**.

    ![Připojit k Azure Storage 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. Do pole **připojit s názvem a klíč** zadejte **zobrazované jméno**, **název účtu úložiště**, Azure Storage **klíč účtu**. Vyberte **jinou** doménu úložiště a pak zadejte `<device name>.<DNS domain>` připojovací řetězec. Pokud jste certifikát nenainstalovali v Průzkumník služby Storage, podívejte se na možnost **použít protokol HTTP** . Vyberte **Další**.

    ![Připojit s názvem a klíčem](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Zkontrolujte **Souhrn připojení** a vyberte **připojit**.

8. Účet úložiště se zobrazí v levém podokně. Vyberte a rozbalte účet úložiště. Vyberte **kontejnery objektů BLOB**, klikněte pravým tlačítkem myši a vyberte **vytvořit kontejner objektů BLOB**. Zadejte název kontejneru objektů BLOB.

9. Vyberte kontejner, který jste právě vytvořili, a v pravém podokně vyberte **nahrát > nahrát soubory**. 

    ![Nahrát soubor VHD 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Přejděte na virtuální pevný disk, který chcete nahrát do **vybraných souborů**. Jako **objekt blob stránky** vyberte **typ objektu BLOB** a vyberte **nahrát**.

    ![Nahrát soubor VHD 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Po načtení VHD do kontejneru objektů BLOB vyberte virtuální pevný disk, klikněte na něj pravým tlačítkem a vyberte **vlastnosti**. 

    ![Nahrát soubor VHD 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Zkopírujte a uložte **identifikátor URI**, který budete používat v pozdějších krocích.

    ![Kopírovat identifikátor URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>Vytvoření image pro virtuální počítač

Pokud chcete vytvořit image pro váš virtuální počítač, upravte `CreateImage.parameters.json` soubor parametrů a pak nasaďte šablonu `CreateImage.json` , která tento soubor parametrů používá.


### <a name="edit-parameters-file"></a>Upravit soubor parametrů

Soubor `CreateImage.parameters.json` má následující parametry: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
    }
```

Upravte soubor `CreateImage.parameters.json` tak, aby obsahoval následující hodnoty pro zařízení Azure Stack Edge pro:

1. Zadejte typ operačního systému odpovídající virtuálnímu pevnému disku, který budete nahrávat. Typ operačního systému může být Windows nebo Linux.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Změňte identifikátor URI image na identifikátor URI obrázku, který jste nahráli v předchozím kroku:

   ```json
   "imageUri": {
       "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
       },
   ```

   Pokud používáte *protokol HTTP* s Průzkumník služby Storage, změňte identifikátor URI na URI *http* .

3. Zadejte jedinečný název obrázku. Tento obrázek slouží k vytvoření virtuálního počítače v pozdějších krocích. 

   Tady je ukázkový kód JSON, který se používá v tomto článku.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Linux"
        },
        "imageName": {
          "value": "myaselinuximg"
        },
        "imageUri": {
          "value": "https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd"
        }
      }
    }
    ```

5. Uložte soubor parametrů.


### <a name="deploy-template"></a>Nasazení šablony 

Nasaďte šablonu `CreateImage.json` . Tato šablona nasadí prostředky imagí, které se použijí k vytvoření virtuálních počítačů v pozdějším kroku.

> [!NOTE]
> Když nasadíte šablonu, pokud dojde k chybě ověřování, vaše přihlašovací údaje Azure pro tuto relaci pravděpodobně vypršely. Opětovným spuštěním `login-AzureRM` příkazu se připojte k Azure Resource Manager na zařízení Azure Stack Edge pro.

1. Spusťte následující příkaz: 
    
    ```powershell
    $templateFile = "Path to CreateImage.json"
    $templateParameterFile = "Path to CreateImage.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```
    Tento příkaz nasadí prostředek bitové kopie. K dotazování prostředku spusťte následující příkaz:

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    Tady je ukázkový výstup úspěšně vytvořeného obrázku.
    
    ```powershell
    PS C:\WINDOWS\system32> login-AzureRMAccount -EnvironmentName aztest -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest
    
   PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateImage\CreateImage.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateImage\CreateImage.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment4"
        
    DeploymentName          : deployment4
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:06:57 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Linux
                              imageName        String                     myaselinuximg
                              imageUri         String
                              https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd
    
    Outputs                 :
    DeploymentDebugLogLevel :    
    PS C:\WINDOWS\system32>
    ```
    
## <a name="create-vm"></a>Vytvoření virtuálního počítače

### <a name="edit-parameters-file-to-create-vm"></a>Upravit soubor parametrů k vytvoření virtuálního počítače
 
K vytvoření virtuálního počítače použijte soubor parametrů `CreateVM.parameters.json`. Přebírá následující parametry.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network, use ASEVNET>"
        },
        "subnetName": {
            "value": "<Name for the subnet, use ASEVNETsubNet>"
        },
        "vnetRG": {
            "value": "<Resource group for Vnet, use ASERG>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Přiřaďte příslušné parametry `CreateVM.parameters.json` pro zařízení Azure Stack Edge pro.

1. Zadejte jedinečný název, název síťového rozhraní a název ipconfig. 
1. Zadejte uživatelské jméno, heslo a podporovanou velikost virtuálního počítače.
1. Když jste povolili síťové rozhraní pro výpočetní prostředky, virtuální přepínač a virtuální síť se v tomto síťovém rozhraní automaticky vytvoří. Můžete zadat dotaz na existující virtuální síť a získat název virtuální sítě, název podsítě a název skupiny prostředků VNET.

    Spusťte následující příkaz:

    ```powershell
    Get-AzureRmVirtualNetwork
    ```
    Zde je ukázkový výstup:
    
    ```powershell
    
    PS C:\WINDOWS\system32> Get-AzureRmVirtualNetwork
    
    Name                   : ASEVNET
    ResourceGroupName      : ASERG
    Location               : dbelocal
    Id                     : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft
                             .Network/virtualNetworks/ASEVNET
    Etag                   : W/"990b306d-18b6-41ea-a456-b275efe21105"
    ResourceGuid           : f8309d81-19e9-42fc-b4ed-d573f00e61ed
    ProvisioningState      : Succeeded
    Tags                   :
    AddressSpace           : {
                               "AddressPrefixes": [
                                 "10.57.48.0/21"
                               ]
                             }
    DhcpOptions            : null
    Subnets                : [
                               {
                                 "Name": "ASEVNETsubNet",
                                 "Etag": "W/\"990b306d-18b6-41ea-a456-b275efe21105\"",
                                 "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/provider
                             s/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                 "AddressPrefix": "10.57.48.0/21",
                                 "IpConfigurations": [],
                                 "ResourceNavigationLinks": [],
                                 "ServiceEndpoints": [],
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
    VirtualNetworkPeerings : []
    EnableDDoSProtection   : false
    EnableVmProtection     : false
    
    PS C:\WINDOWS\system32>
    ```

    Použijte ASEVNET pro název virtuální sítě, ASEVNETsubNet pro název podsítě a ASERG pro název skupiny prostředků VNET.
    
1. Teď budete potřebovat statickou IP adresu, která se přiřadí k virtuálnímu počítači ve výše definované síti podsítě. Nahraďte **PrivateIPAddress** touto adresou v souboru parametrů. Pokud chcete VIRTUÁLNÍmu počítači získat IP adresu z místního serveru DCHP, ponechte tuto `privateIPAddress` hodnotu prázdnou.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Uložte soubor parametrů.

    Tady je ukázkový kód JSON, který se používá v tomto článku.
    
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "vmName": {
              "value": "VM1"
          },
          "adminUsername": {
              "value": "Administrator"
          },
          "Password": {
              "value": "Password1"
          },
        "imageName": {
          "value": "myaselinuximg"
        },
        "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        "vnetName": {
          "value": "ASEVNET"
        },
        "subnetName": {
          "value": "ASEVNETsubNet"
        },
        "vnetRG": {
          "value": "aserg"
        },
        "nicName": {
          "value": "nic5"
        },
        "privateIPAddress": {
          "value": ""
        },
        "IPConfigName": {
          "value": "ipconfig5"
        }
      }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Nasazení šablony k vytvoření virtuálního počítače

Nasaďte šablonu pro vytvoření virtuálního počítače `CreateVM.json` . Tato šablona vytvoří síťové rozhraní z existující virtuální sítě a vytvoří virtuální počítač z nasazené bitové kopie.

1. Spusťte následující příkaz: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    Vytvoření virtuálního počítače bude trvat 15-20 minut. Tady je ukázkový výstup úspěšně vytvořeného virtuálního počítače.
    
    ```powershell
    PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "Deployment6"
       
    DeploymentName          : Deployment6
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:51:28 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              vmName           String                     VM1
                              adminUsername    String                     Administrator
                              password         String                     Password1
                              imageName        String                     myaselinuximg
                              vmSize           String                     Standard_NC4as_T4_v3
                              vnetName         String                     ASEVNET
                              vnetRG           String                     aserg
                              subnetName       String                     ASEVNETsubNet
                              nicName          String                     nic5
                              ipConfigName     String                     ipconfig5
                              privateIPAddress  String
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\WINDOWS\system32
    ```   

    Příkaz lze také spustit `New-AzureRmResourceGroupDeployment` asynchronně s `–AsJob` parametrem. Tady je ukázkový výstup, když je rutina spuštěná na pozadí. Pak můžete zadat dotaz na stav úlohy, která je vytvořena pomocí `Get-Job` rutiny.

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. Ověřte, jestli se virtuální počítač úspěšně zřídil. Spusťte následující příkaz:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Připojení k virtuálnímu počítači

Postup pro připojení se může lišit v závislosti na tom, jestli jste vytvořili virtuální počítač se systémem Windows nebo Linux.

### <a name="connect-to-windows-vm"></a>Připojení k virtuálnímu počítači s Windows

Pomocí těchto kroků se připojte k virtuálnímu počítači s Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Připojení k virtuálnímu počítači se systémem Linux

Pomocí těchto kroků se připojte k virtuálnímu počítači se systémem Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]



## <a name="next-steps"></a>Další kroky

[Rutiny Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)