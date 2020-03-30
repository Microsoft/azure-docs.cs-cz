---
title: Řešení Azure VMware podle clouduSimple – nastavení úlohdns a DHCP pro privátní cloud
description: Popisuje, jak nastavit služby DNS a DHCP pro aplikace a úlohy spuštěné v prostředí CloudSimple Private Cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024683"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Nastavení aplikací a úloh DNS a DHCP v privátním cloudu CloudSimple

Aplikace a úlohy spuštěné v prostředí privátního cloudu vyžadují překlad názvů a služby DHCP pro vyhledávání a přiřazení IP adres.  K poskytování těchto služeb je vyžadována správná infrastruktura DHCP a DNS.  Můžete nakonfigurovat virtuální počítač pro poskytování těchto služeb v prostředí privátního cloudu.  

## <a name="prerequisites"></a>Požadavky

* Distribuovaná skupina portů s nakonfigurovanou sítí VLAN
* Směrování nastavení na místní nebo internetové servery DNS
* Šablona virtuálního počítače nebo ISO pro vytvoření virtuálního počítače

## <a name="linux-based-dns-server-setup"></a>Nastavení serveru DNS založeného na Linuxu

Linux nabízí různé balíčky pro nastavení DNS serverů.  Zde je [příklad nastavení od DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) s pokyny pro nastavení open-source BIND DNS server.

## <a name="windows-based-setup"></a>Instalace systému Windows

Tato témata společnosti Microsoft popisují, jak nastavit server se systémem Windows jako server DNS a jako server DHCP.

* [Windows Server jako DNS Server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Systém Windows Server jako server DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
