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
ms.date: 05/08/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 819d8a9580de435536bf184b10ffbc23c32b247b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Hostované operační systémy podporované v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

## <a name="windows"></a>Windows

Azure zásobníku podporuje Windows hostované operační systémy uvedené v následující tabulce:

| Operační systém | Popis | K dispozici v Marketplace. |
| --- | --- | --- | --- | --- | --- |
| Windows Server, verze. 1709 | 64 bitů | Základní s kontejnery |
| Windows Server 2016 | 64 bitů |  Datacenter, Datacenter základní datového centra s kontejnery |
| Windows Server 2012 R2 | 64 bitů |  Datové centrum |
| Windows Server 2012 | 64 bitů |  Datové centrum |
| Windows Server 2008 R2 SP1 | 64 bitů |  Datové centrum |
| Windows Server 2008 SP2 | 64 bitů |  Přineste vlastní image |
| Windows 10 *(viz poznámka 1)* | 64-bit, Pro a Enterprise | Přineste vlastní image |

***Poznámka 1:*** *k nasazení Windows 10 klientské operační systémy v zásobníku Azure, musí mít [Windows licence vázané na uživatele](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) nebo nakupovat prostřednictvím kvalifikovaný hostitele víceklientské ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*

Marketplace obrázky nejsou k dispozici pro platím jako--používání nebo licencování BYOL (EA nebo programu SPLA). Použití i na jednu instanci Azure zásobníku není podporováno. Během nasazení vloží Azure zásobníku vhodná verze agenta hosta do bitové kopie.

 Edice Datacenter jsou k dispozici na webu Marketplace pro stahování; Zákazníci můžete zahrnout vlastní Image serveru včetně jiné edice. Bitových kopií systému Windows klienta nejsou k dispozici na webu Marketplace.

## <a name="linux"></a>Linux

Linuxových distribucích uvedené jako dostupné na webu Marketplace zahrnují nezbytné Windows Azure Linux Agent (WALA). Pokud jste přineste vlastní image do protokolů Azure, postupujte podle pokynů v [Linux přidat Image do zásobníku Azure](azure-stack-linux.md).

> [!NOTE]
> Vlastní Image by měly být vytvořeny s nejnovější verzí WALA veřejné. Verze starší než 2.2.18 nemusí fungovat správně v Azure zásobníku.
>
> [init cloudu](https://cloud-init.io/) v tuto chvíli není podporován v zásobníku Azure.

| Distribuce | Popis | Vydavatel | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Na základě centOS 6.9 | 64 bitů | Podvodný Wave | Ano |
| Na základě centOS 7.4 | 64 bitů | Podvodný Wave | Ano |
| ClearLinux | 64 bitů | ClearLinux.org | Ano |
| Kontejner Linux |  64 bitů | CoreOS | Stable |
| Debian 8 "Klára" | 64 bitů | credativ |  Ano |
| Debian 9 "Stretch" | 64 bitů | credativ | Ano |
| Red Hat Enterprise Linux 7.x | 64 bitů | Red Hat |Přineste vlastní image |
| SLES 11SP4 | 64 bitů | SUSE | Ano |
| SLES 12SP3 | 64 bitů | SUSE | Ano |
| Ubuntu 14.04-LTS | 64 bitů | Canonical | Ano |
| Ubuntu 16.04-LTS | 64 bitů | Canonical | Ano |
| Ubuntu 18.04-LTS | 64 bitů | Canonical | Ano |

Další Linuxových distribucích může být podporovaná v budoucnu.
