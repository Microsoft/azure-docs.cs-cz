---
title: Automatizace úloh správy na virtuálních počítačích SQL (classic) | Dokumenty společnosti Microsoft
description: Toto téma popisuje, jak spravovat rozšíření agenta serveru SQL Server, které automatizuje konkrétní úlohy správy serveru SQL Server. Patří mezi ně automatické zálohování, automatické opravy a integrace azure trezoru klíčů. Toto téma používá klasický režim nasazení.
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
ms.openlocfilehash: b76ade40db1e85abc0fb42af2e6f4ab88cb092c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982286"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizace úloh správy na virtuálních počítačích Azure pomocí rozšíření SQL Server Agent Extension (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Classic](../classic/sql-server-agent-extension.md)
> 
>
 
Rozšíření agenta SQL Server IaaS (SQLIaaSAgent) běží na virtuálních počítačích Azure a automatizuje úlohy správy. Toto téma obsahuje přehled služeb podporovaných rozšířením a pokyny pro instalaci, stav a odebrání.

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Chcete-li zobrazit verzi tohoto článku správce prostředků, přečtěte si téma [Rozšíření agenta serveru SQL Server pro Správce prostředků virtuálních kanceláří SQL Server](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Podporované služby
Rozšíření agenta serveru SQL Server IaaS podporuje následující úlohy správy:

| Funkce správy | Popis |
| --- | --- |
| **Automatizované zálohování SQL** |Automatizuje plánování záloh pro všechny databáze pro výchozí instanci serveru SQL Server ve virtuálním počítače. Další informace naleznete [v tématu Automatické zálohování pro SQL Server ve virtuálních počítačích Azure (Classic).](../classic/sql-automated-backup.md) |
| **Automatizované opravy pro SQL Server** |Nakonfiguruje okno údržby, během kterého může probíhat důležité aktualizace windows na vašem virtuálním počítači, takže se můžete vyhnout aktualizacím během špičky pro vaše úlohy. Další informace naleznete [v tématu Automatické opravy pro SQL Server ve virtuálních počítačích Azure (Classic).](../classic/sql-automated-patching.md) |
| **Integrace se službou Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat Azure Key Vault na vašem virtuálním počítači SQL Server. Další informace najdete [v tématu Konfigurace integrace úložiště klíčů Azure pro SQL Server na virtuálních počítačích Azure (Classic).](../classic/ps-sql-keyvault.md) |

## <a name="prerequisites"></a>Požadavky
Požadavky na použití rozšíření agenta SQL Server IaaS na vašem virtuálním počítači:

### <a name="operating-system"></a>Operační systém:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Verze serveru SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Stáhněte a nakonfigurujte nejnovější příkazy Azure PowerShellu](/powershell/azure/overview).

Spusťte Windows PowerShell a připojte ho k předplatnému Azure pomocí příkazu **Přidat azureúčet.**

    Add-AzureAccount

Pokud máte více předplatných, použijte **Select-AzureSubscription** k výběru předplatného, který obsahuje váš cílový klasický virtuální počítač.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

V tomto okamžiku můžete získat seznam klasických virtuálních počítačů a jejich přidružené názvy služeb pomocí příkazu **Get-AzureVM.**

    Get-AzureVM

## <a name="installation"></a>Instalace
U klasických virtuálních počítače je nutné použít prostředí PowerShell k instalaci rozšíření agenta SERVERU SQL Server IaaS a ke konfiguraci jeho přidružených služeb. K instalaci rozšíření použijte rutinu Nastavení **AzureVMSqlServerExtension** PowerShell. Například následující příkaz nainstaluje rozšíření na virtuální počítač se systémem Windows Server (klasické) a pojmenuje jej "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Pokud aktualizujete na nejnovější verzi rozšíření agenta SQL IaaS, musíte restartovat virtuální počítač po aktualizaci rozšíření.

> [!NOTE]
> Klasické virtuální počítače nemají možnost nainstalovat a nakonfigurovat rozšíření agenta SQL IaaS prostřednictvím portálu.

> [!NOTE]
> Rozšíření agenta SQL Server IaaS je podporováno jenom na [iniciálách galerie virtuálních počítačů SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (průběžně zdatné nebo s vlastní licencí). Není podporována, pokud ručně nainstalujete SQL Server do virtuálního počítače se systémem Windows Server pouze v systému OS nebo pokud nasadíte vlastní virtuální pevný disk v pevném rozlišení SQL Server. V těchto případech může být možné nainstalovat a spravovat rozšíření ručně pomocí prostředí PowerShell, ale důrazně doporučujeme místo toho nainstalovat bitovou kopii galerie virtuálních počítačů SQL Server a pak ji přizpůsobit.

## <a name="status"></a>Status
Jedním ze způsobů, jak ověřit, že rozšíření je nainstalován, je zobrazit stav agenta na webu Azure Portal. Vyberte virtuální počítač uvedený v okně virtuálního počítače a klikněte na **Rozšíření**. Měli byste vidět **rozšíření SQLIaaSAgent** uvedeny.

![Rozšíření agenta SQL Server IaaS na webu Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Můžete také použít rutinu **Get-AzureVMSqlServerExtension** Azure Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Odstranění
Na webu Azure Portal můžete rozšíření odinstalovat kliknutím na tři **tečky** v okně Rozšíření vlastností virtuálního počítače. Potom klepněte na tlačítko **Odinstalovat**.

![Odinstalace rozšíření agenta SQL Server IaaS na webu Azure Portal](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Můžete také použít rutinu **Remove-AzureVMSqlServerExtension** Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Další kroky
Začněte používat jednu ze služeb podporovaných rozšířením. Další podrobnosti naleznete v tématech uvedených v části [Podporované služby](#supported-services) v tomto článku.

Další informace o spuštění SQL Serveru ve virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure .](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

