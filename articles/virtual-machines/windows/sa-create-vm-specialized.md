---
title: Vytvoření virtuálního počítače z disku specializované v Azure | Microsoft Docs
description: Vytvoření nového virtuálního počítače připojením specializované nespravované disku, v modelu nasazení Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: ffa36967eb987f5e1b66f007ae60a63e640a609a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Vytvoření virtuálního počítače z specializované virtuálního pevného disku v účtu úložiště

Vytvoření nového virtuálního počítače připojením specializované nespravované disk jako disk operačního systému pomocí prostředí Powershell. Specializované disk je kopie virtuálního pevného disku z existující virtuální počítač, který uchovává uživatelské účty, aplikace a další data o stavu z vašeho původního virtuálního počítače. 

Máte dvě možnosti:
* [Nahrání virtuálního pevného disku](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Zkopírujte virtuální pevný disk existující virtuální počítač Azure](sa-create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>Než začnete
Pokud používáte prostředí PowerShell, ujistěte se, že máte nejnovější verzi modulu prostředí AzureRM.Compute PowerShell. Spusťte následující příkaz k její instalaci.

```powershell
Install-Module AzureRM.Compute 
```
Další informace najdete v tématu [Azure PowerShell verze](/powershell/azure/overview).


## <a name="option-1-upload-a-specialized-vhd"></a>Možnost 1: Nahrát specializované virtuálního pevného disku

Můžete nahrát virtuální pevný disk z virtuálního počítače specializované vytvořené pomocí místní virtualizace nástroje, jako je technologie Hyper-V nebo na virtuální počítač exportovat z jiného cloudu.

### <a name="prepare-the-vm"></a>Příprava virtuálního počítače
Můžete nahrát specializované VHD, který byl vytvořen pomocí místní počítač nebo virtuální pevný disk exportovaný z jiného cloudu. Specializované virtuálního pevného disku uchovává uživatelské účty, aplikace a další data o stavu z vašeho původního virtuálního počítače. Pokud máte v úmyslu použít virtuální pevný disk jako-je chcete vytvořit nový virtuální počítač, zkontrolujte následující kroky. 
  
  * [Příprava virtuálního pevného disku Windows nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nechcete** generalize virtuální počítač pomocí nástroje Sysprep.
  * Odeberte všechny hosta virtualizačních nástrojů a agentů, které jsou nainstalovány do virtuálního počítače (tj. nástroje VMware).
  * Zajistěte, aby že virtuální počítač nakonfigurovaný tak, aby jeho IP adresu a nastavení DNS pomocí protokolu DHCP pro vyžádání obsahu. To zajistí, že server získá IP adresu v rámci virtuální sítě, při spuštění. 


### <a name="get-the-storage-account"></a>Získat účet úložiště
Potřebujete účet úložiště v Azure k ukládání nahrané image virtuálního počítače. Můžete použít existující účet úložiště, nebo vytvořte novou. 

Chcete-li zobrazit účty úložiště k dispozici, zadejte:

```powershell
Get-AzureRmStorageAccount
```

Pokud chcete použít existující účet úložiště, pokračujte [nahrajte image virtuálního počítače](#upload-the-vm-vhd-to-your-storage-account) části.

Pokud potřebujete vytvořit účet úložiště, postupujte takto:

1. Je třeba na název skupiny prostředků, kde by měl být vytvořen účet úložiště. Chcete-li zjistit všechny skupiny prostředků, které jsou v rámci vašeho předplatného, zadejte:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Chcete-li vytvořit skupinu prostředků s názvem **myResourceGroup** v **západní USA** oblast, zadejte:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Vytvořit účet úložiště s názvem **můj_účet_úložiště** v této skupině prostředků s použitím [AzureRmStorageAccount nový](/powershell/module/azurerm.storage/new-azurermstorageaccount) rutiny:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště
Použití [přidat AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) rutiny Odeslat bitovou kopii do kontejneru v účtu úložiště. Tento příklad nahrávání souboru **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` na účet úložiště s názvem **můj_účet_úložiště** v **myResourceGroup** skupinu prostředků. Soubor se umístí do kontejner s názvem **můj_kontejner** a nový název souboru bude **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Pokud bylo úspěšné, můžete získat odpovědi, která vypadá podobně jako tento:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

V závislosti na připojení k síti a velikost souboru virtuálního pevného disku tento příkaz může trvat nějakou dobu pro dokončení.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Možnost 2: Zkopírujte virtuální pevný disk z existujícího virtuálního počítače Azure

Virtuální pevný disk můžete zkopírovat na jiný účet úložiště pro použití při vytvoření nového virtuálního počítače duplicitní.

### <a name="before-you-begin"></a>Než začnete
Ujistěte se, že jste:

* Neobsahuje informace o **zdrojové a cílové účty úložiště**. Pro zdrojový virtuální počítač je potřeba mít názvy účtů a kontejner úložiště. Obvykle bude název kontejneru **virtuální pevné disky**. Také musíte mít cílový účet úložiště. Pokud jste již nemáte, můžete vytvořit pomocí buď na portálu (**všechny služby** > účty úložiště > Přidat) nebo pomocí [AzureRmStorageAccount nový](/powershell/module/azurerm.storage/new-azurermstorageaccount) rutiny. 
* Stáhli a nainstalovali [nástroj AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Zrušit přidělení virtuálního počítače
Zrušit přidělení virtuálního počítače, což uvolní virtuální pevný disk, který se má zkopírovat. 

* **Portál**: klikněte na tlačítko **virtuální počítače** > **Můjvp** > Zastavit
* **Prostředí PowerShell**: použití [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) zastavit (zrušit přidělení) virtuálního počítače s názvem **Můjvp** ve skupině prostředků **myResourceGroup**.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

**Stav** pro virtuální počítač ve službě Azure portal změní z **Zastaveno** k **zastaveném (nepřiřazeném)**.

### <a name="get-the-storage-account-urls"></a>Získání adres URL účtu úložiště
Je třeba účty úložiště zdrojové a cílové adresy URL. Jako adresy URL vzhledu: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Pokud znáte název účtu a kontejneru úložiště, můžete nahradit pouze informace k vytvoření adresu URL do závorek. 

Portál Azure nebo Azure Powershell můžete použít k získání adresy URL:

* **Portál**: klikněte **>** pro **všechny služby** > **účty úložiště** > *úložiště účet* > **objekty BLOB** a váš zdrojový soubor virtuálního pevného disku je pravděpodobně v **virtuální pevné disky** kontejneru. Klikněte na tlačítko **vlastnosti** kontejneru a zkopírujte text s názvem bez přípony **URL**. Budete potřebovat adresy URL zdrojového a cílového kontejnery. 
* **Prostředí PowerShell**: použití [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) získání informací o pro virtuální počítač s názvem **Můjvp** ve skupině prostředků **myResourceGroup**. Ve výsledcích vyhledejte v **profilu úložiště** část **Uri virtuálního pevného disku**. První část identifikátoru Uri je adresa URL ke kontejneru a poslední část je název virtuálního pevného disku operačního systému pro virtuální počítač.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Získat přístupové klíče k úložišti
Najděte přístupové klíče pro zdrojové a cílové účty úložiště. Další informace o přístupových klíčů najdete v tématu [účty Azure storage](../../storage/common/storage-create-storage-account.md).

* **Portál**: klikněte na tlačítko **všechny služby** > **účty úložiště** > *účet úložiště*  >   **Přístupové klíče**. Zkopírujte klíč označený jako **key1**.
* **Prostředí PowerShell**: použití [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) k získání klíče úložiště pro účet úložiště **můj_účet_úložiště** ve skupině prostředků **myResourceGroup**. Zkopírujte klíč s názvem bez přípony **key1**.

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Zkopírujte virtuální pevný disk
Můžete zkopírovat soubory mezi účty úložiště pomocí nástroje AzCopy. Pro cílový kontejner Pokud zadaný kontejner neexistuje, bude vytvořena pro vás. 

Pokud chcete použít AzCopy, otevřete příkazový řádek na místním počítači a přejděte do složky, kde je nainstalován nástroj AzCopy. Je podobná *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Zkopírujte všechny soubory v rámci kontejneru, můžete použít **/S** přepínače. Tímto lze kopírovat virtuální pevný disk operačního systému a všech datových disků, pokud jsou ve stejném kontejneru. Tento příklad ukazuje, jak zkopírovat všechny soubory v kontejneru **mysourcecontainer** v účtu úložiště **mysourcestorageaccount** ke kontejneru **mydestinationcontainer**v **mydestinationstorageaccount** účet úložiště. Nahraďte názvy účtů úložiště a kontejnery vlastními. Nahraďte `<sourceStorageAccountKey1>` a `<destinationStorageAccountKey1>` s vlastními klíči.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Pokud chcete zkopírovat konkrétní virtuální pevný disk v kontejneru s více soubory, můžete také zadat název souboru pomocí přepínače /Pattern. V tomto příkladu pouze soubor s názvem **myFileName.vhd** budou zkopírovány.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Po dokončení, zobrazí se zpráva, která vypadá podobně jako:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Řešení potíží
* Při použití nástroje AZCopy, pokud se zobrazí chyba "Serveru se nepodařilo ověřit žádost", zajistěte, aby hodnotu hlavičky autorizace je vytvořen. správně včetně podpis. Pokud používáte 2 klíč nebo klíč sekundární úložiště, zkuste použít primární nebo 1. úložiště klíč.

## <a name="create-the-new-vm"></a>Vytvoření nového virtuálního počítače 

Budete muset vytvořit sítě a další prostředky virtuálních počítačů, které má být používána nového virtuálního počítače.

### <a name="create-the-subnet-and-vnet"></a>Vytvoření podsítě a sítě vNet

Vytvořit virtuální síť a podsíť [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

1. Vytvořte podsíť. Tento příklad vytvoří podsíť s názvem **mySubNet**, ve skupině prostředků **myResourceGroup**a nastaví předpona adresy podsítě **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Vytvořte síť vNet. Tento příklad nastaví název virtuální sítě, který se má **myVnetName**, umístění, do **západní USA**a předponu adresy virtuální sítě, abyste **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvořte skupinu zabezpečení sítě a pravidlo protokolu RDP
Abyste mohli přihlásit k virtuálnímu počítači pomocí protokolu RDP, musíte mít pravidlo zabezpečení, která umožňuje přístup RDP na portu 3389. Protože byl vytvořen virtuální pevný disk pro nový virtuální počítač ze stávajícího specializované virtuálního počítače, po vytvoření virtuálního počítače je můžete použít existující účet ze zdrojového virtuálního počítače, který měl oprávnění k přihlášení pomocí protokolu RDP.
To je potřeba dokončit před vytvořením síťového rozhraní, které bude přidružen.  
Tento příklad nastaví název skupiny NSG na **myNsg** a název pravidla protokolu RDP na **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Další informace o koncových bodů a pravidla NSG najdete v tématu [otevřít porty pro virtuální počítač v Azure pomocí prostředí PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Vytvoření veřejné IP adresy a síťové karty
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvoření veřejné IP adresy. V tomto příkladu je název veřejné IP adresy nastavena **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Vytvořit síťovou kartu. V tomto příkladu je síťový adaptér je nastavit název **myNicName**. Tento krok také přidruží skupina zabezpečení sítě dříve vytvořili pomocí tento síťový adaptér.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Nastavte název virtuálního počítače a velikosti

Tento příklad nastaví název virtuálního počítače na "Můjvp" a "Standard_A2" na velikost virtuálního počítače.
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Přidání síťového adaptéru
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurace disku operačního systému

1. Nastavte identifikátor URI pro disk VHD, který jste nahráli nebo zkopírovali. V tomto příkladu soubor virtuálního pevného disku s názvem **myOsDisk.vhd** je uložen v účtu úložiště s názvem **Můj_účet_úložiště** v kontejneru nazvaném **Můj_kontejner**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Přidejte disk operačního systému. V tomto příkladu když je vytvořen disk operačního systému, je termín "osDisk" appened na název virtuálního počítače. Chcete-li vytvořit název disku operačního systému. Tento příklad také určuje, že tento virtuální pevný disk systému Windows by měl být připojen virtuální počítač jako disk operačního systému.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Volitelné: Pokud máte datových disků, které musí být připojené k virtuálnímu počítači, přidejte datových disků pomocí adresy URL dat virtuálních pevných disků a odpovídající logické jednotky (LUN).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Pokud používáte účet úložiště, data a adresy URL disku operačního systému vypadat přibližně takto: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. To můžete najít na portálu procházení, aby cílový kontejner úložiště, klepnutím na operačního systému nebo data virtuálního pevného disku, který jste zkopírovali, a pak kopírování obsah adresy URL.


### <a name="complete-the-vm"></a>Dokončení virtuálního počítače 

Vytvoření virtuálního počítače pomocí konfigurace, které jsme právě vytvořili.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Pokud tento příkaz byl úspěšný, zobrazí se výstup takto:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Zkontrolujte, zda byl vytvořen virtuální počítač
Měli byste vidět nově vytvořený virtuální počítač buď v [portál Azure](https://portal.azure.com)v části **všechny služby** > **virtuální počítače**, nebo pomocí následujících prostředí PowerShell příkazy:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Další postup
K přihlášení do nového virtuálního počítače, přejděte do virtuálního počítače v [portál](https://portal.azure.com), klikněte na tlačítko **Connect**a otevřete soubor Remote Desktop RDP. Pomocí přihlašovacích údajů účtu původní virtuálního počítače pro přihlášení do nového virtuálního počítače. Další informace najdete v tématu [jak se připojit a přihlásit se na virtuálním počítači Azure s Windows](connect-logon.md).

