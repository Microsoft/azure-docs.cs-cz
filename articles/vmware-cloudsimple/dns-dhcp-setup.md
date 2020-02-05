---
title: Řešení Azure VMware (AVS) – nastavení úloh DNS a DHCP pro privátní cloud služby AVS
description: Popisuje, jak nastavit DNS a DHCP pro aplikace a úlohy spuštěné v prostředí privátního cloudu služby AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024683"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>Nastavení aplikací DNS a DHCP a úloh v privátním cloudu služby AVS

Aplikace a úlohy, které běží v prostředí privátního cloudu služby AVS, vyžadují překlad IP adres a služby DHCP pro vyhledání a přiřazení IP adresy. K poskytování těchto služeb se vyžaduje správná infrastruktura DHCP a DNS. Virtuální počítač můžete nakonfigurovat tak, aby poskytoval tyto služby v prostředí privátního cloudu služby AVS. 

## <a name="prerequisites"></a>Požadavky

* Distribuovaná skupina portů s nakonfigurovanou sítí VLAN
* Nastavení směrování na místní nebo internetové servery DNS
* Vytvoření virtuálního počítače pomocí šablony virtuálního počítače nebo ISO

## <a name="linux-based-dns-server-setup"></a>Instalace serveru DNS se systémem Linux

Linux nabízí různé balíčky pro nastavení serverů DNS. Tady je [Příklad nastavení z DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) s pokyny pro nastavení Open-Source serveru DNS BIND.

## <a name="windows-based-setup"></a>Instalace založená na systému Windows

Tato témata Microsoftu popisují, jak nastavit server Windows jako server DNS a jako server DHCP.

* [Windows Server jako server DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server jako server DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
