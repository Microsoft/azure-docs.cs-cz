---
title: Automatizace úloh správy pomocí rozšíření Agent IaaS
description: Tento článek popisuje, jak spravovat rozšíření agenta SQL Server IaaS, které automatizuje konkrétní úlohy správy serveru SQL Server. Patří mezi ně automatické zálohování, automatické opravy a integrace Azure Key Vault.
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
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77030774"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizace úloh správy na virtuálních počítačích Azure pomocí rozšíření SQL Server IaaS Agent Extension
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Rozšíření agenta SQL Server IaaS (SqlIaasExtension) běží na virtuálních počítačích Azure za účelem automatizace úloh správy. Tento článek obsahuje přehled služeb, které podporuje rozšíření. Tento článek také obsahuje pokyny pro instalaci, stav a odebrání rozšíření.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Chcete-li zobrazit klasickou verzi tohoto článku, naleznete v tématu [SQL Server IaaS Agent Extension for SQL Server Virtuální server (klasické)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Podporované služby
Rozšíření agenta serveru SQL Server IaaS podporuje následující úlohy správy:

| Funkce správy | Popis |
| --- | --- |
| **Automatické zálohování serveru SQL Server** |Automatizuje plánování záloh pro všechny databáze pro výchozí instanci nebo [správně nainstalovanou](virtual-machines-windows-sql-server-iaas-faq.md#administration) pojmenovanou instanci serveru SQL Server na virtuálním počítači. Další informace najdete [v tématu Automatické zálohování pro SQL Server ve virtuálních počítačích Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Automatické opravy serveru SQL Server** |Nakonfiguruje okno údržby, během kterého může probíhat důležité aktualizace windows na vašem virtuálním počítači, takže se můžete vyhnout aktualizacím během špičky pro vaše úlohy. Další informace naleznete [v tématu Automatické opravy pro SQL Server ve virtuálních počítačích Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrace azure trezoru klíčů** |Umožňuje automaticky nainstalovat a nakonfigurovat Azure Key Vault na vašem virtuálním počítači SQL Server. Další informace najdete [v tématu Konfigurace integrace Azure Key Vault pro SQL Server na virtuálních počítačích Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Po instalaci a spuštění rozšíření agenta serveru SQL Server Iaas zpřístupní funkce správy:

* Na panelu SQL Serveru virtuálního počítače na webu Azure Portal a prostřednictvím azure powershellu pro sql server na Azure Marketplace.
* Prostřednictvím Azure PowerShell pro ruční instalace rozšíření. 

## <a name="prerequisites"></a>Požadavky
Tady jsou požadavky na použití rozšíření agenta SQL Server IaaS na vašem virtuálním počítači:

**Operační systém**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Verze serveru SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [Stažení a konfigurace nejnovějších příkazů Azure PowerShellu](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Instalace
Rozšíření SQL Server IaaS se nainstaluje při registraci virtuálního počítače SQL Server u [poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md). V případě potřeby můžete agenta SERVERU SQL Server IaaS nainstalovat ručně pomocí níže uvedeného příkazu prostředí PowerShell: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> Instalací rozšíření se restartuje služba SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalace na virtuální počítač s jedinou pojmenovanou instancí SERVERU SQL Server
Rozšíření SQL Server IaaS bude fungovat s pojmenovanou instancí na serveru SQL Server, pokud je výchozí instance odinstalována a rozšíření IaaS je přeinstalováno.

Chcete-li použít pojmenovanou instanci serveru SQL Server, postupujte takto:
   1. Nasazení virtuálního počítače SQL Serveru z Azure Marketplace. 
   1. Odinstalujte rozšíření IaaS z [webu Azure Portal](https://portal.azure.com).
   1. Odinstalujte SQL Server zcela v rámci virtuálního serveru SQL Server.
   1. Nainstalujte SQL Server s pojmenovanou instancí v rámci virtuálního serveru SQL Server. 
   1. Nainstalujte rozšíření IaaS z webu Azure Portal.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Získání stavu rozšíření SQL Server IaaS
Jedním ze způsobů, jak ověřit, že rozšíření je nainstalován, je zobrazit stav agenta na webu Azure Portal. V okně virtuálního počítače vyberte **Všechna nastavení** a pak vyberte **Rozšíření**. Měli byste vidět **rozšíření SqlIaasExtension** uvedeny.

![Stav rozšíření agenta SQL Server IaaS na webu Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Můžete také použít rutinu **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Předchozí příkaz potvrzuje, že je agent nainstalován a poskytuje obecné informace o stavu. Konkrétní informace o stavu automatického zálohování a oprav můžete získat pomocí následujících příkazů:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Odstranění
Na webu Azure Portal můžete rozšíření odinstalovat výběrem tři **tečky** v okně Rozšíření vlastností virtuálního počítače. Vyberte **Odstranit**.

![Odinstalace rozšíření agenta SQL Server IaaS na webu Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Můžete také použít rutinu **Remove-AzVMSqlServerExtension** PowerShell:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Další kroky
Začněte používat jednu ze služeb, které rozšíření podporuje. Další informace naleznete v článcích uvedených v části [Podporované služby](#supported-services) v tomto článku.

Další informace o spuštění SQL Serveru na virtuálních počítačích Azure najdete v tématu [Co je SQL Server na virtuálních počítačích Azure?](virtual-machines-windows-sql-server-iaas-overview.md).
