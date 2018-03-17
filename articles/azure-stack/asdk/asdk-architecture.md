---
title: "Azure zásobníku Development Kit architektura | Microsoft Docs"
description: "Popisuje architekturu Azure zásobníku Development Kit (ASDK)."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architektura Microsoft Azure zásobníku Development Kit
Azure zásobníku Development Kit (ASDK) je jedním uzlem nasazení Azure zásobníku. Všechny součásti jsou nainstalovány v virtuální počítače běžící na jeden hostitelský počítač. 

## <a name="logical-architecture-diagram"></a>Logická architektura diagram
Následující diagram znázorňuje Logická architektura ASDK a jeho součástí.

![Architektura ASDK](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Role virtuálních počítačů
ASDK nabízí služby pomocí následující virtuální počítače hostované na hostitelském počítači development kit:

| Název | Popis |
| ----- | ----- |
| **AzS-ACS01** | Služba Azure zásobníku úložiště.|
| **AzS-ADFS01** | Služba Active Directory Federation Services (ADFS).  |
| **AzS-BGPNAT01** | Okraj směrovače a poskytuje možnosti NAT a VPN Azure zásobníku. |
| **AzS-CA01** | Certifikační autorita služby pro služby role Azure zásobníku.|
| **AzS-DC01** | Službě Active Directory, DNS a DHCP services pro Microsoft Azure zásobníku.|
| **AzS-ERCS01** | Konzola nouzový obnovení virtuálního počítače. |
| **AzS-GWY01** | Hraniční brána službami, jako je připojení site-to-site VPN pro sítě klienta.|
| **AzS-NC01** | Síťový adaptér, který spravuje zásobník Azure síťové služby.  |
| **AzS-SLB01** | Vyrovnávání multiplexor služby v Azure zásobníku pro klienty a služby infrastruktury Azure zásobníku zatížení.  |
| **AzS-SQL01** | Vnitřní datové úložiště pro role infrastruktury Azure zásobníku.  |
| **AzS-WAS01** | Portál pro správu Azure zásobníku a služby Správce prostředků Azure.|
| **AzS-WASP01**| Portál Azure zásobník uživatele (klientů) a služby Správce prostředků Azure.|
| **AzS-XRP01** | Řadič pro správu infrastruktury pro Microsoft Azure zásobníku, včetně poskytovateli prostředků výpočty, síť a úložiště.|


## <a name="next-steps"></a>Další postup
[Další informace o úlohách správy základní ASDK](asdk-admin-basics.md)
