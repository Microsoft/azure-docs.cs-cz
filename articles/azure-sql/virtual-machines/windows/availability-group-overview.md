---
title: Přehled skupin dostupnosti Always On SQL Server
description: Tento článek představuje SQL Server skupiny dostupnosti Always On na Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 8bbd56499c9b62248662fc5e8df0d5b3e1b672d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504163"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Skupina dostupnosti Always On u SQL Server na virtuálních počítačích Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek představuje skupiny dostupnosti Always On pro SQL Server v Azure Virtual Machines (VM). 

## <a name="overview"></a>Přehled

Skupiny dostupnosti Always On v Azure Virtual Machines jsou podobně jako [skupiny dostupnosti Always On-premises](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Vzhledem k tomu, že virtuální počítače jsou hostované v Azure, existuje několik dalších důležitých informací, jako je třeba redundance virtuálních počítačů, a směrování provozu v síti Azure. 

Následující diagram znázorňuje skupinu dostupnosti pro SQL Server na virtuálních počítačích Azure:

![Skupina dostupnosti](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>Redundance virtuálního počítače 

Aby bylo možné zvýšit redundanci a vysokou dostupnost, SQL Server virtuální počítače buď ve stejné [skupině dostupnosti](../../../virtual-machines/availability-set-overview.md), nebo v různých [zónách dostupnosti](../../../availability-zones/az-overview.md).

Umístění sady virtuálních počítačů do stejné skupiny dostupnosti chrání před výpadky v rámci datového centra, které způsobilo selhání zařízení (virtuální počítače v rámci skupiny dostupnosti nesdílejí prostředky) nebo aktualizace (virtuální počítače v rámci skupiny dostupnosti nejsou aktualizované ve stejnou dobu). Zóny dostupnosti chránit před selháním celého datového centra, přičemž každá zóna představuje sadu Datacenter v rámci oblasti.  Díky zajištění umístění prostředků do různých Zóny dostupnosti nemůže žádný výpadek na úrovni datacentra přebírat všechny vaše virtuální počítače offline.

Při vytváření virtuálních počítačů Azure musíte zvolit mezi konfigurací skupin dostupnosti vs Zóny dostupnosti.  Virtuální počítač Azure se nemůže zúčastnit obou.


## <a name="connectivity"></a>Připojení 

V tradičním místním nasazení se klienti připojují k naslouchacího procesu skupiny dostupnosti pomocí názvu virtuální sítě (VNN) a naslouchací proces směruje provoz do příslušné repliky SQL Server ve skupině dostupnosti. Pro směrování provozu v síti Azure ale existuje dodatečný požadavek. 

Při SQL Server na virtuálních počítačích Azure nakonfigurujte [Nástroj pro vyrovnávání zatížení](availability-group-vnn-azure-load-balancer-configure.md) , který bude směrovat provoz do naslouchacího procesu skupiny dostupnosti, nebo pokud používáte SQL Server 2019 CU8 a novější, můžete nakonfigurovat [naslouchací proces DNN (Distributed Network Name)](availability-group-distributed-network-name-dnn-listener-configure.md) , který nahradí tradiční NASLOUCHACÍ proces skupiny dostupnosti vnn. 

Další podrobnosti o možnostech připojení clusteru najdete v tématu [Směrování hadr připojení k SQL Server na virtuálních počítačích Azure](hadr-cluster-best-practices.md#connectivity). 

### <a name="vnn-listener"></a>Naslouchací proces VNN 

Použijte [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) ke směrování provozu z klienta na klasický naslouchací proces názvu virtuální sítě (vnn) skupiny dostupnosti v síti Azure. 

Nástroj pro vyrovnávání zatížení uchovává IP adresy pro naslouchací proces VNN. Pokud máte více než jednu skupinu dostupnosti, každá skupina vyžaduje naslouchací proces VNN. Jeden nástroj pro vyrovnávání zatížení může podporovat více posluchačů.

Informace o tom, jak začít, najdete v tématu [Konfigurace nástroje pro vyrovnávání zatížení](availability-group-vnn-azure-load-balancer-configure.md). 

### <a name="dnn-listener"></a>Naslouchací proces DNN

SQL Server 2019 CU8 zavádí podporu pro naslouchací proces DNN (Distributed Network Name). Naslouchací proces DNN nahrazuje klasický naslouchací proces skupiny dostupnosti, kdy nutnost Azure Load Balancer směrovat provoz v síti Azure. 

Naslouchací proces DNN je doporučené řešení připojení HADR v Azure, protože zjednodušuje nasazení, snižuje údržbu a náklady a snižuje dobu převzetí služeb při selhání v případě selhání. 

Použijte naslouchací proces DNN k nahrazení stávajícího naslouchacího procesu VNN, případně ho použijte ve spojení s existujícím naslouchací proces VNN, aby vaše skupina dostupnosti měla dva odlišné body připojení – jeden pomocí názvu naslouchacího procesu VNN (a port, pokud není výchozí), a druhý pomocí názvu a portu naslouchacího procesu DNN. To může být užitečné pro zákazníky, kteří se chtějí vyhnout latenci převzetí služeb při selhání nástroje pro vyrovnávání zatížení, ale pořád využívají SQL Server funkce, které závisejí na naslouchací službě VNN, jako jsou distribuované skupiny dostupnosti, Service Broker nebo FileStream. Další informace najdete v tématu [interoperabilita DNN Listener a funkce SQL Server](availability-group-dnn-interoperability.md) .

Informace o tom, jak začít, najdete v tématu [Konfigurace naslouchacího procesu DNN](availability-group-distributed-network-name-dnn-listener-configure.md).


## <a name="deployment"></a>Nasazení 

Existuje několik možností, jak nasadit skupinu dostupnosti, která se SQL Server na virtuálních počítačích Azure. některé s víc než jiným Automation než jiné. 

Následující tabulka poskytuje porovnání dostupných možností:

| | portál Azure | Azure CLI/PowerShell | Šablony pro rychlý Start | Ruční |
|---------|---------|---------|---------|---------|
|**Verze SQL Serveru** |2016 + |2016 +|2016 +|2012 +|
|**Edice SQL Serveru** |Enterprise |Enterprise |Enterprise |Enterprise, Standard|
|**Verze Windows serveru**| 2016 + | 2016 + | 2016 + | Vše|
|**Vytvoří cluster za vás.**|Yes|Yes | Yes |No|
|**Vytvoří skupinu dostupnosti pro vás.** |Yes |No|No|No|
|**Nezávisle vytvoří naslouchací proces a vyrovnávání zatížení.** |No|No|No|Yes|
|**Je možné vytvořit naslouchací proces DNN pomocí této metody?**|No|No|No|Yes|
|**Konfigurace kvora služby WSFC**|Disk s kopií cloudu|Disk s kopií cloudu|Disk s kopií cloudu|Vše|
|**DR s více oblastmi** |No|No|No|Yes|
|**Podpora více podsítí** |Yes|Yes|Yes|Yes|
|**Podpora pro existující službu AD**|Yes|Yes|Yes|Yes|
|**DR s více zónami ve stejné oblasti**|Yes|Yes|Yes|Yes|
|**Distributed AG bez AD**|No|No|No|Yes|
|**Distribuovaný AG bez clusteru** |No|No|No|Yes|

Další informace najdete v tématech [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), [šablony rychlý Start](availability-group-quickstart-template-configure.md)a [Ruční](availability-group-manually-configure-prerequisites-tutorial.md).

## <a name="considerations"></a>Požadavky 

Na hostovaném clusteru s podporou převzetí služeb při selhání ve virtuálním počítači Azure IaaS doporučujeme použít jednu síťovou kartu na server (uzel clusteru) a jednu podsíť. Sítě Azure mají fyzickou redundanci, která v clusteru hostů virtuálních počítačů Azure IaaS vyžaduje další síťové adaptéry a podsítě, které nejsou potřebné. I když ověřovací zpráva clusteru vydá varování, že uzly jsou dosažitelné pouze v jedné síti, můžete toto varování bezpečně ignorovat ve všech hostovaných clusterech ve virtuálních počítačích Azure IaaS. 

## <a name="next-steps"></a>Další kroky

Projděte si [osvědčené postupy hadr](hadr-cluster-best-practices.md) a pak začněte s nasazením skupiny dostupnosti pomocí [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), [šablon rychlý Start](availability-group-quickstart-template-configure.md) nebo [ručně](availability-group-manually-configure-prerequisites-tutorial.md).

Případně můžete nasadit [skupinu dostupnosti bez clusteru](availability-group-clusterless-workgroup-configure.md) nebo skupinu dostupnosti ve [více oblastech](availability-group-manually-configure-multiple-regions.md).
