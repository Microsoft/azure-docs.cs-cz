---
title: Možnosti redundance pro Azure Managed disks
description: Přečtěte si o redundantním úložišti a místně redundantním úložišti zóny pro Azure Managed disks.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f0f3baf1bf56f958408f789961812c0555f289f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043639"
---
# <a name="redundancy-options-for-managed-disks"></a>Možnosti redundance pro spravované disky

Služba Azure Managed disks nabízí dvě možnosti redundance úložiště, zónu redundantní úložiště (ZRS) jako verzi Preview a místně redundantní úložiště. ZRS poskytuje vyšší dostupnost pro spravované disky než místně redundantní úložiště (LRS). Latence zápisu pro disky LRS je ale lepší než disky ZRS, protože disky LRS synchronně zapisují data do tří kopií v jednom datovém centru.

## <a name="locally-redundant-storage-for-managed-disks"></a>Místně redundantní úložiště pro spravované disky

Místně redundantní úložiště (LRS) replikuje vaše data třikrát v rámci jednoho datového centra ve vybrané oblasti. LRS chrání vaše data proti racku serveru a selhání jednotky. 

Existuje několik způsobů, jak můžete aplikaci ochránit pomocí LRS disků z celé zóny, která může nastat kvůli přirozeným haváriím nebo problémům s hardwarem:
- Použijte aplikaci, jako je SQL Server AlwaysOn, která může synchronně zapisovat data do dvou zón a automaticky převzít služby při selhání do jiné zóny během havárie.
- Převezměte časté zálohování disků LRS pomocí snímků ZRS.
- Povolte zotavení po havárii mezi zónami pro disky LRS prostřednictvím [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Zotavení po havárii mezi zónami ale neposkytuje žádný cíl bodu obnovení (RPO).

Pokud váš pracovní postup nepodporuje synchronní zápisy na úrovni aplikace v různých zónách nebo vaše aplikace musí splňovat žádné RPO, ZRS disky by ideálním způsobem.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Redundantní úložiště zóny pro Managed Disks (Preview)

Zóna – redundantní úložiště (ZRS) replikuje spravovaný disk Azure synchronně ve třech zónách dostupnosti Azure ve vybrané oblasti. Každá zóna dostupnosti je samostatné fyzické umístění s nezávislým napájením, chlazením a sítí. 

Disky ZRS umožňují obnovení z chyb v zónách dostupnosti. Pokud se celá zóna vypnula, může se k virtuálnímu počítači v jiné zóně připojit ZRS disk. K zajištění vylepšené dostupnosti pro clusterované nebo distribuované aplikace, jako je SQL FCI, SAP ASCS/SCS nebo GFS2, můžete také použít disky ZRS jako sdílený disk. Sdílený ZRS disk můžete připojit k primárnímu a sekundárnímu virtuálnímu počítači v různých zónách, abyste mohli využít ZRS i [zóny dostupnosti](../availability-zones/az-overview.md). Pokud vaše primární zóna selže, můžete k sekundárnímu virtuálnímu počítači rychle převzít služby při selhání pomocí [trvalé rezervace SCSI](disks-shared-enable.md#supported-scsi-pr-commands).

### <a name="limitations"></a>Omezení

V rámci verze Preview má ZRS pro spravované disky následující omezení:

- Podporováno pouze s jednotkami SSD (Solid-State Drive) a standardním SSD.
- Aktuálně k dispozici pouze v oblasti EastUS2EUAP.
- Disky ZRS se dají vytvářet jenom pomocí šablon Azure Resource Manager pomocí `2020-12-01` rozhraní API.

Zaregistrujte se do verze Preview [tady](https://aka.ms/ZRSDisksPreviewSignUp).

### <a name="billing-implications"></a>Důsledky fakturace

Podrobnosti najdete na [stránce s cenami za Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Porovnání s jinými typy disků

S výjimkou větší latence zápisu jsou disky používající ZRS stejné jako disky používající LRS. Mají stejné cíle výkonu.

### <a name="create-zrs-managed-disks"></a>Vytvoření ZRS spravovaných disků

`2020-12-01`K vytvoření disku ZRS použijte rozhraní API s šablonou Azure Resource Manager.

#### <a name="create-a-vm-with-zrs-disks"></a>Vytvoření virtuálního počítače s ZRS disky

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Vytváření virtuálních počítačů se sdíleným ZRS diskem připojeným k virtuálním počítačům v různých zónách

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Vytvoření sady škálování virtuálních počítačů s ZRS disky

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>Další kroky

- Pomocí těchto ukázkových [Azure Resource Manager šablon můžete vytvořit virtuální počítač s disky s ZRS](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks).