---
title: Dostupnost SAP HANA na virtuálních počítačích Azure – přehled | Dokumentace Microsoftu
description: Popisuje operace SAP HANA na nativních virtuálních počítačích Azure.
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
ms.openlocfilehash: 7049a4b5159687ab928cda7ddc6b1a35959529ac
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972103"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Vysoká dostupnost SAP HANA pro virtuální počítače Azure

Řada funkcí Azure můžete použít k nasazení kritických pro chod databáze, jako je SAP HANA na virtuálních počítačích Azure. Tento článek obsahuje pokyny o tom, jak dosáhnout dostupnosti pro instance SAP HANA, které jsou hostované ve virtuálních počítačích Azure. Tento článek popisuje několik scénářů, které můžete implementovat s využitím infrastruktury Azure a zvyšují dostupnost SAP HANA v Azure. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte zkušenosti s infrastruktury jako služby (IaaS) základy v Azure, včetně: 

- Jak nasadit virtuální počítače nebo virtuální sítě pomocí webu Azure portal nebo Powershellu.
- Pomocí – multiplatformního rozhraní příkazového řádku Azure (Azure CLI), včetně možnosti používat šablony JavaScript Object Notation (JSON).

Tento článek také předpokládá, že máte zkušenosti s instalaci instance systému SAP HANA a správě a provozování instance systému SAP HANA. Je obzvláště důležité se seznámit s operací systémové replikace HANA a nastavení. To zahrnuje úlohy, jako je zálohování a obnovení pro databáze SAP HANA.

Tyto články poskytují dobrý přehled o využití SAP HANA v Azure:

- [Ruční instalace jedné instance SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Nastavení systémové replikace SAP HANA ve virtuálních počítačích Azure](sap-hana-high-availability.md)
- [Zálohování SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Je také vhodné se seznámit s tyto články o SAP HANA:

- [Vysoká dostupnost pro SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Nejčastější dotazy: Vysoká dostupnost pro SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Provést systémové replikace SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 aktualizace Service PACKU 01 co vaší nové: vysoká dostupnost](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Doporučení pro síť pro systémové replikace SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA služba automatické restartování](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurace replikace systému SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Nad rámec zkušenosti s nasazením virtuálních počítačů v Azure, se před definováním dostupnost architektury v Azure, doporučujeme, abyste si přečetli [Správa dostupnosti virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Smlouvy o úrovni služeb pro komponenty Azure

Azure má různé dostupnosti smlouvy SLA pro různé součásti, jako jsou sítě, úložiště a virtuální počítače. Všechny smlouvy SLA, jsou popsány. Další informace najdete v tématu [smlouvy o úrovni služeb Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

[Smlouva SLA pro Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) popisuje dva různé smlouvy SLA pro dva různé konfigurace:

- Jeden virtuální počítač, který používá [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) pro disk s operačním systémem a všechny datové disky. Tato možnost poskytuje měsíční dostupnost 99,9 %.
- Více (alespoň dva) virtuální počítače, které jsou uspořádány do [dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tato možnost poskytuje měsíční dostupnost 99,95 %.

Váš požadavek na dostupnost proti smlouvy o úrovni služeb, které komponenty Azure můžete zadat míru. Zvolte váš scénáře pro SAP HANA po dosažení požadované úrovně dostupnosti.

## <a name="next-steps"></a>Další postup

- Další informace o [dostupnost SAP HANA v rámci jedné oblasti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Další informace o [dostupnost SAP HANA v různých oblastech Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


