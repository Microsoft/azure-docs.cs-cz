---
title: -Azure Virtual Machines – přehled skupin dostupnosti pro SQL Server | Dokumentace Microsoftu
description: Tento článek představuje skupiny dostupnosti SQL serveru na virtuálních počítačích Azure.
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
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861838"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Představení skupin dostupnosti AlwaysOn SQL serveru na virtuálních počítačích Azure #

Tento článek představuje skupiny dostupnosti SQL serveru na virtuálních počítačích Azure. 

Skupiny dostupnosti AlwaysOn na virtuálních počítačích Azure se podobají skupin dostupnosti AlwaysOn v místním prostředí. Další informace najdete v tématu [vždy na skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Diagram ukazuje části kompletní skupiny dostupnosti SQL serveru ve službě Azure Virtual Machines.

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Klíčovým rozdílem pro skupinu dostupnosti v Azure Virtual Machines je, že virtuální počítače Azure vyžadují [nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md). Nástroje pro vyrovnávání zatížení obsahuje IP adresy pro naslouchací proces skupiny dostupnosti. Pokud máte více než jedné skupině dostupnosti vyžaduje každá skupina naslouchací proces. Jeden nástroj pro vyrovnávání zatížení může podporovat víc naslouchacích procesů.

Kromě toho v clusteru převzetí služeb při selhání hosta virtuálního počítače Azure IaaS, doporučujeme jednu síťovou kartu na serveru (uzlu clusteru) a jednu podsíť. Sítě Azure má fyzický redundanci díky další síťové adaptéry a podsítě zbytečné v clusteru hostů virtuálních počítačů Azure IaaS. I když sestavu ověření clusteru, vydá upozornění, že uzly jsou pouze v jedné síti dostupný, toto upozornění můžete ignorovat v clusterech převzetí služeb při selhání hosta virtuálního počítače Azure IaaS. 

|  | Verze systému Windows Server | Verze SQL serveru | SQL Server Edition | Konfigurace služby WSFC kvora | Zotavení po Havárii s více oblastí | Podpora více podsítí | Podpora pro existující AD | Zotavení po Havárii s více zón stejné oblasti | Podpora DIST AG se žádné domény služby AD | Podpora DIST AG se žádný cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Zahájení instalace serveru | Disk s kopií cloudu | Ne | Ano | Ano | Ano | Ne | Ne |
| [Šablony pro rychlý start](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Zahájení instalace serveru | Disk s kopií cloudu | Ne | Ano | Ano | Ano | Ne | Ne |
| [Šablony portálu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Zahájení instalace serveru | Sdílená složka | Ne | Ne | Ne | Ne | Ne | Ne |
| [Ruční](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Vše | Vše | Vše | Vše | Ano | Ano | Ano | Ano | Ano | Ano |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Až budete připravení k vytvoření skupiny dostupnosti systému SQL Server na Azure Virtual Machines, najdete v těchto kurzech.

## <a name="manually-with-azure-cli"></a>Ručně pomocí rozhraní příkazového řádku Azure
Pomocí Azure CLI můžete nakonfigurovat a nasadit skupinu dostupnosti možnost se doporučuje, protože je to nejlepší z hlediska rychle a snadno nasazení. Pomocí rozhraní příkazového řádku Azure, vytváření clusteru převzetí služeb při selhání Windows, připojení ke clusteru, virtuální počítače s SQL serverem a také vytvoření naslouchacího procesu a interního nástroje Load Balancer všechny jde dosáhnout za méně než 30 minut. Tato možnost se stále vyžaduje ruční vytvoření skupiny dostupnosti, ale automatizuje všechny další kroky nezbytné konfigurace. 

Další informace najdete v tématu [pomocí příkazového řádku virtuálního počítače SQL Azure ke konfiguraci skupiny dostupnosti Always On pro SQL Server na Virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticky pomocí šablon Azure Quickstart
Šablony rychlý start Azure využívat poskytovatele prostředků SQL virtuálních počítačů k nasazení clusteru převzetí služeb při selhání Windows, připojení k němu virtuální počítače s SQL serverem, vytvořit naslouchací proces, konfigurace interního nástroje pro vyrovnávání zatížení. Tato možnost stále vyžaduje ruční vytvoření skupiny dostupnosti a interní nástroj pro vyrovnávání zatížení (ILB), ale automatizuje a zjednodušuje všechny ostatní potřebný postup konfigurace (včetně konfigurace ILB). 

Další informace najdete v tématu [šablonu pro rychlý start Azure použijte ke konfiguraci skupiny dostupnosti Always On pro SQL Server na Virtuálním počítači Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticky pomocí šablony Azure Portal

[Konfigurace skupiny dostupnosti Always On na virtuálním počítači Azure automaticky - Resource Manageru](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Ručně na webu Azure portal

Můžete také vytvořit virtuální počítače sami bez šablony. Nejprve zajistěte splnění požadavků a pak vytvořte skupiny dostupnosti. V následujících tématech: 

- [Konfigurace požadavků pro skupiny dostupnosti AlwaysOn SQL serveru na virtuálních počítačích Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Vytvoření skupiny dostupnosti Always On pro zlepšení dostupnosti a zotavení po havárii](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Další postup

[Konfigurace AlwaysOn SQL serveru na skupině dostupnosti na virtuálních počítačích, které jsou v různých oblastech Azure](virtual-machines-windows-portal-sql-availability-group-dr.md)
