---
title: SAP HANA dostupnosti na virtuálních počítačích Azure – přehled | Microsoft Docs
description: Popisuje SAP HANA operací na virtuálních počítačích Azure nativní.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d5694207f9283a5db5a937d9f8867d1a5f661aac
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA vysoké dostupnosti pro virtuální počítače Azure

Řada funkcí Azure můžete použít k nasazení důležitých databáze jako SAP HANA na virtuálních počítačích Azure. Tento článek obsahuje pokyny o tom, jak dosáhnout dostupnosti pro SAP HANA instancí, které jsou hostované ve virtuálních počítačích Azure. Článek popisuje několik scénářů, které můžete implementovat pomocí infrastruktury Azure mají zvýšit dostupnost SAP HANA v Azure. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámeni s infrastruktury jako služby (IaaS) základy v Azure, včetně: 

- Postup nasazení virtuálního počítače nebo virtuální sítě prostřednictvím portálu Azure nebo prostředí PowerShell.
- Pomocí napříč platformami rozhraní příkazového řádku Azure (Azure CLI), včetně možnosti použití šablon JavaScript Object Notation (JSON).

Tento článek také předpokládá, že jste obeznámeni s instalací SAP HANA instance a správě aplikace a operační instance SAP HANA. Je obzvlášť důležité znát operace replikace systému HANA a instalační program. To zahrnuje úlohy, jako je zálohování a obnovení pro databáze SAP HANA.

Tyto články poskytují dobrý přehled o používání SAP HANA v Azure:

- [Ruční instalace jedné instance SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Nastavení replikace systému SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Zálohování SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Je také vhodné se seznámit s tyto články o SAP HANA:

- [Vysoká dostupnost pro SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Nejčastější dotazy: Vysoká dostupnost pro SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Provést replikaci systému SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SP 01 co je nového: vysoké dostupnosti](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Doporučení sítě pro replikaci systému SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA služby Automatické restartování](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurace replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Překračuje-li se seznámit s nasazením virtuálních počítačů v Azure, před v Azure definováním vaší architektury dostupnost, doporučujeme, abyste si přečetli [Správa dostupnosti virtuálních počítačích s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Smlouvy o úrovni služeb pro Azure součásti

Azure má jiný dostupnost SLA pro různé součásti, jako jsou sítě, úložiště a virtuálních počítačů. Všechny smlouvy SLA, jsou popsány. Další informace najdete v tématu [smlouvy o úrovni služeb Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

[Smlouvy o úrovni služeb pro virtuální počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) popisuje dva různé smlouvy SLA pro dva různé konfigurace:

- Jeden virtuální počítač, který používá [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) pro disk operačního systému a všech datových disků. Tato možnost poskytuje měsíčního provozu 99,9 procent.
- Více (alespoň dva) virtuálních počítačů, které jsou uspořádány do [sady dostupnosti. Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tato možnost poskytuje měsíčního provozu 99,95 procent.

Měření váš požadavek na dostupnost proti SLA, které můžete zadat Azure součásti. Zvolte scénářů pro SAP HANA zajistit dostupnost vaší požadované úrovni.

## <a name="next-steps"></a>Další postup

- Další informace o [SAP HANA dostupnosti v rámci jedné oblasti Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Další informace o [SAP HANA dostupnosti v oblastech Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


