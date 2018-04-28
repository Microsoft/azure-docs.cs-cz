---
title: Podporované hostované operační systémy pro Azure zásobníku | Microsoft Docs
description: V zásobníku Azure lze tyto hostované operační systémy.
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: ff3aea4e449e3d489b0c0f01345ecd9773c7d885
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Hostované operační systémy podporované v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

## <a name="windows"></a>Windows
Azure zásobníku podporuje hostované operační systémy Windows, které jsou uvedeny v následující tabulce: obrázky v Marketplace nejsou k dispozici ke stažení pro Azure zásobník. Bitových kopií systému Windows klienta nejsou k dispozici na webu Marketplace.

Během nasazení vloží Azure zásobníku vhodná verze agenta hosta do bitové kopie.

| Operační systém | Popis | K dispozici v Marketplace. |
| --- | --- | --- | --- | --- | --- |
| Windows Server, verze. 1709 | 64 bitů | Základní s kontejnery |
| Windows Server 2016 | 64 bitů |  Datacenter, Datacenter základní datového centra s kontejnery |
| Windows Server 2012 R2 | 64 bitů |  Datové centrum |
| Windows Server 2012 | 64 bitů |  Datové centrum |
| Windows Server 2008 R2 SP1 | 64 bitů |  Datové centrum |
| Windows Server 2008 SP2 | 64 bitů |  Přineste vlastní image |
| Windows 10 *(viz poznámka 1)* | 64-bit, Pro a Enterprise | Přineste vlastní image |

***Poznámka 1:****k nasazení Windows 10 klientské operační systémy v zásobníku Azure, musí mít [Windows licence vázané na uživatele](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) nebo nakupovat prostřednictvím kvalifikovaný hostitele víceklientské ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*

Marketplace obrázky nejsou k dispozici pro platím jako--používání nebo licencování BYOL (EA nebo programu SPLA). Použití i na jednu instanci Azure zásobníku není podporováno. 

Pouze edice Datacenter jsou k dispozici na webu Marketplace; Zákazníci můžete zahrnout vlastní Image serveru včetně jiné edice.

## <a name="linux"></a>Linux

Linuxových distribucích tady zahrnují nezbytné Windows Azure Linux Agent (WALA).

> [!NOTE]   
> Vlastní Image by měly být vytvořeny s nejnovější verzí WALA veřejné. Verze starší než 2.2.18 nemusí fungovat správně v Azure zásobníku.  
>
> [init cloudu](https://cloud-init.io/) v tuto chvíli není podporován v zásobníku Azure.

| Distribuce | Popis | Vydavatel | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Kontejner Linux |  64 bitů | CoreOS | Stable |
| Na základě centOS 6.9 | 64 bitů | Podvodný Wave | Ano |
| Na základě centOS 7.4 | 64 bitů | Podvodný Wave | Ano |
| ClearLinux | 64 bitů | ClearLinux.org | Ano |
| Debian 8 "Klára" | 64 bitů | credativ |  Ano |
| Debian 9 "Stretch" | 64 bitů | credativ | Ano |
| Red Hat Enterprise Linux 7.x (čekání na schválení) | 64 bitů | Red Hat |Přineste vlastní image |
| SLES 11SP4 | 64 bitů | SUSE | Ano |
| SLES 12SP3 | 64 bitů | SUSE | Ano |
| Ubuntu 14.04-LTS | 64 bitů | Canonical | Ano |
| Ubuntu 16.04-LTS | 64 bitů | Canonical | Ano |

Další Linuxových distribucích může být podporovaná v budoucnu.
