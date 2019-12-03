---
title: Změna modelu licencí pro virtuální počítač s SQL Server v Azure
description: Přečtěte si, jak přepínat licencování pro SQL Server virtuální počítač v Azure z průběžných plateb podle aktuálního využití Zvýhodněné hybridní využití Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 06d7b7abe7741c465f3d40a90340e03b2c24f258
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707503"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Změna modelu licencí pro virtuální počítač s SQL Server v Azure
Tento článek popisuje, jak změnit model licencí pro SQL Server virtuální počítač (VM) v Azure pomocí nového poskytovatele prostředků SQL VM, **Microsoft. SqlVirtualMachine**.

Existují dva licenční modely pro virtuální počítač, který je hostitelem SQL Server: průběžné platby a Zvýhodněné hybridní využití Azure. Model licencí vašeho SQL Server virtuálního počítače můžete upravit pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu. 

Model s průběžnými platbami znamená, že náklady na spuštění virtuálního počítače Azure za sekundu zahrnují náklady na SQL Server licenci.
[Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) umožňuje používat vlastní SQL Server licenci s virtuálním počítačem, na kterém běží SQL Server. 

Zvýhodněné hybridní využití Azure umožňuje použití licencí SQL Server se Software Assurance ("kvalifikovaná licence") na virtuálních počítačích Azure. U Zvýhodněné hybridní využití Azure se zákazníkům neúčtují použití licence SQL Server na virtuálním počítači. Ale pořád musí platit za náklady na základní cloudové výpočetní prostředky (tj. základní sazba), úložiště a zálohy. Musí také platit za vstupně-výstupní operace spojené s jejich používáním služeb (podle potřeby).

Podle podmínek produktu společnosti Microsoft: "zákazníci musí značit, že používají Azure SQL Database (spravované instance, Elastický fond a Izolovaná databáze), Azure Data Factory, služba SSIS (SQL Server Integration Services) nebo SQL Server Virtual Machines v rámci Azure Zvýhodněné hybridní využití pro SQL Server při konfiguraci úloh v Azure

Chcete-li určit použití Zvýhodněné hybridní využití Azure pro SQL Server na virtuálním počítači Azure a kompatibilní, máte tři možnosti:

- Zřízení virtuálního počítače pomocí SQL Server image s vlastní licencí z Azure Marketplace Tato možnost je k dispozici pouze pro zákazníky, kteří mají smlouva Enterprise.
- Zřídit virtuální počítač pomocí SQL Server image s průběžnými platbami z Azure Marketplace a aktivovat Zvýhodněné hybridní využití Azure.
- Samoobslužná instalace SQL Server na virtuálním počítači Azure, ruční [registrace u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md)a aktivace zvýhodněné hybridní využití Azure.

Typ licence SQL Server se nastaví při zřizování virtuálního počítače. Můžete ho kdykoli změnit. Přepínání modelů licencí nepředstavuje žádné výpadky, nerestartuje virtuální počítač ani službu SQL Server, nepřidá žádné další náklady a okamžitě se projeví. Aktivace Zvýhodněné hybridní využití Azure *snižuje* náklady.

## <a name="prerequisites"></a>Předpoklady

Změna modelu licencování virtuálního počítače s SQL Server má následující požadavky: 

- [Předplatné Azure](https://azure.microsoft.com/free/).
- [SQL Server virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zaregistrovaný u [poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) je požadavek na využití [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>Změna licence pro virtuální počítače, které jsou už zaregistrované u poskytovatele prostředků 

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Licenční model můžete změnit přímo z portálu: 

1. Otevřete [Azure Portal](https://portal.azure.com) a otevřete [prostředek virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) pro váš virtuální počítač s SQL Server. 
1. V části **Nastavení**vyberte **Konfigurovat** . 
1. Vyberte možnost **zvýhodněné hybridní využití Azure** a zaškrtnutím políčka potvrďte, že máte licenci SQL Server se Software Assurance. 
1. V dolní části stránky **Konfigurace** vyberte **použít** . 

![Zvýhodněné hybridní využití Azure na portálu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Ke změně modelu licence můžete použít rozhraní příkazového řádku Azure CLI.  

Následující fragment kódu převede svůj licenční model s průběžnými platbami na vlastní licenci (nebo pomocí Zvýhodněné hybridní využití Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Následující fragment kódu přepíná model vlastní licence na průběžné platby: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Pro změnu modelu licence můžete použít PowerShell.

Následující fragment kódu převede svůj licenční model s průběžnými platbami na vlastní licenci (nebo pomocí Zvýhodněné hybridní využití Azure):

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

Následující fragment kódu přepíná model vlastní licence na průběžné platby:

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

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>Změna licence pro virtuální počítače, které nejsou zaregistrované u poskytovatele prostředků

Pokud jste zřídili SQL Server virtuální počítač z Azure Marketplace imagí s průběžnými platbami, pak typ licence SQL Server bude průběžně platit. Pokud jste zřídili SQL Server virtuální počítač s použitím image s vlastní licencí z Azure Marketplace, bude typ licence AHUB. Všechny SQL Server virtuální počítače zřízené ve výchozím nastavení (průběžné platby) nebo vlastní licence Azure Marketplace se budou automaticky registrovat u poskytovatele prostředků virtuálního počítače SQL, aby mohli změnit [typ licence](#change-the-license-for-vms-already-registered-with-the-resource-provider).

Máte nárok jenom na samoobslužnou instalaci SQL Server na virtuálním počítači Azure prostřednictvím Zvýhodněné hybridní využití Azure. [Tyto virtuální počítače byste měli zaregistrovat u poskytovatele prostředků virtuálních počítačů SQL](virtual-machines-windows-sql-register-with-resource-provider.md) , a to tak, že nastavíte licenci SQL Server jako zvýhodněné hybridní využití Azure, abyste označili zvýhodněné hybridní využití Azure využití podle podmínek produktu společnosti Microsoft.

Typ licence SQL Server virtuálního počítače můžete změnit jako průběžné platby nebo Zvýhodněné hybridní využití Azure, jenom když je virtuální počítač SQL Server zaregistrovaný u poskytovatele prostředků SQL VM.

## <a name="remarks"></a>Poznámky

- Zákazníci Azure Cloud Solution Provider (CSP) můžou pomocí Zvýhodněné hybridní využití Azure nejdřív nasadit virtuální počítač s průběžnými platbami a pak ho převést na vlastní licenci, pokud mají aktivní program Software Assurance.
- Pokud SQL Server prostředek virtuálního počítače, vrátíte se k pevně zakódovanému nastavení licence image. 
- Možnost změnit licenční model je funkcí poskytovatele prostředků virtuálního počítače SQL. Nasazení bitové kopie Azure Marketplace pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s poskytovatelem prostředků. Zákazníci, kteří si SQL Server vlastní instalaci, ale budou muset [zaregistrovat SQL Server virtuální počítač](virtual-machines-windows-sql-register-with-resource-provider.md)ručně. 
- Přidání virtuálního počítače SQL Server do skupiny dostupnosti vyžaduje opětovné vytvoření virtuálního počítače. V takovém případě se všechny virtuální počítače přidané do skupiny dostupnosti vrátí k výchozímu typu licence s průběžnými platbami. Zvýhodněné hybridní využití Azure bude nutné znovu povolit. 


## <a name="limitations"></a>Omezení

- Změna licenčního modelu je dostupná jenom pro zákazníky, kteří mají Software Assurance.
- Změna licenčního modelu je podporovaná jenom pro edice Standard a Enterprise systému SQL Server. Změny licencí pro Express, web a vývojáře nejsou podporovány. 
- Změna licenčního modelu je podporovaná jenom pro virtuální počítače nasazené pomocí modelu Azure Resource Manager. Virtuální počítače nasazené prostřednictvím klasického modelu se nepodporují. Virtuální počítač můžete migrovat z modelu Classic na model Správce prostředků a zaregistrovat ho u poskytovatele prostředků virtuálního počítače SQL. Po zaregistrování virtuálního počítače u poskytovatele prostředků virtuálního počítače SQL budou na virtuálním počítači k dispozici změny modelu licencí.
- Změna licenčního modelu je povolená jenom pro instalace veřejného cloudu.
- Změna licenčního modelu je podporovaná jenom na virtuálních počítačích, které mají jednu síťovou kartu (síťové rozhraní). Na virtuálních počítačích, které mají více než jednu síťovou kartu, před provedením postupu byste nejdřív měli odebrat jednu ze síťových karet (pomocí Azure Portal). V opačném případě se zobrazí chybová zpráva podobná následující: 
   
  `The virtual machine '\<vmname\>' has more than one NIC associated.` 
   
  I když po změně modelu licencí budete moct síťové rozhraní přidat zpátky do virtuálního počítače, operace provedené prostřednictvím konfigurační stránky SQL Server v Azure Portal, jako jsou automatické opravy a zálohování, se už nepovažují za podporované.

## <a name="known-errors"></a>Známé chyby

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Prostředek "Microsoft. SqlVirtualMachine/SqlVirtualMachines/\<Resource-Group >" v části Skupina prostředků "\<> Resource-Group" nebyl nalezen.
K této chybě dojde, když se pokusíte změnit model licence na virtuálním počítači s SQL Server, který nebyl zaregistrován u poskytovatele prostředků SQL VM:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Budete muset zaregistrovat předplatné u poskytovatele prostředků a pak [zaregistrovat svůj SQL Server virtuální počítač s poskytovatelem prostředků](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Nejde ověřit argument u parametru SKU.
K této chybě může dojít při pokusu o změnu modelu licence SQL Server VM pomocí Azure PowerShell verzí novějších než 4,0:

`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Pokud chcete tuto chybu vyřešit, odkomentujte tyto řádky výše zmíněného fragmentu kódu PowerShellu při přepínání vašeho licenčního modelu:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
K ověření verze Azure PowerShell použijte následující kód:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


