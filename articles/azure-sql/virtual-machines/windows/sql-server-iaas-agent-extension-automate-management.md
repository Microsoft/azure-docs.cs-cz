---
title: Automatizace úloh správy pomocí rozšíření agenta IaaS
description: Tento článek popisuje, jak spravovat rozšíření agenta SQL Server IaaS, které automatizuje konkrétní úlohy správy SQL Server. Mezi ně patří automatizované zálohování, automatizované opravy a Integrace Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: df3e2ae2737aa8bfedd0e8d7daa9d42771f1937b
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570344"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizace úloh správy na virtuálních počítačích Azure pomocí rozšíření agenta SQL Server IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Rozšíření agenta SQL Server IaaS (SqlIaasExtension) běží na virtuálních počítačích Azure za účelem automatizace úloh správy. Tento článek poskytuje přehled služeb, které podporuje rozšíření. Tento článek také poskytuje pokyny k instalaci, stavu a odebrání rozšíření.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Pokud chcete zobrazit klasickou verzi tohoto článku, přečtěte si téma [SQL Server rozšíření agenta IaaS pro SQL Server virtuální počítače (Classic)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Podporované služby
Rozšíření agenta SQL Server IaaS podporuje následující úlohy správy:

| Funkce správy | Description |
| --- | --- |
| **SQL Server automatizované zálohování** |Automatizuje plánování záloh pro všechny databáze, a to buď pro výchozí instanci, nebo pro [správnou nainstalovanou](frequently-asked-questions-faq.md#administration) pojmenovanou instanci SQL Server na virtuálním počítači. Další informace najdete v tématu [automatizované zálohování pro SQL Server ve virtuálních počítačích Azure (Správce prostředků)](automated-backup-sql-2014.md). |
| **SQL Server automatizované opravy** |Nakonfiguruje časový interval pro správu a údržbu, během kterého může probíhat důležitá aktualizace Windows pro váš virtuální počítač, takže se můžete vyhnout aktualizacím během špičky pro vaše zatížení. Další informace najdete v tématu [automatizované opravy pro SQL Server ve virtuálních počítačích Azure (Správce prostředků)](automated-patching.md). |
| **Integrace se službou Azure Key Vault** |Umožňuje automaticky nainstalovat a nakonfigurovat Azure Key Vault na VIRTUÁLNÍm počítači s SQL Server. Další informace najdete v tématu [Konfigurace integrace Azure Key Vault pro SQL Server v Azure Virtual Machines (Správce prostředků)](azure-key-vault-integration-configure.md). |

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
* Windows Server 2019 

**Verze SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [Stažení a konfigurace nejnovějších příkazů Azure PowerShell](/powershell/azure/)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Instalace
Rozšíření SQL Server IaaS se nainstaluje při registraci SQL Server virtuálního počítače pomocí [poskytovatele prostředků SQL Server virtuálního počítače](sql-vm-resource-provider-register.md). V případě potřeby můžete ručně nainstalovat agenta SQL Server IaaS pomocí následujícího příkazu PowerShellu: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!WARNING]
> Instalace rozšíření restartuje službu SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalace na virtuální počítač s jednou s názvem SQL Server instance
Rozšíření SQL Server IaaS bude pracovat s pojmenovanou instancí v SQL Server, pokud je výchozí instance odinstalována a přeinstalováno rozšíření IaaS.

Chcete-li použít pojmenovanou instanci SQL Server, postupujte podle následujících kroků:
   1. Nasazení virtuálního počítače s SQL Server z Azure Marketplace. 
   1. Odinstalujte rozšíření IaaS z [Azure Portal](https://portal.azure.com).
   1. Odinstalace SQL Server kompletně v rámci SQL Server virtuálního počítače.
   1. Nainstalujte SQL Server s pojmenovanou instancí v rámci SQL Server virtuálního počítače. 
   1. Nainstalujte rozšíření IaaS z Azure Portal.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Získat stav rozšíření SQL Server IaaS
Jedním ze způsobů, jak ověřit, že je rozšíření nainstalované, je zobrazit stav agenta v Azure Portal. V okně virtuálního počítače vyberte **všechna nastavení** a pak vyberte **rozšíření**. Měla by se zobrazit uvedená rozšíření **SqlIaasExtension** .

![Stav rozšíření agenta SQL Server IaaS v Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

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

## <a name="removal"></a>Instalační
V Azure Portal můžete rozšíření odinstalovat tak, že vyberete tři tečky v okně **rozšíření** ve vlastnostech virtuálního počítače. Vyberte **Odstranit**.

![Odinstalace rozšíření agenta SQL Server IaaS v Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

Můžete také použít rutinu prostředí PowerShell **Remove-AzVMSqlServerExtension** :

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Další kroky
Začněte používat jednu ze služeb, které podporuje rozšíření. Další informace najdete v článcích, na které se odkazuje v tomto článku v části věnované [podporovaným službám](#supported-services) .

Další informace o spouštění SQL Server v Azure Virtual Machines najdete v tématu [co je SQL Server na Virtual Machines Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
