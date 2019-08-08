---
title: Zpráva k vydání verze virtuálního počítače Azure SQL Server | Microsoft Docs
description: Přečtěte si o nových funkcích a vylepšeních SQL Server na virtuálním počítači Azure.
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
ms.date: 08/01/2019
ms.openlocfilehash: e656cd4d901ad9f3180963047a85cd980e56df8b
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774204"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Zpráva k vydání verze pro virtuální počítač v Azure SQL Server

Azure umožňuje nasadit virtuální počítač s imagí SQL Server vestavěnou. Tento článek shrnuje nové funkce a vylepšení v posledních verzích [SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). V tomto článku jsou uvedeny také významné aktualizace obsahu, které přímo nesouvisejí s vydáním, ale publikované ve stejném časovém rámci. Vylepšení dalších služeb Azure najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates) .

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
| **Nové režimy instalace SQL IaaS** | Nyní je možné nainstalovat rozšíření SQL IaaS v [režimu prostého režimu](virtual-machines-windows-sql-server-agent-extension.md) , abyste se vyhnuli restartování služby SQL Server.  |
| **Úpravy edice SQL Server** | Nyní můžete změnit [vlastnost edice](virtual-machines-windows-sql-change-edition.md) pro váš virtuální počítač s SQL Server. |
| **Změny v SQL VM RP** | VIRTUÁLNÍ počítač [s SQL Server můžete zaregistrovat pomocí poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) – dokonce [imagí Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms) – s VYUŽITÍm nových režimů SQL pro IaaS. |
| **Image BYOL pomocí AHUB** | Image BYOL nasazené z Marketplace teď můžou svůj [typ licence přepnout na PAYG](virtual-machines-windows-sql-ahb.md#remarks).| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Květen 2019

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Nová správa virtuálních počítačů s SQL v Azure Portal** | Nyní existuje nový způsob, jak spravovat SQL Server virtuální počítač v Azure Portal. Další informace najdete v tématu [správa SQL serverch virtuálních počítačů v Azure Portal](virtual-machines-windows-sql-manage-portal.md).  |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Vylepšení dokumentace

| Dokumentace | Podrobnosti |
| --- | --- |
| **Nová správa portálu virtuálních počítačů SQL** | Přibližně tucet článků bylo aktualizováno na nové prostředí portálu pro správu virtuálních počítačů SQL. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>Duben 2019

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Rozšiřování podpory pro SQL Server 2008/2008 R2** | [Podporu](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server 2008 a SQL Server 2008 R2 můžete podpořit tak, že migrujete na virtuální počítač Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Březen 2019

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Podpora vlastních imagí** | Teď můžete nainstalovat [rozšíření SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) na vlastní image OS a SQL, což nabízí omezené funkce flexibilního licencování [](virtual-machines-windows-sql-ahb.md). Při registraci vlastní image pomocí poskytovatele prostředků SQL zadejte typ licence jako AHUB, jinak se registrace nezdaří. | 
| **Podpora pojmenovaných instancí** | Teď můžete využít [rozšíření SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) s pojmenovanou instancí, pokud se výchozí instance správně odinstalovala. | 
| **Vylepšení portálu** | Prostředí Azure Portal pro nasazení SQL Server virtuálního počítače bylo přepracované ke zvýšení použitelnosti. Další informace najdete v stručných [rychlých startech](quickstart-sql-vm-create-portal.md) a podrobné [návody](virtual-machines-windows-portal-sql-server-provision.md) k nasazení SQL Server virtuálního počítače.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Únor 2019

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Vylepšení portálu** | Nyní je možné změnit licenční model pro SQL Server virtuální počítač z průběžných plateb na vlastní licenci pomocí [Azure Portal](virtual-machines-windows-sql-ahb.md#change-license-for-vms-already-registered-with-resource-provider)|
|**Zjednodušení nasazení AG pomocí Azure SQL VM CLI** | Nasazení skupiny dostupnosti do virtuálního počítače s SQL Server v Azure je teď snazší než kdy dřív. [Azure SQL VM CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) umožňuje vytvořit NASLOUCHACÍ proces WSFC, interního nástroje a AG vše z příkazového řádku a v čase záznamu. Další informace najdete v tématu věnovaném [použití Azure SQL VM CLI ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Prosinec 2018

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Nový poskytovatel prostředků skupiny clusterů SQL** | Nového poskytovatele prostředků (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups), který definuje metadata clusteru s podporou převzetí služeb při selhání systému Windows. Když se připojíte k SQL Server virtuálního počítače k *SqlVirtualMachineGroups* , nasaďte Clusterová služba převzetí služeb při selhání Windows a připojí virtuální počítač ke clusteru.  |
|**Automatizace nastavení nasazení skupiny dostupnosti pomocí šablon Azure pro rychlý Start** |Nyní je možné vytvořit cluster s podporou převzetí služeb při selhání s Windows, připojit SQL Server k virtuálním počítačům, vytvořit naslouchací proces a nakonfigurovat interní Load Balancer pomocí dvou šablon Azure pro rychlý Start. Další informace najdete v tématu [použití šablony pro rychlý Start Azure ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Automatická registrace zprostředkovatele prostředků virtuálního počítače SQL** | SQL Server virtuální počítače nasazené po tomto měsíci se automaticky zaregistrují s novým poskytovatelem prostředků SQL Server. SQL Server virtuální počítače nasazené před tímto měsícem se pořád musí zaregistrovat ručně. Další informace najdete v tématu [Registrace existujícího virtuálního počítače s SQL pomocí poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Listopadu 2018

| Vylepšení služby | Podrobnosti |
| --- | --- |
| **Nový poskytovatel prostředků virtuálního počítače SQL** |  Nový poskytovatel prostředků pro virtuální počítače s SQL Server (Microsoft. SqlVirtualMachine), který poskytuje lepší správu SQL Server virtuálních počítačů. Další informace o registraci virtuálního počítače najdete v tématu [Registrace existujícího virtuálního počítače s SQL pomocí nového poskytovatele prostředků](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Přepnutí modelu licencování** | Pomocí Azure CLI nebo PowerShellu teď můžete přepínat mezi modelem licence pro platby za využití a vlastní použití vlastního modelu licencí pro váš virtuální počítač SQL. Další informace najdete v tématu [Změna modelu licencování pro virtuální počítač s SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Další zdroje

**Virtuální počítače s Windows**:

* [Přehled SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Zřízení virtuálního počítače s SQL Server Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrace databáze na SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuální počítače se systémem Linux**:

* [Přehled SQL Server na virtuálním počítači se systémem Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače s SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Nejčastější dotazy (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentace k SQL Serveru na Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
