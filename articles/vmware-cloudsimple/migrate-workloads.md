---
title: Řešení Azure VMware od CloudSimple – migrace virtuálních počítačích úloh do privátního cloudu
description: Popisuje, jak migrovat virtuální počítače z místního virtuálního centra do cloudového privátního cloudu vCenter.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019991"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Migrace virtuálních počítačích s pracovními vytížením z místního virtuálního centra do prostředí private cloudvcenter

Chcete-li migrovat virtuální počítače z místního datového centra do vašeho cloudu CloudSimple Private Cloud, je k dispozici několik možností.  Privátní cloud poskytuje nativní přístup k vMware vCenter a nástroje podporované vMware lze použít pro migraci úloh. Tento článek popisuje některé možnosti migrace vCenter.

## <a name="prerequisites"></a>Požadavky

Migrace virtuálních počítačů a dat z místního datového centra vyžaduje připojení k síti z datového centra do prostředí privátního cloudu.  K vytvoření připojení k síti použijte některou z následujících metod:

* [Připojení VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) mezi místním prostředím a privátním cloudem.
* Spojení ExpressRoute Global Reach mezi místním okruhem ExpressRoute a okruhem CloudSimple ExpressRoute.

Síťová cesta z místního prostředí vCenter do privátního cloudu musí být dostupná pro migraci virtuálních počítačů pomocí vMotion.  VMotion síť na vašem místním vCenter musí mít schopnosti směrování.  Ověřte, zda brána firewall umožňuje veškerý provoz vMotion mezi místním vCenterem a privátním cloudem vCenter. (V privátním cloudu je směrování v síti vMotion ve výchozím nastavení nakonfigurováno.)

## <a name="migrate-isos-and-templates"></a>Migrace ISO a šablon

Chcete-li vytvořit nové virtuální počítače v privátním cloudu, použijte iso a šablony virtuálních počítačů.  Chcete-li nahrát ISO a šablony do vcenter priviamu cloudu a zpřístupnit je, použijte následující metodu.

1. Nahrajte ISO do privátního cloudu vCenter z ui vCenter.
2. [Publikování knihovny obsahu](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) v privátním cloudu vCenter:

    1. Publikujte místní knihovnu obsahu.
    2. Vytvořte novou knihovnu obsahu v privátním cloudu vCenter.
    3. Přihlaste se k odběru publikované místní knihovny obsahu.
    4. Synchronizujte knihovnu obsahu pro přístup k odebíranému obsahu.

## <a name="migrate-vms-using-powercli"></a>Migrace virtuálních proudů pomocí powercli

Chcete-li migrovat virtuální počítače z místního virtuálního centra do privátního cloudu vCenter, použijte nástroj VMware PowerCLI nebo Nástroj pro migraci úloh vCenter, který je dostupný v laboratořích VMware.  Následující ukázkový skript zobrazuje příkazy migrace PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Chcete-li používat názvy cílových serverů vCenter a hostitelů ESXi, nakonfigurujte předávání DNS z místního do privátního cloudu.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrace virtuálních montovin pomocí sítě VPN vrstvy NSX 2

Tato možnost umožňuje migraci úloh za provozu z místního prostředí VMware do privátního cloudu v Azure.  S touto roztaženou sítí vrstvy 2 bude podsíť z místní sítě dostupná v privátním cloudu.  Po migraci není pro virtuální počítačů vyžadováno přiřazení nové IP adresy.

[Migrace úloh pomocí roztažených sítí vrstvy 2](migration-layer-2-vpn.md) popisuje, jak pomocí sítě VPN vrstvy 2 roztáhnout síť vrstvy 2 z místního prostředí do privátního cloudu.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrace virtuálních počítače pomocí nástrojů pro zálohování a zotavení po havárii

Migraci virtuálních počítače do privátního cloudu lze provést pomocí nástrojů pro zálohování a obnovení a nástrojů pro zotavení po havárii.  Použijte privátní cloud jako cíl pro obnovení ze záloh, které jsou vytvořeny pomocí nástroje jiného výrobce.  Privátní cloud lze také použít jako cíl pro zotavení po havárii pomocí VMware SRM nebo nástroje třetí strany.

Další informace pomocí těchto nástrojů naleznete v následujících tématech:

* [Zálohování virtuálních počítačů s úlohami v cloudu CloudSimple Private Cloud pomocí Veeam B&R](backup-workloads-veeam.md)
* [Nastavení cloudového privátního cloudu jako lokality pro zotavení po havárii pro místní úlohy VMware](disaster-recovery-zerto.md)
