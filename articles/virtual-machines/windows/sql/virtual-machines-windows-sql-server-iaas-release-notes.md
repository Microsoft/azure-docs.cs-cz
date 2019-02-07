---
title: SQL Server na virtuálním počítači Azure zpráva k vydání verze | Dokumentace Microsoftu
description: Další informace o nové funkce a vylepšení systému SQL Server na Virtuálním počítači Azure
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 7c854759e9a0ba13b698dd07dabb25100895bc85
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766873"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>SQL Server na virtuálním počítači Azure zpráva k vydání verze

Azure umožňuje nasadit virtuální počítač pomocí image vytvořené systému SQL Server. Tento článek obsahuje seznam nových funkcí a vylepšení, které můžete očekávat v nejnovější verzi systému SQL Server nasazený na virtuálním počítači Azure. 

## <a name="december-2018"></a>Prosinec 2018

| **Změna** | Podrobnosti |
| --- | --- |
| **Nový poskytovatel prostředků skupiny clusteru SQL** | Existuje nového poskytovatele prostředků (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups), který definuje metadata o převzetí služeb při selhání clusteru Windows. Připojení virtuálního počítače s SQL serverem na *SqlVirtualMachineGroups* bootstraps služba Windows Failover Cluster a připojí se virtuální počítač do clusteru.  |
|**Automatizace nastavení nasazení skupiny dostupnosti s využitím šablon Azure Quickstart** |Nyní je možné vytvořit Cluster převzetí služeb při selhání Windows, připojení k němu virtuální počítače s SQL serverem, vytvořte naslouchací proces a konfigurace interního nástroje pro vyrovnávání zatížení s dvě šablony Quickstart pro Azure. Další informace najdete v tématu [vytvoření služby WSFC, naslouchací proces a nakonfigurujte ILB pro skupiny dostupnosti Always On na virtuálním počítači SQL serveru pomocí šablony Azure Quickstart](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registrace poskytovatele prostředků automatické virtuálního počítače SQL** | Virtuální počítače SQL serveru nasadit po tento měsíc jsou automaticky registrované nového poskytovatele prostředků SQL serveru. SQL Server virtuální počítače nasazené před tento měsíc bude stále muset ručně zaregistrovat. Další informace najdete v tématu [zaregistrovat existující virtuální počítač SQL s poskytovatelem prostředků pro nové](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>. Listopadu 2018

| **Změna** | Podrobnosti |
| --- | --- |
| **Nový poskytovatel prostředků virtuálního počítače SQL** |  Existuje nového poskytovatele prostředků pro virtuální počítače SQL serveru (Microsoft.SqlVirtualMachine), která umožňuje lepší správu vašeho virtuálního počítače s SQL serverem. Další informace o registraci vašeho virtuálního počítače najdete v tématu [zaregistrovat existující virtuální počítač SQL s poskytovatelem prostředků pro nové](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider). |
|**Přepnout licenčního modelu** |Teď můžete přepínat mezi model plateb za využití a přinést vlastní licenci pro váš virtuální počítač SQL s použitím Azure CLI nebo Powershellu. Další informace najdete v tématu [jak změnit licenční model virtuálního počítače SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Další materiály

**Virtuální počítače s Windows**:

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Zřídit Windows SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrace databáze systému SQL Server na Virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuální počítače s Linuxem**:

* [Přehled SQL serveru na virtuálním počítači s Linuxem](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače s SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Nejčastější dotazy (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentace k SQL Serveru na Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
