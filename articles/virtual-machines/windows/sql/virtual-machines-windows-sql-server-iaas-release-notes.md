---
title: Změny v dokumentaci pro SQL Server v Azure Virtual Machines | Microsoft Docs
description: Přečtěte si o nových funkcích a vylepšeních SQL Server na virtuálním počítači Azure.
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
ms.openlocfilehash: b9d2b3916e4509e5ce3aa8a9ddd66f1cf14a8827
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690878"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Změny v dokumentaci pro SQL Server v Azure Virtual Machines

Azure umožňuje nasadit virtuální počítač s imagí SQL Server vestavěnou. Tento článek shrnuje změny v dokumentaci spojené s novými funkcemi a vylepšeními v posledních verzích [SQL Server v Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Leden 2020

| Změny | Podrobnosti |
| --- | --- |
| **Podpora Azure Government** | Nyní je možné zaregistrovat SQL Server virtuálních počítačů pomocí poskytovatele prostředků virtuálních počítačů SQL pro virtuální počítače hostované v cloudu [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) . | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Změny | Podrobnosti |
 --- | --- |
| **Bezplatná replika DR v Azure** | Pokud máte [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), můžete hostovat [bezplatnou pasivní instanci](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) pro zotavení po havárii v Azure pro místní instanci SQL Server. | 
| **Registrace zprostředkovatele hromadného prostředku** | Virtuální počítače SQL teď můžete [hromadně zaregistrovat](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) u poskytovatele prostředků. | 
|**Konfigurace optimalizovaného úložiště výkonu** | Při vytváření nového virtuálního počítače SQL Server teď můžete [svoji konfiguraci úložiště plně přizpůsobit](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) . |
|**Premium – sdílení souborů pro FCI** | Nyní můžete vytvořit instanci clusteru s podporou převzetí služeb při selhání pomocí [sdílené složky Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) místo původní metody [prostory úložiště s přímým přístupem](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| **Vyhrazený hostitel Azure** | Virtuální počítač s SQL Server můžete spustit na [vyhrazeném hostiteli Azure](virtual-machines-windows-sql-dedicated-host.md). | 
| **Přesunout virtuální počítač SQL do jiné oblasti** | Pomocí Azure Site Recovery [migrujte virtuální počítač s SQL Server z jedné oblasti do druhé](virtual-machines-windows-sql-move-different-region.md). |
|  **Nové režimy instalace SQL IaaS** | Nyní je možné nainstalovat rozšíření SQL Server IaaS v [režimu prostého režimu](virtual-machines-windows-sql-server-agent-extension.md) , abyste se vyhnuli restartování služby SQL Server.  |
| **Úpravy edice SQL Server** | Nyní můžete změnit [vlastnost edice](virtual-machines-windows-sql-change-edition.md) pro váš virtuální počítač s SQL Server. |
| **Změny zprostředkovatele prostředků virtuálních počítačů SQL** | Pomocí nových režimů IaaS SQL můžete [zaregistrovat svůj virtuální počítač s SQL Server pomocí poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md) . Tato možnost zahrnuje image [Windows serveru 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) .|
| **Využití imagí s vlastní licencí pomocí Zvýhodněné hybridní využití Azure** | Bitové kopie s vlastní licencí nasazené z Azure Marketplace teď můžou [jako typ licence přepnout na průběžné platby](virtual-machines-windows-sql-ahb.md#remarks).| 
| **Nová SQL Server Správa virtuálních počítačů v Azure Portal** | Nyní je možné spravovat SQL Server virtuální počítač v Azure Portal. Další informace najdete v tématu [Správa virtuálních počítačů s SQL Server v Azure Portal](virtual-machines-windows-sql-manage-portal.md).  | 
| **Rozšířená podpora pro SQL Server 2008/2008 R2** | [Podporu](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server 2008 a SQL Server 2008 R2 můžete podpořit tak, že migrujete *jako* na virtuální počítač Azure. | 
| **Podpora vlastních imagí** | Teď můžete nainstalovat [rozšíření SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) na vlastní image OS a SQL, což nabízí omezené funkce [flexibilního licencování](virtual-machines-windows-sql-ahb.md). Při registraci vlastní image pomocí poskytovatele prostředků SQL zadejte typ licence jako "AHUB". V opačném případě se registrace nezdaří. | 
| **Podpora pojmenovaných instancí** | Nyní můžete použít [rozšíření SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) s pojmenovanou instancí, pokud byla výchozí instance správně odinstalována. | 
| **Vylepšení portálu** | Prostředí Azure Portal pro nasazení SQL Server virtuálního počítače bylo přepracované ke zvýšení použitelnosti. Další informace najdete v tématu Stručný [rychlý Start](quickstart-sql-vm-create-portal.md) a podrobnější [návod](virtual-machines-windows-portal-sql-server-provision.md) k nasazení SQL Server virtuálního počítače.|
|  **Vylepšení portálu** | Je teď možné změnit licenční model pro SQL Server virtuální počítač z průběžných plateb na vlastní licenci pomocí [Azure Portalu](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider).|
| **Zjednodušení nasazení skupiny dostupnosti pomocí Azure SQL Server VM CLI** | Nasazení skupiny dostupnosti do virtuálního počítače s SQL Server v Azure je teď snazší než dřív. Pomocí [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) můžete z příkazového řádku vytvořit cluster s podporou převzetí služeb při selhání systému Windows, interní nástroj pro vyrovnávání zatížení a skupiny dostupnosti. Další informace najdete v tématu [použití rozhraní příkazového řádku azure SQL Server ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Změny | Podrobnosti |
| --- | --- |
|  **Nový poskytovatel prostředků pro cluster SQL Server** | Nový poskytovatel prostředků (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) definuje metadata clusteru s podporou převzetí služeb při selhání systému Windows. Když se připojíte k SQL Server virtuálního počítače *SqlVirtualMachineGroups* , služba Windows Server failover cluster (WSFC) se připojí k tomuto virtuálnímu počítači a připojí ho ke clusteru.  |
| **Automatizované nastavení nasazení skupiny dostupnosti pomocí šablon Azure pro rychlý Start** |Nyní je možné vytvořit cluster s podporou převzetí služeb při selhání Windows, připojit SQL Server k virtuálním počítačům, vytvořit naslouchací proces a nakonfigurovat interní nástroj pro vyrovnávání zatížení pomocí dvou šablon Azure pro rychlý Start. Další informace najdete v tématu [použití šablon pro rychlý Start Azure ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Automatická registrace do poskytovatele prostředků virtuálního počítače SQL** | SQL Server virtuální počítače nasazené po tomto měsíci se automaticky zaregistrují s novým poskytovatelem prostředků SQL Server. Je potřeba, aby se virtuální počítače SQL Server nasazené před tímto měsíc pořád zaregistrovaly ručně. Další informace najdete v tématu [registrace virtuálního počítače s SQL Server v Azure pomocí poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
|**Nový poskytovatel prostředků virtuálního počítače SQL** |  Nový poskytovatel prostředků (Microsoft. SqlVirtualMachine) poskytuje lepší správu vašich SQL Serverch virtuálních počítačů. Další informace o registraci virtuálních počítačů najdete v tématu [registrace virtuálního počítače s SQL Server v Azure pomocí poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Přepnutí modelu licencování** | Pomocí Azure CLI nebo PowerShellu se teď můžete přepínat mezi modely s platbami za využití a vlastní licence pro váš SQL Server virtuální počítač. Další informace najdete v tématu [Změna licenčního modelu pro SQL Server virtuální počítač v Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Další zdroje informací:

**Virtuální počítače s Windows**:

* [Přehled SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Zřízení virtuálního počítače s SQL Server Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrace databáze na SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Osvědčené postupy výkonu pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuální počítače se systémem Linux**:

* [Přehled SQL Server na virtuálním počítači se systémem Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače s SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Nejčastější dotazy (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentace k SQL Serveru na Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
