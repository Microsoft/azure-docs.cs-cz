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
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1b1c7192eb8389d3ad3a1c7c935d9c7e2d8769a9
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359914"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak změnit licenční model virtuálního počítače s SQL serverem v Azure
Tento článek popisuje, jak změnit licenční model pro virtuální počítače s SQL serverem v Azure pomocí nového poskytovatele prostředků SQL VM - **Microsoft.SqlVirtualMachine**. Existují dva licenční modely pro virtuální počítač (VM), který je hostitelem SQL serveru – platby za využití a používání vlastní licence (BYOL). A teď se pomocí Powershellu nebo rozhraní příkazového řádku Azure, můžete upravit který licenční model virtuálního počítače s SQL Server používá. 

**Platby za využití** modelu znamená, že za sekundu náklady na provozování virtuálního počítače Azure zahrnují cenu licence SQL serveru.

**Bring your-používání vlastní licence** modelu se také označuje jako [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), a to umožňuje používat vlastní licenci na SQL Server v případě virtuálních počítačů s SQL serverem. Další informace o cenách najdete v tématu [cenové Průvodce virtuálního počítače s SQL serverem](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Přepínání mezi těmito dvěma modely licence s sebou nese náklady **bez výpadků**, neprovede restartování virtuálního počítače, přidá **bez dalších poplatků** (ve skutečnosti aktivace AHB *snižuje* nákladů) a je **okamžitou platností**. 

## <a name="prerequisites"></a>Požadavky
Použití zprostředkovatele prostředků virtuálního počítače s SQL se vyžaduje rozšíření SQL IaaS. V důsledku toho pokud chcete pokračovat s využitím poskytovatele prostředků virtuálního počítače s SQL, budete potřebovat následující:
- [Předplatného Azure](https://azure.microsoft.com/free/).
- A [virtuálního počítače s SQL serverem](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) s [rozšíření SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) nainstalované. 


## <a name="register-existing-sql-server-vm-with-new-resource-provider"></a>Registrace existující SQL serveru virtuálního počítače s poskytovatelem prostředků pro nové
Umožňuje přepnout mezi licenční modely je funkce poskytované nového poskytovatele prostředků SQL VM (Microsoft.SqlVirtualMachine). Virtuální počítače SQL serveru nasadit po prosince 2018 jsou automaticky zaregistrované u nového poskytovatele prostředků. Stávající virtuální počítače nasazené před tímto datem je však nutné se ručně zaregistrované u poskytovatele prostředků, aby je bylo možné přepnout jejich licenční model. 




  > Pokud odstraníte váš prostředek virtuálního počítače s SQL, přejdete zpět na pevně zakódované licence nastavení bitové kopie. 


### <a name="powershell"></a>PowerShell

Následující fragment kódu se můžete připojit k Azure a ověřte, které ID předplatného, které používáte. 
```PowerShell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>
```

Následující fragment kódu nejprve zaregistrovat nového poskytovatele prostředků SQL pro vaše předplatné a pak zaregistruje existujícího virtuálního počítače SQL serveru pomocí nového poskytovatele prostředků. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine


# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Portál
Můžete také registraci nového poskytovatele prostředků virtuálního počítače SQL pomocí portálu. Chcete proto postupujte podle těchto kroků:
1. Otevřete na webu Azure portal a přejděte do **všechny služby**. 
1. Přejděte do **předplatná** a vyberte předplatné, které vás zajímají.  
1. V **předplatná** okno, přejděte na **poskytovatele prostředků**. 
1. Typ `sql` ve filtru, abyste vyvolali se zprostředkovatelé prostředků souvisejících s SQL. 
1. Vyberte buď *zaregistrovat*, *přeregistrovat*, nebo *Unregister* pro **Microsoft.SqlVirtualMachine** poskytovateli v závislosti na vaší požadovanou akci. 

  ![Upravit poskytovatele](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>Použití prostředí PowerShell 
Chcete-li změnit váš licenční model můžete použít PowerShell.  Ujistěte se, že váš virtuální počítač SQL Server již byl zaregistrován u nového poskytovatele prostředků SQL před přepnutím licenčního modelu. 

Následující fragment kódu přepne licencí platit za použití modelu BYOL (nebo pomocí programu zvýhodněné hybridní využití Azure): 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
``` 

Následující fragment kódu přepne na platby za využití BYOL model:
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Chcete-li přepnout mezi licence, musí používat nového poskytovatele prostředků virtuálního počítače s SQL. Pokud se pokusíte spustit tyto příkazy před zaregistrováním virtuálního počítače s SQL serverem pomocí nového poskytovatele, může dojít k této chybě: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Pokud se zobrazí tato chyba, [registraci nového poskytovatele prostředků virtuálního počítače s SQL serverem](#register-existing-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Použití Azure CLI
Chcete-li změnit váš licenční model můžete použít rozhraní příkazového řádku Azure.  Ujistěte se, že váš virtuální počítač SQL Server již byl zaregistrován u nového poskytovatele prostředků SQL před přepnutím licenčního modelu. 

Následující fragment kódu přepne licencí platit za použití modelu BYOL (nebo pomocí programu zvýhodněné hybridní využití Azure):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

Následující fragment kódu přepne na platby za využití BYOL model: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Chcete-li přepnout mezi licence, musí používat nového poskytovatele prostředků virtuálního počítače s SQL. Pokud se pokusíte spustit tyto příkazy před zaregistrováním virtuálního počítače s SQL serverem pomocí nového poskytovatele, může dojít k této chybě: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Pokud se zobrazí tato chyba, [registraci nového poskytovatele prostředků virtuálního počítače s SQL serverem](#register-existing-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Zobrazit aktuální licencování 

Následující fragment kódu umožňuje zobrazit vaše aktuální model správy licencí pro váš virtuální počítač s SQL serverem. 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
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

`Set-AzureRmResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Chcete-li vyřešit tuto chybu, Odkomentujte tyto řádky ve výše uvedených fragmentu kódu Powershellu při přepnutí váš licenční model: 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Použijte následující kód k ověření verze Azure Powershellu:

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na nejčastější dotazy týkající se virtuálních počítačů Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na virtuálním počítači s Windows, doprovodné materiály k cenám](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server na poznámky k verzi virtuálního počítače Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


