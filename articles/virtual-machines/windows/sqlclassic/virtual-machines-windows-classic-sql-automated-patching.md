---
title: Automatizované opravy pro virtuální počítače s SQL serverem (Classic) | Dokumentace Microsoftu
description: Vysvětluje funkci automatizované opravy pro SQL Server virtuální počítače provozované v Azure pomocí modelem nasazení classic.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aa912e3eb76d72e7a79c83d7e51d493310bd36b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362131"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatizované opravy pro SQL Server na virtuálních počítačích Azure (klasické)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Classic](../classic/sql-automated-patching.md)
> 
> 

Automatizované opravy vytváří časového období údržby pro virtuální počítač Azure s SQL serverem. Automatizované aktualizace je možné nainstalovat pouze během tohoto časového období údržby. Pro SQL Server tím se zajistí, že aktualizace systému a jakékoli přidružené požadavky na restarty vyskytovat na nejlepší možné době pro databázi. 

> [!IMPORTANT]
> Nainstalují se pouze aktualizace Windows označené jako **Důležité**. Ostatní aktualizace SQL Serveru, jako jsou kumulativní aktualizace, je potřeba nainstalovat ručně. 

Automatizované opravy závisí na [rozšíření agenta SQL Server IaaS](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Chcete-li zobrazit Resource Manageru verze tohoto článku, najdete v článku [automatizované opravy pro SQL Server v Azure virtuální počítače Resource Manageru](../sql/virtual-machines-windows-sql-automated-patching.md).

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

* [Nainstalujte nejnovější Azure PowerShell příkazy](/powershell/azure/overview).

**Rozšíření systému SQL Server IaaS**:

* [Instalace rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které je možné nakonfigurovat pro automatizované opravy. Pro klasické virtuální počítače musíte použít PowerShell k nakonfigurování těchto nastavení.

| Nastavení | Možné hodnoty | Popis |
| --- | --- | --- |
| **Automatizované opravy** |Povolí nebo zakáže (zakázáno) |Povolí nebo zakáže automatické opravy pro virtuální počítač Azure. |
| **Plán údržby** |Každý den od pondělí, úterý, středa, čtvrtek, pátek, sobota, neděle |Plán pro stahování a instalaci aktualizací Windows, SQL Server a Microsoft pro virtuální počítač. |
| **Hodina spouštění údržby** |0-24 |Místní spuštění aktualizace virtuálního počítače. |
| **Trvání časového období údržby** |30-180 |Počet minut povoleno dokončení stahování a instalaci aktualizací. |
| **Oprava kategorie** |Důležité |Kategorie aktualizace stáhnout a nainstalovat. |

## <a name="configuration-with-powershell"></a>Konfigurace pomocí Powershellu
V následujícím příkladu prostředí PowerShell slouží ke konfiguraci automatické opravy na existujícím virtuálním počítači SQL serveru. **New-AzureVMSqlServerAutoPatchingConfig** příkaz nakonfiguruje nové časové období údržby pro automatické aktualizace.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Podle tohoto příkladu, následující tabulka popisuje praktický na cílovém virtuálním počítači Azure:

| Parametr | Efekt |
| --- | --- |
| **DayOfWeek** |Každý čtvrtek nainstalovány opravy. |
| **MaintenanceWindowStartingHour** |Zahájení aktualizace v 11:00. |
| **MaintenanceWindowDuration** |Opravy musí být nainstalován během 120 minut. Podle času spuštění, musí provést v 1:00 pm. |
| **PatchCategory** |Jedinou možnou nastavení tohoto parametru je "Důležitá". |

To může trvat několik minut, instalaci a konfiguraci agenta SQL Server IaaS.

Chcete-li zakázat automatické opravy, spusťte stejný skript bez parametru / Enable do New-AzureVMSqlServerAutoPatchingConfig. Stejně jako u instalace, může trvat několik minut, chcete-li zakázat automatické opravy.

## <a name="next-steps"></a>Další postup
Informace o dalších úlohách dostupných automation najdete v tématu [rozšíření agenta SQL Server IaaS](../classic/sql-server-agent-extension.md).

Další informace o spuštění SQL serveru na virtuálních počítačích Azure najdete v tématu [systému SQL Server na Azure Virtual Machines – přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

