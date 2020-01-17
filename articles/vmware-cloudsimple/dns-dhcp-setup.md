---
title: Řešení Azure VMware podle CloudSimple – nastavení služby DNS a DHCP pro privátní cloud
description: Popisuje, jak nastavit DNS a DHCP pro aplikace a úlohy běžící v prostředí privátního cloudu CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121858"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Nastavení aplikací a úloh DNS a DHCP v privátním cloudu CloudSimple

Aplikace a úlohy běžící v prostředí privátního cloudu vyžadují překlad IP adres a služby DHCP pro vyhledání a přiřazení IP adresy.  K poskytování těchto služeb se vyžaduje správná infrastruktura DHCP a DNS.  Virtuální počítač můžete nakonfigurovat tak, aby poskytoval tyto služby v prostředí vašeho privátního cloudu.  

## <a name="prerequisites"></a>Požadavky

* Distribuovaná skupina portů s nakonfigurovanou sítí VLAN
* Nastavení směrování na místní nebo internetové servery DNS
* Vytvoření virtuálního počítače pomocí šablony virtuálního počítače nebo ISO

## <a name="linux-based-dns-server-setup"></a>Instalace serveru DNS se systémem Linux

Linux nabízí různé balíčky pro nastavení serverů DNS.  Tady je [Příklad nastavení z DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) s pokyny pro nastavení Open-Source serveru DNS BIND.

## <a name="windows-based-setup"></a>Instalace založená na systému Windows

Tato témata Microsoftu popisují, jak nastavit server Windows jako server DNS a jako server DHCP.

* [Windows Server jako server DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server jako server DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
