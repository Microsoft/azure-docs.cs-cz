---
title: Automatické opravy pro virtuální servery SQL Server (Správce prostředků) | Dokumenty společnosti Microsoft
description: Vysvětluje funkci automatické opravy pro virtuální počítače SQL Serveru spuštěné v Azure pomocí Správce prostředků.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127681"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatizované opravy pro SQL Server v Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatizované opravy vytvoří pro virtuální počítač Azure s SQL Serverem časové období údržby. Automatizované aktualizace je možné nainstalovat pouze během tohoto časového období údržby. V případě SQL Serveru toto omezení zajišťuje, že aktualizace systému a případná restartování proběhnou v době, která je pro databázi nejvhodnější. 

> [!IMPORTANT]
> Jsou nainstalovány pouze aktualizace systému Windows a serveru SQL Server označené jako **důležité** nebo **kritické.** Ostatní aktualizace serveru SQL Server, například aktualizace Service Pack a kumulativní aktualizace, které nejsou označeny jako **důležité** nebo **důležité,** musí být nainstalovány ručně. 

Automatizované opravy závisí na [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Požadavky
Chcete-li používat automatické opravy, zvažte následující požadavky:

**Operační systém**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze serveru SQL Server**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* Pokud plánujete nakonfigurovat automatické opravy pomocí PowerShellu, [nainstalujte nejnovější příkazy Azure PowerShellu.](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Automatické opravy závisí na rozšíření agenta SQL Server IaaS. Aktuální sql virtuální počítač galerie obrázky přidat toto rozšíření ve výchozím nastavení. Další informace naleznete v tématu [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které lze nakonfigurovat pro automatické opravy. Skutečné kroky konfigurace se liší v závislosti na tom, jestli používáte portál Azure nebo příkazy Azure Windows PowerShell.

| Nastavení | Možné hodnoty | Popis |
| --- | --- | --- |
| **Automatické opravy** |Povolit/zakázat (zakázáno) |Povolí nebo zakáže automatické opravy pro virtuální počítač Azure. |
| **Plán údržby** |Každý den, pondělí, úterý, středa, čtvrtek, pátek, sobota, neděle |Plán stahování a instalace aktualizací systému Windows, SQL Server a Microsoft pro váš virtuální počítač. |
| **Počáteční hodina údržby** |0-24 |Místní čas spuštění aktualizace virtuálního počítače. |
| **Doba trvání okna údržby** |30-180 |Počet minut povolených k dokončení stahování a instalace aktualizací. |
| **Kategorie opravy** |Důležité | Kategorie aktualizací systému Windows ke stažení a instalaci.|

## <a name="configuration-in-the-portal"></a>Konfigurace na portálu
Portál Azure můžete použít ke konfiguraci automatické opravy během zřizování nebo pro stávající virtuální počítače.

### <a name="new-vms"></a>Nové virtuální společnosti
Na webu Azure Portal nakonfigurujte automatické opravy při vytváření nového virtuálního počítače SQL Serveru v modelu nasazení Správce prostředků.

Na kartě **Nastavení serveru SQL Server** vyberte možnost Změnit **konfiguraci** v části **Automatické opravy**. Následující snímek obrazovky portálu Azure zobrazuje okno **automatické opravy SQL.**

![Automatické opravy SQL na webu Azure Portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Kontext najdete v tématu úplné téma o [zřizování virtuálního počítače SQL Server v Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Existující virtuální chod

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U existujících virtuálních počítačů SQL Serveru otevřete [prostředek virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) a v části **Nastavení**vyberte **Možnost Oprava** . 

![Automatické opravy SQL pro existující virtuální servery](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Po dokončení můžete změny uložit klepnutím na tlačítko **OK** v dolní části **konfiguračního** okna serveru SQL Server.

Pokud poprvé povolíte automatické opravy, Azure nakonfiguruje agenta SQL Server IaaS na pozadí. Během této doby portál Azure nemusí zobrazit, že automatické opravy je nakonfigurovaný. Počkejte několik minut, než bude agent nainstalován, nakonfigurován. Poté portál Azure odráží nové nastavení.

## <a name="configuration-with-powershell"></a>Konfigurace s PowerShellem
Po zřízení virtuálního počítače SQL nakonfigurujte automatické opravy pomocí prostředí PowerShell.

V následujícím příkladu se prostředí PowerShell používá ke konfiguraci automatickéopravy na existujícím virtuálním počítači sql serveru. Příkaz **New-AzVMSqlServerAutoPatchingConfig** konfiguruje nové okno údržby pro automatické aktualizace.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Pokud rozšíření ještě není nainstalováno, instalace rozšíření restartuje službu SQL Server.

Na základě tohoto příkladu následující tabulka popisuje praktický vliv na cílový virtuální počítač Azure:

| Parametr | Účinek |
| --- | --- |
| **Dayofweek** |Patche instalovány každý čtvrtek. |
| **MaintenanceWindowStartingHour** |Aktualizace můžete zahájit v 11:00. |
| **ÚdržbaWindowsDoba trvání** |Záplaty musí být nainstalovány do 120 minut. Na základě času zahájení musí být dokončeny do 13:00. |
| **Kategorie patch** |Jediné možné nastavení pro tento parametr je **Důležité**. Tím nainstalujete aktualizaci systému Windows označenou jako Důležité; nenainstaluje žádné aktualizace serveru SQL Server, které nejsou zahrnuty v této kategorii. |

Instalace a konfigurace agenta SQL Server IaaS agenta serveru SQL Server může trvat několik minut.

Chcete-li zakázat automatické opravy, spusťte stejný skript bez parametru **-Enable** na **příkaz New-AzVMSqlServerAutoPatchingConfig**. Absence parametru **-Enable** signalizuje příkaz k zakázání funkce.

## <a name="next-steps"></a>Další kroky
Informace o dalších dostupných úlohách automatizace naleznete v tématu [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md).

Další informace o spuštění SQL Serveru na virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure přehled](virtual-machines-windows-sql-server-iaas-overview.md).

