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
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 34d76d7c85a478b5e31a692e653752aa1653884c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91293658"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Představení skupin dostupnosti Always On SQL Server v Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek představuje SQL Server skupiny dostupnosti v Azure Virtual Machines. 

Skupiny dostupnosti Always On v Azure Virtual Machines jsou podobné skupinám dostupnosti Always On místně. Další informace najdete v tématu [skupiny dostupnosti Always On (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Následující diagram znázorňuje části úplné SQL Server skupiny dostupnosti ve službě Azure Virtual Machines.

![Skupina dostupnosti](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Klíčový rozdíl pro skupinu dostupnosti v Azure Virtual Machines je, že tyto virtuální počítače vyžadují [Nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md). Nástroj pro vyrovnávání zatížení uchovává IP adresy pro naslouchací proces skupiny dostupnosti. Pokud máte více než jednu skupinu dostupnosti, každá skupina vyžaduje naslouchací proces. Jeden nástroj pro vyrovnávání zatížení může podporovat více posluchačů.

V clusteru s podporou převzetí služeb při selhání hosta virtuálního počítače Azure IaaS doporučujeme jednu síťovou kartu na server (uzel clusteru) a jednu podsíť. Sítě Azure mají fyzickou redundanci, která v clusteru hostů virtuálních počítačů Azure IaaS vyžaduje další síťové adaptéry a podsítě, které nejsou potřebné. I když ověřovací zpráva clusteru vydá varování, že uzly jsou dosažitelné pouze v jedné síti, můžete toto varování bezpečně ignorovat ve všech hostovaných clusterech ve virtuálních počítačích Azure IaaS. 

Aby bylo možné zvýšit redundanci a vysokou dostupnost, musí být virtuální počítače s SQL Server buď ve stejné [skupině dostupnosti](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets), nebo v různých [zónách dostupnosti](/azure/availability-zones/az-overview). 

|  | Verze Windows serveru | Verze SQL Server | Edice SQL Server | Konfigurace kvora služby WSFC | DR s více oblastmi | Podpora více podsítí | Podpora pro existující službu AD | DR se stejnou oblastí ve více zónách | Podpora balíčku DIST-AG bez domény AD | Podpora balíčku DIST-AG bez clusteru |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| **[portál Azure](availability-group-azure-portal-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | Rozlehlé | Disk s kopií cloudu | No | Yes | Yes | Yes | No | No |
| **[Azure CLI/PowerShell](availability-group-az-cli-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | Rozlehlé | Disk s kopií cloudu | No | Yes | Yes | Yes | No | No |
| **[Šablony pro rychlý Start](availability-group-quickstart-template-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016  | Rozlehlé | Disk s kopií cloudu | No | Yes | Yes | Yes | No | No |
| **[Ruční](availability-group-manually-configure-prerequisites-tutorial.md)** | Vše | Vše | Vše | Vše | Yes | Yes | Yes | Yes | Yes | Yes |

Šablona **SQL Serverho clusteru AlwaysOn (Preview)** byla odebrána z Azure Marketplace a již není k dispozici. 

Až budete připraveni vytvořit skupinu dostupnosti SQL Server v Azure Virtual Machines, přečtěte si tyto kurzy.

## <a name="manually-with-azure-cli"></a>Ruční pomocí Azure CLI

Pro konfiguraci a nasazení skupiny dostupnosti doporučujeme použít Azure CLI, protože to je nejjednodušší a nejrychlejší nasazení. Pomocí Azure CLI se při vytváření clusteru s podporou převzetí služeb při selhání systému Windows, připojení SQL Server virtuálních počítačů do clusteru a vytváření naslouchacího procesu a interních Load Balancer dají dosáhnout za méně než 30 minut. Tato možnost stále vyžaduje ruční vytvoření skupiny dostupnosti, ale automatizuje všechny ostatní nezbytné kroky konfigurace. 

Další informace najdete v tématu věnovaném [použití Azure SQL VM CLI ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](availability-group-az-cli-configure.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatické zprovoznění šablon Azure pro rychlý Start

Šablony pro rychlý Start Azure využívají poskytovatele prostředků virtuálního počítače SQL k nasazení clusteru s podporou převzetí služeb při selhání s Windows, připojte SQL Server k tomuto virtuálnímu počítači, vytvořte naslouchací proces a nakonfigurujte interní Load Balancer. Tato možnost stále vyžaduje ruční vytvoření skupiny dostupnosti a interní Load Balancer (interního nástroje). Ale automatizuje a zjednodušuje všechny další nezbytné kroky konfigurace, včetně konfigurace interního nástroje. 

Další informace najdete v tématu [použití šablony pro rychlý Start Azure ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure](availability-group-quickstart-template-configure.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatické s Azure Portal šablonou

[Automatická konfigurace skupiny dostupnosti Always On na virtuálním počítači Azure Správce prostředků](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-the-azure-portal"></a>Ručně v Azure Portal

Virtuální počítače můžete také vytvořit sami bez šablony. Nejdřív dokončete požadované součásti a pak vytvořte skupinu dostupnosti. Přečtěte si následující témata: 

- [Konfigurace požadavků pro skupiny dostupnosti Always On SQL Server v Azure Virtual Machines](availability-group-manually-configure-prerequisites-tutorial.md)

- [Vytvoření skupiny dostupnosti Always On pro zlepšení dostupnosti a zotavení po havárii](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Další kroky

[Konfigurace skupiny dostupnosti Always On SQL Server v Azure Virtual Machines v různých oblastech](availability-group-manually-configure-multiple-regions.md)
