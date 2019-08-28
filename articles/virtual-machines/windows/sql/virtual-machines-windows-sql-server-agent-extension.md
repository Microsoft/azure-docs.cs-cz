---
title: Automatizace úloh správy na virtuálních počítačích Azure pomocí rozšíření agenta SQL Server IaaS | Microsoft Docs
description: Tento článek popisuje, jak spravovat rozšíření agenta SQL Server IaaS, které automatizuje konkrétní úlohy správy SQL Server. Mezi ně patří automatizované zálohování, automatizované opravy a Integrace Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f4dd529481a6216e43d35c76ecee734543d487f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100481"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizace úloh správy na virtuálních počítačích Azure pomocí rozšíření agenta SQL Server IaaS
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Rozšíření agenta SQL Server IaaS (SqlIaasExtension) běží na virtuálních počítačích Azure za účelem automatizace úloh správy. Tento článek poskytuje přehled služeb, které podporuje rozšíření. Tento článek také poskytuje pokyny k instalaci, stavu a odebrání rozšíření.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Pokud chcete zobrazit klasickou verzi tohoto článku, přečtěte si téma [SQL Server rozšíření agenta IaaS pro SQL Server virtuální počítače (Classic)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Existují tři režimy správy pro rozšíření SQL Server IaaS: 

- **Úplný** režim zajišťuje všechny funkce, ale vyžaduje restartování oprávnění SQL Server a správce systému. Tato možnost je nainstalována ve výchozím nastavení. Použijte ji ke správě SQL Server virtuálního počítače s jedinou instancí. 

- **Odlehčený** nevyžaduje restartování SQL Server, ale podporuje pouze změnu typu licence a edice SQL Server. Tuto možnost použijte pro SQL Server virtuálních počítačů s více instancemi nebo pro účast v instanci clusteru s podporou převzetí služeb při selhání (FCI). 

- **Agent** je vyhrazený pro SQL Server 2008 a SQL Server 2008 R2 nainstalované v systému Windows Server 2008. Informace o použití tohoto režimu pro bitovou kopii systému Windows Server 2008 naleznete v tématu [registrace systému Windows server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Podporované služby
Rozšíření agenta SQL Server IaaS podporuje následující úlohy správy:

| Funkce správy | Popis |
| --- | --- |
| **SQL Server automatizované zálohování** |Automatizuje plánování záloh pro všechny databáze, a to buď pro výchozí instanci, nebo pro [správnou nainstalovanou](virtual-machines-windows-sql-server-iaas-faq.md#administration) pojmenovanou instanci SQL Server na virtuálním počítači. Další informace najdete v tématu [automatizované zálohování pro SQL Server ve virtuálních počítačích Azure (Správce prostředků)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL Server automatizované opravy** |Nakonfiguruje časový interval pro správu a údržbu, během kterého může probíhat důležitá aktualizace Windows pro váš virtuální počítač, takže se můžete vyhnout aktualizacím během špičky pro vaše zatížení. Další informace najdete v tématu [automatizované opravy pro SQL Server ve virtuálních počítačích Azure (Správce prostředků)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrace Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat Azure Key Vault na VIRTUÁLNÍm počítači s SQL Server. Další informace najdete v tématu [Konfigurace integrace Azure Key Vault pro SQL Server v Azure Virtual Machines (Správce prostředků)](virtual-machines-windows-ps-sql-keyvault.md). |

Po instalaci a spuštění rozšíření agenta SQL Server IaaS se zpřístupní funkce správy:

* Na panelu SQL Server virtuálního počítače v Azure Portal a Azure PowerShell pro SQL Server imagí na Azure Marketplace.
* Prostřednictvím Azure PowerShell pro ruční instalaci rozšíření. 

## <a name="prerequisites"></a>Požadavky
Tady jsou požadavky na použití rozšíření agenta SQL Server IaaS na vašem VIRTUÁLNÍm počítači:

**Operační systém**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server. 2019 

**Verze SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Stažení a konfigurace nejnovějších příkazů Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Režimy správy změn

Aktuální režim SQL Server agenta IaaS můžete zobrazit pomocí prostředí PowerShell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server virtuální počítače, které mají nainstalovanou zjednodušenou příponu IaaS, mohou upgradovat režim na _úplný_ s použitím Azure Portal. SQL Server virtuálních počítačů v režimu _bez agenta_ se může upgradovat na _úplný_ , až se operační systém upgraduje na Windows 2008 R2 a novější. Není možné nadowngradovat – k tomu je potřeba odinstalaci rozšíření SQL IaaS a instalaci znovu. 

Postup upgradu režimu agenta na úplný: 


# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejít na prostředek [virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Vyberte svůj virtuální počítač SQL Server a vyberte **Přehled**. 
1. V případě SQL Server virtuálních počítačů pomocí agenta nebo režimu zjednodušeného IaaS vyberte možnost **jediný typ licence a aktualizace edice jsou k dispozici ve zprávě rozšíření SQL IaaS** .

   ![Výběry pro změnu režimu z portálu](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Zaškrtněte políčko **Souhlasím s restartováním služby SQL Server na virtuálním počítači** a pak vyberte **Potvrdit** , abyste upgradovali režim IaaS na úplný. 

    ![Zaškrtávací políčko pro vyjádření souhlasu s restartováním služby SQL Server na virtuálním počítači](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Spusťte následující příkaz AZ CLI Code fragment:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Spusťte následující fragment kódu prostředí PowerShell:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```

---


##  <a name="installation"></a>Instalace
Rozšíření SQL Server IaaS se nainstaluje při registraci SQL Server virtuálního počítače pomocí [poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md). V případě potřeby můžete ručně nainstalovat agenta SQL Server IaaS pomocí úplného nebo zjednodušeného režimu. 

Rozšíření agenta SQL Server IaaS v plném režimu se automaticky nainstaluje při zřizování jednoho z SQL Server imagí Azure Marketplace virtuálních počítačů pomocí Azure Portal. 

### <a name="install-in-full-mode"></a>Nainstalovat v plném režimu
Úplný režim pro rozšíření SQL Server IaaS nabízí plnou spravovatelnost pro jednu instanci na SQL Serverm virtuálním počítači. Pokud existuje výchozí instance, bude rozšíření fungovat s výchozí instancí a nebude podporovat správu jiných instancí. Pokud neexistuje žádná výchozí instance, ale pouze jedna pojmenovaná instance, bude spravovat pojmenovanou instanci. Pokud neexistuje žádná výchozí instance a existuje více pojmenovaných instancí, rozšíření se nedaří nainstalovat. 

Pomocí PowerShellu nainstalujte agenta SQL Server IaaS s úplným režimem:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Install 'Full' SQL Server IaaS agent extension
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parametr | Přijatelné hodnoty                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` Nebo `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> Pokud rozšíření ještě není nainstalované, instalace úplného rozšíření restartuje službu SQL Server. Abyste se vyhnuli restartování služby SQL Server, nainstalujte zjednodušený režim s možností omezené správy.
> 
> Aktualizace rozšíření SQL Server IaaS nerestartuje službu SQL Server. 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalace na virtuální počítač s jednou s názvem SQL Server instance
Rozšíření SQL Server IaaS bude pracovat s pojmenovanou instancí v SQL Server, pokud je výchozí instance odinstalována a přeinstalováno rozšíření IaaS.

Použití pojmenované instance SQL Server:
   1. Nasazení virtuálního počítače s SQL Server z Azure Marketplace. 
   1. Odinstalujte rozšíření IaaS z [Azure Portal](https://portal.azure.com).
   1. Odinstalace SQL Server kompletně v rámci SQL Server virtuálního počítače.
   1. Nainstalujte SQL Server s pojmenovanou instancí v rámci SQL Server virtuálního počítače. 
   1. Nainstalujte rozšíření IaaS z Azure Portal.  


### <a name="install-in-lightweight-mode"></a>Instalace v odlehčeném režimu
Odlehčený režim neprovede restart služby SQL Server, ale nabízí omezené funkce. 

Pomocí PowerShellu nainstalujte agenta SQL Server IaaS s odlehčeným režimem:


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parametr | Přijatelné hodnoty                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` Nebo `PAYG`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Získat stav rozšíření SQL Server IaaS
Jedním ze způsobů, jak ověřit, že je rozšíření nainstalované, je zobrazit stav agenta v Azure Portal. V okně virtuálního počítače vyberte **všechna nastavení** a pak vyberte **rozšíření**. Měla by se zobrazit uvedená rozšíření **SqlIaasExtension** .

![Stav rozšíření agenta SQL Server IaaS v Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Můžete také použít rutinu **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Předchozí příkaz potvrdí, že je agent nainstalovaný a poskytuje obecné informace o stavu. Konkrétní informace o stavu automatizovaného zálohování a oprav můžete získat pomocí následujících příkazů:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Odebrání
V Azure Portal můžete rozšíření odinstalovat tak, že vyberete tři tečky v okně **rozšíření** ve vlastnostech virtuálního počítače. Vyberte **Odstranit**.

![Odinstalace rozšíření agenta SQL Server IaaS v Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Můžete také použít rutinu prostředí PowerShell **Remove-AzVMSqlServerExtension** :

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Další postup
Začněte používat jednu ze služeb, které podporuje rozšíření. Další informace najdete v článcích, na které se odkazuje v tomto článku v části věnované [podporovaným službám](#supported-services) .

Další informace o spouštění SQL Server v Azure Virtual Machines najdete v tématu [co je SQL Server na Virtual Machines Azure](virtual-machines-windows-sql-server-iaas-overview.md).

