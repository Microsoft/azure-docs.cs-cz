---
title: Automatizace úloh správy na virtuálních počítačích SQL (Classic) | Dokumentace Microsoftu
description: Toto téma popisuje, jak spravovat rozšíření agenta SQL serveru, který automatizuje určité úlohy správy systému SQL Server. Patří mezi ně automatizovaného zálohování, automatické opravy a integrace Azure Key Vault. Toto téma používá režim nasazení classic.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 55a31d7a0ab603dd7fe7de514d11d003e044240a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008327"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizace úloh správy v Azure Virtual Machines pomocí rozšíření agenta SQL serveru (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Classic](../classic/sql-server-agent-extension.md)
> 
>
 
Rozšíření SQL Server IaaS agenta (SQLIaaSAgent) běží na virtuálních počítačích Azure k automatizaci úloh správy. Toto téma obsahuje přehled služby podporuje rozšíření, stejně jako pokyny pro instalaci, stavu a odebrání.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Chcete-li zobrazit Resource Manageru verze tohoto článku, najdete v článku [rozšíření agenta SQL Server pro SQL Server virtuální počítače Resource Manageru](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Podporované služby
Rozšíření agenta SQL Server IaaS podporuje následující úlohy správy:

| Funkce správy | Popis |
| --- | --- |
| **Automatizované zálohování SQL** |Automatizuje plánování zálohování pro všechny databáze pro výchozí instanci systému SQL Server ve virtuálním počítači. Další informace najdete v tématu [automatizované zálohování pro SQL Server na virtuálních počítačích Azure (klasické)](../classic/sql-automated-backup.md). |
| **Automatizované opravy SQL** |Nakonfiguruje časové období údržby, během které důležité aktualizace Windows k vašemu virtuálnímu počítači může proběhnout, takže se můžete vyhnout aktualizace během špiček pro vaši úlohu. Další informace najdete v tématu [automatizované opravy pro SQL Server na virtuálních počítačích Azure (klasické)](../classic/sql-automated-patching.md). |
| **Integrace se službou Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat služby Azure Key Vault na virtuální počítač s SQL serverem. Další informace najdete v tématu [konfigurace integrace Azure Key pro SQL Server na virtuálních počítačích Azure (klasické) trezor](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Požadavky
Požadavky pro použití rozšíření agenta SQL Server IaaS na vašem virtuálním počítači:

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

Spusťte prostředí Windows PowerShell a připojte ho k předplatnému Azure pomocí **Add-AzureAccount** příkazu.

    Add-AzureAccount

Pokud máte více předplatných, použijte **Select-AzureSubscription** vyberte předplatné, která obsahuje cílové klasický virtuální počítač.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

V tomto okamžiku můžete získat seznam klasických virtuálních počítačů a jejich názvy přidružené služby s **Get-AzureVM** příkazu.

    Get-AzureVM

## <a name="installation"></a>Instalace
Pro klasické virtuální počítače musíte použít PowerShell k instalaci rozšíření agenta SQL Server IaaS a konfiguraci souvisejících služeb. Použití **Set-AzureVMSqlServerExtension** rutiny prostředí PowerShell k instalaci rozšíření. Například následující příkaz nainstaluje rozšíření na virtuální počítač s Windows serverem (classic) a pojmenuje ho "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Při aktualizaci na nejnovější verzi rozšíření agenta SQL IaaS, je nutné restartovat virtuální počítač po aktualizaci rozšíření.

> [!NOTE]
> Klasické virtuální počítače nemají možnost instalace a konfigurace rozšíření agenta SQL IaaS prostřednictvím portálu.

> [!NOTE]
> Rozšíření agenta SQL Server IaaS je podporována pouze na [Image virtuálního počítače s SQL serverem z Galerie](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (s průběžnými platbami nebo přeneste svůj – používání vlastní licence). Pokud ručně instalovat SQL Server na virtuálním počítači pouze pro operační systém Windows Server nebo nasadit vlastní VHD virtuálního počítače SQL serveru není podporována. V těchto případech je možné nainstalovat a spravovat rozšíření ručně pomocí Powershellu, ale důrazně doporučujeme místo toho nainstalovat image virtuálního počítače s SQL serverem z galerie a pak ji přizpůsobit.

## <a name="status"></a>Status
Chcete-li zobrazit stav agenta na webu Azure Portal je jeden způsob, jak ověřit, že rozšíření je nainstalované. Vyberte virtuální počítač uvedený v okně virtuálního počítače a potom klikněte na **rozšíření**. Měli byste vidět **SQLIaaSAgent** uvedená rozšíření.

![Server rozšíření agenta SQL IaaS na webu Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Můžete také použít **Get-AzureVMSqlServerExtension** rutiny Azure Powershellu.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Odebrání
Na webu Azure Portal můžete odinstalovat rozšíření po kliknutí na tři tečky **rozšíření** okno vlastností virtuálního počítače. Pak klikněte na tlačítko **odinstalovat**.

![Odinstalace rozšíření IaaS agenta SQL serveru na webu Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Můžete také použít **odebrat AzureVMSqlServerExtension** rutiny Powershellu.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Další kroky
Začněte používat jednu ze služeb podporovaný rozšířením. Další podrobnosti najdete v tématech odkazuje [podporované služby](#supported-services) části tohoto článku.

Další informace o spuštění SQL serveru na virtuálních počítačích Azure najdete v tématu [systému SQL Server na Azure Virtual Machines – přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

