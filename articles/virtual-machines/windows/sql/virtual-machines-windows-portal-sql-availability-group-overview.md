---
title: -Azure Virtual Machines – přehled skupin dostupnosti pro SQL Server | Dokumentace Microsoftu
description: Tento článek představuje skupiny dostupnosti SQL serveru na virtuálních počítačích Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
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
ms.openlocfilehash: 5f8ae6d9138a7413b0cca4cca7bcc47c13212674
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358047"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Představení skupin dostupnosti AlwaysOn SQL serveru na virtuálních počítačích Azure #

Tento článek představuje skupiny dostupnosti SQL serveru na virtuálních počítačích Azure. 

Skupiny dostupnosti AlwaysOn na virtuálních počítačích Azure se podobají skupin dostupnosti AlwaysOn v místním prostředí. Další informace najdete v tématu [vždy na skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Diagram ukazuje části kompletní skupiny dostupnosti SQL serveru ve službě Azure Virtual Machines.

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Klíčovým rozdílem pro skupinu dostupnosti v Azure Virtual Machines je, že virtuální počítače Azure vyžadují [nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md). Nástroje pro vyrovnávání zatížení obsahuje IP adresy pro naslouchací proces skupiny dostupnosti. Pokud máte více než jedné skupině dostupnosti vyžaduje každá skupina naslouchací proces. Jeden nástroj pro vyrovnávání zatížení může podporovat víc naslouchacích procesů.

Kromě toho v clusteru převzetí služeb při selhání hosta virtuálního počítače Azure IaaS, doporučujeme jednu síťovou kartu na serveru (uzlu clusteru) a jednu podsíť. Sítě Azure má fyzický redundanci díky další síťové adaptéry a podsítě zbytečné v clusteru hostů virtuálních počítačů Azure IaaS. I když sestavu ověření clusteru, vydá upozornění, že uzly jsou pouze v jedné síti dostupný, toto upozornění můžete ignorovat v clusterech převzetí služeb při selhání hosta virtuálního počítače Azure IaaS. 

Až budete připravení k vytvoření skupiny dostupnosti systému SQL Server na Azure Virtual Machines, najdete v těchto kurzech.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Automaticky ze šablony vytvořit skupinu dostupnosti

[Konfigurace skupiny dostupnosti Always On na virtuálním počítači Azure automaticky - Resource Manageru](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Ručně na webu Azure portal vytvořit skupinu dostupnosti

Můžete také vytvořit virtuální počítače sami bez šablony. Nejprve zajistěte splnění požadavků a pak vytvořte skupiny dostupnosti. V následujících tématech: 

- [Konfigurace požadavků pro skupiny dostupnosti AlwaysOn SQL serveru na virtuálních počítačích Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Vytvoření skupiny dostupnosti Always On pro zlepšení dostupnosti a zotavení po havárii](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Další postup

[Konfigurace AlwaysOn SQL serveru ve skupině dostupnosti v Azure Virtual Machines v různých oblastech](virtual-machines-windows-portal-sql-availability-group-dr.md).
