---
title: "Automatizaci úloh správy na virtuálních počítačích SQL (klasické) | Microsoft Docs"
description: "Toto téma popisuje, jak spravovat rozšíření agenta systému SQL Server, který automatizuje konkrétní úlohy správy systému SQL Server. Mezi ně patří automatizovaného zálohování, automatizovaných oprav a Azure Key Vault integrace. Toto téma používá režim nasazení classic."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ff9a8b91b0359c57fae5b1a01b5d895ab9a1685
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizaci úloh správy ve virtuálních počítačích Azure s rozšíření agenta systému SQL Server (klasické)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Classic](../classic/sql-server-agent-extension.md)
> 
>
 
Rozšíření agenta IaaS serveru SQL (SQLIaaSAgent) běží na virtuálních počítačích Azure k automatizaci úloh správy. Toto téma obsahuje přehled služby podporuje rozšíření a také pokyny pro instalaci, stavu a odebrání.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Resource Manager verzi v tomto článku najdete v tématu [rozšíření agenta systému SQL Server pro SQL Server virtuální počítače Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Podporované služby
Rozšíření agenta systému SQL Server IaaS podporuje následující úlohy správy:

| Funkce správy | Popis |
| --- | --- |
| **Automatizované zálohování SQL** |Automatizuje plánování zálohování pro všechny databáze pro výchozí instanci systému SQL Server ve virtuálním počítači. Další informace najdete v tématu [automatizované zálohování pro SQL Server v Azure Virtual Machines (klasické)](../classic/sql-automated-backup.md). |
| **Automatizované opravy pro SQL** |Konfiguruje okno údržby, během které důležité aktualizace systému Windows k virtuálnímu počítači může trvat místní, takže se můžete vyhnout aktualizace během špiček pro úlohy. Další informace najdete v tématu [automatizované opravy pro SQL Server v Azure Virtual Machines (klasické)](../classic/sql-automated-patching.md). |
| **Integrace se službou Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat Azure Key Vault na virtuální počítač s SQL serverem. Další informace najdete v tématu [nakonfigurovat klíč trezoru integrace se službou Azure pro systém SQL Server na virtuálních počítačích Azure (klasický)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Požadavky
Požadavky pro použití IaaS agenta rozšíření systému SQL Server na virtuální počítač:

### <a name="operating-system"></a>Operační systém:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Verze systému SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Stáhnout a nakonfigurovat nejnovější příkazy prostředí Azure PowerShell](/powershell/azure/overview).

Spusťte prostředí Windows PowerShell a připojte ho k předplatnému Azure s **Add-AzureAccount** příkaz.

    Add-AzureAccount

Pokud máte více předplatných, použijte **Select-AzureSubscription** vyberte předplatné, který obsahuje cílových classic virtuálních počítačů.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

V tomto okamžiku můžete získat seznam klasické virtuální počítače a jejich názvy přidružená služba s **Get-AzureVM** příkaz.

    Get-AzureVM

## <a name="installation"></a>Instalace
Pro klasické virtuální počítače musíte použít PowerShell k instalaci rozšíření agenta systému SQL Server IaaS a konfiguraci jejích přidružených služeb. Použití **Set-AzureVMSqlServerExtension** rutiny prostředí PowerShell k instalaci rozšíření. Například následující příkaz instalaci rozšíření na virtuálním počítači Windows serveru (klasické) a pojmenuje ji "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Při aktualizaci na nejnovější verzi agenta rozšíření IaaS SQL, je nutné restartovat virtuální počítač po aktualizaci rozšíření.

> [!NOTE]
> Klasické virtuální počítače nemají možnost instalace a konfigurace rozšíření agenta systému SQL IaaS prostřednictvím portálu.
> 
> 

## <a name="status"></a>Status
Chcete-li ověřit, zda je nainstalován rozšíření je zobrazíte stav agenta na portálu Azure. Vyberte virtuální počítač uvedený v okně virtuálního počítače a potom klikněte na **rozšíření**. Měli byste vidět **SQLIaaSAgent** rozšíření uvedené.

![Rozšíření IaaS agenta systému SQL Server na portálu Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Můžete také **Get-AzureVMSqlServerExtension** rutiny Azure Powershellu.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Odebrání
Na portálu Azure můžete odinstalovat rozšíření kliknutím na tlačítko se třemi tečkami na **rozšíření** okno vaší vlastnosti virtuálního počítače. Pak klikněte na tlačítko **odinstalovat**.

![Odinstalovat rozšíření IaaS agenta systému SQL Server na portálu Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Můžete také **odebrat AzureVMSqlServerExtension** rutiny prostředí Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Další kroky
Začněte pomocí jedné ze služeb, podporuje rozšíření. Další podrobnosti najdete v tématech v odkazuje [podporované služby](#supported-services) tohoto článku.

Další informace o spuštění systému SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na virtuálních počítačích Azure přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

