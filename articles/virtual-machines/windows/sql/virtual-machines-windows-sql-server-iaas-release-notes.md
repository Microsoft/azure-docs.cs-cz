---
title: SQL Server na virtuálním počítači Azure zpráva k vydání verze | Dokumentace Microsoftu
description: Další informace o nové funkce a vylepšení systému SQL Server na Virtuálním počítači Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/13/2019
ms.openlocfilehash: 23e072369aa8ac6ca6ada5ec185df1a8d7e03c5b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282538"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>SQL Server na virtuálním počítači Azure zpráva k vydání verze

Azure umožňuje nasadit virtuální počítač pomocí image vytvořené systému SQL Server. Tento článek shrnuje nové funkce a vylepšení v posledních verzích [systému SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). V článku jsou uvedeny také důležité aktualizace obsahu, které jsou přímo související s vydáním ale publikovat ve stejném časovém rámci. Vylepšení k jiným službám Azure, najdete v části [aktualizace služeb](https://azure.microsoft.com/updates)

## <a name="march-2019"></a>2019. března

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Možnosti podpory pro vlastní image** | Teď si můžete nainstalovat [rozšíření SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) vlastních imagí operačního systému a SQL, který nabízí omezené funkce [flexibilní licencování](virtual-machines-windows-sql-ahb.md). Při registraci vlastní image u poskytovatele prostředků SQL, zadejte typ licence jako "AHUB" jako v opačném případě registrace selže.  | 
| **Možnosti podpory pojmenovaná instance** | Teď můžete využít [rozšíření SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) pomocí pojmenované instance, pokud výchozí instanci správně odinstaloval. | 
| **Rozšíření portálu** | Azure portal ještě neznáte pro nasazení virtuálního počítače s SQL serverem má bylo přepracované použitelnosti. Další informace najdete v tématu zadání [rychlý Start](quickstart-sql-vm-create-portal.md) a důkladnější [postupy](virtual-machines-windows-portal-sql-server-provision.md) příručka k nasazení virtuálního počítače s SQL serverem. |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Dokumentace k vylepšení

Žádný


## <a name="february-2019"></a>. Února 2019

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Zlepšení portálu** | Nyní je možné změnit licenční model virtuálního počítače serveru SQL z modelu průběžné platby pomocí přineste svůj – používání vlastní licence [webu Azure portal](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1).|
|**Zjednodušení nasazení skupinu dostupnosti pomocí Azure CLI virtuálního počítače SQL** | Je teď snadnější než někdy k nasazení skupiny dostupnosti do virtuálního počítače s SQL serverem v Azure. [Azure CLI pro virtuální počítač SQL](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) vám umožní vytvořit naslouchacího procesu ILB služby WSFC a AG vše z příkazového řádku a záznamů včas! Další informace najdete v tématu [pomocí příkazového řádku virtuálního počítače SQL Azure ke konfiguraci skupiny dostupnosti Always On pro SQL Server na Virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Prosinec 2018

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Nový poskytovatel prostředků skupiny clusteru SQL** | Nového poskytovatele prostředků (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups), který definuje metadata clusteru převzetí služeb při selhání Windows. Připojení virtuálního počítače s SQL serverem na *SqlVirtualMachineGroups* bootstraps služba Windows Failover Cluster a připojí se virtuální počítač do clusteru.  |
|**Automatizace nastavení nasazení skupiny dostupnosti s využitím šablon Azure Quickstart** |Nyní je možné vytvořit Cluster převzetí služeb při selhání Windows, připojení k němu virtuální počítače s SQL serverem, vytvořte naslouchací proces a konfigurace interního nástroje pro vyrovnávání zatížení s dvě šablony Quickstart pro Azure. Další informace najdete v tématu [šablonu pro rychlý start Azure použijte ke konfiguraci skupiny dostupnosti Always On pro SQL Server na Virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registrace poskytovatele prostředků automatické virtuálního počítače SQL** | Virtuální počítače SQL serveru nasadit po tento měsíc jsou automaticky registrované nového poskytovatele prostředků SQL serveru. SQL Server virtuální počítače nasazené před tento měsíc stále potřeba ručně zaregistrovat. Další informace najdete v tématu [zaregistrovat existující virtuální počítač SQL s poskytovatelem prostředků pro virtuální počítač SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>. Listopadu 2018

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Nový poskytovatel prostředků virtuálního počítače SQL** |  Nového poskytovatele prostředků pro virtuální počítače SQL serveru (Microsoft.SqlVirtualMachine), která poskytuje lepší správu vašeho virtuálního počítače s SQL serverem. Další informace o registraci vašeho virtuálního počítače najdete v tématu [zaregistrovat existující virtuální počítač SQL s poskytovatelem prostředků pro nové](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). |
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
