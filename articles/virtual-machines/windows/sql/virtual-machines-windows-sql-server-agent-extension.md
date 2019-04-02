---
title: Automatizace úloh správy na virtuálních počítačích SQL (Resource Manager) | Dokumentace Microsoftu
description: Tento článek popisuje, jak spravovat rozšíření agenta SQL serveru, který automatizuje určité úlohy správy systému SQL Server. Patří mezi ně automatizovaného zálohování, automatické opravy a integrace Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 71878d5d033f0005d2c8c36d9f59799e125a19dd
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762697"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizace úloh správy ve službě Azure Virtual Machines pomocí rozšíření agenta SQL serveru (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Rozšíření agenta SQL Server IaaS (SqlIaasExtension) běží na virtuálních počítačích Azure za účelem automatizace úloh správy. Tento článek obsahuje přehled služby podporuje rozšíření, stejně jako pokyny pro instalaci, stavu a odebrání.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Chcete-li zobrazit klasickou verzi tohoto článku, najdete v článku [rozšíření agenta SQL serveru pro SQL Server virtuální počítače Classic](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

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

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze systému SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Stáhnout a nakonfigurovat nejnovější příkazy Azure Powershellu](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> V tuto chvíli [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) není podporována pro FCI Server SQL v Azure. Doporučujeme odinstalovat rozšíření z virtuálních počítačů, které jsou součástí FCI. Funkce podporované rozšíření nejsou k dispozici pro virtuální počítače s SQL, po daný agent nebude odinstalován.

## <a name="installation"></a>Instalace
Rozšíření agenta SQL Server IaaS je automaticky nainstalován při zřizování Image Galerie virtuálních počítačů SQL serveru. Rozšíření SQL IaaS nabízí možnosti správy pro jednu instanci na virtuálním počítači SQL serveru. Dojde-li výchozí instanci, pak bude rozšíření fungovat s výchozí instance a nebude podporovat, správu ostatních instancí. Pokud není žádná výchozí instance, ale pouze jednu pojmenovanou instanci, bude ho spravovat pojmenovanou instanci. Pokud není žádná výchozí instance a existuje víc instancí s názvem, se nezdaří rozšíření k instalaci. 



Pokud je potřeba ručně znovu nainstalujte rozšíření na jednom z těchto virtuálních počítačů SQL serveru, použijte následující příkaz Powershellu:

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> Pokud rozšíření ještě není nainstalovaná, instalaci rozšíření restartuje službu systému SQL Server. Aktualizuje se rozšíření SQL IaaS však nerestartuje služby SQL Server. 

> [!NOTE]
> I když je možné nainstalovat rozšíření agenta SQL Server IaaS vlastní Image SQL serveru, funkce jsou aktuálně omezené na [Změna typu licence](virtual-machines-windows-sql-ahb.md). Další funkce, které poskytuje rozšíření SQL IaaS budou fungovat jenom na [Image virtuálního počítače s SQL serverem z Galerie](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (s průběžnými platbami nebo přeneste svůj – používání vlastní licence).

### <a name="use-a-single-named-instance"></a>Pomocí jediné pojmenovaná instance
Rozšíření SQL IaaS bude fungovat s pojmenovanou instanci na bitovou kopii systému SQL Server, pokud výchozí instance je správně odinstalovat a přeinstalovat rozšíření IaaS.

Pokud chcete použít pojmenovanou instanci systému SQL Server, postupujte takto:
   1. Nasazení virtuálního počítače s SQL serverem z marketplace. 
   1. Odinstalovat rozšíření IaaS v rámci [webu Azure portal](https://portal.azure.com).
   1. Odinstalujte systém SQL Server zcela v rámci virtuálního počítače SQL serveru.
   1. Nainstalujte SQL Server pomocí pojmenované instance na virtuálním počítači SQL serveru. 
   1. Instalace rozšíření IaaS z webu Azure portal.  

## <a name="status"></a>Status
Chcete-li zobrazit stav agenta na webu Azure Portal je jeden způsob, jak ověřit, že rozšíření je nainstalované. Vyberte **všechna nastavení** v okně virtuálního počítače a potom kliknout na **rozšíření**. Měli byste vidět **SqlIaasExtension** uvedená rozšíření.

![Rozšíření agenta SQL Server IaaS na webu Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Můžete také použít **Get-AzVMSqlServerExtension** rutiny Azure Powershellu.

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Předchozí příkaz potvrdí, agent je nainstalovaný a poskytuje obecné informace stavu. Získáte také konkrétní stavové informace o automatické zálohování a opravy pomocí následujících příkazů.

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Odebrání
Na webu Azure Portal, můžete po kliknutí na tři tečky na odinstalovat rozšíření **rozšíření** okno vlastností virtuálního počítače. Potom klikněte na **Odstranit**.

![Odinstalace rozšíření agenta SQL Server IaaS na webu Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Můžete také použít **odebrat AzVMSqlServerExtension** rutiny Powershellu.

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>Další postup
Začněte používat jednu ze služeb podporovaný rozšířením. Další podrobnosti najdete v článcích odkazuje [podporované služby](#supported-services) části tohoto článku.

Další informace o spuštění SQL serveru na virtuálních počítačích Azure najdete v tématu [systému SQL Server na Azure Virtual Machines – přehled](virtual-machines-windows-sql-server-iaas-overview.md).

