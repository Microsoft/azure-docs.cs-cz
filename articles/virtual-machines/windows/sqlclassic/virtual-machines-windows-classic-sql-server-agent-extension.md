---
title: Automatizace úloh správy na virtuálních počítačích SQL (Classic) | Microsoft Docs
description: Toto téma popisuje, jak spravovat rozšíření agenta SQL Server, které automatizuje konkrétní úlohy správy SQL Server. Mezi ně patří automatizované zálohování, automatizované opravy a Integrace Azure Key Vault. Toto téma používá klasický režim nasazení.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e58b3f651a7a8f65d3450cf4b069d4e3f252d928
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100266"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizace úloh správy v Azure Virtual Machines s rozšířením agenta SQL Server (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Classic](../classic/sql-server-agent-extension.md)
> 
>
 
Rozšíření agenta SQL Server IaaS (SQLIaaSAgent) se spouští na virtuálních počítačích Azure pro automatizaci úloh správy. Toto téma poskytuje přehled služeb podporovaných rozšířením a také pokyny k instalaci, stavu a odebrání.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Pokud chcete zobrazit Správce prostředků verzi tohoto článku, přečtěte si téma [rozšíření agenta SQL Server pro SQL Server virtuální počítače Správce prostředků](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Podporované služby
Rozšíření agenta SQL Server IaaS podporuje následující úlohy správy:

| Funkce správy | Popis |
| --- | --- |
| **Automatizované zálohování SQL** |Automatizuje plánování záloh pro všechny databáze pro výchozí instanci SQL Server ve virtuálním počítači. Další informace najdete v tématu [automatizované zálohování pro SQL Server v Azure Virtual Machines (Classic)](../classic/sql-automated-backup.md). |
| **Automatizované opravy SQL** |Nakonfiguruje časový interval pro správu a údržbu, během kterého může probíhat důležitá aktualizace Windows pro váš virtuální počítač, takže se můžete vyhnout aktualizacím během špičky pro vaše zatížení. Další informace najdete v tématu [automatizované opravy pro SQL Server v Azure Virtual Machines (Classic)](../classic/sql-automated-patching.md). |
| **Integrace se službou Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat Azure Key Vault na VIRTUÁLNÍm počítači s SQL Server. Další informace najdete v tématu [Konfigurace integrace Azure Key Vault pro SQL Server na virtuálních počítačích Azure (Classic)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Požadavky
Požadavky na používání rozšíření agenta SQL Server IaaS na vašem VIRTUÁLNÍm počítači:

### <a name="operating-system"></a>Operační systém:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Verze SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Stáhněte a nakonfigurujte nejnovější příkazy Azure PowerShell](/powershell/azure/overview).

Spusťte Windows PowerShell a připojte ho k předplatnému Azure pomocí příkazu **Add-AzureAccount** .

    Add-AzureAccount

Pokud máte více předplatných, pomocí **Select-AzureSubscription** vyberte předplatné, které obsahuje váš cílový klasický virtuální počítač.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

V tomto okamžiku můžete získat seznam klasických virtuálních počítačů a jejich přidružených názvů služeb pomocí příkazu **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Instalace
U klasických virtuálních počítačů musíte pomocí PowerShellu nainstalovat rozšíření agenta SQL Server IaaS a nakonfigurovat jeho přidružené služby. K instalaci rozšíření použijte rutinu PowerShellu **set-AzureVMSqlServerExtension** . Například následující příkaz nainstaluje rozšíření na virtuální počítač s Windows serverem (Classic) a pojmenuje ho "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Pokud aktualizujete na nejnovější verzi rozšíření agenta SQL IaaS, musíte po aktualizaci rozšíření restartovat virtuální počítač.

> [!NOTE]
> Klasické virtuální počítače nemají možnost nainstalovat a nakonfigurovat rozšíření agenta SQL IaaS prostřednictvím portálu.

> [!NOTE]
> Rozšíření agenta SQL Server IaaS se podporuje jenom v [imagí Galerie virtuálních počítačů v SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (průběžné platby nebo vlastní licence). Není podporováno, pokud SQL Server nainstalujete ručně na virtuální počítač se systémem Windows Server pouze v operačním systému nebo pokud nasadíte přizpůsobený virtuální pevný disk SQL Server virtuálního počítače. V těchto případech může být možné ručně nainstalovat a spravovat rozšíření pomocí PowerShellu, ale důrazně se doporučuje nainstalovat Image Galerie virtuálních počítačů SQL Server a pak ji přizpůsobit.

## <a name="status"></a>Stav
Jedním ze způsobů, jak ověřit, že je rozšíření nainstalované, je zobrazit stav agenta na webu Azure Portal. Vyberte virtuální počítač uvedený v okně virtuální počítač a pak klikněte na **rozšíření**. Měla by se zobrazit uvedená rozšíření **SQLIaaSAgent** .

![Rozšíření agenta SQL Server IaaS na webu Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Můžete také použít rutinu **Get-AzureVMSqlServerExtension** prostředí Azure PowerShell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Odebrání
Na webu Azure Portal můžete rozšíření odinstalovat kliknutím na tři tečky v okně **rozšíření** ve vlastnostech virtuálního počítače. Pak klikněte na **odinstalovat**.

![Odinstalace rozšíření agenta SQL Server IaaS na webu Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Můžete také použít rutinu PowerShellu **Remove-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Další kroky
Začněte používat jednu ze služeb, které rozšíření podporuje. Další podrobnosti najdete v tématech uvedených v části věnované [podporovaným službám](#supported-services) tohoto článku.

Další informace o spouštění SQL Server v Azure Virtual Machines najdete v tématu [SQL Server na azure Virtual Machines Overview](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

