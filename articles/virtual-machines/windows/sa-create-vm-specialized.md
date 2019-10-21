---
title: Vytvoření virtuálního počítače ze specializovaného disku v Azure | Microsoft Docs
description: Vytvořte nový virtuální počítač připojením specializovaného nespravovaného disku v modelu nasazení Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: cc3c1d9352d9df44a51a917700c656055b8b8361
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088628"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Vytvoření virtuálního počítače ze specializovaného virtuálního pevného disku v účtu úložiště

Vytvořte nový virtuální počítač připojením specializovaného nespravovaného disku jako disku s operačním systémem pomocí PowerShellu. Specializovaný disk je kopií virtuálního pevného disku ze stávajícího virtuálního počítače, který udržuje uživatelské účty, aplikace a další stavová data z původního virtuálního počítače. 

Máte dvě možnosti:
* [Nahrání virtuálního pevného disku](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Zkopírování virtuálního pevného disku existujícího virtuálního počítače Azure](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="option-1-upload-a-specialized-vhd"></a>Možnost 1: Nahrání specializovaného virtuálního pevného disku

Virtuální pevný disk můžete nahrát z specializovaného virtuálního počítače vytvořeného pomocí nástroje pro místní virtualizaci, jako je Hyper-V nebo virtuální počítač exportovaný z jiného cloudu.

### <a name="prepare-the-vm"></a>Příprava virtuálního počítače
Můžete nahrát specializovaný virtuální pevný disk, který byl vytvořen pomocí místního virtuálního počítače nebo virtuálního pevného disku exportovaný z jiného cloudu. Specializovaný virtuální pevný disk udržuje uživatelské účty, aplikace a data dalších stavů z původního virtuálního počítače. Pokud máte v úmyslu použít virtuální pevný disk jako vytvoření nového virtuálního počítače, ujistěte se, že jsou splněné následující kroky. 
  
  * [Připravte si virtuální pevný disk Windows, který se má nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Neprovádějte GENERALIZACI virtuálního počítače pomocí nástroje Sysprep.
  * Odeberte všechny virtualizační nástroje a agenty hosta, které jsou nainstalované na virtuálním počítači (například nástroje VMware).
  * Ujistěte se, že je virtuální počítač nakonfigurovaný tak, aby z protokolu DHCP vyčetl adresu IP a nastavení DNS. Tím se zajistí, že server při spuštění získá IP adresu v rámci virtuální sítě. 


### <a name="get-the-storage-account"></a>Získat účet úložiště
K uložení nahrané image virtuálního počítače potřebujete účet úložiště v Azure. Můžete buď použít existující účet úložiště, nebo vytvořit nový. 

Chcete-li zobrazit dostupné účty úložiště, zadejte:

```powershell
Get-AzStorageAccount
```

Pokud chcete použít existující účet úložiště, přejděte do části nahrání image virtuálního počítače.

Pokud potřebujete vytvořit účet úložiště, použijte následující postup:

1. Potřebujete název skupiny prostředků, ve které se má účet úložiště vytvořit. Pokud chcete zjistit všechny skupiny prostředků ve vašem předplatném, zadejte:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Pokud chcete v **západní USA** oblasti vytvořit skupinu prostředků s názvem **myResourceGroup** , zadejte:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Vytvořte účet úložiště s názvem **mystorageaccount** v této skupině prostředků pomocí rutiny [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) :
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště
Pomocí rutiny [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) nahrajte image do kontejneru v účtu úložiště. Tento příklad nahraje soubor **myVHD. VHD** z `"C:\Users\Public\Documents\Virtual hard disks\"` do účtu úložiště s názvem **mystorageaccount** ve skupině prostředků **myResourceGroup** . Soubor se umístí do kontejneru s názvem **myContainer** a nový název souboru bude **myUploadedVHD. VHD**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


V případě úspěchu získáte odpověď, která vypadá nějak takto:

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

V závislosti na připojení k síti a velikosti souboru VHD může dokončení tohoto příkazu chvíli trvat.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Možnost 2: Zkopírování virtuálního pevného disku z existujícího virtuálního počítače Azure

Virtuální pevný disk můžete zkopírovat do jiného účtu úložiště, abyste ho mohli použít při vytváření nového duplicitního virtuálního počítače.

### <a name="before-you-begin"></a>Před zahájením
Ujistěte se, že:

* Obsahuje informace o **zdrojovém a cílovém účtu úložiště**. Pro zdrojový virtuální počítač je potřeba mít účet úložiště a názvy kontejnerů. Název kontejneru bude obvykle **VHD**. Musíte mít také cílový účet úložiště. Pokud ho ještě nemáte, můžete ho vytvořit buď pomocí portálu (**všechny služby** > účty úložiště > Přidat), nebo pomocí rutiny [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) . 
* Stáhli a nainstalovali jste [Nástroj AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Zrušení přidělení virtuálního počítače
Zrušení přidělení virtuálního počítače, který uvolní virtuální pevný disk pro zkopírování. 

* **Portál**: Klikněte na **virtuální počítače** > **myVM** > zastavit.
* **PowerShell**: Pomocí [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) zastavte (navrácení) virtuálního počítače s názvem **myVM** ve skupině prostředků **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

**Stav** virtuálního počítače ve Azure Portal se změní ze zastaveno na **Zastaveno (přidělení zrušeno)** .

### <a name="get-the-storage-account-urls"></a>Získání adres URL účtu úložiště
Potřebujete adresy URL zdrojového a cílového účtu úložiště. Adresy URL vypadají jako: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Pokud už znáte účet úložiště a název kontejneru, můžete jenom nahradit informace mezi závorkami a vytvořit tak adresu URL. 

Adresu URL můžete získat pomocí Azure Portal nebo Azure PowerShellu:

* **Portál**: ** > ** Kliknětena >  **všechny služby**účty úložiště objekty blob účtu úložiště a zdrojový soubor VHD se pravděpodobně nacházejí v kontejneru VHD. ** > ** **>** Klikněte na **vlastnosti** kontejneru a zkopírujte text označený **Adresa URL**. Budete potřebovat adresy URL zdrojového i cílového kontejneru. 
* **PowerShell**: K získání informací o virtuálním počítači s názvem **myVM** ve skupině prostředků **myResourceGroup**použijte [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) . Ve výsledcích vyhledejte část **profil úložiště** pro **identifikátor URI virtuálního pevného disku**. První část identifikátoru URI je adresa URL kontejneru a poslední část je název virtuálního pevného disku operačního systému pro virtuální počítač.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Získání přístupových klíčů k úložišti
Vyhledejte přístupové klíče pro zdrojové a cílové účty úložiště. Další informace o přístupových klíčích najdete v tématu [informace o účtech Azure Storage](../../storage/common/storage-create-storage-account.md).

* **Portál**: Klikněte na **všechny služby**  >  **účty**  > úložiště **přístupové klíče** *účtu* > úložiště. Zkopírujte klíč označený jako **klíč1**.
* **PowerShell**: Pomocí [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) Získejte klíč úložiště pro účet úložiště **mystorageaccount** ve skupině prostředků **myResourceGroup**. Zkopírujte klíč označený jako **klíč1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Kopírování VHD
Soubory mezi účty úložiště můžete kopírovat pomocí AzCopy. Pro cílový kontejner, pokud zadaný kontejner neexistuje, vytvoří se za vás. 

Pokud chcete používat AzCopy, otevřete na svém místním počítači příkazový řádek a přejděte do složky, ve které je nainstalovaná AzCopy. Bude se podobat *složce C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Chcete-li zkopírovat všechny soubory v rámci kontejneru, použijte přepínač **/s** . Tato možnost slouží ke zkopírování virtuálního pevného disku s operačním systémem a všech datových disků, pokud jsou ve stejném kontejneru. Tento příklad ukazuje, jak zkopírovat všechny soubory v kontejneru **mysourcecontainer** v účtu úložiště **mysourcestorageaccount** do kontejneru **mydestinationcontainer** v účtu úložiště **mydestinationstorageaccount** . Názvy účtů úložiště a kontejnerů nahraďte vlastními. `<sourceStorageAccountKey1>` Nahraďte `<destinationStorageAccountKey1>` a vlastními klíči.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Pokud chcete zkopírovat pouze konkrétní virtuální pevný disk v kontejneru s více soubory, můžete také zadat název souboru pomocí přepínače/Pattern. V tomto příkladu se zkopíruje pouze soubor s názvem **myFileName. VHD** .

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Po dokončení se zobrazí zpráva, která vypadá nějak takto:

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
* Pokud se při použití AZCopy zobrazí chyba "Server selhal při ověření žádosti", ujistěte se, že je hodnota autorizační hlavičky správně vytvořená, včetně signatury. Pokud používáte klíč 2 nebo sekundární klíč úložiště, zkuste použít primární nebo první klíč úložiště.

## <a name="create-the-new-vm"></a>Vytvoření nového virtuálního počítače 

Musíte vytvořit síť a další prostředky virtuálních počítačů, které má nový virtuální počítač používat.

### <a name="create-the-subnet-and-vnet"></a>Vytvoření podsítě a virtuální sítě

Vytvořte virtuální síť a podsíť [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

1. Vytvořte podsíť. Tento příklad vytvoří podsíť s názvem **mySubNet**ve skupině prostředků **myResourceGroup**a nastaví předponu adresy podsítě na **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Vytvořte virtuální síť. V tomto příkladu se nastaví název virtuální sítě, která se má **myVnetName**, umístění, které se má **západní USA**, a předpona adresy pro virtuální síť **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvoření skupiny zabezpečení sítě a pravidla protokolu RDP
   Abyste se mohli přihlásit ke svému VIRTUÁLNÍmu počítači pomocí protokolu RDP, musíte mít bezpečnostní pravidlo, které umožňuje přístup protokolu RDP na portu 3389. Vzhledem k tomu, že virtuální pevný disk pro nový virtuální počítač byl vytvořen z existujícího specializovaného virtuálního počítače, můžete po jeho vytvoření použít existující účet ze zdrojového virtuálního počítače, který měl oprávnění k přihlášení pomocí protokolu RDP.
   Tento postup je nutné provést před vytvořením síťového rozhraní, ke kterému bude přidružen.  
   V tomto příkladu se nastaví název NSG na **myNsg** a název pravidla RDP na **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Další informace o koncových bodech a pravidlech NSG najdete v tématu [otevření portů k virtuálnímu počítači v Azure pomocí PowerShellu](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Vytvoření veřejné IP adresy a síťové karty
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvořte veřejnou IP adresu. V tomto příkladu je název veřejné IP adresy nastavený na **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Vytvořte síťovou kartu. V tomto příkladu je název síťové karty nastavený na **myNicName**. Tento krok taky přidruží skupinu zabezpečení sítě vytvořenou dříve s tímto síťovým ADAPTÉRem.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Nastavení názvu a velikosti virtuálního počítače

V tomto příkladu se nastaví název virtuálního počítače na "myVM" a velikost virtuálního počítače na "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Přidat síťovou kartu
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurace disku s operačním systémem

1. Nastavte identifikátor URI pro virtuální pevný disk, který jste nahráli nebo zkopírovali. V tomto příkladu je soubor VHD s názvem **myOsDisk. VHD** uložený v účtu úložiště s názvem **myStorageAccount** v kontejneru s názvem **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Přidejte disk s operačním systémem. V tomto příkladu se při vytvoření disku s operačním systémem do názvu virtuálního počítače připojí pojem "osDisk", aby se vytvořil název disku operačního systému. Tento příklad také určuje, že tento virtuální pevný disk systému Windows by měl být připojen k virtuálnímu počítači jako disk s operačním systémem.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Volitelné: Pokud máte datové disky, které je potřeba připojit k virtuálnímu počítači, přidejte datové disky pomocí adres URL datových pevných disků a příslušné logické jednotky (LUN).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Při použití účtu úložiště vypadají adresy URL disků a disků s operačním systémem něco podobného: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. To můžete na portálu najít tak, že přejdete do cílového kontejneru úložiště, kliknete na operační systém nebo na zkopírovaný datový disk VHD a pak zkopírujete obsah této adresy URL.


### <a name="complete-the-vm"></a>Dokončete virtuální počítač. 

Vytvořte virtuální počítač pomocí konfigurací, které jsme právě vytvořili.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Pokud byl tento příkaz úspěšný, zobrazí se výstup podobný tomuto:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Ověřte, že byl virtuální počítač vytvořen.
Nově vytvořený virtuální počítač by se měl zobrazit buď v [Azure Portal](https://portal.azure.com), v části **všechny** > **virtuální počítače**služeb, nebo pomocí následujících příkazů PowerShellu:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Další kroky
Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [jak se připojit a přihlásit se k virtuálnímu počítači Azure s Windows](connect-logon.md).

