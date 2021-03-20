---
title: Odstranění trezoru Azure Site Recovery
description: Zjistěte, jak odstranit trezor Recovery Services nakonfigurovaný pro Azure Site Recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: sideeksh
ms.openlocfilehash: a33e04a24013d5450c98b91048fa418958d16886
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89426380"
---
# <a name="delete-a-site-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Tento článek popisuje, jak odstranit Recovery Services trezor pro Site Recovery. Postup odstranění trezoru používaného v Azure Backup najdete v tématu [odstranění trezoru záloh v Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Než začnete

Než budete moct odstranit trezor, musíte odebrat registrované servery a položky v trezoru. To, co je potřeba odebrat, závisí na scénářích replikace, které jste nasadili. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Odstranění trezoru – virtuální počítač Azure do Azure

1. Pomocí [těchto pokynů](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) odstraňte všechny chráněné virtuální počítače.
2. Pak odstraňte trezor.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Odstranění trezoru – virtuální počítač VMware do Azure

1. Pomocí [těchto pokynů](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) odstraňte všechny chráněné virtuální počítače.
2. Pomocí [těchto kroků](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) odstraňte všechny zásady replikace.
3. Pomocí [těchto kroků](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)odstraňte odkazy na vCenter.
4. Při vyřazení konfiguračního serveru z provozu postupujte podle [těchto pokynů](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) .
5. Pak odstraňte trezor.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Odstranění trezoru – virtuální počítač Hyper-V (s VMM) do Azure

1. Pomocí [těchto kroků](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) odstraňte virtuální počítače Hyper-V spravované nástrojem System Center VMM.
2. Zrušte přidružení a odstranění všech zásad replikace. Provedete to ve vašem trezoru > **Site Recovery infrastruktury**  >  **pro zásady replikace nástroje System Center VMM**  >  .
3. Pomocí [těchto kroků](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) zrušíte registraci připojeného serveru VMM.
4. Pak odstraňte trezor.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Odstranění trezoru – virtuální počítač Hyper-V do Azure

1. Pomocí [těchto kroků](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) odstraníte všechny chráněné virtuální počítače.
2. Zrušte přidružení a odstranění všech zásad replikace. Provedete to ve vašem trezoru > **Site Recovery infrastruktury**  >  **pro zásady replikace lokalit Hyper-V**  >  .
3. Pokud chcete zrušit registraci hostitele Hyper-V, postupujte podle [těchto pokynů](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) .
4. Odstraňte lokalitu Hyper-V.
5. Pak odstraňte trezor.


## <a name="use-powershell-to-force-delete-the-vault"></a>Použití PowerShellu k vynucení odstranění trezoru 

> [!Important]
> Pokud testujete produkt a nejste v souvislosti s ztrátou dat, použijte metodu vynutit odstranění pro rychlé odstranění trezoru a všech jeho závislostí.
> Příkaz PowerShellu odstraní celý obsah trezoru a **nebude vratný**.

Pokud chcete odstranit Site Recovery trezor i v případě, že jsou k dispozici chráněné položky, použijte tyto příkazy:

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

Přečtěte si další informace o [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)a [Remove-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
