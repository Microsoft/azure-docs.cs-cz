---
title: Změny v dokumentaci pro SQL Server v Azure Virtual Machines
description: Seznamte se s novými funkcemi a vylepšeními různých vydání SQL Server v Azure Virtual Machines.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/15/2020
ms.openlocfilehash: 262e15754ba481ee37b6af9783e5a3bc82d9450c
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556348"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Změny v dokumentaci pro SQL Server v Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure umožňuje nasadit virtuální počítač s imagí SQL Server vestavěnou. Tento článek shrnuje změny v dokumentaci spojené s novými funkcemi a vylepšeními v posledních verzích [SQL Server v Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 

## <a name="october-2020"></a>Říjen 2020

| Změny | Podrobnosti |
| --- | --- |
| **DNN pro AG** | Teď můžete nakonfigurovat [naslouchací proces DNN (Distributed Network Name](availability-group-distributed-network-name-dnn-listener-configure.md) ) pro SQL Server 2019 CU8 a později nahradit tradiční [naslouchací proces vnn](availability-group-overview.md#connectivity), což je nutnost Azure Load Balancer.   | 

## <a name="september-2020"></a>Září 2020

| Změny | Podrobnosti |
| --- | --- |
| **Automatická registrace rozšíření** | Teď můžete povolit funkci [automatické registrace](sql-agent-extension-automatic-registration-all-vms.md) a automaticky registrovat všechny SQL Server virtuální počítače, které jsou už nasazené ve vašem předplatném, pomocí [rozšíření agenta SQL IaaS](sql-server-iaas-agent-extension-automate-management.md). Tato akce se vztahuje na všechny existující virtuální počítače a také automaticky registruje všechny SQL Server virtuálních počítačů přidaných v budoucnu.   | 


## <a name="august-2020"></a>Srpen 2020

| Změny | Podrobnosti |
| --- | --- |
| **Konfigurace AG na portálu** | Skupinu dostupnosti je teď možné [nakonfigurovat prostřednictvím Azure Portal](availability-group-azure-portal-configure.md). Tato funkce je aktuálně ve verzi Preview a je nasazená, takže pokud vaše požadovaná oblast není k dispozici, vraťte se brzy. | 


## <a name="july-2020"></a>Červenec 2020


| Změny | Podrobnosti |
| --- | --- |
| **Migrace protokolu na Ultra disk** | Přečtěte si, jak můžete [migrovat soubor protokolu na disk](storage-migrate-to-ultradisk.md) s vysokým výkonem, abyste mohli využívat vysoký výkon a nízkou latenci. | 
| **Vytvoření AG pomocí Azure PowerShell** | Je teď možné zjednodušit vytvoření skupiny dostupnosti pomocí [Azure PowerShell](availability-group-az-commandline-configure.md) a také pomocí Azure CLI. | 


## <a name="june-2020"></a>Červen 2020

| Změny | Podrobnosti |
| --- | --- |
| **Název distribuované sítě (DNN)** | SQL Server 2019 v systému Windows Server 2016 + nyní používá náhled pro směrování provozu do instance clusteru s podporou převzetí služeb při selhání (FCI) pomocí [názvu distribuované sítě](./failover-cluster-instance-distributed-network-name-dnn-configure.md) namísto použití Azure Load Balancer. Tato podpora zjednodušuje a zjednodušuje připojení k řešení vysoké dostupnosti (HA) v Azure. | 
| **FCI se sdílenými disky Azure** | Je teď možné nasadit [instanci clusteru s podporou převzetí služeb při selhání (FCI)](failover-cluster-instance-overview.md) pomocí [sdílených disků Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md). |
| **Znovu uspořádané dokumenty FCI** | Dokumentace k [instancím clusteru s podporou převzetí služeb při selhání se SQL Server na virtuálních počítačích Azure](failover-cluster-instance-overview.md) se přepsala a reorganizovat pro přehlednost. Rozdělili jsme část konfiguračního obsahu, jako jsou [Doporučené postupy konfigurace clusteru](hadr-cluster-best-practices.md), jak připravit [virtuální počítač na SQL Server FCI](failover-cluster-instance-prepare-vm.md)a jak nakonfigurovat [Azure Load Balancer](./availability-group-vnn-azure-load-balancer-configure.md). | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>Květen 2020 

| Změny | Podrobnosti |
| --- | --- |
| **Rodina Azure SQL** | SQL Server v Azure Virtual Machines je teď součástí [řady produktů SQL Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md). Podívejte se na náš [Nový vzhled](../index.yml). V produktu se nic nezměnilo, ale dokumentace má za cíl, aby bylo rozhodnutí produktu Azure SQL snazší. | 


## <a name="january-2020"></a>Leden 2020

| Změny | Podrobnosti |
| --- | --- |
| **Podpora Azure Government** | Je teď možné zaregistrovat SQL Server virtuální počítače s rozšířením agenta SQL IaaS pro virtuální počítače hostované v cloudu [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) . | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Změny | Podrobnosti |
 --- | --- |
| **Bezplatná replika DR v Azure** | Pokud máte [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), můžete hostovat [bezplatnou pasivní instanci](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) pro zotavení po havárii v Azure pro místní instanci SQL Server. | 
| **Hromadná registrace rozšíření SQL IaaS** | Nyní můžete [hromadně registrovat](sql-agent-extension-manually-register-vms-bulk.md) SQL Server virtuálních počítačů pomocí [rozšíření agenta SQL IaaS](sql-server-iaas-agent-extension-automate-management.md). | 
|**Konfigurace úložiště optimalizované pro výkon** | Při vytváření nového virtuálního počítače SQL Server teď můžete [svoji konfiguraci úložiště plně přizpůsobit](storage-configuration.md#new-vms) . |
|**Premium – sdílení souborů pro FCI** | Nyní můžete vytvořit instanci clusteru s podporou převzetí služeb při selhání pomocí [sdílené složky Premium](failover-cluster-instance-premium-file-share-manually-configure.md) místo původní metody [prostory úložiště s přímým přístupem](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Vyhrazený hostitel Azure** | Virtuální počítač SQL Server můžete spustit na [vyhrazeném hostiteli Azure](dedicated-host.md). | 
| **Migrace virtuálního počítače SQL Server do jiné oblasti** | Pomocí Azure Site Recovery [migrujte virtuální počítač s SQL Server z jedné oblasti do druhé](move-sql-vm-different-region.md). |
|  **Nové režimy instalace SQL IaaS** | Nyní je možné nainstalovat rozšíření SQL Server IaaS v [režimu prostého režimu](sql-server-iaas-agent-extension-automate-management.md) , abyste se vyhnuli restartování služby SQL Server.  |
| **Úpravy edice SQL Server** | Nyní můžete změnit [vlastnost edice](change-sql-server-edition.md) pro váš virtuální počítač s SQL Server. |
| **Změny rozšíření agenta SQL IaaS** | Pomocí nových režimů IaaS SQL můžete [zaregistrovat svůj SQL Server virtuální počítač s rozšířením agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md) . Tato možnost zahrnuje image [Windows serveru 2008](sql-server-iaas-agent-extension-automate-management.md#management-modes) .|
| **Využití imagí s vlastní licencí pomocí Zvýhodněné hybridní využití Azure** | Bitové kopie s vlastní licencí nasazené z Azure Marketplace teď můžou [jako typ licence přepnout na průběžné platby](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks).| 
| **Nová SQL Server Správa virtuálních počítačů v Azure Portal** | Nyní je možné spravovat SQL Server virtuální počítač v Azure Portal. Další informace najdete v tématu [Správa virtuálních počítačů s SQL Server v Azure Portal](manage-sql-vm-portal.md).  | 
| **Rozšířená podpora pro SQL Server 2008 a 2008 R2** | [Podporu](sql-server-2008-extend-end-of-support.md) SQL Server 2008 a SQL Server 2008 R2 můžete podpořit tak, že migrujete *jako* na virtuální počítač Azure. | 
| **Podpora vlastních imagí** | Nyní můžete nainstalovat [rozšíření SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) na vlastní operační systém a SQL Server Image, což nabízí omezené funkce [flexibilního licencování](licensing-model-azure-hybrid-benefit-ahb-change.md). Při registraci vlastní image pomocí rozšíření agenta SQL IaaS zadejte typ licence jako "AHUB". V opačném případě se registrace nezdaří. | 
| **Podpora pojmenovaných instancí** | Nyní můžete použít [rozšíření SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) s pojmenovanou instancí, pokud byla výchozí instance správně odinstalována. | 
| **Vylepšení portálu** | Prostředí Azure Portal pro nasazení SQL Server virtuálního počítače bylo přepracované ke zvýšení použitelnosti. Další informace najdete v tématu Stručný [rychlý Start](sql-vm-create-portal-quickstart.md) a podrobnější [návod](create-sql-vm-portal.md) k nasazení SQL Server virtuálního počítače.|
| **Vylepšení portálu** | Je teď možné změnit licenční model pro SQL Server virtuální počítač z průběžných plateb na vlastní licenci pomocí [Azure Portalu](licensing-model-azure-hybrid-benefit-ahb-change.md#change-license-model).|
| **Zjednodušení nasazení skupiny dostupnosti u virtuálního počítače s SQL Server prostřednictvím rozhraní příkazového řádku Azure** | Nasazení skupiny dostupnosti do virtuálního počítače s SQL Server v Azure je teď snazší než dřív. Pomocí [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid&preserve-view=true) můžete vytvořit cluster s podporou převzetí služeb při selhání systému Windows, interní nástroj pro vyrovnávání zatížení a skupiny dostupnosti, a to vše z příkazového řádku. Další informace najdete v tématu věnovaném [konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure pomocí Azure CLI](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Změny | Podrobnosti |
| --- | --- |
|  **Nový poskytovatel prostředků pro cluster SQL Server** | Nový poskytovatel prostředků (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) definuje metadata clusteru s podporou převzetí služeb při selhání systému Windows. Když se připojíte k SQL Server virtuálního počítače *SqlVirtualMachineGroups* , služba Windows Server failover cluster (WSFC) se připojí k tomuto virtuálnímu počítači a připojí ho ke clusteru.  |
| **Automatizované nastavení nasazení skupiny dostupnosti pomocí šablon Azure pro rychlý Start** |Nyní je možné vytvořit cluster s podporou převzetí služeb při selhání s Windows, připojit SQL Server k virtuálním počítačům, vytvořit naslouchací proces a nakonfigurovat interní nástroj pro vyrovnávání zatížení pomocí dvou šablon Azure pro rychlý Start. Další informace najdete v tématu [použití šablon pro rychlý Start Azure ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](availability-group-quickstart-template-configure.md). | 
| **Automatická registrace do rozšíření agenta SQL IaaS** | SQL Server virtuální počítače nasazené po tomto měsíci se automaticky zaregistrují s novým rozšířením agenta SQL IaaS. Je potřeba, aby se virtuální počítače SQL Server nasazené před tímto měsíc pořád zaregistrovaly ručně. Další informace najdete v tématu [registrace virtuálního počítače s SQL Server v Azure pomocí rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md).|
|**Nové rozšíření agenta SQL IaaS** |  Nový poskytovatel prostředků (Microsoft. SqlVirtualMachine) poskytuje lepší správu vašich SQL Serverch virtuálních počítačů. Další informace o registraci virtuálních počítačů najdete v tématu [registrace virtuálního počítače s SQL Server v Azure pomocí rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md). |
|**Přepnutí modelu licencování** | Pomocí Azure CLI nebo PowerShellu se teď můžete přepínat mezi modely s platbami za využití a vlastní licence pro váš SQL Server virtuální počítač. Další informace najdete v tématu [Změna licenčního modelu pro SQL Server virtuální počítač v Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Další zdroje

**Virtuální počítače s Windows** :

* [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Zřízení SQL Server na virtuálním počítači s Windows](create-sql-vm-portal.md)
* [Migrace databáze na SQL Server na virtuálním počítači Azure](migrate-to-vm-from-sql-server.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Osvědčené postupy výkonu pro SQL Server v Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines](application-patterns-development-strategies.md)

**Virtuální počítače se systémem Linux** :

* [Přehled SQL Server na virtuálním počítači se systémem Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Zřízení SQL Server na virtuálním počítači se systémem Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Nejčastější dotazy (Linux)](../linux/frequently-asked-questions-faq.md)
* [Dokumentace k SQL Server on Linux](/sql/linux/sql-server-linux-overview)