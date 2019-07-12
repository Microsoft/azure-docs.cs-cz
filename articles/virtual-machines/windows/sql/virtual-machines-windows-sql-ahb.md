---
title: Jak chcete-li změnit licenční model virtuálního počítače s SQL serverem v Azure
description: Zjistěte, jak přepnout licencování pro virtuální počítač SQL v Azure z "s průběžnými platbami" k "přineste si – používání vlastní licence" pomocí zvýhodněné hybridní využití Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786765"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak změnit licenční model virtuálního počítače s SQL serverem v Azure
Tento článek popisuje, jak změnit licenční model pro virtuální počítače s SQL serverem v Azure pomocí nového poskytovatele prostředků SQL VM - **Microsoft.SqlVirtualMachine**.

Existují dva licenční modely pro virtuální počítač (VM) hostitelem systému SQL Server – s průběžnými platbami a Azure Hybrid Benefit (AHB). A teď se pomocí webu Azure portal, rozhraní příkazového řádku Azure nebo PowerShell můžete změnit licenční model virtuálního počítače s SQL serverem. 

**s průběžnými platbami** modelu (PAYG) znamená, že za sekundu náklady na provozování virtuálního počítače Azure zahrnují cenu licence SQL serveru.
[Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) umožňuje používat vlastní licenci na SQL Server v případě virtuálních počítačů s SQL serverem. 

Microsoft Azure Hybrid Benefit pro SQL Server umožňuje používat licence SQL serveru s programem Software Assurance ("licenčních kvalifikovanou") na Azure Virtual Machines. S programem Azure Hybrid Benefit pro SQL Server zákazníkům nebudou fakturované využívání licenci na SQL Server na virtuálním počítači, ale přesto musíte zaplatit za náklady základní cloudové výpočetní (to znamená, že se základní sazba), úložiště a zálohám, jakož i vstupně-výstupní operace spojené s jejich u se služeb (podle vhodnosti).

Podle podmínky produktu společnosti Microsoft "zákazníkům musíte uvést, že používáte Azure SQL Database (spravovaná Instance, elastický fond a izolované databáze), Azure Data Factory, SQL Server Integration Services nebo SQL Server Virtual Machines v Azure Hybrid Výhoda pro SQL Server při konfiguraci úlohy v Azure."

K označení použití zvýhodněné hybridní využití Azure pro SQL Server na virtuálním počítači Azure a být kompatibilní, existují tři možnosti:

1. Zřízení virtuálního počítače pomocí image BYOL SQL serveru na webu Azure Marketplace, k dispozici jenom pro zákazníky se smlouvou Enterprise.
1. Zřízení virtuálního počítače pomocí webu Azure Marketplace image SQL serveru průběžné platby a aktivovat AHB.
1. Samoobslužné ručně nainstalovat systém SQL Server na virtuálním počítači Azure [registraci jejich virtuální počítač s SQL serverem](virtual-machines-windows-sql-register-with-resource-provider.md) a aktivovat AHB.

Typ licence SQL serveru je nastavena, když virtuální počítač je zřízený a můžete kdykoli později změnit. Přepínání mezi modelem licencování s sebou nese náklady **bez výpadků**, neprovede restartování virtuálního počítače, přidá **bez dalších poplatků** (ve skutečnosti aktivace AHB *snižuje* nákladů) a je **okamžitou platností**. 

## <a name="prerequisites"></a>Požadavky

Použití zprostředkovatele prostředků virtuálního počítače s SQL se vyžaduje rozšíření SQL IaaS. V důsledku toho pokud chcete pokračovat s využitím poskytovatele prostředků virtuálního počítače s SQL, budete potřebovat následující:
- [Předplatného Azure](https://azure.microsoft.com/free/).
- [Program software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A [virtuálního počítače s SQL serverem](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zaregistrované [poskytovatele prostředků SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md) nainstalované. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Změna licence pro virtuální počítače, které jsou už zaregistrované u poskytovatele prostředků 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Můžete změnit licenční model přímo z portálu. 

1. Otevřít [webu Azure portal](https://portal.azure.com) a spustit [prostředků virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) pro virtuální počítač s SQL serverem. 
1. Vyberte **nakonfigurovat** pod **nastavení**. 
1. Vyberte **zvýhodněné hybridní využití Azure** možnost a zaškrtnutím políčka potvrďte, že máte licenci SQL Server s programem Software Assurance. 
1. Vyberte **použít** v dolní části **konfigurovat** stránky. 

![AHB portálu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Chcete-li změnit váš licenční model můžete použít rozhraní příkazového řádku Azure.  

Následující fragment kódu přepne s průběžnými platbami licenční model BYOL (nebo pomocí programu zvýhodněné hybridní využití Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Následující fragment kódu přepne model přineste si – vlastní licenci na průběžné platby: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Chcete-li změnit váš licenční model můžete použít PowerShell.

Následující fragment kódu přepne s průběžnými platbami licenční model BYOL (nebo pomocí programu zvýhodněné hybridní využití Azure):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

Následující fragment kódu přepne na průběžné platby BYOL model:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Změna licence pro virtuální počítače není zaregistrované u poskytovatele prostředků

Zřízení virtuálního počítače s SQL serverem z imagí z Marketplace průběžné platby Azure typ licence SQL bude průběžné platby. Zřízení virtuálního počítače SQL serveru pomocí BYOL image z Azure Marketplace typ licence bude AHUB. Zřídit všechny virtuální počítače s SQL serverem z výchozí hodnoty (PAYG) nebo Image BYOL Azure Marketplace se automaticky zaregistruje poskytovatele prostředků virtuálního počítače s SQL, aby si mohli změnit [typ licence](#change-license-for-vms-already-registered-with-resource-provider)

Máte nárok jenom na vlastní instalace systému SQL Server na virtuálním počítači Azure prostřednictvím programu zvýhodněné hybridní využití Azure a měli byste [registrovat tyto virtuální počítače s poskytovatelem prostředků pro virtuální počítač SQL](virtual-machines-windows-sql-register-with-resource-provider.md) tak, že nastavíte jako AHB udávajících AHB využití podle licence SQL serveru Podmínky produktu společnosti Microsoft.

Typ licence virtuálního počítače s SQL serverem jako průběžné platby nebo AHB lze změnit jen v případě virtuálního počítače SQL je zaregistrované u poskytovatele prostředků virtuálního počítače SQL; a všechny virtuální počítače SQL by se měly registrovat poskytovatele prostředků SQL virtuálních počítačů kvůli shodě s licencí.

## <a name="remarks"></a>Poznámky

 - Zákazníci Azure Cloud Solution (Partner CSP) můžete využít zvýhodněné hybridní využití Azure tak, že nejprve nasazení virtuálního počítače s průběžnými platbami a pokud mají aktivním programem SA jeho převodu do přineste svůj – používání vlastní licence.
 - Pokud odstraníte váš prostředek virtuálního počítače s SQL serverem, přejdete zpět na pevně zakódované licence nastavení bitové kopie. 
  - Umožňuje změnit licenční model je funkce poskytovatele prostředků virtuálního počítače s SQL. Nasazení imagí marketplace na webu Azure portal automaticky zaregistruje poskytovatele prostředků virtuálního počítače s SQL serverem. Ale zákazníci, kteří jsou vlastní instalace systému SQL Server bude nutné ručně [registraci jejich virtuální počítač s SQL serverem](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Přidání virtuálního počítače s SQL serverem na skupinu dostupnosti vyžaduje opětovné vytvoření virtuálního počítače. Jako takové, všechny virtuální počítače přidat do skupiny dostupnosti sady vrátí zpátky na výchozí typ licence s průběžnými platbami a AHB muset znovu povolit. 


## <a name="limitations"></a>Omezení

 - Změna licenčního modelu je dostupná jenom pro zákazníky s programem software assurance.
 - Změna licenčního modelu je podporována pouze pro edice standard a enterprise systému SQL Server. Změny licencí pro Express, Web a pro vývojáře se nepodporují. 
 - Změna licenčního modelu je podporována pouze pro virtuální počítače nasazené pomocí modelu Resource Manager. Virtuální počítače nasazené pomocí klasického modelu, nejsou podporovány. 
 - Změna licenčního modelu je povolena pouze pro veřejné cloudové zařízení.
 - Změna licenčního modelu je podporována pouze na virtuálních počítačích, které mají jednu síťovou kartu (síťové rozhraní). Na virtuálních počítačích, které mají více než jedním síťovým rozhraním, byste měli nejprve odebrat jedné síťové karty (pomocí webu Azure portal) před dalším pokusem postup. V opačném případě bude narazíte na chybu podobnou této: `The virtual machine '\<vmname\>' has more than one NIC associated.` I když je možné přidat síťové rozhraní zpět k virtuálnímu počítači po změně licencování, se operace provádí prostřednictvím stránky konfigurace SQL na webu Azure Portal, jako jsou automatické opravy a zálohování, nelze nadále považovat podporována.


## <a name="known-errors"></a>Známých chyb

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Prostředek "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<; resource-group >" ve skupině prostředků "\<; resource-group >" nebyl nalezen. Vlastnost 'sqlServerLicenseType' nebyl nalezen v tomto objektu. Ověřte, že vlastnost existuje a je možné nastavit.
Této chybě dochází při pokusu o změnu licenčního modelu na virtuálním počítači SQL serveru, který není zaregistrovaný u poskytovatele prostředků virtuálního počítače s SQL. Musíte se zaregistrovat poskytovatele prostředků pro vaše [předplatné](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)a pak zaregistrujte virtuální počítač s SQL serverem pomocí SQL [poskytovatele prostředků](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Nelze ověřit argument u parametru "Sku.
K této chybě může dojít při pokusu o změnu váš licenční model virtuálního počítače s SQL serverem, při použití Azure Powershellu > 4.0: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Chcete-li vyřešit tuto chybu, Odkomentujte tyto řádky ve výše uvedených fragmentu kódu Powershellu při přepnutí váš licenční model:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Použijte následující kód k ověření verze Azure Powershellu:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na nejčastější dotazy týkající se virtuálních počítačů Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na virtuálním počítači s Windows, doprovodné materiály k cenám](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server na poznámky k verzi virtuálního počítače Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


