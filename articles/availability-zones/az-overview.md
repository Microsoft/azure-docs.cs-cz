---
title: "Dostupnost zóny přehled | Microsoft Docs"
description: "Tento článek obsahuje přehled o dostupnosti zóny v Azure."
services: 
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: db4801d105a6ca7382756224aa84c9e7b305194f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>Přehled dostupnosti zón v Azure (Preview)

Dostupnost zóny pomoci při ochraně před selháním úrovni datového centra. Jsou umístěny uvnitř oblasti Azure, a každé z nich má svou vlastní nezávisle spotřeby zdroje, sítě a chlazení. K zajištění odolnosti, je minimálně tři samostatné zóny ve všech oblastech povoleno. Fyzické a logické oddělení dostupnost zóny v rámci oblasti chrání aplikace a data před selháním úrovni zóny. 

![koncepční zobrazení jednu zónu směrem dolů v oblasti.](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Oblasti, které podporují dostupnost zóny

- Východní USA 2
- USA – střed
- Západní Evropa
- Francie – střed

## <a name="services-that-support-availability-zones"></a>Služby, které podporují dostupnost zóny

Služby Azure, které podporují dostupnost zóny jsou:

- Virtuální počítače s Linuxem
- Virtuální počítače s Windows
- Škálovací sady virtuálních počítačů
- Managed Disks
- Load Balancer
- Veřejná IP adresa
- Databáze SQL

## <a name="get-started-with-the-availability-zones-preview"></a>Začínáme s preview dostupnost zóny

Dostupnost zóny náhled je dostupný v východní USA 2, nám centrální, západní Evropa a Francie centrální oblasti pro konkrétní služby Azure. 

1. [Registrace pro náhled zóny dostupnosti](http://aka.ms/azenroll). 
2. Přihlaste se k předplatnému Azure.
3. Vyberte oblast, která podporuje dostupnost zóny.
4. Ke spuštění pomocí služby dostupnost zóny, použijte jednu z následujících odkazů. 
    - [Vytvoření virtuálního počítače](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Vytvoření škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
    - [Přidejte Disk spravované pomocí prostředí PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Load Balancer](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>Další postup
- [Šablony Rychlý start](http://aka.ms/azqs)
