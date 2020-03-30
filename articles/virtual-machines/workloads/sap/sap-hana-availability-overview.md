---
title: Dostupnost SAP HANA na virtuálních počítačích Azure – přehled | Dokumenty společnosti Microsoft
description: Popisuje operace SAP HANA na nativních virtuálních počítačích Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccaf45cf617bd31a584b6c73f3dd08877bc8587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71266061"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Sap HANA vysoká dostupnost pro virtuální počítače Azure

K nasazení důležitých databází, jako je SAP HANA na virtuálních počítačích Azure, můžete použít mnoho funkcí Azure. Tento článek obsahuje pokyny, jak dosáhnout dostupnosti pro instance SAP HANA, které jsou hostované ve virtuálních počítačích Azure. Článek popisuje několik scénářů, které můžete implementovat pomocí infrastruktury Azure ke zvýšení dostupnosti SAP HANA v Azure. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámeni se základy infrastruktury jako služby (IaaS) v Azure, včetně: 

- Jak nasadit virtuální počítače nebo virtuální sítě přes portál Azure nebo PowerShell.
- Použití rozhraní příkazového řádku Azure pro více platforem (Azure CLI), včetně možnosti používat šablony JavaScript Object Notation (JSON).

Tento článek také předpokládá, že jste obeznámeni s instalací sap hana instance a s správou a provozem SAP HANA instance. Je obzvláště důležité znát nastavení a operace replikace systému HANA. To zahrnuje úlohy, jako je zálohování a obnovení pro databáze SAP HANA.

Tyto články poskytují dobrý přehled o používání SAP HANA v Azure:

- [Ruční instalace sap hana s jednou instancí na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Nastavení replikace systému SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Zálohování SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Je také dobré znát tyto články o SAP HANA:

- [Vysoká dostupnost pro SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Časté dotazy: Vysoká dostupnost pro SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Provedení systémové replikace pro SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 Co je nového: Vysoká dostupnost](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Doporučení sítě pro replikaci systému SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Automatické restartování služby SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurace replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Kromě toho, že jste obeznámeni s nasazováním virtuálních počítačů v Azure, doporučujeme, abyste si před definováním architektury dostupnosti v Azure přečetli [Správu dostupnosti virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Smlouvy o úrovni služeb pro součásti Azure

Azure má různé možnosti sla pro různé součásti, jako je síť, úložiště a virtuální počítače. Všechny sla jsou zdokumentovány. Další informace naleznete v [tématu Microsoft Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/). 

[SLA pro virtuální počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) popisuje tři různé sla, pro tři různé konfigurace:

- Jeden virtuální počítač, který používá [Azure premium SSD](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) pro disk operačního systému a všechny datové disky. Tato možnost poskytuje měsíční uptime 99,9 procenta.
- Více (alespoň dva) virtuální počítače, které jsou uspořádané v [azure dostupnosti .](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) Tato možnost poskytuje měsíční dobu uptime 99.95 procent.
- Více (alespoň dva) virtuálních počítačů, které jsou uspořádané v [zóně dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Tato možnost poskytovala měsíční uptime 99,99 procenta.

Změřte požadavek na dostupnost podle sla, které můžou komponenty Azure poskytovat. Potom zvolte scénáře pro SAP HANA k dosažení požadované úrovně dostupnosti.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [dostupnosti SAP HANA v rámci jedné oblasti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Přečtěte si o [dostupnosti SAP HANA v rámci oblastí Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


