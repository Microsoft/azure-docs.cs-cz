---
title: Poznámky k verzi pro SQL Server v Azure Virtual Machines | Microsoft Docs
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
ms.date: 08/01/2019
ms.openlocfilehash: 759bec280b40973cf651dec3741a71d67c01fe04
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200164"
---
# <a name="release-notes-for-sql-server-on-azure-virtual-machines"></a>Poznámky k verzi pro SQL Server v Azure Virtual Machines

Azure umožňuje nasadit virtuální počítač s imagí SQL Server vestavěnou. Tento článek shrnuje nové funkce a vylepšení v posledních verzích [SQL Server v Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). V tomto článku jsou uvedeny také významné aktualizace obsahu, které přímo nesouvisejí s vydáním, ale publikované ve stejném časovém rámci. Vylepšení dalších služeb Azure najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates).

## <a name="august-2019"></a>Srpen 2019

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Vyhrazený hostitel Azure** | Virtuální počítač s SQL Server můžete spustit na [vyhrazeném hostiteli Azure](virtual-machines-windows-sql-dedicated-host.md). |
| &nbsp; | &nbsp; |


## <a name="july-2019"></a>Červenec 2019

### <a name="documentation-improvements"></a>Vylepšení dokumentace

| Dokumentace | Podrobnosti |
| --- | --- |
| **Přesunout virtuální počítač SQL do jiné oblasti** | Pomocí Azure Site Recovery [migrujte virtuální počítač s SQL Server z jedné oblasti do druhé](virtual-machines-windows-sql-move-different-region.md). |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>Červeně 2019

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Nové režimy instalace SQL IaaS** | Nyní je možné nainstalovat rozšíření SQL Server IaaS v [režimu prostého režimu](virtual-machines-windows-sql-server-agent-extension.md) , abyste se vyhnuli restartování služby SQL Server.  |
| **Úpravy edice SQL Server** | Nyní můžete změnit [vlastnost edice](virtual-machines-windows-sql-change-edition.md) pro váš virtuální počítač s SQL Server. |
| **Změny zprostředkovatele prostředků virtuálních počítačů SQL** | Pomocí nových režimů IaaS SQL můžete [zaregistrovat svůj virtuální počítač s SQL Server pomocí poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md) . Tato funkce zahrnuje [image Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms).|
| **Využití imagí s vlastní licencí pomocí Zvýhodněné hybridní využití Azure** | Bitové kopie s vlastní licencí nasazené z Azure Marketplace teď můžou [jako typ licence přepnout na průběžné platby](virtual-machines-windows-sql-ahb.md#remarks).| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Květen 2019

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Nová SQL Server Správa virtuálních počítačů v Azure Portal** | Nyní je možné spravovat SQL Server virtuální počítač v Azure Portal. Další informace najdete v tématu [Správa virtuálních počítačů s SQL Server v Azure Portal](virtual-machines-windows-sql-manage-portal.md).  | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Vylepšení dokumentace

| Dokumentace | Podrobnosti |
| --- | --- |
| **Nový portál pro správu virtuálních počítačů SQL Server** | O desítkových článcích bylo aktualizováno na nové prostředí portálu pro správu virtuálních počítačů SQL Server. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>Duben 2019

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Rozšířená podpora pro SQL Server 2008/2008 R2** | [Podporu](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server 2008 a SQL Server 2008 R2 můžete podpořit tak, že migrujete *jako* na virtuální počítač Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Březen 2019

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Podpora vlastních imagí** | Teď můžete nainstalovat [rozšíření SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) na vlastní image OS a SQL, což nabízí omezené funkce [flexibilního licencování](virtual-machines-windows-sql-ahb.md). Při registraci vlastní image pomocí poskytovatele prostředků SQL zadejte typ licence jako "AHUB". V opačném případě se registrace nezdaří. | 
| **Podpora pojmenovaných instancí** | Nyní můžete použít [rozšíření SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) s pojmenovanou instancí, pokud byla výchozí instance správně odinstalována. | 
| **Vylepšení portálu** | Prostředí Azure Portal pro nasazení SQL Server virtuálního počítače bylo přepracované ke zvýšení použitelnosti. Další informace najdete v tématu Stručný [rychlý Start](quickstart-sql-vm-create-portal.md) a podrobnější [návod](virtual-machines-windows-portal-sql-server-provision.md) k nasazení SQL Server virtuálního počítače.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Únor 2019

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Vylepšení portálu** | Je teď možné změnit licenční model pro SQL Server virtuální počítač z průběžných plateb na vlastní licenci pomocí [Azure Portalu](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider).|
|**Zjednodušení nasazení skupiny dostupnosti pomocí Azure SQL Server VM CLI** | Nasazení skupiny dostupnosti do virtuálního počítače s SQL Server v Azure je teď snazší než dřív. Pomocí [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) můžete z příkazového řádku vytvořit cluster s podporou převzetí služeb při selhání systému Windows, interní nástroj pro vyrovnávání zatížení a skupiny dostupnosti. Další informace najdete v tématu [použití rozhraní příkazového řádku azure SQL Server ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Prosinec 2018

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Nový poskytovatel prostředků pro cluster SQL Server** | Nový poskytovatel prostředků (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) definuje metadata clusteru s podporou převzetí služeb při selhání systému Windows. Když se připojíte k SQL Server virtuálního počítače *SqlVirtualMachineGroups* , služba Windows Server failover cluster (WSFC) se připojí k tomuto virtuálnímu počítači a připojí ho ke clusteru.  |
|**Automatizované nastavení nasazení skupiny dostupnosti pomocí šablon Azure pro rychlý Start** |Nyní je možné vytvořit cluster s podporou převzetí služeb při selhání Windows, připojit SQL Server k virtuálním počítačům, vytvořit naslouchací proces a nakonfigurovat interní nástroj pro vyrovnávání zatížení pomocí dvou šablon Azure pro rychlý Start. Další informace najdete v tématu [použití šablon pro rychlý Start Azure ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Automatická registrace do poskytovatele prostředků virtuálního počítače SQL** | SQL Server virtuální počítače nasazené po tomto měsíci se automaticky zaregistrují s novým poskytovatelem prostředků SQL Server. Je potřeba, aby se virtuální počítače SQL Server nasazené před tímto měsíc pořád zaregistrovaly ručně. Další informace najdete v tématu [registrace virtuálního počítače s SQL Server v Azure pomocí poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Listopadu 2018

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Nový poskytovatel prostředků virtuálního počítače SQL** |  Nový poskytovatel prostředků (Microsoft. SqlVirtualMachine) poskytuje lepší správu vašich SQL Serverch virtuálních počítačů. Další informace o registraci virtuálních počítačů najdete v tématu [registrace virtuálního počítače s SQL Server v Azure pomocí poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Přepnutí modelu licencování** | Pomocí Azure CLI nebo PowerShellu se teď můžete přepínat mezi modely s platbami za využití a vlastní licence pro váš SQL Server virtuální počítač. Další informace najdete v tématu [Změna licenčního modelu pro SQL Server virtuální počítač v Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Další zdroje

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
