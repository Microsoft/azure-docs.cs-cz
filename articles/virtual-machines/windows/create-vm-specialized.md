---
title: Vytvoření virtuálního počítače s Windows ze specializovaného virtuálního pevného disku v Azure
description: Vytvořte nový virtuální počítač s Windows připojením specializovaného spravovaného disku jako disku s operačním systémem pomocí modelu nasazení Správce prostředků.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: cddc7f4f453f22b0cb36b1d3a1e9c2fba2dcabaf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455088"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Vytvoření virtuálního počítače s Windows ze specializovaného disku pomocí PowerShellu

Vytvořte nový virtuální počítač připojením specializovaného spravovaného disku jako disku s operačním systémem. Specializovaný disk je kopií virtuálního pevného disku (VHD) ze stávajícího virtuálního počítače, který obsahuje uživatelské účty, aplikace a další údaje o stavu z původního virtuálního počítače. 

Máte několik možností:
* [Použijte existující spravovaný disk](#option-1-use-an-existing-disk). Tato možnost je užitečná, pokud máte virtuální počítač, který nefunguje správně. Pokud chcete vytvořit nový virtuální počítač, můžete virtuální počítač odstranit a pak znovu použít spravovaný disk. 
* [Nahrání virtuálního pevného disku](#option-2-upload-a-specialized-vhd) 
* [Zkopírování existujícího virtuálního počítače Azure pomocí snímků](#option-3-copy-an-existing-azure-vm)

Pomocí Azure Portal můžete také [vytvořit nový virtuální počítač ze specializovaného virtuálního pevného disku](create-vm-specialized-portal.md).

V tomto článku se dozvíte, jak používat spravované disky. Pokud máte starší verzi nasazení, která vyžaduje použití účtu úložiště, přečtěte si téma [Vytvoření virtuálního počítače ze specializovaného virtuálního pevného disku v účtu úložiště](/previous-versions/azure/virtual-machines/windows/sa-create-vm-specialized).

> [!IMPORTANT]
> 
> Při použití specializovaného disku k vytvoření nového virtuálního počítače si nový virtuální počítač zachová název počítače původního virtuálního počítače. Další informace specifické pro počítač (např. CMID) jsou také uchovávány, a v některých případech mohou tyto duplicitní informace způsobovat problémy. Při kopírování virtuálního počítače si pamatujte na typy informací specifických pro konkrétní počítač, na kterých aplikace závisí.  
> Proto nepoužívejte specializovaný disk, pokud chcete vytvořit více virtuálních počítačů. Místo toho můžete pro rozsáhlejší nasazení [vytvořit image](capture-image-resource.md) a pak [pomocí této image vytvořit víc virtuálních počítačů](create-vm-generalized-managed.md).

Doporučujeme omezit počet souběžných nasazení na 20 virtuálních počítačů z jednoho virtuálního pevného disku nebo snímku. 

## <a name="option-1-use-an-existing-disk"></a>Možnost 1: použití existujícího disku

Pokud máte virtuální počítač, který jste odstranili a chcete znovu použít disk s operačním systémem k vytvoření nového virtuálního počítače, použijte [příkaz Get-AzDisk](/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Tento disk teď můžete připojit jako disk s operačním systémem k [novému virtuálnímu počítači](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Možnost 2: nahrání specializovaného virtuálního pevného disku

Virtuální pevný disk můžete nahrát z specializovaného virtuálního počítače vytvořeného pomocí nástroje pro místní virtualizaci, jako je Hyper-V nebo virtuální počítač exportovaný z jiného cloudu.

### <a name="prepare-the-vm"></a>Příprava virtuálního počítače
Použijte virtuální pevný disk tak, jak je, k vytvoření nového virtuálního počítače. 
  
  * [Připravte si virtuální pevný disk Windows, který se má nahrát do Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Neprovádějte generalizaci** virtuálního počítače pomocí nástroje Sysprep.
  * Odeberte všechny virtualizační nástroje a agenty hosta, které jsou nainstalované na virtuálním počítači (například nástroje VMware).
  * Ujistěte se, že je virtuální počítač nakonfigurovaný tak, aby získal IP adresu a nastavení DNS z protokolu DHCP. Tím se zajistí, že při spuštění serveru získá IP adresu v rámci virtuální sítě. 


### <a name="upload-the-vhd"></a>Nahrání virtuálního pevného disku

Virtuální pevný disk teď můžete nahrát přímo do spravovaného disku. Pokyny najdete v tématu [nahrání virtuálního pevného disku do Azure pomocí Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Možnost 3: Zkopírování existujícího virtuálního počítače Azure

Pomocí snímku virtuálního počítače můžete vytvořit kopii virtuálního počítače, který používá spravované disky, a pak pomocí tohoto snímku vytvořit nový spravovaný disk a nový virtuální počítač.

Pokud chcete zkopírovat existující virtuální počítač do jiné oblasti, můžete použít AzCopy a [vytvořit kopii disku v jiné oblasti](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Pořídit snímek disku s operačním systémem

Můžete pořídit snímek celého virtuálního počítače (včetně všech disků) nebo jenom jednoho disku. Následující kroky ukazují, jak pořídit snímek jenom disku s operačním systémem virtuálního počítače pomocí rutiny [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) . 

Nejdřív nastavte některé parametry. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Získejte objekt virtuálního počítače.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Získejte název disku s operačním systémem.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Vytvořte konfiguraci snímku. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Pořídit snímek.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Pokud chcete pomocí tohoto snímku vytvořit virtuální počítač, který musí být vysoký, přidejte do `-AccountType Premium_LRS` příkazu New-AzSnapshotConfig parametr. Tento parametr vytvoří snímek tak, aby byl uložen jako spravovaný disk úrovně Premium. Premium Managed Disks jsou dražší než standard, takže před použitím tohoto parametru se ujistěte, že budete potřebovat prémii.

### <a name="create-a-new-disk-from-the-snapshot"></a>Vytvoření nového disku ze snímku

Pomocí [New-AzDisk](/powershell/module/az.compute/new-azdisk)vytvořte ze snímku spravovaný disk. V tomto příkladu se pro název disku používá *myOSDisk* .

Vytvořte novou skupinu prostředků pro nový virtuální počítač.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Nastavte název disku operačního systému. 

```powershell
$osDiskName = 'myOsDisk'
```

Vytvořte spravovaný disk.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Vytvoření nového virtuálního počítače 

Vytvořte sítě a další prostředky virtuálních počítačů, které má nový virtuální počítač používat.

### <a name="create-the-subnet-and-virtual-network"></a>Vytvoření podsítě a virtuální sítě

Vytvořte [virtuální síť](../../virtual-network/virtual-networks-overview.md) a podsíť pro virtuální počítač.

1. Vytvořte podsíť. Tento příklad vytvoří podsíť s názvem *mySubNet* ve skupině prostředků *myDestinationResourceGroup* a nastaví předponu adresy podsítě na *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Vytvořte virtuální síť. V tomto příkladu se nastaví název virtuální sítě na *myVnetName*, umístění, které se má *západní USA*, a předpona adresy pro virtuální síť na *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Vytvoření skupiny zabezpečení sítě a pravidla protokolu RDP
Abyste se mohli přihlásit ke svému VIRTUÁLNÍmu počítači pomocí protokolu RDP (Remote Desktop Protocol), budete muset mít bezpečnostní pravidlo, které umožňuje přístup protokolu RDP na portu 3389. V našem příkladu byl virtuální pevný disk pro nový virtuální počítač vytvořený z existujícího specializovaného virtuálního počítače, takže můžete použít účet, který existoval na zdrojovém virtuálním počítači pro protokol RDP.

V tomto příkladu se nastaví název skupiny zabezpečení sítě (NSG) na *myNsg* a název pravidla RDP na *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Další informace o koncových bodech a pravidlech NSG najdete v tématu [otevření portů k virtuálnímu počítači v Azure pomocí PowerShellu](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Vytvoření veřejné IP adresy a síťové karty
Pokud chcete povolit komunikaci s virtuálním počítačem ve virtuální síti, budete potřebovat [veřejnou IP adresu](../../virtual-network/public-ip-addresses.md) a síťové rozhraní.

1. Vytvořte veřejnou IP adresu. V tomto příkladu je název veřejné IP adresy nastavený na *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Vytvořte síťovou kartu. V tomto příkladu je název síťové karty nastavený na *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Nastavení názvu a velikosti virtuálního počítače

V tomto příkladu se nastaví název virtuálního počítače na *myVM* a velikost virtuálního počítače na *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Přidat síťovou kartu
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Přidat disk s operačním systémem 

Přidejte disk s operačním systémem do konfigurace pomocí [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk). V tomto příkladu se nastaví velikost disku na *128 GB* a připojí se spravovaný disk jako disk s operačním *systémem Windows* .
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Dokončete virtuální počítač. 

Vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm) s konfiguracemi, které jsme právě vytvořili.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Pokud je tento příkaz úspěšný, zobrazí se výstup podobný tomuto:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Ověřte, že byl virtuální počítač vytvořen.
Nově vytvořený virtuální počítač by se měl zobrazit buď v [Azure Portal](https://portal.azure.com) v části **Procházet**  >  **virtuální počítače**, nebo pomocí následujících příkazů PowerShellu.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Další kroky
Přihlaste se k novému virtuálnímu počítači. Další informace najdete v tématu [jak se připojit a přihlásit se k virtuálnímu počítači Azure s Windows](connect-logon.md).