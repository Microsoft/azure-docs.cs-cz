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
ms.openlocfilehash: d9cedd05e949d9684473d620629314a34802c1fc
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977299"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatizované opravy pro SQL Server v Azure Virtual Machines (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Automatizované opravy vytváří časového období údržby pro virtuální počítač Azure s SQL serverem. Automatické aktualizace lze nainstalovat pouze během tohoto časového období údržby. Pro SQL Server tento rescriction zajišťuje, aktualizace systému a jakékoli přidružené požadavky na restarty vyskytovat na nejlepší možné době pro databázi. 

> [!IMPORTANT]
> Pouze aktualizace Windows označené **důležité** jsou nainstalovány. Další aktualizace SQL serveru, jako je kumulativní aktualizace, musí ručně doinstalovat. 

Automatizované opravy závisí [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete použít, automatické opravy, vezměte v úvahu následující požadavky:

**Operační systém**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze systému SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

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

V **nastavení systému SQL Server** okně vyberte **automatické opravy**. Ukazuje následující snímek obrazovky Azure portal **automatizované opravy SQL** okno.

![Automatizované opravy SQL na webu Azure portal](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Kontext, naleznete v tématu dokončení na [zřizování virtuálního počítače s SQL serverem v Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Stávající virtuální počítače
Existující virtuální počítače systému SQL Server vyberte virtuální počítač systému SQL Server. Vyberte **konfigurace systému SQL Server** část **nastavení** okno.

![Automatické opravy SQL pro stávající virtuální počítače](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

V **konfigurace systému SQL Server** okna, klikněte na tlačítko **upravit** tlačítko v automatizovaných oprav oddílu.

![Konfigurace SQL automatizované opravy pro existující virtuální počítače](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Až budete hotovi, klikněte na tlačítko **OK** tlačítko v dolní části **konfigurace systému SQL Server** okno a uložte provedené změny.

Chcete-li povolit automatické opravy poprvé, Azure nakonfiguruje agenta SQL Server IaaS na pozadí. Během této doby nemusí zobrazit na webu Azure portal, že je nakonfigurovaný automatizované opravy. Počkejte několik minut, než agent nainstalován, nakonfigurován. Následně se odráží na webu Azure portal nové nastavení.

> [!NOTE]
> Můžete také nakonfigurovat automatické opravy pomocí šablony. Další informace najdete v tématu [šablona Azure quickstart pro automatizované opravy](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).
> 
> 

## <a name="configuration-with-powershell"></a>Konfigurace pomocí Powershellu
Po zřízení virtuálního počítače s SQL, pomocí prostředí PowerShell můžete nakonfigurovat automatizované opravy.

V následujícím příkladu prostředí PowerShell slouží ke konfiguraci automatické opravy na existujícím virtuálním počítači SQL serveru. **AzureRM.Compute\New AzVMSqlServerAutoPatchingConfig** příkaz nakonfiguruje nové časové období údržby pro automatické aktualizace.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

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

Chcete-li zakázat automatické opravy, spusťte stejný skript bez **-povolit** parametr **AzureRM.Compute\New AzVMSqlServerAutoPatchingConfig**. Chybí **-povolit** parametr signály příkazu zakažte funkci.

## <a name="next-steps"></a>Další postup
Informace o dalších úlohách dostupných automation najdete v tématu [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Další informace o spuštění SQL serveru na virtuálních počítačích Azure najdete v tématu [systému SQL Server na Azure Virtual Machines – přehled](virtual-machines-windows-sql-server-iaas-overview.md).

