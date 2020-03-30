---
title: Vytvoření virtuálního počítače ze specializovaného disku v Azure
description: Vytvořte nový virtuální počítače připojením specializovaného nespravovaného disku v modelu nasazení Správce prostředků.
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
ms.openlocfilehash: d887ef2ef74bb433d6e8ae7f53cd0b77f5948303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073351"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Vytvoření virtuálního virtuálního zařízení ze specializovaného virtuálního pevného disku v účtu úložiště

Vytvořte nový virtuální počítače připojením specializovaného nespravovaného disku jako disku operačního systému pomocí prostředí Powershell. Specializovaný disk je kopie virtuálního pevného disku z existujícího virtuálního počítače, který udržuje uživatelské účty, aplikace a další stavová data z původního virtuálního počítače. 

Máte dvě možnosti:
* [Nahrání virtuálního pevného disku](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Kopírování virtuálního pevného disku existujícího virtuálního počítače Azure](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>Možnost 1: Nahrání specializovaného virtuálního pevného disku

Virtuální pevný disk můžete nahrát ze specializovaného virtuálního počítače vytvořeného pomocí místního virtualizačního nástroje, jako je Hyper-V, nebo virtuálního počítače exportovaného z jiného cloudu.

### <a name="prepare-the-vm"></a>Příprava virtuálního počítače
Můžete nahrát specializovaný virtuální pevný disk, který byl vytvořen pomocí místního virtuálního počítače nebo virtuálního pevného disku exportovaného z jiného cloudu. Specializovaný virtuální pevný disk udržuje uživatelské účty, aplikace a další stavová data z původního virtuálního počítače. Pokud máte v úmyslu použít virtuální pevný disk jako-je k vytvoření nového virtuálního virtuálního soudu, ujistěte se, že jsou dokončeny následující kroky. 
  
  * [Příprava virtuálního pevného disku windows pro nahrání do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nezobecnit** virtuálního mísu pomocí sysprep.
  * Odeberte všechny nástroje pro virtualizaci hosta a agenty, které jsou nainstalované ve virtuálním počítači (tj. nástroje VMware).
  * Ujistěte se, že virtuální počítače je nakonfigurovaný pro vytahování jeho IP adresu a nastavení DNS přes DHCP. Tím zajistíte, že server získá ip adresu v rámci virtuální sítě při spuštění. 


### <a name="get-the-storage-account"></a>Získání účtu úložiště
K uložení nahrané image virtuálního počítače potřebujete účet úložiště v Azure. Můžete buď použít existující účet úložiště, nebo vytvořit nový. 

Chcete-li zobrazit dostupné účty úložiště, zadejte:

```powershell
Get-AzStorageAccount
```

Pokud chcete použít existující účet úložiště, přejděte k části Nahrát obrázek virtuálního zařízení.

Pokud potřebujete vytvořit účet úložiště, postupujte takto:

1. Potřebujete název skupiny prostředků, kde by měl být vytvořen účet úložiště. Chcete-li zjistit všechny skupiny prostředků, které jsou ve vašem předplatném, zadejte:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Chcete-li vytvořit skupinu prostředků s názvem **myResourceGroup** v oblasti **Západní USA,** zadejte:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Vytvořte účet úložiště s názvem **mystorageaccount** v této skupině prostředků pomocí rutiny [New-AzStorageAccount:](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Nahrání virtuálního pevného disku do účtu úložiště
Pomocí rutiny [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) nahrajte obrázek do kontejneru ve vašem účtu úložiště. Tento příklad odešle soubor **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` účtu úložiště s názvem **mystorageaccount** ve skupině prostředků **myResourceGroup.** Soubor bude umístěn do kontejneru s názvem **mycontainer** a nový název souboru bude **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Pokud je úspěšná, dostanete odpověď, která vypadá podobně jako toto:

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

V závislosti na síťovém připojení a velikosti souboru VHD může dokončení tohoto příkazu chvíli trvat.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Možnost 2: Kopírování virtuálního pevného disku z existujícího virtuálního počítače Azure

Virtuální pevný disk můžete zkopírovat do jiného účtu úložiště a použít ho při vytváření nového duplicitního virtuálního účtu.

### <a name="before-you-begin"></a>Než začnete
Ujistěte se, že:

* Mít informace o **zdrojových a cílových účtech úložiště**. Pro zdrojový virtuální účet musíte mít účet úložiště a názvy kontejnerů. Obvykle bude název kontejneru **vhds**. Musíte mít také účet cílového úložiště. Pokud ještě nemáte, můžete vytvořit pomocí portálu **(Všechny služby** > storage účty > Přidat) nebo pomocí rutiny [New-AzStorageAccount.](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) 
* Stáhli a nainstalovali [nástroj AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Navrátit virtuální ms
Navrátit virtuální ho virtuálního virtuálního provozu, který uvolní virtuální pevný disk ke kopírování. 

* **Portál:** Klikněte na **virtuální stroje** > **myVM** > Stop
* **Powershell**: Pomocí [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) zastavit (navrátit) virtuálního virtuálního ma s názvem **myVM** ve skupině prostředků **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

**Stav** virtuálního počítače na portálu Azure se změní z **Zastaveno** **zastaveno (nabyté navrácené).**

### <a name="get-the-storage-account-urls"></a>Získání adres URL účtu úložiště
Potřebujete adresy URL účtů zdrojového a cílového úložiště. Adresy URL vypadají takto: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Pokud již znáte účet úložiště a název kontejneru, stačí nahradit informace mezi závorkami a vytvořit adresu URL. 

Adresu URL můžete použít na webu Azure Portal nebo Azure Powershell:

* **Portál**: **>** Klikněte na účet úložiště **všech služeb** > **Storage accounts** > *účty* > úložiště**objektů BLOB** a zdrojový soubor virtuálního pevného disku je pravděpodobně v kontejneru **vhds.** Klepněte na **položku Vlastnosti** kontejneru a zkopírujte text s popiskem **URL**. Budete potřebovat adresy URL zdrojového i cílového kontejneru. 
* **Powershell**: Pomocí [get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) získat informace o virtuálním virtuálním m s názvem **myVM** ve skupině prostředků **myResourceGroup**. Ve výsledcích se podívejte do části **Profil úložiště** pro **Vhd Uri**. První část Uri je adresa URL kontejneru a poslední část je název virtuálního pevného disku operačního systému pro virtuální ho.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Získání přístupových klíčů úložiště
Najděte přístupové klíče pro účty zdrojového a cílového úložiště. Další informace o přístupových klíčích najdete [v tématu O účtech úložiště Azure](../../storage/common/storage-create-storage-account.md).

* **Portál:** Klikněte na Všechny**účty** >  **služeb,** > *přístupové klíče účtu* > **Access keys**úložiště . Zkopírujte klíč označený jako **key1**.
* **Powershell**: Pomocí [klíče Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) získáte klíč úložiště pro účet úložiště **mystorageaccount** ve skupině prostředků **myResourceGroup**. Zkopírujte klíč s popiskem **key1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Kopírování virtuálního pevného disku
Soubory mezi účty úložiště můžete kopírovat pomocí azcopy. Pro cílový kontejner, pokud zadaný kontejner neexistuje, bude vytvořen pro vás. 

Chcete-li použít AzCopy, otevřete příkazový řádek v místním počítači a přejděte do složky, kde je nainstalován azCopy. Bude podobný *c:\programové soubory (x86)\Microsoft SDKs\Azure\AzCopy*. 

Chcete-li zkopírovat všechny soubory v kontejneru, použijte přepínač **/S.** To lze použít ke kopírování virtuálního pevného disku operačního systému a všech datových disků, pokud jsou ve stejném kontejneru. Tento příklad ukazuje, jak zkopírovat všechny soubory v kontejneru **mysourcecontainer** v účtu úložiště **mysourcestorageaccount** do kontejneru **mydestinationcontainer** v účtu úložiště **mydestinationstorageaccount.** Nahraďte názvy účtů úložiště a kontejnerů vlastními. Nahraďte `<sourceStorageAccountKey1>` a `<destinationStorageAccountKey1>` s vlastními klíči.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Pokud chcete zkopírovat pouze konkrétní virtuální pevný disk v kontejneru s více soubory, můžete také zadat název souboru pomocí přepínače /Pattern. V tomto příkladu bude zkopírován pouze soubor s názvem **myFileName.vhd.**

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Po dokončení se zobrazí zpráva, která vypadá podobně:

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
* Při použití AZCopy, pokud se zobrazí chyba "Server se nepodařilo ověřit požadavek", ujistěte se, že hodnota hlavičky Autorizace je vytvořena správně včetně podpisu. Pokud používáte klíč 2 nebo sekundární klíč úložiště, zkuste použít primární nebo první klíč úložiště.

## <a name="create-the-new-vm"></a>Vytvoření nového virtuálního virtuálního movitého virtuálního montova 

Musíte vytvořit sítě a další prostředky virtuálních zařízení, které mají být použity pro nový virtuální hod.

### <a name="create-the-subnet-and-vnet"></a>Vytvoření podsítě a virtuální sítě

Vytvořte virtuální síť a podsíť [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

1. Vytvořte podsíť. Tento příklad vytvoří podsíť s názvem **mySubNet**ve skupině prostředků **myResourceGroup**a nastaví předponu adresy podsítě na **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Vytvořte virtuální síť. Tento příklad nastaví název virtuální sítě na **myVnetName**, umístění na **západ USA**a předponu adresy pro virtuální síť na **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvoření skupiny zabezpečení sítě a pravidla protokolu RDP
   Chcete-li mít možnost přihlásit se k virtuálnímu počítači pomocí protokolu RDP, musíte mít pravidlo zabezpečení, které umožňuje přístup k protokolu RDP na portu 3389. Vzhledem k tomu, že virtuální pevný disk pro nový virtuální počítač byl vytvořen z existujícího specializovaného virtuálního počítače, můžete po vytvoření virtuálního počítače použít existující účet ze zdrojového virtuálního počítače, který měl oprávnění k přihlášení pomocí protokolu RDP.
   To je třeba dokončit před vytvořením síťového rozhraní, se kterým bude přidružen.  
   Tento příklad nastaví název skupiny nsg na **myNsg** a název pravidla RDP na **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Další informace o koncových bodech a pravidlech nsg najdete [v tématu Otevírání portů k virtuálnímu počítači v Azure pomocí PowerShellu](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Vytvoření veřejné IP adresy a nic
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a síťové rozhraní.

1. Vytvořte veřejnou IP adresu. V tomto příkladu je název veřejné IP adresy nastaven na **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Vytvořte nic. V tomto příkladu je název nic nastaven na **myNicName**. Tento krok také přidruží skupinu zabezpečení sítě vytvořenou dříve s touto nicovou sítí.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Nastavení názvu a velikosti virtuálního počítače

Tento příklad nastaví název virtuálního počítače na "myVM" a velikost virtuálního počítače na "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Přidání nic
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurace disku operačního systému

1. Nastavte identifikátor URI pro virtuální pevný disk, který jste nahráli nebo zkopírovali. V tomto příkladu je soubor VHD s názvem **myOsDisk.vhd** uložen v účtu úložiště s názvem **myStorageAccount** v kontejneru s názvem **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Přidejte disk operačního systému. V tomto příkladu při vytvoření disku s os je termín "osDisk" připojen k názvu virtuálního počítače k vytvoření názvu disku operačního systému. Tento příklad také určuje, že tento virtuální pevný disk založený na systému Windows by měl být připojen k virtuálnímu počítače jako disk operačního systému.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Volitelné: Pokud máte datové disky, které je potřeba připojit k virtuálnímu počítače, přidejte datové disky pomocí adres URL datových virtuálních pevných disků a příslušného čísla logické jednotky (Lun).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Při použití účtu úložiště vypadají adresy URL dat a disků `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`operačního systému přibližně takto: . Najdete to na portálu procházením cílového kontejneru úložiště, kliknutím na operační systém nebo datový virtuální pevný disk, který byl zkopírován, a následným zkopírováním obsahu adresy URL.


### <a name="complete-the-vm"></a>Dokončení virtuálního virtuálního mísy 

Vytvořte virtuální virtuální počítače pomocí konfigurací, které jsme právě vytvořili.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Pokud byl tento příkaz úspěšný, zobrazí se výstup takto:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Ověření, že byl virtuální virtuální hotel vytvořen
Nově vytvořený virtuální počítač byste měli vidět buď na [webu Azure Portal](https://portal.azure.com), v části Virtuální**počítače** **všech služeb** > , nebo pomocí následujících příkazů Prostředí PowerShell:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Další kroky
Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [Jak se připojit a přihlásit k virtuálnímu počítači Azure se systémem Windows](connect-logon.md).

