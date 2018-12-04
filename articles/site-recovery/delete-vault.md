---
title: Odstranění trezoru služby Recovery Services nastavený pro tuto službu Azure Site Recovery
description: Zjistěte, jak odstranit trezor služby Recovery Services, který je nakonfigurovaný pro Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 7dc1be1316d833d4a37a8b46bf0145089bb3ae22
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839115"
---
# <a name="delete-a-site-recovery-services-vault"></a>Odstranění trezoru služby Site Recovery

Odstranění trezoru služby Azure Site Recovery můžete zabránit závislosti. Akce, které je potřeba se liší v závislosti na scénáři Site Recovery. Chcete-li odstranit trezor použít ve službě Azure Backup, přečtěte si téma [odstranění trezoru služby Backup v Azure](../backup/backup-azure-delete-vault.md).



## <a name="delete-a-site-recovery-vault"></a>Odstranit trezor Site Recovery 
Pokud chcete trezor odstranit, postupujte podle doporučené kroky pro váš scénář.

### <a name="vmware-vms-to-azure"></a>Virtuální počítače VMware do Azure

1. Odstraňte všechny chráněné virtuální počítače pomocí následujících kroků v [zakažte ochranu pro VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Odstranit všechny zásady replikace pomocí následujících kroků v [odstranění zásady replikace](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Odstranit odkazy na vCenter pomocí následujících kroků v [odstranění serveru vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Odstranění konfiguračního serveru pomocí následujících kroků v [vyřadit z provozu konfigurační server](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Odstranění trezoru.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Virtuální počítače Hyper-V (s VMM) do Azure
1. Odstraňte všechny chráněné virtuální počítače pomocí následujících kroků v[zakázat ochranu virtuálního počítače s technologií Hyper-V (s VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Zrušit přidružení a odstranit tak, že přejdete do svého trezoru všechny zásady replikace -> **infrastruktura Site Recovery** -> **pro System Center VMM** -> **replikace Zásady**

3.  Odstranit odkazy na servery VMM pomocí následujících kroků v [zrušit registraci serveru VMM připojené](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Odstranění trezoru.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Virtuální počítače Hyper-V (bez Virtual Machine Manager) do Azure
1. Odstraňte všechny chráněné virtuální počítače pomocí následujících kroků v [zakázat ochranu virtuálního počítače Hyper-V (Hyper-V do Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Zrušit přidružení a odstranit tak, že přejdete do svého trezoru všechny zásady replikace -> **infrastruktura Site Recovery** -> **pro servery Hyper-V** -> **zásady replikace**

3. Odstranit odkazy na servery Hyper-V pomocí následujících kroků v [zrušit registraci hostitele Hyper-V](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Odstraníte lokalitu Hyper-V.

5. Odstranění trezoru.


## <a name="use-powershell-to-force-delete-the-vault"></a>Pomocí Powershellu Vynutit odstranění trezoru 

> [!Important]
> Pokud testujete produktu a nejsou obavy o ztrátu dat, můžete metodu delete platnost rychle odebere trezor a všechny jeho závislosti.
> Příkaz prostředí PowerShell odstraní veškerý obsah trezoru a je **není reverzibilního**.

Chcete-li odstranit trezor Site Recovery, i když nejsou chráněné položky, použijte tyto příkazy:

    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmRecoveryServicesVault -Name "vaultname"

    Remove-AzureRmRecoveryServicesVault -Vault $vault

Další informace o [Get-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault?view=azurermps-6.0.0), a [Remove-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/remove-azurermrecoveryservicesvault?view=azurermps-6.0.0).
