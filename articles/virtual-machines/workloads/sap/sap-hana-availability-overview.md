---
title: SAP HANA dostupnost na virtuálních počítačích Azure – přehled | Microsoft Docs
description: Popisuje SAP HANA operací na nativních virtuálních počítačích Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 757dfc34e3be12d09b8f965a2bb0295adb712c11
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504231"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA vysoké dostupnosti pro virtuální počítače Azure

K nasazení důležitých databází, jako je SAP HANA na virtuálních počítačích Azure, můžete použít spoustu možností Azure. Tento článek poskytuje pokyny, jak dosáhnout dostupnosti pro SAP HANA instance hostované ve virtuálních počítačích Azure. Článek popisuje několik scénářů, které můžete implementovat pomocí infrastruktury Azure ke zvýšení dostupnosti SAP HANA v Azure. 

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že jste obeznámeni se základními principy infrastruktury jako služby (IaaS) v Azure, včetně těchto: 

- Jak nasadit virtuální počítače nebo virtuální sítě pomocí Azure Portal nebo PowerShellu
- Použití rozhraní příkazového řádku Azure pro více platforem (Azure CLI), včetně možnosti použít šablony JavaScript Object Notation (JSON).

Tento článek také předpokládá, že máte zkušenosti s instalací instancí SAP HANA a s využitím SAP HANA instancí pro správu a provoz. Je obzvláště důležité, abyste se seznámili s nastavením a operacemi replikace systému HANA. To zahrnuje úlohy, jako je zálohování a obnovení pro databáze SAP HANA.

Tyto články poskytují dobrý přehled o používání SAP HANA v Azure:

- [Ruční instalace SAP HANA s jednou instancí na virtuálních počítačích Azure](./hana-get-started.md)
- [Nastavení replikace systému SAP HANA na virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Zálohování SAP HANA na virtuálních počítačích Azure](./sap-hana-backup-guide.md)

Je také vhodné se seznámit s těmito články o SAP HANA:

- [Vysoká dostupnost pro SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Nejčastější dotazy: vysoká dostupnost pro SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Provést replikaci systému pro SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2,0 SPS 01 co je nového: vysoká dostupnost](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Doporučení sítě pro replikaci SAP HANA systému](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Automatické restartování služby SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurace replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Než se seznámíte s nasazením virtuálních počítačů v Azure, před definováním architektury dostupnosti v Azure doporučujeme, abyste si přečetli téma [Správa dostupnosti virtuálních počítačů s Windows v Azure](../../availability.md).

## <a name="service-level-agreements-for-azure-components"></a>Smlouvy o úrovni služeb pro součásti Azure

Azure má různé SLA dostupnosti pro různé komponenty, jako jsou sítě, úložiště a virtuální počítače. Všechny SLA jsou zdokumentovány. Další informace najdete v tématu [Microsoft Azure smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/). 

[Smlouva SLA pro Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) popisuje tři různé SLA pro tři různé konfigurace:

- Jeden virtuální počítač, který používá [Azure Premium SSD](../../managed-disks-overview.md) pro disk s operačním systémem a všechny datové disky. Tato možnost poskytuje měsíční dobu provozu 99,9 procent.
- Víc (aspoň dva) virtuální počítače, které jsou uspořádané do [skupiny dostupnosti Azure](../../windows/tutorial-availability-sets.md). Tato možnost poskytuje měsíční dobu provozu 99,95 procent.
- Víc (aspoň dva) virtuální počítače, které jsou uspořádané do [zóny dostupnost](../../../availability-zones/az-overview.md). Tato možnost zadala za měsíc 99,99% dobu provozu.

Změřte svůj požadavek na dostupnost proti SLA, kterou můžou součásti Azure poskytovat. Pak zvolte scénáře, které SAP HANA, abyste dosáhli požadované úrovně dostupnosti.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [dostupnosti SAP HANA v rámci jedné oblasti Azure](./sap-hana-availability-one-region.md).
- Přečtěte si o [dostupnosti SAP HANA v různých oblastech Azure](./sap-hana-availability-across-regions.md). 















