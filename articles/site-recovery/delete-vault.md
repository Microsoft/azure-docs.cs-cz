---
title: Odstranění trezoru obnovení webu Azure
description: Přečtěte si, jak odstranit trezor služby Recovery Services nakonfigurovaný pro Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894983"
---
# <a name="delete-a-site-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Tento článek popisuje, jak odstranit trezor služby Recovery Services pro obnovení webu. Pokud chcete odstranit trezor použitý v Azure Backup, přečtěte si [tématu Odstranění trezoru zálohování v Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Než začnete

Před odstraněním úschovny je nutné odebrat registrované servery a položky v úschovně. Co je potřeba odebrat, závisí na scénářích replikace, které jste nasadili. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Odstranění virtuálního počítače trezoru a Azure do Azure

1. Podle [těchto pokynů](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) odstraňte všechny chráněné virtuální počítače.
2. Potom odstraňte úschovnu.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Odstranění virtuálního počítače v trezoru-VMware do Azure

1. Podle [těchto pokynů](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) odstraňte všechny chráněné virtuální počítače.
2. Chcete-li odstranit všechny zásady replikace, odstraňte následující [kroky.](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)
3. Pomocí [těchto kroků](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)odstraňte odkazy na vCenter .
4. Podle [těchto pokynů](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) vyřaďte konfigurační server z provozu.
5. Potom odstraňte úschovnu.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Odstranění virtuálního počítače hyper-V vaultu (s VMM) do Azure

1. Odstraněním virtuálních montovek Hyper-V spravovaných systémem VMM postupujte [takto.](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)
2. Zrušte přidružení a odstraňte všechny zásady replikace. Udělejte to v trezoru > **infrastruktury** > obnovení webu pro**zásady replikace****v systémového centra VMM** > .
3. Chcete-li zrušit registraci připojeného serveru VMM, [postupujte takto.](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)
4. Potom odstraňte úschovnu.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Odstranění virtuálního počítače hyper-V trezoru do Azure

1. Podle [těchto kroků](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) odstraňte všechny chráněné virtuální počítače.
2. Zrušte přidružení a odstraňte všechny zásady replikace. Udělejte to v trezoru > **infrastruktury** > obnovení webu pro > **zásady replikace****webů s technologiemi Hyper-V**.
3. Podle [těchto pokynů](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) odregistrute hostitele Hyper-V.
4. Odstraňte web Hyper-V.
5. Potom odstraňte úschovnu.


## <a name="use-powershell-to-force-delete-the-vault"></a>Vynucení odstranění trezoru pomocí PowerShellu 

> [!Important]
> Pokud testujete produkt a neobáváte se ztráty dat, použijte metodu force delete k rychlému odebrání trezoru a všech jeho závislostí.
> Příkaz Prostředí PowerShell odstraní veškerý obsah úložiště a **není reverzibilní**.

Chcete-li odstranit trezor Site Recovery i v případě, že existují chráněné položky, použijte tyto příkazy:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Další informace o [službě Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)a [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
