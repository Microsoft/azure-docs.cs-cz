---
title: Skupiny dostupnosti SQL serveru – virtuální počítače Azure – přehled | Microsoft Docs
description: Tento článek představuje skupin dostupnosti SQL Server na virtuálních počítačích Azure.
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
ms.openlocfilehash: 4a531fb87d9cd2743138ba7a027bdf0d132b9747
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29396427"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Představení skupiny dostupnosti SQL serveru Always On na virtuálních počítačích Azure #

Tento článek představuje skupiny dostupnosti systému SQL Server na virtuálních počítačích Azure. 

Always On skupin dostupnosti ve virtuálních počítačích Azure se podobá Always On skupiny dostupnosti místně. Další informace najdete v tématu [skupin dostupnosti Always On (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

Diagram znázorňuje části celé skupiny dostupnosti SQL Server v Azure Virtual Machines.

![Skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Klíčovým rozdílem pro skupinu dostupnosti v Azure Virtual Machines je virtuální počítače Azure, vyžadují, aby se [nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md). Nástroje pro vyrovnávání zatížení obsahuje IP adresy pro naslouchací proces skupiny dostupnosti. Pokud máte více než jedna skupina dostupnosti vyžaduje každá skupina naslouchací proces. Jedna služba Vyrovnávání zatížení může podporovat více naslouchací procesy.

Jakmile budete připraveni k sestavení aroup dostupnosti systému SQL Server na virtuálních počítačích Azure, naleznete v těchto kurzech.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Automaticky vytvořit skupinu dostupnosti ze šablony

[Konfigurace skupiny dostupnosti Always On ve virtuálním počítači Azure automaticky - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Ručně vytvořit skupinu dostupnosti na portálu Azure

Můžete také vytvořit virtuální počítače sami bez šablony. Nejprve splnit požadavky a potom vytvořit skupinu dostupnosti. Najdete v následujících tématech: 

- [Konfigurace požadavků pro skupiny dostupnosti SQL serveru Always On na virtuálních počítačích Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Vytvoření vždy na skupiny dostupnosti ke zlepšení dostupnosti a zotavení po havárii](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Další postup

[Konfigurace systému SQL Server vždy na skupiny dostupnosti na virtuálních počítačích, které jsou v různých oblastech Azure](virtual-machines-windows-portal-sql-availability-group-dr.md).
