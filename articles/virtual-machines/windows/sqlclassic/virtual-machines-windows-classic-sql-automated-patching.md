---
title: Automatizované opravy pro virtuální počítače s SQL Server (Classic) | Microsoft Docs
description: Vysvětluje funkci automatizované opravy SQL Server Virtual Machines běží v Azure s využitím klasického režimu nasazení.
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
ms.openlocfilehash: 555bd26ac894a3e06aa46eb0c51e20a35d21a9e7
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078067"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatizované opravy pro SQL Server v Azure Virtual Machines (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/automated-patching.md)
> * [Klasický](../classic/sql-automated-patching.md)
> 
> 

Automatizované opravy vytvoří pro virtuální počítač Azure s SQL Serverem časové období údržby. Automatizované aktualizace je možné nainstalovat pouze během tohoto časového období údržby. Pro SQL Server to zajistí, aby se aktualizace systému a všechny související restarty vyskytnout v nejlepším možné době databáze. 

> [!IMPORTANT]
> Nainstalují se pouze aktualizace Windows označené jako **Důležité**. Ostatní aktualizace SQL Serveru, jako jsou kumulativní aktualizace, je potřeba nainstalovat ručně. 

Automatizované opravy závisí na [rozšíření agenta SQL Server IaaS](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Pokud si chcete zobrazit Správce prostředků verzi tohoto článku, přečtěte si téma [automatizované opravy pro SQL Server v Azure Virtual Machines správce prostředků](../../../azure-sql/virtual-machines/windows/automated-patching.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete používat automatizované opravy, vezměte v úvahu následující požadavky:

**Operační systém**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Verze SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Nainstalujte nejnovější příkazy Azure PowerShell](/powershell/azure/overview).

**IaaS rozšíření SQL Server**:

* [Nainstalujte rozšíření SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Nastavení
Následující tabulka popisuje možnosti, které je možné nakonfigurovat pro automatizované opravy. U klasických virtuálních počítačů musíte ke konfiguraci těchto nastavení použít PowerShell.

| Nastavení | Možné hodnoty | Description |
| --- | --- | --- |
| **Automatizované opravy** |Povolit/zakázat (zakázáno) |Povolí nebo zakáže automatizované opravy pro virtuální počítač Azure. |
| **Plán údržby** |Každodenní, pondělí, úterý, středa, čtvrtek, pátek, sobota, neděle |Plán pro stažení a instalaci aktualizací Windows, SQL Server a Microsoft pro váš virtuální počítač. |
| **Hodina zahájení údržby** |0-24 |Místní čas zahájení aktualizace virtuálního počítače. |
| **Doba trvání časového období údržby** |30-180 |Počet minut, po které je povoleno dokončení stahování a instalace aktualizací. |
| **Kategorie opravy** |Důležité |Kategorie aktualizací, které se mají stáhnout a nainstalovat. |

## <a name="configuration-with-powershell"></a>Konfigurace pomocí PowerShellu
V následujícím příkladu se prostředí PowerShell používá ke konfiguraci automatizované opravy na stávajícím virtuálním počítači s SQL Server. Příkaz **New-AzureVMSqlServerAutoPatchingConfig** nakonfiguruje nové okno údržby pro automatické aktualizace.

```azurepowershell
$aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM
```

Na základě tohoto příkladu popisuje následující tabulka praktický efekt na cílovém virtuálním počítači Azure:

| Parametr | Efekt |
| --- | --- |
| **DayOfWeek** |Opravy se nainstalují každý čtvrtek. |
| **MaintenanceWindowStartingHour** |Začněte s aktualizacemi na 11:10:00. |
| **MaintenanceWindowDuration** |Opravy musí být nainstalované během 120 minut. V závislosti na čase spuštění musí být dokončeny pomocí 1:13:00. |
| **PatchCategory** |Jediným možným nastavením pro tento parametr je "důležité". |

Instalace a konfigurace agenta SQL Server IaaS může trvat několik minut.

Chcete-li zakázat automatizované opravy, spusťte stejný skript bez parametru-Enable pro rutinu New-AzureVMSqlServerAutoPatchingConfig. Jako u instalace může trvat několik minut, než se zakáže automatizované opravy.

## <a name="next-steps"></a>Další kroky
Informace o dalších dostupných úlohách automatizace najdete v tématu [SQL Server rozšíření agenta IaaS](../classic/sql-server-agent-extension.md).

Další informace o spuštění SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na azure Virtual Machines přehled](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

