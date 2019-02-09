---
title: Azure Stack Development Kit architektura | Dokumentace Microsoftu
description: Popisuje architekturu Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9157acc7517aea56f087a3dbff0fe7114f8b4c87
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958801"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architektura Microsoft Azure Stack Development Kit
Azure Stack Development Kit (ASDK) je jedním uzlem nasazení služby Azure Stack na jednom hostiteli počítači. Směrování compontents hrany jsou nainstalovány na hostitelském počítači a poskytují schopnosti NAT a síť VPN pro službu Azure Stack. Role služby infrastruktury Azure Stack spustit ve vrstvě Hyper-V fyzickém hostitelském počítači.


## <a name="virtual-machine-roles"></a>Role virtuálních počítačů
ASDK nabízí služby pomocí následující virtuální počítače hostované na hostitelském počítači development kit:

| Název | Popis |
| ----- | ----- |
| **AzS-ACS01** | Úložiště služby Azure Stack.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
| **AzS-CA01** | Certifikační autority služby role služby Azure Stack.|
| **AzS-DC01** | Active Directory, DNS a DHCP services pro Microsoft Azure Stack.|
| **AzS-ERCS01** | Konzola pro nouzový zotavení virtuálního počítače. |
| **AzS-GWY01** | Hraniční brána službami, jako je připojení site-to-site VPN sítí klientů.|
| **AzS-NC01** | Síťový adaptér, který spravuje síťové služby Azure Stack.  |
| **AzS-SLB01** | Vyrovnávání zatížení multiplexor služby ve službě Azure Stack pro klienty a služby infrastruktury Azure stacku.  |
| **AzS-SQL01** | Vnitřní datové úložiště pro infrastrukturu rolích Azure stacku.  |
| **AzS-WAS01** | Služby Azure Resource Manageru a portálu pro správu Azure Stack.|
| **AzS-WASP01**| Služby Azure Resource Manageru a portálu Azure Stack uživatele (tenant).|
| **AzS-XRP01** | Řadič pro správu infrastruktury pro Microsoft Azure Stack, včetně poskytovatelé prostředků Compute, Network a Storage.|


## <a name="next-steps"></a>Další postup
[Další informace o základní úlohy správy ASDK](asdk-admin-basics.md)
