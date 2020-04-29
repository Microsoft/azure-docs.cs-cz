---
title: Automatizované opravy pro virtuální počítače s SQL Server (Správce prostředků) | Microsoft Docs
description: Vysvětluje funkci automatizované opravy SQL Server Virtual Machines spuštěnou v Azure pomocí Správce prostředků.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 292f3e8819f6f9f4b2989423814e02dfcfb4bfdb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127681"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatizované opravy pro SQL Server v Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatizované opravy vytvoří pro virtuální počítač Azure s SQL Serverem časové období údržby. Automatizované aktualizace je možné nainstalovat pouze během tohoto časového období údržby. V případě SQL Serveru toto omezení zajišťuje, že aktualizace systému a případná restartování proběhnou v době, která je pro databázi nejvhodnější. 

> [!IMPORTANT]
> Jsou nainstalovány pouze aktualizace Windows a SQL Server označené jako **důležité** nebo **kritické** . Další aktualizace SQL Server, jako jsou aktualizace Service Pack a kumulativní aktualizace, které nejsou označeny jako **důležité** nebo **kritické** , musí být nainstalovány ručně. 

Automatizované opravy závisí na [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete používat automatizované opravy, vezměte v úvahu následující požadavky:

**Operační systém**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze SQL Server**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* Pokud plánujete nakonfigurovat automatizované opravy pomocí PowerShellu, [nainstalujte nejnovější příkazy Azure PowerShell](/powershell/azure/overview) .

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatizované opravy spoléhá na rozšíření agenta SQL Server IaaS. Aktuální Image Galerie virtuálních počítačů v systému SQL toto rozšíření ve výchozím nastavení přidat. Další informace najdete v tématu [SQL Server rozšíření agenta IaaS](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které je možné nakonfigurovat pro automatizované opravy. Skutečné kroky konfigurace se liší v závislosti na tom, jestli používáte příkazy Azure Portal nebo Azure Windows PowerShellu.

| Nastavení | Možné hodnoty | Popis |
| --- | --- | --- |
| **Automatizované opravy** |Povolit/zakázat (zakázáno) |Povolí nebo zakáže automatizované opravy pro virtuální počítač Azure. |
| **Plán údržby** |Každodenní, pondělí, úterý, středa, čtvrtek, pátek, sobota, neděle |Plán pro stažení a instalaci aktualizací Windows, SQL Server a Microsoft pro váš virtuální počítač. |
| **Hodina zahájení údržby** |0-24 |Místní čas zahájení aktualizace virtuálního počítače. |
| **Doba trvání časového období údržby** |30-180 |Počet minut, po které je povoleno dokončení stahování a instalace aktualizací. |
| **Kategorie opravy** |Důležité | Kategorie aktualizací Windows, které se mají stáhnout a nainstalovat|

## <a name="configuration-in-the-portal"></a>Konfigurace na portálu
Pomocí Azure Portal můžete nakonfigurovat automatizované opravy během zřizování nebo pro stávající virtuální počítače.

### <a name="new-vms"></a>Nové virtuální počítače
Pomocí Azure Portal můžete nakonfigurovat automatizované opravy při vytváření nového virtuálního počítače s SQL Server v modelu nasazení Správce prostředků.

Na kartě **nastavení SQL Server** v části **automatizovaná oprava**proveďte možnost **změnit konfiguraci** . Na následujícím Azure Portal snímku obrazovky se zobrazuje okno **automatizované opravy SQL** .

![Automatizované opravy SQL v Azure Portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Kontext najdete v tématu úplné téma o [zřízení SQL Server virtuálního počítače v Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Existující virtuální počítače

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U stávajících virtuálních počítačů s SQL Server otevřete svůj [prostředek virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) a v části **Nastavení**vyberte **opravit** . 

![Automatické opravy SQL pro existující virtuální počítače](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Po dokončení klikněte na tlačítko **OK** v dolní části okna **Konfigurace SQL Server** a uložte provedené změny.

Pokud povolíte automatizované opravy poprvé, Azure nakonfiguruje agenta SQL Server IaaS na pozadí. Během této doby se Azure Portal nemusí ukázat, že je nakonfigurovaná automatizovaná oprava. Počkejte několik minut, než se agent nainstaluje a nakonfiguruje. Poté, co Azure Portal odráží nové nastavení.

## <a name="configuration-with-powershell"></a>Konfigurace pomocí PowerShellu
Po zřízení virtuálního počítače s SQL nakonfigurujte automatizované opravy pomocí PowerShellu.

V následujícím příkladu se prostředí PowerShell používá ke konfiguraci automatizované opravy na stávajícím virtuálním počítači s SQL Server. Příkaz **New-AzVMSqlServerAutoPatchingConfig** nakonfiguruje nové okno údržby pro automatické aktualizace.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s set-AzVMSqlServerExtension-AutoPatchingSettings $aps-VMName $vmname-ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Pokud rozšíření ještě není nainstalované, instalace rozšíření restartuje službu SQL Server.

Na základě tohoto příkladu popisuje následující tabulka praktický efekt na cílovém virtuálním počítači Azure:

| Parametr | Účinek |
| --- | --- |
| **DayOfWeek** |Opravy se nainstalují každý čtvrtek. |
| **MaintenanceWindowStartingHour** |Začněte s aktualizacemi na 11:10:00. |
| **MaintenanceWindowsDuration** |Opravy musí být nainstalované během 120 minut. V závislosti na čase spuštění musí být dokončeny pomocí 1:13:00. |
| **PatchCategory** |Jediným možným nastavením pro tento parametr je **důležité**. Tato instalace nainstaluje službu Windows Update označenou jako důležitou; neinstaluje žádné aktualizace SQL Server, které nejsou zahrnuty v této kategorii. |

Instalace a konfigurace agenta SQL Server IaaS může trvat několik minut.

Chcete-li zakázat automatizované opravy, spusťte stejný skript bez parametru **-Enable** pro rutinu **New-AzVMSqlServerAutoPatchingConfig**. Pokud parametr **-Enable** nesignalizuje, příkaz tuto funkci zakáže.

## <a name="next-steps"></a>Další kroky
Informace o dalších dostupných úlohách automatizace najdete v tématu [SQL Server rozšíření agenta IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Další informace o spuštění SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na azure Virtual Machines přehled](virtual-machines-windows-sql-server-iaas-overview.md).

