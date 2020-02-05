---
title: Řešení Azure VMware (AVS) – migrace virtuálních počítačů úloh do služby AVS Private Cloud
description: Popisuje, jak migrovat virtuální počítače z místního serveru vCenter do služby AVS Private Cloud vCenter.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: af02bd947c73b670306704a10a09ca981b34c9a9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019991"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-avs-private-cloud-vcenter-environment"></a>Migrace virtuálních počítačů úloh z místního serveru vCenter do prostředí služby AVS Private Cloud vCenter

K migraci virtuálních počítačů z místního datacentra do privátního cloudu služby AVS je k dispozici několik možností. Privátní cloud služby AVS poskytuje nativní přístup k VMware vCenter a nástroje, které VMware podporuje, se dají použít k migraci úloh. Tento článek popisuje některé možnosti migrace vCenter.

## <a name="prerequisites"></a>Požadavky

Migrace virtuálních počítačů a dat z místního datacentra vyžaduje připojení k síti z datového centra k prostředí vašeho privátního cloudu služby AVS. K navázání připojení k síti použijte některou z následujících metod:

* [Připojení VPN typu Site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) mezi místním prostředím a Vaším privátním cloudem služby AVS.
* ExpressRoute Global Reach propojení mezi místním okruhem ExpressRoute a okruhem služby AVS ExpressRoute.

Síťová cesta z místního prostředí vCenter do privátního cloudu služby AVS musí být dostupná pro migraci virtuálních počítačů pomocí vMotion. VMotion síť na místním serveru vCenter musí mít možnosti směrování. Ověřte, že brána firewall povoluje veškerý vMotion provoz mezi místním serverem vCenter a privátním cloudem vCenter. (V privátním cloudu služby AVS je směrování v síti vMotion ve výchozím nastavení nakonfigurované.)

## <a name="migrate-isos-and-templates"></a>Migrace soubory ISO a šablon

Pokud chcete vytvořit nové virtuální počítače v privátním cloudu služby AVS, použijte soubory ISO a šablony virtuálních počítačů. Pokud chcete nahrát soubory ISO a šablony do svého privátního cloudu služby AVS a zpřístupnit je, použijte následující metodu.

1. Nahrajte soubor ISO do privátního cloudu služby AVS z rozhraní vCenter.
2. [Publikujte knihovnu obsahu](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) na serveru služby AVS Private Cloud vCenter:

    1. Publikujte místní knihovnu obsahu.
    2. Vytvořte novou knihovnu obsahu ve službě AVS privátní cloud vCenter.
    3. Přihlaste se k odběru publikované místní knihovny obsahu.
    4. Synchronizuje knihovnu obsahu pro přístup k předplacenému obsahu.

## <a name="migrate-vms-using-powercli"></a>Migrace virtuálních počítačů pomocí PowerCLI

Pokud chcete migrovat virtuální počítače z místního serveru vCenter do služby AVS (Private Cloud vCenter), použijte VMware PowerCLI nebo nástroj pro migraci úloh pro víc vCenter, který je dostupný na platformě VMware Labs. Následující vzorový skript ukazuje příkazy pro migraci PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Pokud chcete použít názvy cílového serveru vCenter a hostitele ESXi, nakonfigurujte přesměrování DNS z místního prostředí do privátního cloudu služby AVS.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrace virtuálních počítačů pomocí sítě VPN vrstvy 2 NSX

Tato možnost umožňuje migrovat úlohy z místního prostředí VMware do privátního cloudu služby AVS v Azure. V této roztažené síti vrstvy 2 bude v privátním cloudu služby AVS k dispozici podsíť z místního prostředí. Po migraci se pro virtuální počítače nevyžaduje přiřazení nové IP adresy.

[Migrace úloh pomocí roztažené sítě vrstvy 2](migration-layer-2-vpn.md) popisuje použití sítě VPN vrstvy 2 k roztažení sítě vrstvy 2 z místního prostředí do privátního cloudu služby AVS.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrace virtuálních počítačů pomocí nástrojů pro zálohování a zotavení po havárii

Migraci virtuálních počítačů do privátního cloudu se systémem AVS můžete provést pomocí nástrojů pro zálohování a obnovení a nástroje pro zotavení po havárii. Použijte privátní cloud služby AVS jako cíl pro obnovení ze záloh vytvořených pomocí nástroje třetí strany. Privátní cloud služby AVS se dá také použít jako cíl pro zotavení po havárii pomocí VMware SRM nebo nástroje třetí strany.

Další informace o těchto nástrojích najdete v následujících tématech:

* [Zálohování virtuálních počítačů s úlohami v privátním cloudu se systémem AVS pomocí Veeam B & R](backup-workloads-veeam.md)
* [Nastavení privátního cloudu pro funkci AVS jako lokality pro zotavení po havárii pro místní úlohy VMware](disaster-recovery-zerto.md)
