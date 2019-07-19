---
title: Odstranění trezoru Recovery Services nakonfigurovaného pro službu Azure Site Recovery
description: Zjistěte, jak odstranit trezor Recovery Services nakonfigurovaný pro Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: a13dee2010688b02fd86fb05900826470a7d7a08
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876045"
---
# <a name="delete-a-site-recovery-services-vault"></a>Odstranění trezoru služby Site Recovery Services

Závislosti můžou zabránit odstranění trezoru Azure Site Recovery. Akce, které je třeba provést, se liší v závislosti na scénáři Site Recovery. Postup odstranění trezoru používaného v Azure Backup najdete v tématu [odstranění trezoru záloh v Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-a-site-recovery-vault"></a>Odstranění trezoru Site Recovery 
Pokud chcete trezor odstranit, postupujte podle doporučených kroků pro váš scénář.
### <a name="azure-vms-to-azure"></a>Virtuální počítače Azure do Azure

1. Pomocí postupu v části [zakázání ochrany pro VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure)odstraňte všechny chráněné virtuální počítače.
2. Odstraňte trezor.

### <a name="vmware-vms-to-azure"></a>Virtuální počítače VMware do Azure

1. Pomocí postupu v části [zakázání ochrany pro VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)odstraňte všechny chráněné virtuální počítače.

2. Odstraňte všechny zásady replikace podle kroků v části [odstranění zásad replikace](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Pomocí postupu v části [odstranění serveru vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)odstraňte odkazy na vCenter.

4. Odstraňte konfigurační server podle kroků v části vyřazení [konfiguračního serveru z provozu](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Odstraňte trezor.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Virtuální počítače Hyper-V (s VMM) do Azure
1. Pomocí postupu v části[zakázání ochrany pro virtuální počítač Hyper-V (s VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)odstraňte všechny chráněné virtuální počítače.

2. Zrušení přidružení & odstranění všech zásad replikace, a to tak, že přejdete do trezoru – > **Site Recovery infrastruktury** – >**Zásady replikace** **pro System Center VMM** -> 

3.  Pomocí postupu v části [zrušení registrace připojeného serveru VMM](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server)odstraňte odkazy na servery VMM.

4.  Odstraňte trezor.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Virtuální počítače Hyper-V (bez Virtual Machine Manager) do Azure
1. Pomocí postupu v části [zakázání ochrany pro virtuální počítač Hyper-v (Hyper-v do Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure)odstraňte všechny chráněné virtuální počítače.

2. Zrušení přidružení & odstranění všech zásad replikace tak, že přejdete do trezoru – > **Site Recovery infrastruktury** – >**Zásady replikace**  ->  **pro lokality Hyper-V**.

3. Pomocí postupu v části [zrušení registrace hostitele Hyper-v](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site)odstraňte odkazy na servery Hyper-v.

4. Odstraňte lokalitu Hyper-V.

5. Odstraňte trezor.


## <a name="use-powershell-to-force-delete-the-vault"></a>Použití PowerShellu k vynucení odstranění trezoru 

> [!Important]
> Pokud testujete produkt a nejste v souvislosti s ztrátou dat, použijte metodu vynutit odstranění pro rychlé odstranění trezoru a všech jeho závislostí.
> Příkaz PowerShellu odstraní celý obsah trezoru a **nebude vratný**.

Pokud chcete odstranit Site Recovery trezor i v případě, že jsou k dispozici chráněné položky, použijte tyto příkazy:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Přečtěte si další informace o [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)a [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
