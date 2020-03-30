---
title: Změny dokumentace pro SQL Server ve virtuálních počítačích Azure| Dokumenty společnosti Microsoft
description: Další informace o nových funkcích a vylepšeních pro SQL Server na virtuálním počítači Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201641"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Změny dokumentace pro SQL Server na virtuálních počítačích Azure

Azure umožňuje nasadit virtuální počítač (VM) s integrovanou image SQL Serveru. Tento článek shrnuje změny dokumentace spojené s novými funkcemi a vylepšení v posledních verzích [SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Leden 2020

| Změny | Podrobnosti |
| --- | --- |
| **Podpora Azure Government** | Teď je možné zaregistrovat virtuální počítače SQL Serveru u poskytovatele prostředků virtuálních počítačů SQL pro virtuální počítače hostované v cloudu [Azure Government.](https://azure.microsoft.com/global-infrastructure/government/) | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Změny | Podrobnosti |
 --- | --- |
| **Bezplatná replika zotavení po havárii v Azure** | Můžete hostovat [bezplatnou pasivní instanci](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) pro zotavení po havárii v Azure pro místní instanci SERVERU SQL Server, pokud máte [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registrace zprostředkovatele hromadných prostředků** | Nyní můžete [hromadně zaregistrovat](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) virtuální počítače SQL u poskytovatele prostředků. | 
|**Konfigurace úložiště optimalizovaná pro výkon** | Nyní můžete [plně přizpůsobit konfiguraci úložiště](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) při vytváření nového virtuálního počítače SQL Server. |
|**Premium sdílení souborů pro FCI** | Nyní můžete vytvořit instanci clusteru s podporou převzetí služeb při selhání pomocí [sdílené složky Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) namísto původní metody [Storage Spaces Direct](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| **Vyhrazený hostitel Azure** | Virtuální počítač SQL Server můžete spustit na [vyhrazeném hostiteli Azure](virtual-machines-windows-sql-dedicated-host.md). | 
| **Přesunutí virtuálního virtuálního aplikace SQL do jiné oblasti** | K [migraci virtuálního počítače SQL Serveru z jedné oblasti do druhé](virtual-machines-windows-sql-move-different-region.md)použijte Azure Site Recovery . |
|  **Nové režimy instalace SQL IaaS** | Nyní je možné nainstalovat rozšíření SQL Server IaaS v [zjednodušeném režimu,](virtual-machines-windows-sql-server-agent-extension.md) aby se zabránilo restartování služby SQL Server.  |
| **Úprava edice serveru SQL Server** | Nyní můžete změnit [vlastnost edice](virtual-machines-windows-sql-change-edition.md) pro váš virtuální počítač SQL Server. |
| **Změny zprostředkovatele prostředků virtuálního aplikace SQL VM** | Virtuální počítač SQL Server můžete [zaregistrovat u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md) pomocí nových režimů SQL IaaS. Tato funkce zahrnuje bitové kopie [systému Windows Server 2008.](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)|
| **Představte si vlastní licence pomocí hybridní výhody Azure** | Image s vlastní licencí nasazené z Azure Marketplace teď můžou [přepnout svůj typ licence na](virtual-machines-windows-sql-ahb.md#remarks)průběžný platby .| 
| **Nová správa virtuálních počítačů SQL Serveru na webu Azure Portal** | Teď existuje způsob, jak spravovat virtuální počítač SQL Server na webu Azure Portal. Další informace najdete [v tématu Správa virtuálních počítačích SQL Server na webu Azure Portal](virtual-machines-windows-sql-manage-portal.md).  | 
| **Rozšířená podpora serveru SQL Server 2008/2008 R2** | [Rozšiřte podporu](virtual-machines-windows-sql-server-2008-eos-extend-support.md) pro SQL Server 2008 a SQL Server 2008 R2 migrací *stejně jako* na virtuální počítač Azure. | 
| **Vlastní podpora obrazu** | Nyní můžete nainstalovat [rozšíření SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) na vlastní operační systém a bitové kopie SQL, které nabízí omezenou funkčnost [flexibilních licencí](virtual-machines-windows-sql-ahb.md). Při registraci vlastní bitové kopie u poskytovatele prostředků SQL zadejte typ licence jako "AHUB". V opačném případě se registrace nezdaří. | 
| **Podpora pojmenované instance** | Nyní můžete použít [rozšíření SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) s pojmenovanou instancí, pokud byla správně odinstalována výchozí instance. | 
| **Vylepšení portálu** | Prostředí portálu Azure pro nasazení virtuálního počítače SQL Server bylo přepracováno, aby se zlepšila použitelnost. Další informace naleznete v krátkém [rychlém startu](quickstart-sql-vm-create-portal.md) a podrobnější [návod k](virtual-machines-windows-portal-sql-server-provision.md) nasazení virtuálního ms serveru SQL Server.|
| **Vylepšení portálu** | Nyní je možné změnit licenční model pro virtuální počítač SQL Server z průběžných plateb na licenci přineste si vlastní pomocí [portálu Azure](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider).|
| **Zjednodušení nasazení skupiny dostupnosti pomocí rozhraní příkazu CLI virtuálního počítače Azure SQL Server** | Nasazení skupiny dostupnosti do virtuálního počítače SQL Serveru v Azure je teď jednodušší než kdy dřív. [Pomocí příkazového řádku Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) můžete vytvořit cluster s podporou převzetí služeb při selhání systému Windows, interní vyrovnávání zatížení a naslouchací procesy skupiny dostupnosti z příkazového řádku. Další informace najdete [v tématu Použití rozhraní příkazu CLI virtuálního počítače Azure SQL Server ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Změny | Podrobnosti |
| --- | --- |
|  **Nový poskytovatel prostředků pro cluster serveru SQL Server** | Nový poskytovatel prostředků (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) definuje metadata clusteru windows převzetí služeb při selhání. Připojení mon. *SqlVirtualMachineGroups*  |
| **Automatické nastavení nasazení skupiny dostupnosti pomocí šablon rychlého startu Azure** |Teď je možné vytvořit cluster windows převzetí služeb při selhání, připojit SQL Server virtuální počítače k němu, vytvořit naslouchací proces a nakonfigurovat interní vyrovnávání zatížení se dvěma šablonami Azure quickstart. Další informace najdete [v tématu Použití šablon rychlého spuštění Azure ke konfiguraci skupiny dostupnosti always on pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Automatická registrace k poskytovateli prostředků virtuálního zařízení SQL** | Virtuální servery SQL Server nasazené po tomto měsíci se automaticky zaevidují u nového poskytovatele prostředků serveru SQL Server. Virtuální servery SQL Server nasazené před tímto měsícem stále musí být ručně zaregistrovány. Další informace najdete [v tématu Registrace virtuálního počítače SQL Server v Azure u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
|**Nový poskytovatel prostředků virtuálního virtuálního připojení SQL** |  Nový poskytovatel prostředků (Microsoft.SqlVirtualMachine) poskytuje lepší správu virtuálních počítačů SQL Server. Další informace o registraci virtuálních počítačů najdete [v tématu Registrace virtuálního počítače SQL Server v Azure u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Přepnout licenční model** | Teď můžete přepínat mezi pay-per-usage a bring-your-own-license modely pro váš virtuální počítač SQL Server pomocí Azure CLI nebo PowerShell. Další informace najdete [v tématu Jak změnit licenční model pro virtuální počítač SQL Server v Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Další zdroje

**Virtuální aplikace Windows**:

* [Přehled sql serveru na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Zřízení virtuálního virtuálního modulu SQL Server pro Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrace databáze na SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Doporučené postupy pro výkon pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md)
* [Vzorce aplikací a strategie vývoje pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuální počítače S Linuxem**:

* [Přehled SQL Serveru na virtuálním počítači s Linuxem](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Nejčastější dotazy (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Sql Server v dokumentaci k Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
