---
title: Přehled skupin dostupnosti
description: Tento článek představuje skupiny dostupnosti serveru SQL Server na virtuálních počítačích Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037501"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Představujeme skupiny dostupnosti SQL Serveru na virtuálních počítačích Azure

Tento článek představuje skupiny dostupnosti SQL Server na virtuálních počítačích Azure. 

Vždy na dostupnost skupiny na virtuálních počítačích Azure jsou podobné vždy na dostupnost skupiny v místním prostředí. Další informace naleznete [v tématu Vždy na skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Diagram znázorňuje části kompletní skupiny dostupnosti serveru SQL Server ve virtuálních počítačích Azure.

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Klíčovým rozdílem pro skupinu dostupnosti ve virtuálních počítačích Azure je, že virtuální počítače Azure vyžadují [nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md). Vykladač zatížení obsahuje ip adresy pro naslouchací proces skupiny dostupnosti. Pokud máte více než jednu skupinu dostupnosti, každá skupina vyžaduje naslouchací proces. Jeden vyrovnávání zatížení může podporovat více naslouchacích procesy.

Navíc v clusteru azure IaaS virtuálního počítače s podporou převzetí služeb při selhání doporučujeme jednu síťovou konto na server (uzel clusteru) a jednu podsíť. Sítě Azure má fyzickou redundanci, díky níž je zbytečné používat další síťové adaptéry a podsítě na hostovaném clusteru ve virtuálním počítači Azure IaaS. I když ověřovací zpráva clusteru vydá varování, že uzly jsou dosažitelné pouze v jedné síti, můžete toto varování bezpečně ignorovat ve všech hostovaných clusterech ve virtuálních počítačích Azure IaaS. 

Chcete-li zvýšit redundanci a vysokou dostupnost, virtuální chody serveru SQL Server by měly být buď ve stejné [skupině dostupnosti](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets), nebo v různých [zónách dostupnosti](/azure/availability-zones/az-overview). 

|  | Verze systému Windows Server | Verze serveru SQL Server | Edice SERVERU SQL Server | Konfigurace kvora wsfc | Zotavení po havárii s více oblastmi | Podpora více podsítí | Podpora stávajícího ad | Zotavení po havárii se stejnou oblastí s více zónami | Podpora Dist-AG bez domény AD | Podpora Dist-AG bez clusteru |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [Cli virtuálního virtuálního serveru SQL](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Orl | Disk s kopií cloudu | Ne | Ano | Ano | Ano | Ne | Ne |
| [Šablony rychlého startu](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Orl | Disk s kopií cloudu | Ne | Ano | Ano | Ano | Ne | Ne |
| [Šablona portálu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Orl | Sdílená složka | Ne | Ne | Ne | Ne | Ne | Ne |
| [Ruční](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Všechny | Všechny | Všechny | Všechny | Ano | Ano | Ano | Ano | Ano | Ano |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Až budete připraveni k vytvoření skupiny dostupnosti SQL Server na virtuálních počítačích Azure, naleznete v těchto kurzech.

## <a name="manually-with-azure-cli"></a>Ručně pomocí azure cli
Pomocí Azure CLI ke konfiguraci a nasazení skupiny dostupnosti je doporučená možnost, protože je to nejlepší z hlediska jednoduchosti a rychlosti nasazení. Pomocí azure cli, vytvoření clusteru Windows převzetí služeb při selhání, připojení SQL Server virtuálních počítačů s clusterem, stejně jako vytvoření naslouchací proces a vnitřní vyrovnávání zatížení lze dosáhnout za méně než 30 minut. Tato možnost stále vyžaduje ruční vytvoření skupiny dostupnosti, ale automatizuje všechny ostatní nezbytné kroky konfigurace. 

Další informace najdete [v tématu Použití rozhraní příkazu CLI virtuálního počítače Azure SQL ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticky pomocí šablon Azure QuickStart
Šablony Rychlého spuštění Azure využívají poskytovatele prostředků virtuálního počítače SQL k nasazení clusteru windows failover, připojení virtuálních počítačů SQL Server, vytvoření naslouchací proces a konfiguraci nástroje pro interní vyrovnávání zatížení. Tato možnost stále vyžaduje ruční vytvoření skupiny dostupnosti a interní nástroj pro vyrovnávání zatížení (ILB), ale automatizuje a zjednodušuje všechny ostatní nezbytné kroky konfigurace (včetně konfigurace ILB). 

Další informace najdete [v tématu Použití šablony Rychlého spuštění Azure ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticky pomocí šablony portálu Azure

[Konfigurace skupiny dostupnosti always on v azure virtuálním počítači automaticky – Správce prostředků](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Ručně na webu Azure Portal

Virtuální počítače můžete také vytvořit sami bez šablony. Nejprve vyplňte požadavky a vytvořte skupinu dostupnosti. Podívejte se na následující témata: 

- [Konfigurace požadavků pro skupiny dostupnosti SQL Server always on ve virtuálních počítačích Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Vytvořit skupinu dostupnosti always on pro zlepšení dostupnosti a zotavení po havárii](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Další kroky

[Konfigurace skupiny dostupnosti sql serveru always on na virtuálních počítačích Azure v různých oblastech](virtual-machines-windows-portal-sql-availability-group-dr.md)
