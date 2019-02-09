---
title: Vytvoření virtuálního počítače ze specializovaného disku v Azure | Dokumentace Microsoftu
description: Vytvoření nového virtuálního počítače připojením specializované nespravovaný disk v modelu nasazení Resource Manager.
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
ms.openlocfilehash: 6a54dc6a1068a9f7908760fb70fea45ef34f5b60
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981430"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Vytvoření virtuálního počítače ze specializovaného VHD v účtu úložiště

Vytvoření nového virtuálního počítače připojením specializované nespravovaný disk jako disk s operačním systémem pomocí Powershellu. Specializovaného disku je kopie virtuální pevný disk z existujícího virtuálního počítače, který uchovává uživatelské účty, aplikace a další data o stavu z původního virtuálního počítače. 

Máte dvě možnosti:
* [Nahrání virtuálního pevného disku](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Zkopírujte virtuální pevný disk z existujícího virtuálního počítače Azure](sa-create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]


## <a name="option-1-upload-a-specialized-vhd"></a>Option 1: Nahrání specializovaného virtuálního pevného disku

Můžete nahrát virtuální pevný disk ze specializovaného virtuálního počítače vytvoří místní virtualizace nástroj, jako je Hyper-V nebo virtuální počítač exportovat z jiného cloudu.

### <a name="prepare-the-vm"></a>Příprava virtuálního počítače
Můžete nahrát specializovaného virtuálního pevného disku, který byl vytvořen pomocí virtuálního počítače s místní nebo virtuální pevný disk vyexportovali z jiného cloudu. Specializovaného virtuálního pevného disku uchovává uživatelské účty, aplikace a další data o stavu z původního virtuálního počítače. Pokud máte v úmyslu použít virtuální pevný disk jako – k vytvoření nového virtuálního počítače, ověřte následující kroky. 
  
  * [Příprava virtuálního pevného disku Windows k nahrání do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne** generalizace virtuálního počítače pomocí nástroje Sysprep.
  * Odeberte všechny hostované virtualizačních nástrojů a agenty, kteří jsou nainstalováni na virtuálním počítači (tj. nástroje VMware).
  * Zajistěte, aby že virtuální počítač je nakonfigurovaný na vyžádání jeho IP adresu a nastavení DNS prostřednictvím protokolu DHCP. Tím se zajistí, že server při spuštění získá IP adresu v rámci virtuální sítě. 


### <a name="get-the-storage-account"></a>Získat účet úložiště.
Budete potřebovat účet úložiště v Azure k uložení této odeslané image virtuálního počítače. Můžete použít existující účet úložiště nebo vytvořte novou. 

Pokud chcete zobrazit účty úložiště k dispozici, zadejte:

```powershell
Get-AzStorageAccount
```

Pokud chcete použít existující účet úložiště, pokračujte k nahrávání oddíl image virtuálního počítače.

Pokud potřebujete vytvořit účet úložiště, postupujte podle těchto kroků:

1. Budete potřebovat název skupiny prostředků, ve kterém by měl vytvořit účet úložiště. Pokud chcete zjistit všechny skupiny prostředků, které jsou ve vašem předplatném, zadejte:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Chcete-li vytvořit skupinu prostředků s názvem **myResourceGroup** v **USA – západ** oblast, typ:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Vytvoření účtu úložiště s názvem **mystorageaccount** v této skupině prostředků s použitím [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) rutiny:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště
Použití [přidat AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) rutiny pro nahrání obrázku do kontejneru v účtu úložiště. Tento příklad nahraje soubor **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` na účet úložiště s názvem **mystorageaccount** v **myResourceGroup** skupinu prostředků. Soubor se umístí do kontejneru s názvem **mycontainer** a nový název souboru bude **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


V případě úspěchu se získat odpověď bude vypadat nějak takto:

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

V závislosti na připojení k síti a velikost souboru virtuálního pevného disku tohoto příkazu může trvat dobu.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Option 2: Zkopírujte virtuální pevný disk z existujícího virtuálního počítače Azure

Virtuální pevný disk můžete zkopírovat do jiného účtu úložiště má použít při vytváření nového virtuálního počítače duplicitní.

### <a name="before-you-begin"></a>Před zahájením
Ujistěte se, že jste:

* Mít informace o **zdrojové a cílové účty úložiště**. Pro zdrojový virtuální počítač musíte mít názvy účet a kontejner úložiště. Obvykle bude mít název kontejneru **virtuální pevné disky**. Také musíte mít cílový účet úložiště. Pokud již nemáte, můžete vytvořit pomocí buď na portálu (**všechny služby** > účty úložiště > Přidat) nebo pomocí [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) rutiny. 
* Stáhli a nainstalovali [nástroj AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Uvolněte virtuální počítač
Uvolněte virtuální počítač, což uvolní virtuálního pevného disku, které se mají zkopírovat. 

* **Portál**: Klikněte na tlačítko **virtuálních počítačů** > **myVM** > Zastavit
* **Powershell**: Použití [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) zastavení (uvolnění) virtuálního počítače s názvem **myVM** ve skupině prostředků **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

**Stav** pro virtuální počítač v Azure portal se změní z **Zastaveno** k **zastaveno (přidělení zrušeno)**.

### <a name="get-the-storage-account-urls"></a>Získání adresy URL účtu úložiště
Je třeba adresy URL zdrojových a cílových účtů úložiště. Adresy URL vypadat: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Pokud již znáte název účtu a kontejneru úložiště, můžete nahradit jenom informace mezi hranaté závorky pro vytvoření vaší adresy URL. 

Na webu Azure portal nebo Azure Powershell můžete použít k získání adresy URL:

* **Portál**: Klikněte na tlačítko **>** pro **všechny služby** > **účty úložiště** > *účtu úložiště*  >  **Objekty BLOB** a je pravděpodobně ve zdrojovém souboru virtuálního pevného disku **virtuální pevné disky** kontejneru. Klikněte na tlačítko **vlastnosti** kontejneru a zkopírujte text, označený **URL**. Budete potřebovat adresy URL zdrojového a cílového kontejnerů. 
* **Powershell**: Použití [rutiny Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) získat informace pro virtuální počítač s názvem **myVM** ve skupině prostředků **myResourceGroup**. Ve výsledcích, podívejte se **profil úložiště** části **Uri virtuálního pevného disku**. První část identifikátoru Uri je adresa URL ke kontejneru a poslední částí je název virtuálního pevného disku pro virtuální počítač.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Získání přístupových klíčů úložiště
Najdete přístupové klíče pro zdrojové a cílové účty úložiště. Další informace o přístupových klíčů najdete v tématu [účty Azure storage](../../storage/common/storage-create-storage-account.md).

* **Portál**: Klikněte na tlačítko **všechny služby** > **účty úložiště** > *účtu úložiště* > **přístupové klíče**. Zkopírujte klíč označený jako **key1**.
* **Powershell**: Použití [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) získat klíč úložiště pro účet úložiště **mystorageaccount** ve skupině prostředků **myResourceGroup**. Zkopírujte klíč s názvem **key1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Zkopírujte virtuální pevný disk
Můžete kopírovat soubory mezi účty úložiště pomocí AzCopy. K cílovému kontejneru Pokud zadaný kontejner neexistuje, vytvoří se pro vás. 

Použití nástroje AzCopy, otevřete příkazový řádek na místním počítači a přejděte do složky, ve kterém je nástroj AzCopy nainstalovaný. Budou se podobat *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Zkopírujte všechny soubory v rámci kontejneru, můžete použít **/S** přepnout. To je možné zkopírovat virtuální pevný disk operačního systému a všechny datové disky v případě, že jsou ve stejném kontejneru. Tento příklad ukazuje, jak zkopírovat všechny soubory v kontejneru **mysourcecontainer** v účtu úložiště **mysourcestorageaccount** do kontejneru **mydestinationcontainer**v **mydestinationstorageaccount** účtu úložiště. Názvy účtů úložiště a kontejnerů nahraďte vlastními. Nahraďte `<sourceStorageAccountKey1>` a `<destinationStorageAccountKey1>` s vlastními klíči.

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


Až se dokončí, zobrazí zprávu, která vypadá přibližně takto:

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
* Při použití nástroje AZCopy, pokud se zobrazí chyba "Server se nepodařilo ověřit žádost", ujistěte se, že hodnota hlavičky autorizace je vytvořen správně včetně podpis. Pokud používáte 2 klíč nebo klíč sekundární úložiště, zkuste použít klíč úložiště primární nebo 1.

## <a name="create-the-new-vm"></a>Vytvoření nového virtuálního počítače 

Je potřeba vytvořit sítě a další prostředky virtuálních počítačů pro nový virtuální počítač.

### <a name="create-the-subnet-and-vnet"></a>Vytvoření podsítě a virtuální sítě

Vytvořit virtuální síť a podsíť [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

1. Vytvořte podsíť. Tento příklad vytvoří podsíť s názvem **mySubNet**, ve skupině prostředků **myResourceGroup**a nastaví předponu adresy podsítě na **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Vytvořte virtuální síť. V tomto příkladu nastaví název virtuální sítě bude **myVnetName**, umístění pro **USA – západ**a předpony adresy pro virtuální síť k **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvořte skupinu zabezpečení sítě a pravidlo protokolu RDP
Abyste mohli pro přihlášení k virtuálnímu počítači pomocí protokolu RDP, budete muset mít pravidlo zabezpečení, které povoluje přístup protokolu RDP na portu 3389. Vzhledem k tomu, že byl virtuální pevný disk pro nový virtuální počítač vytvořen z existující specializovaného virtuálního počítače, po vytvoření virtuálního počítače je možné použít existující účet ze zdrojového virtuálního počítače, který má oprávnění k přihlášení pomocí protokolu RDP.
To je potřeba dokončit před vytvořením síťového rozhraní, které budou přidruženy.  
V tomto příkladu nastaví název skupiny NSG na **myNsg** a názvu pravidlo protokolu RDP **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Další informace o koncových bodů a pravidel NSG najdete v tématu [otevření portů k virtuálnímu počítači v Azure pomocí Powershellu](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Vytvoření veřejné IP adresy a síťové karty
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvoření veřejné IP adresy. V tomto příkladu je název veřejné IP adresy nastavena **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Vytvořte síťové rozhraní V tomto příkladu je název síťové karty nastavený na **myNicName**. Tento krok také přiřadí skupiny zabezpečení sítě vytvořené dříve s tento síťový adaptér
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Nastavte název virtuálního počítače a velikost

V tomto příkladu nastaví na název virtuálního počítače "myVM" a velikost virtuálního počítače na "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Přidání síťového rozhraní
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurace disku s operačním systémem

1. Nastavte identifikátor URI virtuálního pevného disku, který jste nahráli nebo zkopírovali. V tomto příkladu soubor virtuálního pevného disku s názvem **myOsDisk.vhd** se ukládají v účtu úložiště s názvem **myStorageAccount** v kontejneru nazvaném **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Přidáte disk s operačním systémem. V tomto příkladu se vytvoří disk s operačním systémem, termín "osDisk" při appened na název virtuálního počítače, chcete-li vytvořit název disku operačního systému. Tento příklad také určuje, že tento virtuální pevný disk založený na Windows by měl být připojené k virtuálnímu počítači jako disku s operačním systémem.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Volitelné: Pokud máte datové disky, které musí být připojené k virtuálnímu počítači přidáte datové disky pomocí adresy URL dat virtuálních pevných disků a příslušné logické jednotky (LUN).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Pokud používáte účet úložiště, data a adresy URL disku operačního systému vypadat přibližně takto: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. To můžete najít na portálu pro procházení cílový kontejner úložiště, klikněte na operačního systému nebo datového virtuálního pevného disku, který jste zkopírovali, a následným kopírováním obsahu z adresy URL.


### <a name="complete-the-vm"></a>Dokončete tento virtuální počítač 

Vytvoření virtuálního počítače pomocí konfigurace, které jsme právě vytvořili.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Pokud tento příkaz byl úspěšný, zobrazí se výstup podobný tomuto:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Ověřte, že se vytvořil virtuální počítač
Nově vytvořený virtuální počítač by se měla zobrazit buď v [webu Azure portal](https://portal.azure.com)v části **všechny služby** > **virtuálních počítačů**, nebo použijte následující příkaz Powershellu příkazy:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Další postup
Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [jak se připojit a přihlaste se na virtuálním počítači Azure s Windows](connect-logon.md).

