---
title: Řešení Azure VMware podle CloudSimple – migrace virtuálních počítačů úloh do privátního cloudu
description: Popisuje, jak migrovat virtuální počítače z místního serveru vCenter do CloudSimple privátního cloudu vCenter.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 851305775d14ed371440434b2fbe9334ec0a21b9
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899316"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Migrace virtuálních počítačů úloh z místního serveru vCenter do prostředí s vCenter v privátním cloudu

K migraci virtuálních počítačů z místního datacentra do privátního cloudu CloudSimple je k dispozici několik možností.  Privátní Cloud poskytuje nativní přístup k VMware vCenter a nástroje, které VMware podporuje, se dají použít k migraci úloh. Tento článek popisuje některé možnosti migrace vCenter.

## <a name="prerequisites"></a>Požadavky

Migrace virtuálních počítačů a dat z místního datacentra vyžaduje připojení k síti z datového centra k prostředí vašeho privátního cloudu.  K navázání připojení k síti použijte některou z následujících metod:

* [Připojení VPN typu Site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) mezi místním prostředím a Vaším privátním cloudem.
* ExpressRoute Global Reach propojení mezi místním okruhem ExpressRoute a okruhem CloudSimple ExpressRoute.

Síťová cesta z místního prostředí vCenter do vašeho privátního cloudu musí být dostupná pro migraci virtuálních počítačů pomocí vMotion.  VMotion síť na místním serveru vCenter musí mít možnosti směrování.  Ověřte, že brána firewall umožňuje veškerý vMotion provoz mezi místními servery vCenter a privátním cloudem vCenter. (V privátním cloudu je směrování v síti vMotion ve výchozím nastavení nakonfigurované.)

## <a name="migrate-isos-and-templates"></a>Migrace soubory ISO a šablon

Pokud chcete vytvořit nové virtuální počítače v privátním cloudu, použijte soubory ISO a šablony virtuálních počítačů.  Pro nahrání soubory ISO a šablon do svého privátního cloudu vCenter a jejich zpřístupnění použijte následující metodu.

1. Nahrajte soubor ISO do privátního cloudu vCenter z uživatelského rozhraní vCenter.
2. [Publikujte knihovnu obsahu](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) v rámci vašeho privátního cloudu vCenter:

    1. Publikujte místní knihovnu obsahu.
    2. Vytvořte novou knihovnu obsahu v rámci serveru vCenter privátního cloudu.
    3. Přihlaste se k odběru publikované místní knihovny obsahu.
    4. Synchronizuje knihovnu obsahu pro přístup k předplacenému obsahu.

## <a name="migrate-vms-using-powercli"></a>Migrace virtuálních počítačů pomocí PowerCLI

K migraci virtuálních počítačů z místního serveru vCenter do privátního cloudu vCenter použijte VMware PowerCLI nebo nástroj pro migraci úloh pro různé systémy vCenter, který je k dispozici v VMware Labs.  Následující vzorový skript ukazuje příkazy pro migraci PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Pokud chcete použít názvy cílového serveru vCenter a hostitele ESXi, nakonfigurujte přesměrování DNS z místního prostředí do privátního cloudu.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrace virtuálních počítačů pomocí sítě VPN vrstvy 2 NSX

Tato možnost umožňuje migrovat úlohy z místního prostředí VMware do privátního cloudu v Azure za provozu.  V této roztažené síti vrstvy 2 bude podsíť z místního prostředí k dispozici v privátním cloudu.  Po migraci se pro virtuální počítače nevyžaduje přiřazení nové IP adresy.

[Migrace úloh pomocí roztažené sítě vrstvy 2](migration-layer-2-vpn.md) popisuje použití sítě VPN vrstvy 2 k roztažení sítě vrstvy 2 z místního prostředí do privátního cloudu.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrace virtuálních počítačů pomocí nástrojů pro zálohování a zotavení po havárii

Migrace virtuálních počítačů do privátního cloudu se dá udělat pomocí nástrojů pro zálohování a obnovení a nástroje pro zotavení po havárii.  Použijte privátní cloud jako cíl pro obnovení ze záloh vytvořených pomocí nástroje třetí strany.  Privátní cloud se taky dá použít jako cíl pro zotavení po havárii pomocí VMware SRM nebo nástroje třetí strany.

Další informace o těchto nástrojích najdete v následujících tématech:

* [Zálohování virtuálních počítačů s úlohami v privátním cloudu CloudSimple pomocí Veeam B&R](backup-workloads-veeam.md)
* [Nastavení privátního cloudu CloudSimple jako lokality pro zotavení po havárii pro místní úlohy VMware](disaster-recovery-zerto.md)
