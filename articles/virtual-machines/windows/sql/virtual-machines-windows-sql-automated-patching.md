---
title: Automatizované opravy pro virtuální počítače s SQL serverem (Resource Manager) | Dokumentace Microsoftu
description: Vysvětluje funkci automatizované opravy pro SQL Server virtuální počítače provozované v Azure pomocí Resource Manageru.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 4f0d681c93ab7ac7fef941892a95282a2fd59b89
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075721"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatizované opravy pro SQL Server v Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatizované opravy vytváří časového období údržby pro virtuální počítač Azure s SQL serverem. Automatizované aktualizace je možné nainstalovat pouze během tohoto časového období údržby. V případě SQL Serveru toto omezení zajišťuje, že aktualizace systému a případná restartování proběhnou v době, která je pro databázi nejvhodnější. 

> [!IMPORTANT]
> Nainstalují se pouze aktualizace Windows označené jako **Důležité**. Ostatní aktualizace SQL Serveru, jako jsou kumulativní aktualizace, je potřeba nainstalovat ručně. 

Automatizované opravy závisí na [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete použít, automatické opravy, vezměte v úvahu následující požadavky:

**Operační systém**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze systému SQL Server**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Nainstalujte nejnovější Azure PowerShell příkazy](/powershell/azure/overview) Pokud plánujete nakonfigurovat automatické opravy s využitím Powershellu.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatizované opravy spoléhá na rozšíření agenta SQL Server IaaS. Aktuální Image SQL z Galerie virtuálních počítačů přidat toto rozšíření ve výchozím nastavení. Další informace najdete v tématu [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které je možné nakonfigurovat pro automatizované opravy. Skutečný konfigurační kroky se liší v závislosti na tom, zda používáte portál Azure portal nebo příkazů Windows Powershellu Azure.

| Nastavení | Možné hodnoty | Popis |
| --- | --- | --- |
| **Automatizované opravy** |Povolí nebo zakáže (zakázáno) |Povolí nebo zakáže automatické opravy pro virtuální počítač Azure. |
| **Plán údržby** |Každý den od pondělí, úterý, středa, čtvrtek, pátek, sobota, neděle |Plán pro stahování a instalaci aktualizací Windows, SQL Server a Microsoft pro virtuální počítač. |
| **Hodina spouštění údržby** |0-24 |Místní spuštění aktualizace virtuálního počítače. |
| **Trvání časového období údržby** |30-180 |Počet minut povoleno dokončení stahování a instalaci aktualizací. |
| **Oprava kategorie** |Důležité | Kategorie aktualizací Windows ke stažení a instalaci.|

## <a name="configuration-in-the-portal"></a>Konfigurace na portálu
Na webu Azure portal můžete použít ke konfiguraci automatické opravy během zřizování nebo pro stávající virtuální počítače.

### <a name="new-vms"></a>Nové virtuální počítače
Konfigurovat automatické opravy při vytváření nového virtuálního počítače SQL serveru v modelu nasazení Resource Manageru pomocí webu Azure portal.

V **nastavení systému SQL Server** kartu, vyberte možnost **změna konfigurace** pod **automatické opravy**. Ukazuje následující snímek obrazovky Azure portal **automatizované opravy SQL** okno.

![Automatizované opravy SQL na webu Azure portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Kontext, naleznete v tématu dokončení na [zřizování virtuálního počítače s SQL serverem v Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Stávající virtuální počítače

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Existující virtuální počítače systému SQL Server, otevřete vaši [prostředků virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) a vyberte **opravy** pod **nastavení**. 

![Automatické opravy SQL pro stávající virtuální počítače](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Až budete hotovi, klikněte na tlačítko **OK** tlačítko v dolní části **konfigurace systému SQL Server** okno a uložte provedené změny.

Chcete-li povolit automatické opravy poprvé, Azure nakonfiguruje agenta SQL Server IaaS na pozadí. Během této doby nemusí zobrazit na webu Azure portal, že je nakonfigurovaný automatizované opravy. Počkejte několik minut, než agent nainstalován, nakonfigurován. Následně se odráží na webu Azure portal nové nastavení.

## <a name="configuration-with-powershell"></a>Konfigurace pomocí Powershellu
Po zřízení virtuálního počítače s SQL, pomocí prostředí PowerShell můžete nakonfigurovat automatizované opravy.

V následujícím příkladu prostředí PowerShell slouží ke konfiguraci automatické opravy na existujícím virtuálním počítači SQL serveru. **New-AzVMSqlServerAutoPatchingConfig** příkaz nakonfiguruje nové časové období údržby pro automatické aktualizace.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Pokud rozšíření ještě není nainstalovaná, instalaci rozšíření restartuje službu systému SQL Server.

Podle tohoto příkladu, následující tabulka popisuje praktický na cílovém virtuálním počítači Azure:

| Parametr | Efekt |
| --- | --- |
| **DayOfWeek** |Každý čtvrtek nainstalovány opravy. |
| **MaintenanceWindowStartingHour** |Zahájení aktualizace v 11:00. |
| **MaintenanceWindowsDuration** |Opravy musí být nainstalován během 120 minut. Podle času spuštění, musí provést v 1:00 pm. |
| **PatchCategory** |Jedinou možnou nastavení pro tento parametr je **důležité**. Tím se nainstaluje aktualizace Windows označené důležité; to není možné nainstalovat všechny aktualizace systému SQL Server, které nejsou zahrnuté v této kategorii. |

To může trvat několik minut, instalaci a konfiguraci agenta SQL Server IaaS.

Chcete-li zakázat automatické opravy, spusťte stejný skript bez **-povolit** parametr **New-AzVMSqlServerAutoPatchingConfig**. Chybí **-povolit** parametr signály příkazu zakažte funkci.

## <a name="next-steps"></a>Další postup
Informace o dalších úlohách dostupných automation najdete v tématu [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Další informace o spuštění SQL serveru na virtuálních počítačích Azure najdete v tématu [systému SQL Server na Azure Virtual Machines – přehled](virtual-machines-windows-sql-server-iaas-overview.md).

