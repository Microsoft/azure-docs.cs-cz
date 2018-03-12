---
title: "Automatizované opravy pro virtuální počítače serveru SQL (klasické) | Microsoft Docs"
description: "Vysvětluje funkci automatizované opravy pro SQL Server virtuální počítače běžící v Azure pomocí režimu nasazení classic."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: jroth
ms.openlocfilehash: 1da89bbf88e9f1f2105e7afab5c3fdabcecdbc64
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatizované opravy pro SQL Server na virtuálních počítačích Azure (klasický)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Classic](../classic/sql-automated-patching.md)
> 
> 

Automatizovaných oprav určuje časové období údržby pro virtuální počítač Azure systémem SQL Server. Automatické aktualizace lze nainstalovat pouze během tohoto časového období údržby. Pro systém SQL Server tím se zajistí, že aktualizace systému a všechny přidružené restartuje dojít na nejlepší možný čas pro databázi. 

> [!IMPORTANT]
> Jenom Windows aktualizace označené **důležité** jsou nainstalovány. Další aktualizace systému SQL Server, jako je například kumulativní aktualizace, musí být nainstalován ručně. 

Automatizovaných oprav závisí na [rozšíření agenta systému SQL Server IaaS](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Resource Manager verzi v tomto článku najdete v tématu [automatizované opravy pro SQL Server ve službě Správce prostředků virtuálních počítačů Azure](../sql/virtual-machines-windows-sql-automated-patching.md).

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

* [Nainstalujte nejnovější příkazy prostředí Azure PowerShell](/powershell/azure/overview).

**Rozšíření systému SQL Server IaaS**:

* [Nainstalujte rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které mohou být konfigurovány pro automatizované opravy. Pro klasické virtuální počítače musíte použít PowerShell k nakonfigurování těchto nastavení.

| Nastavení | Možné hodnoty | Popis |
| --- | --- | --- |
| **Automatizované opravy** |Povolí nebo zakáže (zakázáno) |Povolí nebo zakáže automatizované opravy pro virtuální počítač Azure. |
| **Plán údržby.** |Každý den, pondělí, úterý, středu, čtvrtek a pátek, sobota, neděle |Plán pro stahování a instalace aktualizací s Windows, SQL Server a Microsoft pro virtuální počítač. |
| **Hodina spouštění údržby** |0-24 |Místní spuštění aktualizovat virtuální počítač. |
| **Doba trvání okna údržby** |30-180 |Počet minut oprávnění k dokončení stahování a instalaci aktualizací. |
| **Oprava kategorie** |Důležité |Kategorie aktualizací, které se stáhnout a nainstalovat. |

## <a name="configuration-with-powershell"></a>Konfigurace pomocí prostředí PowerShell
V následujícím příkladu prostředí PowerShell slouží ke konfiguraci automatizovaných oprav na existující virtuální počítač serveru SQL. **New-AzureVMSqlServerAutoPatchingConfig** příkaz nakonfiguruje nové okno údržby pro automatické aktualizace.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Podle toho, v tomto příkladu, následující tabulka popisuje praktická vliv na cílovém virtuálním počítači Azure:

| Parametr | Efekt |
| --- | --- |
| **DayOfWeek** |Každý čtvrtek nainstalovány opravy. |
| **MaintenanceWindowStartingHour** |Začátek aktualizace na 11:00. |
| **MaintenanceWindowsDuration** |Během 120 minut musí být nainstalované opravy. Podle času zahájení, musí provést podle 1:00 pm. |
| **PatchCategory** |Jedinou možnou nastavení pro tento parametr je "Důležité". |

Ho může trvat několik minut k instalaci a konfiguraci IaaS Agent serveru SQL Server.

Chcete-li zakázat automatizovaných oprav, spusťte stejný skript bez parametru-povolit New-AzureVMSqlServerAutoPatchingConfig. Stejně jako u instalace, se může trvat několik minut zakázat automatizovaných oprav.

## <a name="next-steps"></a>Další postup
Informace o dalších úlohách, k dispozici automation najdete v tématu [rozšíření agenta systému SQL Server IaaS](../classic/sql-server-agent-extension.md).

Další informace o spuštění systému SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na virtuálních počítačích Azure přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

