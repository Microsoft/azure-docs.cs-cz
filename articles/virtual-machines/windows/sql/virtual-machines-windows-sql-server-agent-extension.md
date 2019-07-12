---
title: Automatizace úloh správy ve službě Azure Virtual Machines pomocí rozšíření agenta SQL Server IaaS | Dokumentace Microsoftu
description: Tento článek popisuje, jak spravovat rozšíření agenta SQL serveru, který automatizuje určité úlohy správy systému SQL Server. Patří mezi ně automatizovaného zálohování, automatické opravy a integrace Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798059"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>Automatizace úloh správy ve službě Azure Virtual Machines pomocí rozšíření agenta SQL Server IaaS
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Rozšíření agenta SQL Server IaaS (SqlIaasExtension) běží na virtuálních počítačích Azure za účelem automatizace úloh správy. Tento článek obsahuje přehled a služeb podporuje rozšíření, stejně jako pokyny pro instalaci, stavu a odebrání.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Chcete-li zobrazit klasickou verzi tohoto článku, najdete v článku [rozšíření agenta SQL serveru pro SQL Server virtuální počítače Classic](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Existují tři režimy správy SQL pro rozšíření SQL IaaS: **Úplné**, **Lightweight**, a **NoAgent**. 

- **Úplné** režimu zajišťuje všechny funkce, ale vyžaduje restartování systému SQL Server a SA oprávnění. Toto je možnost, která je ve výchozím nastavení nainstalované a by měla sloužit ke správě virtuálního počítače s SQL serverem s jednou instancí. 

- **Zjednodušené** nevyžaduje restartování systému SQL Server, ale podporuje jenom změnit typ licence a edici systému SQL Server. Tato možnost musí být použito pro virtuální počítače s SQL serverem s více instancemi nebo zapojený do instance clusteru převzetí služeb při selhání (FCI). 

- **NoAgent** je vyhrazený pro SQL Server 2008 a SQL Server 2008 R2 nainstalovaná v systému Windows Server 2008. Informace o použití `NoAgent` režim pro vaši image Windows serveru 2008, viz [registrace Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Podporované služby
Rozšíření agenta SQL Server IaaS podporuje následující úlohy správy:

| Funkce správy | Popis |
| --- | --- |
| **Automatizované zálohování SQL** |Automatizuje plánování zálohování pro všechny databáze pro buď výchozí instanci nebo [správně nainstalována](virtual-machines-windows-sql-server-iaas-faq.md#administration) pojmenovanou instanci systému SQL Server na virtuálním počítači. Další informace najdete v tématu [automatizované zálohování pro SQL Server na Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Automatizované opravy SQL** |Nakonfiguruje časové období údržby, během které důležité aktualizace Windows k vašemu virtuálnímu počítači může proběhnout, takže se můžete vyhnout aktualizace během špiček pro vaši úlohu. Další informace najdete v tématu [automatizované opravy pro SQL Server na Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrace se službou Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat služby Azure Key Vault na virtuální počítač s SQL serverem. Další informace najdete v tématu [konfigurace integrace Azure Key pro SQL Server na virtuálních počítačích Azure (Resource Manager) trezor](virtual-machines-windows-ps-sql-keyvault.md). |

Po dokončení instalace a spuštění, rozšíření agenta SQL Server IaaS zpřístupní tyto funkce pro správu na serveru SQL Server panelu virtuálního počítače na webu Azure Portal a pomocí Azure Powershellu pro marketplace Image SQL serveru a Azure Prostředí PowerShell pro ruční instalace rozšíření. 

## <a name="prerequisites"></a>Požadavky
Požadavky pro použití rozšíření agenta SQL Server IaaS na vašem virtuálním počítači:

**Operační systém**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server. 2019 

**Verze systému SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Stáhnout a nakonfigurovat nejnovější příkazy Azure Powershellu](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Změnit režim správy

Aktuální režim agenta SQL IaaS můžete zobrazit pomocí prostředí PowerShell: 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

Pro virtuální počítače SQL serveru, které mají *NoAgent* nebo *zjednodušené* nainstalované rozšíření IaaS, můžete upgradovat režim, který chcete *úplné* pomocí webu Azure portal. Není možné přejít na nižší verzi – v takovém případě budete muset úplně odinstalovat rozšíření SQL IaaS a znovu nainstalovat. 

Do režimu upgradu agenta na *úplné*, postupujte takto: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do vaší [virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) prostředků. 
1. Vyberte virtuální počítač systému SQL Server a pak vyberte **přehled**. 
1. Pro virtuální počítače s SQL se *NoAgent* nebo *zjednodušené* režimy IaaS, vyberte zprávu pro **licenci edice a typ aktualizace jsou dostupné s rozšířením SQL IaaS pouze**.

    ![Spusťte Změna režimu z portálu](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Souhlas s **restartovat službu systému SQL Server** výběrem zaškrtávacího políčka a pak vyberte **potvrdit** upgrade IaaS režim na hodnotu "full". 

    ![Povolit úplnou správu IaaS rozšíření](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>Instalace
Je nainstalované rozšíření SQL IaaS, při registraci virtuálního počítače s SQL serverem s [poskytovatele prostředků SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider). Ale v případě potřeby agenta SQL IaaS můžete také nainstalovat ručně pomocí *úplné* nebo *zjednodušené* režim instalace. 

*Úplné* rozšíření agenta SQL Server IaaS se automaticky nainstaluje při zřizování Image Galerie virtuálních počítačů SQL serveru pomocí webu Azure portal. 

### <a name="full-mode-installation"></a>Režim zobrazení na celé instalace
*Úplné* rozšíření SQL IaaS nabízí úplné možnosti správy pro jednu instanci na virtuálním počítači SQL serveru. Dojde-li výchozí instanci, pak bude rozšíření fungovat s výchozí instance a nebude podporovat, správu ostatních instancí. Pokud není žádná výchozí instance, ale pouze jednu pojmenovanou instanci, bude ho spravovat pojmenovanou instanci. Pokud není žádná výchozí instance a existuje víc instancí s názvem, se nezdaří rozšíření k instalaci. 

Instalace *úplné* režimu SQL IaaS se restartovat službu systému SQL Server. Chcete-li zabránit restartování služby SQL Server, nainstalujte *zjednodušené* režimu se místo toho omezené možnosti správy. 

Instalace agenta SQL IaaS s *úplné* režimu pomocí prostředí PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parametr | Přípustné hodnoty                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, nebo `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - Pokud rozšíření ještě není nainstalovaná, instalace **úplné** rozšíření restartuje službu systému SQL Server. Použití **zjednodušené** režimu, aby se zabránilo restartovat službu systému SQL Server. 
> - Aktualizuje se rozšíření SQL IaaS nerestartuje služby SQL Server. 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalace na virtuální počítač pomocí jednoho pojmenované instance SQL serveru
Rozšíření SQL IaaS budou fungovat s pojmenovanou instanci na SQL serveru, pokud výchozí instance je odinstalovat a přeinstalovat rozšíření IaaS.

Pokud chcete použít pojmenovanou instanci systému SQL Server, postupujte takto:
   1. Nasazení virtuálního počítače s SQL serverem z marketplace. 
   1. Odinstalovat rozšíření IaaS v rámci [webu Azure portal](https://portal.azure.com).
   1. Odinstalujte systém SQL Server zcela v rámci virtuálního počítače SQL serveru.
   1. Nainstalujte SQL Server pomocí pojmenované instance na virtuálním počítači SQL serveru. 
   1. Instalace rozšíření IaaS z webu Azure portal.  


### <a name="install-in-lightweight-mode"></a>Nainstalujte ve zjednodušeném režimu
Jednoduchý režim nebude restartujte službu systému SQL Server, ale nabízí omezené funkce. 

Instalace agenta SQL IaaS s *zjednodušené* režimu pomocí prostředí PowerShell:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parametr | Přípustné hodnoty                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, nebo `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>Načíst stav rozšíření SQL IaaS
Chcete-li zobrazit stav agenta na webu Azure Portal je jeden způsob, jak ověřit, že rozšíření je nainstalované. Vyberte **všechna nastavení** v okně virtuálního počítače a potom kliknout na **rozšíření**. Měli byste vidět **SqlIaasExtension** uvedená rozšíření.

![Rozšíření agenta SQL Server IaaS na webu Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Můžete také použít **Get-AzVMSqlServerExtension** rutiny Azure Powershellu.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Předchozí příkaz potvrdí, agent je nainstalovaný a poskytuje obecné informace stavu. Získáte také konkrétní stavové informace o automatické zálohování a opravy pomocí následujících příkazů.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Odebrání
Na webu Azure Portal, můžete po kliknutí na tři tečky na odinstalovat rozšíření **rozšíření** okno vlastností virtuálního počítače. Potom klikněte na **Odstranit**.

![Odinstalace rozšíření agenta SQL Server IaaS na webu Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Můžete také použít **odebrat AzVMSqlServerExtension** rutiny Powershellu.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Další postup
Začněte používat jednu ze služeb podporovaný rozšířením. Další informace najdete v článcích odkazuje [podporované služby](#supported-services) části tohoto článku.

Další informace o spuštění SQL serveru na virtuálních počítačích Azure najdete v tématu [systému SQL Server na Azure Virtual Machines – přehled](virtual-machines-windows-sql-server-iaas-overview.md).

