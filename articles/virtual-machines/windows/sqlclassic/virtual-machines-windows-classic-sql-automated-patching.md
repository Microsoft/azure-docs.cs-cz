---
title: Automatické opravy pro virtuální servery SQL Server (classic) | Dokumenty společnosti Microsoft
description: Vysvětluje funkci automatickéopravy pro virtuální počítače SQL Serveru spuštěné v Azure pomocí klasického režimu nasazení.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: efc6d0c25c5186b391deb08ee0e41dcb8ae6edf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978081"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatické opravy pro SQL Server ve virtuálních počítačích Azure (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Classic](../classic/sql-automated-patching.md)
> 
> 

Automatizované opravy vytvoří pro virtuální počítač Azure s SQL Serverem časové období údržby. Automatizované aktualizace je možné nainstalovat pouze během tohoto časového období údržby. Pro SQL Server tím zajistíte, že aktualizace systému a všechny přidružené restartování dojít v nejlepším možném čase pro databázi. 

> [!IMPORTANT]
> Nainstalují se pouze aktualizace Windows označené jako **Důležité**. Ostatní aktualizace SQL Serveru, jako jsou kumulativní aktualizace, je potřeba nainstalovat ručně. 

Automatizované opravy závisí na [rozšíření agenta SQL Server IaaS](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Pokud chcete zobrazit verzi tohoto článku ve Správci prostředků, [přečtěte si téma Automatické opravy pro SQL Server ve Správci prostředků virtuálních počítačů Azure](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Požadavky
Chcete-li používat automatické opravy, zvažte následující požadavky:

**Operační systém**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze serveru SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Nainstalujte nejnovější příkazy Azure PowerShellu](/powershell/azure/overview).

**Rozšíření SQL Server IaaS**:

* [Nainstalujte rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které lze nakonfigurovat pro automatické opravy. U klasických virtuálních počítače je nutné k konfiguraci těchto nastavení použít prostředí PowerShell.

| Nastavení | Možné hodnoty | Popis |
| --- | --- | --- |
| **Automatické opravy** |Povolit/zakázat (zakázáno) |Povolí nebo zakáže automatické opravy pro virtuální počítač Azure. |
| **Plán údržby** |Každý den, pondělí, úterý, středa, čtvrtek, pátek, sobota, neděle |Plán stahování a instalace aktualizací systému Windows, SQL Server a Microsoft pro váš virtuální počítač. |
| **Počáteční hodina údržby** |0-24 |Místní čas spuštění aktualizace virtuálního počítače. |
| **Doba trvání okna údržby** |30-180 |Počet minut povolených k dokončení stahování a instalace aktualizací. |
| **Kategorie opravy** |Důležité |Kategorie aktualizací ke stažení a instalaci. |

## <a name="configuration-with-powershell"></a>Konfigurace s PowerShellem
V následujícím příkladu se prostředí PowerShell používá ke konfiguraci automatickéopravy na existujícím virtuálním počítači sql serveru. Příkaz **New-AzureVMSqlServerAutoPatchingConfig** konfiguruje nové okno údržby pro automatické aktualizace.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Na základě tohoto příkladu následující tabulka popisuje praktický vliv na cílový virtuální počítač Azure:

| Parametr | Účinek |
| --- | --- |
| **Dayofweek** |Patche instalovány každý čtvrtek. |
| **MaintenanceWindowStartingHour** |Aktualizace můžete zahájit v 11:00. |
| **MaintenanceWindowDuration** |Záplaty musí být nainstalovány do 120 minut. Na základě času zahájení musí být dokončeny do 13:00. |
| **Kategorie patch** |Jediné možné nastavení pro tento parametr je "Důležité". |

Instalace a konfigurace agenta SQL Server IaaS agenta serveru SQL Server může trvat několik minut.

Chcete-li zakázat automatické opravy, spusťte stejný skript bez parametru -Enable na new-AzureVMSqlServerAutoPatchingConfig. Stejně jako při instalaci může zakácet automatické opravy několik minut.

## <a name="next-steps"></a>Další kroky
Informace o dalších dostupných úlohách automatizace naleznete v tématu [SQL Server IaaS Agent Extension](../classic/sql-server-agent-extension.md).

Další informace o spuštění SQL Serveru na virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

