---
title: "SAP HANA dostupnosti na virtuálních počítačích Azure – přehled | Microsoft Docs"
description: "Operace na virtuálních počítačích Azure nativní SAP HANA"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Průvodce SAP HANA vysoké dostupnosti pro virtuální počítače Azure
Azure poskytuje řada funkcí, které umožňuje nasadit zvláště důležité databáze jako SAP HANA ve virtuálních počítačích Azure. Tento dokument obsahuje pokyny o tom, jak dosáhnout dostupnosti pro SAP HANA instancí, které jsou hostované v Azure Virtual Machines. V tomto dokumentu jsou popsány několik scénářů, které může být implementováno na infrastrukturu Azure, pokud chcete zvýšit dostupnost SAP HANA v Azure. 

## <a name="prerequisites"></a>Požadavky
Tato příručka předpokládá, že jste obeznámeni s takovou infrastrukturu jako službu (IaaS) základy v Azure jako: 

- Postup nasazení virtuálního počítače nebo virtuální sítě prostřednictvím portálu Azure nebo prostředí PowerShell.
- Azure napříč platformami rozhraní příkazového řádku (CLI), včetně možnosti použití šablon JavaScript Object Notation (JSON).

Tato příručka také předpokládá, že jste obeznámeni s instalaci instance SAP HANA a správě a provozování instance SAP HANA. Zejména nastavit a operace kolem HANA systému replikace nebo úlohy, jako zálohování a obnovení databáze SAP HANA.

Další články, které poskytují dobrý přehled o tématech SAP HANA v Azure jsou:

- [Ruční instalace jedné instance SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Nastavení replikace systému SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Příručce zálohování pro SAP HANA ve virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Článek a obsah, měli byste se seznámit o SAP HANA může být uvedený jako:

- [Vysoká dostupnost pro SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Nejčastější dotazy: Vysoká dostupnost pro SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Jak provádět replikaci systému pro SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SP 01, co je nového: vysoké dostupnosti](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Doporučení sítě pro replikaci systému SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA služby Automatické restartování](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurace replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)


Překračuje-li se seznámit s nasazením virtuálních počítačů v Azure, doporučujeme přečtení tohoto článku [Správa dostupnosti virtuálních počítačích s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) první před pokračováním v Azure definováním vaší architektury dostupnosti.

## <a name="service-level-agreements-for-different-azure-components"></a>Smlouvy o úrovni služeb pro různé součásti Azure
Azure má jiný dostupnost SLA pro různé komponenty, jako jsou sítě, úložiště a virtuálních počítačů. Všechny tyto SLA popsané a nachází se od verze [smlouvy o úrovni služeb Microsoft Azure](https://azure.microsoft.com/support/legal/sla/) stránky. Pokud je rezervovat [SLA pro virtuální počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), si myslíte, že Azure poskytuje dvě různé SLA s dvěma různými konfiguracemi. Smluv SLA jsou definovány jako:

- Jeden virtuální počítač pomocí [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) pro operačního systému disku a všechny datové disky, poskytuje 99,9 % měsíční procento doby provozu
- Více (alespoň dva) virtuálních počítačů, které jsou uspořádány do [sadu dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) zadejte měsíční procento doby provozu 99,95 %

Míra váš požadavek dostupnosti oproti komponenty Azure SLA můžete zadat a následně se rozhodnete pro různé scénáře, je nutné implementovat s SAP HANA lze dosáhnout dostupnosti vyžadují, aby poskytnout.

## <a name="next-steps"></a>Další postup
Nadále dokumenty:

- [SAP HANA dostupnosti v rámci jedné oblasti Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [SAP HANA dostupnosti v oblastech Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


