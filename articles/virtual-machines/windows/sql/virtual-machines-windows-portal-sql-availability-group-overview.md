---
title: Skupiny dostupnosti SQL Server – Azure Virtual Machines – přehled | Microsoft Docs
description: Tento článek představuje SQL Server skupiny dostupnosti na virtuálních počítačích Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: 4a65465528ea2ffd8ba7af705d92efbb23a96d9e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883460"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Představujeme SQL Server skupiny dostupnosti Always On na virtuálních počítačích Azure #

Tento článek představuje SQL Server skupiny dostupnosti v Azure Virtual Machines. 

Skupiny dostupnosti Always On v Azure Virtual Machines jsou podobné skupinám dostupnosti Always On místně. Další informace najdete v tématu [skupiny dostupnosti Always On (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Diagram znázorňuje části úplné SQL Server skupiny dostupnosti ve službě Azure Virtual Machines.

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Klíčový rozdíl pro skupinu dostupnosti ve službě Azure Virtual Machines je, že virtuální počítače Azure vyžadují [Nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md). Nástroj pro vyrovnávání zatížení uchovává IP adresy pro naslouchací proces skupiny dostupnosti. Pokud máte více než jednu skupinu dostupnosti, každá skupina vyžaduje naslouchací proces. Jeden nástroj pro vyrovnávání zatížení může podporovat více posluchačů.

V clusteru s podporou převzetí služeb při selhání hosta virtuálního počítače Azure IaaS doporučujeme jednu síťovou kartu na server (uzel clusteru) a jednu podsíť. Sítě Azure má fyzickou redundanci, díky níž je zbytečné používat další síťové adaptéry a podsítě na hostovaném clusteru ve virtuálním počítači Azure IaaS. I když ověřovací zpráva clusteru vydá varování, že uzly jsou dosažitelné pouze v jedné síti, můžete toto varování bezpečně ignorovat ve všech hostovaných clusterech ve virtuálních počítačích Azure IaaS. 

Aby bylo možné zvýšit redundanci a vysokou dostupnost, musí být virtuální počítače s SQL Server buď ve stejné [skupině dostupnosti](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets), nebo v různých [zónách dostupnosti](/azure/availability-zones/az-overview). 

|  | Verze Windows serveru | Verze SQL Server | SQL Server Edition | Konfigurace kvora služby WSFC | DR s více oblastmi | Podpora více podsítí | Podpora pro existující službu AD | DR se stejnou oblastí ve více zónách | Podpora balíčku DIST-AG bez domény AD | Podpora balíčku DIST-AG bez clusteru |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Rozlehlé | Disk s kopií cloudu | Ne | Ano | Ano | Ano | Ne | Ne |
| [Šablony pro rychlý Start](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Rozlehlé | Disk s kopií cloudu | Ne | Ano | Ano | Ano | Ne | Ne |
| [Šablona portálu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Rozlehlé | Sdílená složka | Ne | Ne | Ne | Ne | Ne | Ne |
| [Zásah](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Vše | Vše | Vše | Vše | Ano | Ano | Ano | Ano | Ano | Ano |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Až budete připraveni vytvořit skupinu dostupnosti SQL Server v Azure Virtual Machines, přečtěte si tyto kurzy.

## <a name="manually-with-azure-cli"></a>Ruční pomocí Azure CLI
Doporučená možnost je použití Azure CLI ke konfiguraci a nasazení skupiny dostupnosti, protože to je nejlepší z hlediska jednoduchosti a rychlosti nasazení. Pomocí Azure CLI se při vytváření clusteru s podporou převzetí služeb při selhání systému Windows, připojení SQL Server virtuálních počítačů do clusteru a vytváření naslouchacího procesu a interních Load Balancer dají dosáhnout za méně než 30 minut. Tato možnost stále vyžaduje ruční vytvoření skupiny dostupnosti, ale automatizuje všechny ostatní nezbytné kroky konfigurace. 

Další informace najdete v tématu věnovaném [použití Azure SQL VM CLI ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatické zprovoznění šablon Azure pro rychlý Start
Šablony pro rychlý Start Azure využívají poskytovatele prostředků virtuálního počítače SQL k nasazení clusteru s podporou převzetí služeb při selhání s Windows, připojte SQL Server k tomuto virtuálnímu počítači, vytvořte naslouchací proces a nakonfigurujte interní Load Balancer. Tato možnost stále vyžaduje ruční vytvoření skupiny dostupnosti a interní Load Balancer (interního nástroje), ale automatizuje a zjednodušuje všechny další nezbytné kroky konfigurace (včetně konfigurace interního nástroje). 

Další informace najdete v tématu [použití šablony pro rychlý Start Azure ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatické s Azure Portal šablonou

[Automatická konfigurace skupiny dostupnosti Always On na virtuálním počítači Azure Správce prostředků](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Ručně v Azure Portal

Virtuální počítače můžete také vytvořit sami bez šablony. Nejdřív dokončete požadované součásti a pak vytvořte skupinu dostupnosti. Přečtěte si následující témata: 

- [Konfigurace požadavků pro skupiny dostupnosti Always On SQL Server v Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Vytvoření skupiny dostupnosti Always On pro zlepšení dostupnosti a zotavení po havárii](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Další postup

[Konfigurace skupiny dostupnosti Always On SQL Server v Azure Virtual Machines v různých oblastech](virtual-machines-windows-portal-sql-availability-group-dr.md)
