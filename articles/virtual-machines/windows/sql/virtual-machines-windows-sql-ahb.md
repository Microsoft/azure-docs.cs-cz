---
title: Jak změnit licenční model virtuálního počítače SQL v Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: cd784163047f4fe15fde719ce56aba64eed60dd2
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336981"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Jak změnit licenční model virtuálního počítače s SQL serverem v Azure
Tento článek popisuje, jak změnit licenční model pro virtuální počítače s SQL serverem v Azure pomocí nového poskytovatele prostředků SQL - **Microsoft.SqlVirtualMachine**. Existují dva licenční modely pro virtuální počítač (VM), který je hostitelem SQL serveru – platby za využití a používání vlastní licence (BYOL). A teď se pomocí Powershellu nebo rozhraní příkazového řádku Azure, můžete upravit který licenční model virtuálního počítače s SQL využívá. 

**Platby za využití** modelu znamená, že za sekundu náklady na provozování virtuálního počítače Azure zahrnují cenu licence SQL serveru.

**Bring your-používání vlastní licence** modelu se také označuje jako [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), a to umožňuje používat vlastní licenci na SQL Server v případě virtuálních počítačů s SQL serverem. Další informace o cenách najdete v tématu [cenové Průvodce virtuálního počítače SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Přepínání mezi těmito dvěma modely licence s sebou nese náklady **bez výpadků**, neprovede restartování virtuálního počítače, přidá **bez dalších poplatků** (ve skutečnosti aktivace AHB snižuje náklady) a je **okamžitou platností**. 


## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Registrace existujícího virtuálního počítače SQL pomocí nového poskytovatele prostředků
Umožňuje přepnout mezi licenční modely je funkce poskytované nového poskytovatele prostředků SQL VM (Microsoft.SqlVirtualMachine). V tuto chvíli bude moci přepnout váš licenční model, je nejprve potřeba registraci nového poskytovatele do vašeho předplatného a pak zaregistrujte existujícího virtuálního počítače s poskytovatelem prostředků pro nový virtuální počítač s SQL. Využívat poskytovatele prostředků virtuálního počítače s SQL, musíte také nainstalovat rozšíření SQL IaaS. To vám umožní zaregistrovat virtuální pevný disk, který byl nasazen virtuální počítač. Další informace najdete v tématu [rozšíření SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

  >[!IMPORTANT]
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

Následující fragment kódu nejprve zaregistrovat nového poskytovatele prostředků SQL pro vaše předplatné a pak zaregistruje existujícího virtuálního počítače SQL pomocí nového poskytovatele prostředků. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your existing SQL VM with the new resource provider
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
Chcete-li změnit váš licenční model můžete použít PowerShell.  Ujistěte se, že váš virtuální počítač SQL již byl zaregistrován u nového poskytovatele prostředků SQL před přepnutím licenčního modelu. 

Následující fragment kódu přepne licencí platit za použití modelu BYOL (nebo pomocí programu zvýhodněné hybridní využití Azure): 
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

Následující fragment kódu přepne na platby za využití BYOL model:
```PowerShell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Chcete-li přepnout mezi licence, musí používat nového poskytovatele prostředků virtuálního počítače s SQL. Pokud se pokusíte spustit tyto příkazy před zaregistrováním virtuálního počítače s SQL s novým poskytovatelem, může dojít k této chybě: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Pokud se zobrazí tato chyba, [registraci nového poskytovatele prostředků virtuálního počítače s SQL](#register-existing-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Použití Azure CLI
Chcete-li změnit váš licenční model můžete použít rozhraní příkazového řádku Azure.  Ujistěte se, že váš virtuální počítač SQL již byl zaregistrován u nového poskytovatele prostředků SQL před přepnutím licenčního modelu. 

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
  >Chcete-li přepnout mezi licence, musí používat nového poskytovatele prostředků virtuálního počítače s SQL. Pokud se pokusíte spustit tyto příkazy před zaregistrováním virtuálního počítače s SQL s novým poskytovatelem, může dojít k této chybě: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Pokud se zobrazí tato chyba, [registraci nového poskytovatele prostředků virtuálního počítače s SQL](#register-existing-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Zobrazit aktuální licencování 

Následující fragment kódu umožňuje zobrazit vaše aktuální model správy licencí pro váš virtuální počítač SQL. 

```PowerShell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na nejčastější dotazy týkající se virtuálních počítačů Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na virtuálním počítači s Windows, doprovodné materiály k cenám](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server na poznámky k verzi virtuálního počítače Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


