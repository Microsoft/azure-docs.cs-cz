---
title: Automatizaci úloh správy na virtuálních počítačích SQL (Resource Manager) | Microsoft Docs
description: Tento článek popisuje, jak spravovat rozšíření agenta systému SQL Server, který automatizuje konkrétní úlohy správy systému SQL Server. Mezi ně patří automatizovaného zálohování, automatizovaných oprav a Azure Key Vault integrace.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/20/2018
ms.author: jroth
ms.openlocfilehash: d9cb4a3bdc5776c4ac70ac376d8b839193e3fc3d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizaci úloh správy ve virtuálních počítačích Azure s rozšíření agenta systému SQL Server (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Rozšíření agenta IaaS serveru SQL (SQLIaaSExtension) běží na virtuálních počítačích Azure k automatizaci úloh správy. Tento článek obsahuje přehled služby podporuje rozšíření a také pokyny pro instalaci, stavu a odebrání.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Klasické verze tohoto článku najdete v tématu [rozšíření agenta systému SQL Server pro SQL Server virtuální počítače Classic](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Podporované služby
Rozšíření agenta systému SQL Server IaaS podporuje následující úlohy správy:

| Funkce správy | Popis |
| --- | --- |
| **Automatizované zálohování SQL** |Automatizuje plánování zálohování pro všechny databáze pro výchozí instanci systému SQL Server ve virtuálním počítači. Další informace najdete v tématu [automatizované zálohování pro SQL Server v Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Automatizované opravy pro SQL** |Konfiguruje okno údržby, během které důležité aktualizace systému Windows k virtuálnímu počítači může trvat místní, takže se můžete vyhnout aktualizace během špiček pro úlohy. Další informace najdete v tématu [automatizované opravy pro SQL Server v Azure Virtual Machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrace se službou Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat Azure Key Vault na virtuální počítač s SQL serverem. Další informace najdete v tématu [nakonfigurovat klíč trezoru integrace se službou Azure pro systém SQL Server na virtuálních počítačích Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Po dokončení instalace a spuštění, rozšíření agenta systému SQL Server IaaS zpřístupní tyto funkce pro správu na panelu systému SQL Server virtuálního počítače na portálu Azure a pomocí prostředí Azure PowerShell pro bitové kopie systému SQL Server marketplace a Azure Prostředí PowerShell pro ruční instalací rozšíření. 

## <a name="prerequisites"></a>Požadavky
Požadavky pro použití IaaS agenta rozšíření systému SQL Server na virtuální počítač:

**Operační systém**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze systému SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Stáhnout a nakonfigurovat nejnovější příkazy prostředí Azure PowerShell](/powershell/azure/overview)

## <a name="installation"></a>Instalace
Rozšíření agenta systému SQL Server IaaS se automaticky nainstaluje při zřizování jednoho z Galerie obrázků virtuálního počítače systému SQL Server. Pokud potřebujete znovu ručně nainstalujte rozšíření na jednu z těchto virtuálních počítačů serveru SQL, použijte následující příkaz Powershellu:

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

> [!IMPORTANT]
> Pokud ještě není nainstalovaná rozšíření, instalaci rozšíření restartuje službu SQL Server.

Také je možné nainstalovat IaaS agenta rozšíření systému SQL Server na virtuálním počítači jen operačního systému Windows Server. Je podporováno pouze pokud jste nainstalovali také ručně SQL Server na tomto počítači. Pak nainstalujte ručně pomocí stejné rozšíření **Set-AzureRmVMSqlServerExtension** rutiny prostředí PowerShell.

> [!NOTE]
> Pokud ručně nainstalovat rozšíření agenta systému SQL Server IaaS na jen operačního systému Windows Server virtuální počítač, se nedají spravovat nastavení konfigurace systému SQL Server prostřednictvím portálu Azure. V tomto scénáři je nutné provést všechny změny v prostředí PowerShell.

## <a name="status"></a>Status
Chcete-li ověřit, zda je nainstalován rozšíření je zobrazíte stav agenta na portálu Azure. Vyberte **všechna nastavení** v okně virtuálního počítače a pak klikněte na **rozšíření**. Měli byste vidět **SQLIaaSExtension** rozšíření uvedené.

![Rozšíření IaaS agenta systému SQL Server na portálu Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Můžete také **Get-AzureRmVMSqlServerExtension** rutiny Azure Powershellu.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Předchozí příkaz potvrdí agenta je nainstalován a poskytuje obecné informace stavu. Můžete také získat stav konkrétní informace o automatizované zálohování a oprav pomocí následujících příkazů.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Odebrání
Na portálu Azure můžete odinstalovat rozšíření kliknutím na tlačítko se třemi tečkami na **rozšíření** okno vaší vlastnosti virtuálního počítače. Potom klikněte na **Odstranit**.

![Odinstalovat rozšíření agenta SQL Server IaaS na portálu Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Můžete také **odebrat AzureRmVMSqlServerExtension** rutiny prostředí PowerShell.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Další postup
Začněte pomocí jedné ze služeb, podporuje rozšíření. Další podrobnosti najdete v článcích v odkazuje [podporované služby](#supported-services) tohoto článku.

Další informace o spuštění systému SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na virtuálních počítačích Azure přehled](virtual-machines-windows-sql-server-iaas-overview.md).

