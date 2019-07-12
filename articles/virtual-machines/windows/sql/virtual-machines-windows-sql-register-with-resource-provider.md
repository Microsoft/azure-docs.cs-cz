---
title: Registrace virtuálních počítačů s SQL serverem v Azure s poskytovatelem prostředků pro virtuální počítač SQL | Dokumentace Microsoftu
description: Virtuální počítač s SQL serverem zaregistrujte poskytovatele prostředků virtuálního počítače SQL ke zlepšení možností správy.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7d7d04299c2d6c7a498701df9fb39c8990484a79
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807255"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrace virtuálních počítačů s SQL serverem v Azure s poskytovatelem prostředků pro virtuální počítač s SQL

Tento článek popisuje postup registrace serveru Azure SQL Server virtuální počítač (VM) s poskytovatelem prostředků pro virtuální počítač SQL. 

Nasazení imagí marketplace virtuálního počítače s SQL serverem na webu Azure portal automaticky zaregistruje poskytovatele prostředků virtuálního počítače SQL serveru. Ale pokud budete chtít vlastní instalace systému SQL Server na virtuálním počítači Azure místo výběru image z Azure Marketplace, pak byste měli zaregistrovat virtuální počítač s SQL serverem s poskytovatelem prostředků dnes pro:

-  **Dodržování předpisů** – dle podmínek produkt společnosti Microsoft, zákazníci, kteří používají [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) musíte uvést společnosti Microsoft při používání Azure Hybrid Benefit – a v takovém případě musíte zaregistrovat u poskytovatele prostředků virtuálního počítače s SQL. 

-  **Funkce výhody** – registrace vašeho virtuálního počítače s SQL serverem s poskytovatelem prostředků odemkne [automatické opravy](virtual-machines-windows-sql-automated-patching.md), [automatické zálohování](virtual-machines-windows-sql-automated-backup-v2.md), možnosti monitorování a správy, jakož i [licencování](virtual-machines-windows-sql-ahb.md) a [edition](virtual-machines-windows-sql-change-edition.md) flexibilitu. Dříve tyto byly k dispozici jenom pro Image virtuálního počítače s SQL serverem na Azure Marketplace.

Vlastní instalace systému SQL Server na Virtuálním počítači Azure nebo zřizování virtuálního počítače Azure z vlastního virtuálního pevného disku se systémem SQL Server je prostřednictvím zvýhodněné hybridní využití Azure pro SQL Server splňovat podmínku, že zákazníci ukazují, které používají na Microsoftu tak, že zaregistrujete pomocí prostředku virtuálního počítače SQL Zprostředkovatel. 

Využívat poskytovatele prostředků virtuálního počítače s SQL, musí také zaregistrujte poskytovatele prostředků SQL VM s vaším předplatným. To lze provést pomocí webu Azure portal, rozhraní příkazového řádku Azure a Powershellu. 

## <a name="prerequisites"></a>Požadavky

Zaregistrovat poskytovatele prostředků virtuálního počítače s SQL serverem, budete potřebovat následující: 

- [Předplatného Azure](https://azure.microsoft.com/free/).
- A [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Azure CLI](/cli/azure/install-azure-cli) a [Powershellu](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Zaregistrovat poskytovatele prostředků virtuálního počítače SQL
Pokud [rozšíření SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) je již nainstalována na virtuálním počítači, poté registraci u poskytovatele prostředků virtuálního počítače s SQL je jednoduše vytvořit metadata prostředku typu Microsoft.SqlVirtualMachine/SqlVirtualMachines. Tady je fragment kódu k registraci u poskytovatele prostředků virtuálního počítače s SQL, pokud rozšíření SQL IaaS je už nainstalovaná na virtuálním počítači. Je potřeba zadat typ požadovaného při registraci u poskytovatele prostředků virtuálního počítače SQL jako buď licenci na SQL Server "PAYG ' nebo"AHUB". 

Registraci virtuálního počítače SQL serverem pomocí Powershellu pomocí následujícího fragmentu kódu:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Pokud na virtuálním počítači není nainstalovaná rozšíření SQL IaaS, pak můžete zaregistrovat u poskytovatele prostředků virtuálního počítače SQL tak, že zadáte zjednodušeném režimu správy SQL. Ve zjednodušeném režimu správy SQL, virtuální počítač SQL poskytovatele prostředků se automaticky nainstalovat rozšíření SQL IaaS v [zjednodušeném režimu](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) a ověření serveru SQL Server instance metadata; tím se restart služby systému SQL Server. Je potřeba zadat typ požadovaného při registraci u poskytovatele prostředků virtuálního počítače SQL jako buď licenci na SQL Server "PAYG ' nebo"AHUB". 

Registrace virtuálního počítače s SQL serverem ve zjednodušeném režimu správy SQL pomocí Powershellu pomocí následujícího fragmentu kódu:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

Registrace poskytovatele prostředků SQL VM v [zjednodušeném režimu](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) bude zajistit dodržování předpisů a povolit flexibilní licencování, stejně jako místní aktualizace edice SQL serveru. Instance clusteru převzetí služeb při selhání a nasazení s více instancemi můžete být zaregistrované u poskytovatele prostředků SQL VM pouze ve zjednodušeném režimu. Můžete postupovat podle pokynů na webu Azure portal a upgradujte na [režim zobrazení na celé](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) a kdykoli povolit sady funkcí komplexní možnosti správy s restartem systému SQL Server. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Zaregistrujte SQL Server 2008/R2 na virtuální počítače s Windows serverem 2008

SQL Server 2008 a 2008 R2 nainstalovaná v systému Windows Server 2008 lze dokument zaregistrovat u poskytovatele prostředků ve virtuální počítač SQL [NoAgent](virtual-machines-windows-sql-server-agent-extension.md) režimu. Tato možnost zajišťuje dodržování předpisů a umožňuje virtuálnímu počítači SQL serveru určeného k monitorování na webu Azure Portal s omezenou funkčností.

Následující tabulka uvádí přijatelné hodnoty pro parametry, které poskytnou během registrace:

| Parametr | Přípustné hodnoty                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, nebo `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` Nebo `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


K registraci vašeho systému SQL Server 2008 nebo 2008 R2 na Windows Server 2008 instance, použijte následující fragment kódu Powershellu:  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Ověřte stav registrace
Můžete ověřit, pokud SQL Server již byl zaregistrován u poskytovatele prostředků virtuálního počítače SQL pomocí webu Azure portal, rozhraní příkazového řádku Azure nebo PowerShell. 

### <a name="azure-portal"></a>Azure Portal 
Pokud chcete ověřit stav registrace pomocí webu Azure portal, postupujte takto.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejděte do vaší [virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md).
1. Ze seznamu vyberte virtuální počítač s SQL serverem. Pokud zde není uveden váš virtuální počítač s SQL serverem, je pravděpodobné, že virtuálního počítače s SQL Server není zaregistrovaný u poskytovatele prostředků virtuálního počítače s SQL. 
1. Zobrazit hodnoty v rámci `Status`. Pokud `Status = Succeeded`, pak virtuální počítač SQL Server je zaregistrován s poskytovatelem prostředků pro virtuální počítač s SQL úspěšně. 

    ![Ověření stavu registrace poskytovatele prostředků SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>Az CLI

Zkontrolujte aktuální stav registrace virtuálního počítače s SQL serverem pomocí následujícího příkazu AZ rozhraní příkazového řádku. `ProvisioningState` Zobrazí `Succeeded` Pokud byla registrace úspěšná. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

Zkontrolujte aktuální stav registrace virtuálního počítače s SQL serverem pomocí následující rutiny Powershellu. `ProvisioningState` Zobrazí `Succeeded` Pokud byla registrace úspěšná. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Chyba naznačuje, že virtuální počítač serveru SQL není zaregistrovaný u poskytovatele prostředků. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrace poskytovatele prostředků SQL VM s předplatným 

Zaregistrovat virtuální počítač s SQL serverem s poskytovatelem prostředků pro virtuální počítač s SQL, zaregistrujte poskytovatele prostředků do vašeho předplatného. Můžete provést pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.

### <a name="azure-portal"></a>portál Azure

Následující kroky se zaregistrujte poskytovatele prostředků virtuálního počítače SQL ke svému předplatnému Azure pomocí webu Azure portal. 

1. Otevřete na webu Azure portal a přejděte do **všechny služby**. 
1. Přejděte do **předplatná** a vyberte předplatné, které vás zajímají.  
1. Na **předplatná** stránce, přejděte na **poskytovatelů prostředků**. 
1. Typ `sql` ve filtru, abyste vyvolali se zprostředkovatelé prostředků souvisejících s SQL. 
1. Vyberte buď *zaregistrovat*, *přeregistrovat*, nebo *Unregister* pro **Microsoft.SqlVirtualMachine** poskytovateli v závislosti na vaší požadovanou akci. 

   ![Upravit poskytovatele](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>Az CLI
Následující fragment kódu se zaregistrujte poskytovatele prostředků virtuálního počítače SQL ke svému předplatnému Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

Následující fragment kódu Powershellu se zaregistrujte poskytovatele prostředků virtuálního počítače SQL ke svému předplatnému Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Poznámky

 - Poskytovatel prostředků virtuálního počítače s SQL podporuje pouze virtuální počítače SQL serveru nasadit pomocí Resource Manageru. Virtuální počítače SQL serveru nasadit s použitím klasického modelu nejsou podporované. 
 - Poskytovatel prostředků virtuálního počítače s SQL podporuje pouze virtuální počítače SQL serveru nasadit do veřejného cloudu. Nasazení do soukromého nebo cloud pro státní správu, nejsou podporovány. 

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server na nejčastější dotazy týkající se virtuálních počítačů Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na virtuálním počítači s Windows, doprovodné materiály k cenám](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server na poznámky k verzi virtuálního počítače Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


