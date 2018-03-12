---
title: "Automatizované opravy pro virtuální počítače serveru SQL (Resource Manager) | Microsoft Docs"
description: "Vysvětluje funkci automatizované opravy pro SQL Server virtuální počítače běžící v Azure pomocí Správce prostředků."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: 
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.openlocfilehash: 398e682db6c42bd7f4864113ddf10a6a75e2b65b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatizované opravy pro SQL Server v Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatizovaných oprav určuje časové období údržby pro virtuální počítač Azure systémem SQL Server. Automatické aktualizace lze nainstalovat pouze během tohoto časového období údržby. Pro systém SQL Server tento rescriction zajistí, že aktualizace systému a všechny přidružené restartuje dojít na nejlepší možný čas pro databázi. 

> [!IMPORTANT]
> Jenom Windows aktualizace označené **důležité** jsou nainstalovány. Další aktualizace systému SQL Server, jako je například kumulativní aktualizace, musí být nainstalován ručně. 

Automatizovaných oprav závisí na [rozšíření agenta systému SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Požadavky
Pomocí automatizované opravy, zvažte následující požadavky:

**Operační systém**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze systému SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Nainstalujte nejnovější příkazy prostředí Azure PowerShell](/powershell/azure/overview) Pokud chcete konfigurovat automatizovaných oprav pomocí prostředí PowerShell.

> [!NOTE]
> Automatizovaných oprav spoléhá na rozšíření agenta systému SQL Server IaaS. Aktuální SQL bitové kopie virtuálních počítačů Galerie přidejte toto rozšíření ve výchozím nastavení. Další informace najdete v tématu [rozšíření agenta systému SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které mohou být konfigurovány pro automatizované opravy. Skutečné konfiguračních kroků se liší v závislosti na tom, zda používáte portál Azure nebo příkazů prostředí Windows PowerShell pro Azure.

| Nastavení | Možné hodnoty | Popis |
| --- | --- | --- |
| **Automatizované opravy** |Povolí nebo zakáže (zakázáno) |Povolí nebo zakáže automatizované opravy pro virtuální počítač Azure. |
| **Plán údržby.** |Každý den, pondělí, úterý, středu, čtvrtek a pátek, sobota, neděle |Plán pro stahování a instalace aktualizací s Windows, SQL Server a Microsoft pro virtuální počítač. |
| **Hodina spouštění údržby** |0-24 |Místní spuštění aktualizovat virtuální počítač. |
| **Doba trvání okna údržby** |30-180 |Počet minut oprávnění k dokončení stahování a instalaci aktualizací. |
| **Oprava kategorie** |Důležité | Kategorie aktualizace systému Windows ke stažení a instalaci.|

## <a name="configuration-in-the-portal"></a>Konfigurace na portálu
Na portálu Azure můžete použít ke konfiguraci automatizovaných oprav při zřizování nebo pro existující virtuální počítače.

### <a name="new-vms"></a>Nové virtuální počítače
Použití portálu Azure ke konfiguraci automatizovaných oprav při vytváření nového virtuálního počítače SQL serveru v modelu nasazení Resource Manager.

V **nastavení systému SQL Server** vyberte **automatizované opravy**. Následující Azure portálu snímek obrazovky ukazuje **automatizovaných oprav SQL** okno.

![Automatizovaných oprav SQL na portálu Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Kontext, naleznete v tématu dokončení na [zřizování virtuálního počítače s SQL serverem v Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Existující virtuální počítače
Pro existující virtuální počítače systému SQL Server vyberte virtuální počítač systému SQL Server. Vyberte **konfigurace systému SQL Server** části **nastavení** okno.

![Automatické opravy SQL pro existující virtuální počítače](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

V **konfigurace systému SQL Server** okně klikněte na tlačítko **upravit** tlačítka na automatické opravy části.

![Konfigurace automatizovaných oprav SQL pro existující virtuální počítače](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Po dokončení klikněte **OK** tlačítko v dolní části **konfigurace systému SQL Server** okno a uložte změny.

Chcete-li povolit automatizovaných oprav poprvé, nakonfiguruje Azure IaaS Agent serveru SQL Server na pozadí. Během této doby nemusí zobrazit na portálu Azure, automatizované opravy je nakonfigurován. Počkejte několik minut, než agent, který se má nainstalovat, nakonfigurovat. Následně se odráží na portálu Azure nové nastavení.

> [!NOTE]
> Můžete také nakonfigurovat automatizovaných oprav pomocí šablony. Další informace najdete v tématu [šablony Azure rychlý start pro automatizované opravy](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).
> 
> 

## <a name="configuration-with-powershell"></a>Konfigurace pomocí prostředí PowerShell
Po zřízení virtuálního počítače SQL, nakonfigurujte automatizovaných oprav pomocí prostředí PowerShell.

V následujícím příkladu prostředí PowerShell slouží ke konfiguraci automatizovaných oprav na existující virtuální počítač serveru SQL. **AzureRM.Compute\New AzureRmVMSqlServerAutoPatchingConfig** příkaz nakonfiguruje nové okno údržby pro automatické aktualizace.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureRmVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Pokud ještě není nainstalovaná rozšíření, instalaci rozšíření restartuje službu SQL Server.

Podle toho, v tomto příkladu, následující tabulka popisuje praktická vliv na cílovém virtuálním počítači Azure:

| Parametr | Efekt |
| --- | --- |
| **DayOfWeek** |Každý čtvrtek nainstalovány opravy. |
| **MaintenanceWindowStartingHour** |Začátek aktualizace na 11:00. |
| **MaintenanceWindowsDuration** |Během 120 minut musí být nainstalované opravy. Podle času zahájení, musí provést podle 1:00 pm. |
| **PatchCategory** |Jedinou možnou nastavení pro tento parametr je **důležité**. Tato možnost nainstaluje služby Windows update označena důležité; nenainstaluje aktualizace systému SQL Server, které nejsou zahrnuté v této kategorii. |

Ho může trvat několik minut k instalaci a konfiguraci IaaS Agent serveru SQL Server.

Pokud chcete zakázat automatizovaných oprav, spusťte stejný skriptu bez **-povolit** parametru **AzureRM.Compute\New AzureRmVMSqlServerAutoPatchingConfig**. Neexistence **-povolit** parametr signály příkaz funkci zakážete.

## <a name="next-steps"></a>Další postup
Informace o dalších úlohách, k dispozici automation najdete v tématu [rozšíření agenta systému SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Další informace o spuštění systému SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na virtuálních počítačích Azure přehled](virtual-machines-windows-sql-server-iaas-overview.md).

