---
title: Jak chcete-li změnit licenční model virtuálního počítače s SQL serverem v Azure | Dokumentace Microsoftu
description: Zjistěte, jak přepnout licencování pro virtuální počítač SQL v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
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
ms.openlocfilehash: eded5b2b8715d6a09f7c1a50012b262cec17bfb1
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762748"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak změnit licenční model virtuálního počítače s SQL serverem v Azure
Tento článek popisuje, jak změnit licenční model pro virtuální počítače s SQL serverem v Azure pomocí nového poskytovatele prostředků SQL VM - **Microsoft.SqlVirtualMachine**. Existují dva licenční modely pro virtuální počítač (VM), který je hostitelem SQL serveru – s průběžnými platbami a používání vlastní licence (BYOL). A teď se pomocí Powershellu nebo rozhraní příkazového řádku Azure, můžete upravit který licenční model virtuálního počítače s SQL Server používá. 

**s průběžnými platbami** modelu (PAYG) znamená, že za sekundu náklady na provozování virtuálního počítače Azure zahrnují cenu licence SQL serveru.

**Přineste svůj – používání vlastní licence** model (BYOL) se také označuje jako [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), a to umožňuje používat vlastní licenci na SQL Server v případě virtuálních počítačů s SQL serverem. Další informace o cenách najdete v tématu [cenové Průvodce virtuálního počítače s SQL serverem](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Přepínání mezi těmito dvěma modely licence s sebou nese náklady **bez výpadků**, neprovede restartování virtuálního počítače, přidá **bez dalších poplatků** (ve skutečnosti aktivace AHB *snižuje* nákladů) a je **okamžitou platností**. 


## <a name="remarks"></a>Poznámky

 - Možnost převést licenční model je aktuálně k dispozici pouze v případě, že začnete s imagí virtuálního počítače s SQL Serverem s průběžnými platbami. Pokud začnete s imagí s použitím vlastní licence z portálu, nebudete moct tuto image převést na průběžné platby.
 - Zákazníky CSP se můžou využívat výhody AHB tak, že nejprve nasazení virtuálního počítače s průběžnými platbami a jeho převodu do přineste svůj – používání vlastní licence. 
 - Tato schopnost je aktuálně povolena pouze pro veřejné cloudové zařízení.
 - Při registraci vlastní image virtuálního počítače s SQL serverem s poskytovatelem prostředků, zadejte typ licence = "AHUB". Opuštění licence zadejte jako prázdné nebo zadání "PAYG" způsobí, že registrace selže. 

## <a name="prerequisites"></a>Požadavky
Použití zprostředkovatele prostředků virtuálního počítače s SQL se vyžaduje rozšíření SQL IaaS. V důsledku toho pokud chcete pokračovat s využitím poskytovatele prostředků virtuálního počítače s SQL, budete potřebovat následující:
- [Předplatného Azure](https://azure.microsoft.com/free/).
- [Program software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A *s průběžnými platbami* [virtuálního počítače s SQL serverem](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) s [rozšíření SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) nainstalované. 


## <a name="register-sql-resource-provider-to-your-subscription"></a>Registrace poskytovatele prostředků SQL k vašemu předplatnému 

Umožňuje přepnout mezi licenční modely je funkce poskytované nového poskytovatele prostředků SQL VM (Microsoft.SqlVirtualMachine). Virtuální počítače SQL serveru nasadit po prosince 2018 jsou automaticky zaregistrované u nového poskytovatele prostředků. Stávající virtuální počítače nasazené před tímto datem je však nutné se ručně zaregistrované u poskytovatele prostředků, aby je bylo možné přepnout jejich licenční model. 

  > [!NOTE] 
  > Pokud odstraníte váš prostředek virtuálního počítače s SQL serverem, přejdete zpět na pevně zakódované licence nastavení bitové kopie. 

Zaregistrovat poskytovatele prostředků SQL virtuálního počítače s SQL serverem, zaregistrujte poskytovatele prostředků ke svému předplatnému. Můžete provést pomocí webu Azure portal, Azure CLI nebo Powershellu. 

### <a name="with-the-azure-portal"></a>S využitím webu Azure Portal
Následující postup k registraci poskytovatele prostředků SQL ke svému předplatnému Azure pomocí webu Azure portal. 

1. Otevřete na webu Azure portal a přejděte do **všechny služby**. 
1. Přejděte do **předplatná** a vyberte předplatné, které vás zajímají.  
1. V **předplatná** okno, přejděte na **poskytovatelů prostředků**. 
1. Typ `sql` ve filtru, abyste vyvolali se zprostředkovatelé prostředků souvisejících s SQL. 
1. Vyberte buď *zaregistrovat*, *přeregistrovat*, nebo *Unregister* pro **Microsoft.SqlVirtualMachine** poskytovateli v závislosti na vaší požadovanou akci. 

   ![Upravit poskytovatele](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure
Následující fragment kódu k registraci poskytovatele prostředků SQL ke svému předplatnému Azure. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="with-powershell"></a>S využitím PowerShellu
Následující fragment kódu k registraci poskytovatele prostředků SQL ke svému předplatnému Azure. 

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```


## <a name="register-sql-server-vm-with-sql-resource-provider"></a>Virtuální počítač s SQL serverem zaregistrovat poskytovatele prostředků SQL
Po registraci poskytovatele prostředků SQL do vašeho předplatného, pak je možné zaregistrovat virtuální počítač s SQL serverem s poskytovatelem prostředků. Můžete provést pomocí rozhraní příkazového řádku Azure a Powershellu. 

### <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure

Registraci virtuálního počítače SQL serverem pomocí Azure CLI následujícím fragmentem kódu: 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```

### <a name="with-powershell"></a>S využitím PowerShellu

Registraci virtuálního počítače SQL serverem pomocí Powershellu pomocí následujícího fragmentu kódu: 

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

## <a name="change-licensing-model"></a>Změnit licenční model

Jakmile se váš virtuální počítač SQL Server byl zaregistrován u poskytovatele prostředků, můžete změnit licenční model pomocí webu Azure portal, rozhraní příkazového řádku Azure nebo PowerShell. 

### <a name="with-the-azure-portal"></a>S využitím webu Azure Portal

Můžete změnit licenční model přímo z portálu. 

1. Přejděte k virtuálnímu počítači SQL serveru v rámci [webu Azure portal](https://portal.azure.com). 
1. Vyberte **konfigurace systému SQL Server** v **nastavení** podokně. 
1. Vyberte **upravit** v **licence SQL serveru** podokno, upravte licenci. 

![AHB portálu](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Tato možnost není k dispozici pro Image přineste svůj – používání vlastní licence. 

### <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure

Chcete-li změnit váš licenční model můžete použít rozhraní příkazového řádku Azure.  

Následující fragment kódu přepne s průběžnými platbami licenční model BYOL (nebo pomocí programu zvýhodněné hybridní využití Azure):

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Následující fragment kódu přepne na průběžné platby BYOL model: 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

### <a name="with-powershell"></a>S využitím PowerShellu 

Chcete-li změnit váš licenční model můžete použít PowerShell. 

Následující fragment kódu přepne s průběžnými platbami licenční model BYOL (nebo pomocí programu zvýhodněné hybridní využití Azure): 

```PowerShell
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

```PowerShell
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


## <a name="view-current-licensing"></a>Zobrazit aktuální licencování 

Následující fragment kódu umožňuje zobrazit vaše aktuální model správy licencí pro váš virtuální počítač s SQL serverem. 

```powershell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>Známých chyb

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>Na virtuálním počítači není nainstalovaná rozšíření SQL IaaS
Rozšíření SQL IaaS je potřebná předpokladem pro registraci virtuálního počítače s SQL serverem s poskytovatelem prostředků pro virtuální počítač s SQL. Pokud se pokusíte zaregistrovat virtuálního počítače s SQL Server před instalací rozšíření SQL IaaS, se setkají následující chybu:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Chcete-li vyřešit tento problém, nainstalujte rozšíření SQL IaaS před pokusem o registraci vašeho virtuálního počítače s SQL serverem. 

  > [!NOTE]
  > Instalace SQL IaaS rozšíření se restartovat službu systému SQL Server a by mělo být provedeno pouze během časového období údržby. Další informace najdete v tématu [instalace rozšíření SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Nelze ověřit argument u parametru "Sku.
K této chybě může dojít při pokusu o změnu váš licenční model virtuálního počítače s SQL serverem, při použití Azure Powershellu > 4.0:

`Set-AzResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Chcete-li vyřešit tuto chybu, Odkomentujte tyto řádky ve výše uvedených fragmentu kódu Powershellu při přepnutí váš licenční model: 
```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Použijte následující kód k ověření verze Azure Powershellu:

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Prostředek 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < resource-group >"ve skupině prostředků '< resource-group >' nebyl nalezen. Vlastnost 'sqlServerLicenseType' nebyl nalezen v tomto objektu. Ověřte, že vlastnost existuje a je možné nastavit.
Této chybě dochází při pokusu o změnu licenčního modelu na virtuálním počítači SQL serveru, který není zaregistrovaný u poskytovatele prostředků SQL. Musíte se zaregistrovat poskytovatele prostředků pro vaše [předplatné](#register-sql-resource-provider-to-your-subscription)a pak zaregistrujte virtuální počítač s SQL serverem pomocí SQL [poskytovatele prostředků](#register-sql-server-vm-with-sql-resource-provider). 

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na nejčastější dotazy týkající se virtuálních počítačů Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na virtuálním počítači s Windows, doprovodné materiály k cenám](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server na poznámky k verzi virtuálního počítače Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


