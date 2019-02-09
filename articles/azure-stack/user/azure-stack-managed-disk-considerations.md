---
title: Rozdíly a aspekty u služby Managed Disks ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o rozdíly a aspekty při práci se službou Managed Disks v Azure stacku.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: acd92c711f432cf103b9309247704ff348287ff6
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964062"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Managed Disks zásobníku: rozdíly a aspekty

Tento článek shrnuje známé rozdíly mezi [Azure Stack Managed Disks](azure-stack-manage-vm-disks.md) a [Managed Disks pro Azure](../../virtual-machines/windows/managed-disks-overview.md). Další informace o základní rozdíly mezi Azure Stack a Azure, najdete v článku [klíče aspekty](azure-stack-considerations.md) článku.

Spravované disky zjednodušují správu disků pro virtuální počítače IaaS pomocí správy [účty úložiště](../azure-stack-manage-storage-accounts.md) přidružené k diskům virtuálních počítačů.

> [!Note]  
> Spravované disky ve službě Azure Stack je k dispozici aktualizace. 1808. Je povolené ve výchozím nastavení při vytváření virtuálních počítačů pomocí portálu Azure Stack od 1811 aktualizace.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Tahák: Spravovaného disku rozdíly

| Funkce | Azure (globální) | Azure Stack |
| --- | --- | --- |
|Šifrování pro neaktivní uložená Data |Šifrování služby Azure Storage (SSE), Azure Disk Encryption (ADE)     |Šifrování AES 128-bit nástroje BitLocker      |
|Image          | Podpora spravovanou vlastní image |Podporováno|
|Možnosti zálohování |Podpora služby Azure Backup |Není dosud podporován. |
|Možnosti zotavení po havárii |Podpora Azure Site Recovery |Není dosud podporován.|
|Typy disků     |Premium SSD, SSD na úrovni Standard (Preview) a Standard pevný disk |Premium SSD, Standard HDD |
|Disky Premium  |Plně podporované. |Je možné zřídit, ale bez omezení výkonu nebo záruk  |
|Prémiové disky vstupně-výstupních operací  |Závisí na velikosti disku  |2300 IOPs na disk |
|Propustnost disků úrovně Premium |Závisí na velikosti disku |145 MB za sekundu na disk |
|Velikost disku  |Disk Azure typu Premium: P4 (32 GB) na P80 (32 TB)<br>Disk Azure SSD na úrovni Standard: E10 (128 GB) na E80 (32 TB)<br>Pevný disk Azure standardní Disk: S4 (32 GB) na S80 (32 TB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GiB |
|Kopie snímků disků|Pořízení snímku Azure připojené do spuštěného virtuálního počítače nepodporuje spravované disky|Není dosud podporován. |
|Výkon disků analýzy |Agregace metrik a metriky pro disk podporované |Není dosud podporován. |
|Migrace      |Poskytuje nástroj pro migraci z existující nespravovaná správce prostředků virtuálních počítačů Azure bez nutnosti znovu vytvořte virtuální počítač  |Není dosud podporován. |

> [!NOTE]  
> Spravované disky vstupně-výstupních operací a propustnosti ve službě Azure Stack je číslo zakončení místo zřízené číslo, které může vliv hardwaru a pracovní postupy spouštěné ve službě Azure Stack.

## <a name="metrics"></a>Metriky

Existují také rozdíly pomocí metrik storage:

- Transakce data v metrikách storage pomocí služby Azure Stack nerozlišuje šířky pásma sítě interní nebo externí.
- Azure Stack data transakcí v metrikách storage neobsahuje virtuální počítač přístup k připojené disky.

## <a name="api-versions"></a>Verze rozhraní API

Služba Azure Managed Disks zásobníku podporuje následující verze rozhraní API:

- 2017-03-30

## <a name="managed-images"></a>Spravovaná Image

Azure Stack podporuje *spravované image*, které umožňují můžete vytvořit objekt spravované image zobecněného virtuálního počítače (nespravované i spravovat), která může vytvářet pouze spravovaný disk virtuálních počítačů do budoucna. Spravované bitové kopie podporují následující dva scénáře:

- Máte zobecněný nespravované virtuální počítače a chcete použít spravované disky do budoucna.
- Máte generalizovaného spravované počítače a chcete vytvořit více, podobně jako spravovaných virtuálních počítačů.

### <a name="migrate-unmanaged-vms-to-managed-disks"></a>Migrace nespravované virtuální počítače na managed disks

Postupujte podle pokynů [tady](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) pro vytvoření spravované image z generalizovaného VHD v účtu úložiště. Tento image je možné k vytvoření spravovaných virtuálních počítačů do budoucna.

### <a name="create-managed-image-from-vm"></a>Vytvoření spravované image z virtuálního počítače

Po vytvoření image z existujícího spravovaného disku virtuálního počítače pomocí skriptu [tady](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell) , následující ukázkový skript vytvoří podobně jako virtuální počítač s Linuxem z existujícího objektu image:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename
# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Další informace najdete v tématu Azure spravované image články [vytvoření spravované image zobecněného virtuálního počítače v Azure](../../virtual-machines/windows/capture-image-resource.md) a [vytvoření virtuálního počítače ze spravované image](../../virtual-machines/windows/create-vm-generalized-managed.md).

## <a name="configuration"></a>Konfigurace

Po použití. 1808 update nebo novější, je třeba provést následující konfiguraci před použitím spravovaných disků:

- Pokud odběr byl vytvořen ještě před aktualizací. 1808, postupujte podle následujících kroků provedete aktualizaci odběru. V opačném případě nasazování virtuálních počítačů v tomto předplatném může selhat s chybovou zprávou "Vnitřní chyba ve Správci disků."
   1. Portál pro klienty, přejděte na **předplatná** a vyhledejte předplatné. Klikněte na tlačítko **poskytovatelů prostředků**, klikněte na **Microsoft.Compute**a potom klikněte na tlačítko **přeregistrovat**.
   2. V rámci stejného předplatného, přejděte na **řízení přístupu (IAM)** a ověřte, že **Azure Stack – spravovaný Disk** je uvedena.
- Pokud používáte prostředí s více tenanty, požádejte vašeho cloudu – operátor (který může být ve vaší vlastní organizaci, nebo od poskytovatele služeb) k překonfigurovat všech vašich adresářů hosta, proveďte kroky v [v tomto článku](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). V opačném případě nasazování virtuálních počítačů v rámci služby předplacené přidružené k tomuto adresáři hosta může selhat s chybovou zprávou "Vnitřní chyba ve Správci disků."

## <a name="next-steps"></a>Další postup

- [Další informace o virtuálních počítačích Azure Stack](azure-stack-compute-overview.md)
