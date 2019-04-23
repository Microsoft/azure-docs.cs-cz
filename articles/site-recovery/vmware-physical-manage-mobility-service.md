---
title: Správa agenta Mobility na servery pro zotavení po havárii virtuálních počítačů VMware a fyzických serverech pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Správa agenta služby Mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 69b8e1c533747d1bade69949911ea43f299f49e9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794231"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Správa agenta mobility na chráněné počítače

Můžete nastavit agenta mobility na serveru při použití Azure Site Recovery pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. Mobility agent koordinuje komunikaci mezi chráněného počítače, konfigurační server/horizontální navýšení kapacity procesového serveru a spravuje replikaci dat. Tento článek shrnuje běžné úlohy správy agenta mobility po nasazení.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Aktualizace služby mobility z webu Azure portal

1. Před zahájením zkontrolujte, že konfigurační server, horizontální navýšení kapacity procesových serverů a všechny hlavní cílové servery, které jsou součástí vašeho nasazení jsou aktualizované před aktualizací služby Mobility na chráněné počítače.
2. Na portálu otevřete trezor > **replikované položky**.
3. Pokud je konfigurační server na nejnovější verzi, se zobrazí oznámení, který čte "je k dispozici nová aktualizace agenta replikace Site recovery. Klikněte na tlačítko nainstalovat."

     ![Okno replikované položky](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Klikněte na oznámení a v **aktualizace agenta**, vyberte počítače, na kterých chcete provést upgrade služby Mobility. Pak klikněte na **OK**.

     ![Replikované položky seznamu virtuálních počítačů](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Úloha aktualizace služby Mobility se spustí pro všechny vybrané počítače.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Aktualizace služby Mobility pomocí skriptu prostředí powershell na Windows serveru

Pomocí následujícího skriptu pro upgrade služby mobility na serveru pomocí rutiny power shell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Aktualizovat účet použitý pro nabízenou instalaci služby Mobility

Při nasazení Site Recovery, aby nabízená instalace služby Mobility, zadat účet, který použije procesový server Site Recovery pro přístup k počítačům a instalaci služby po povolení replikace pro počítač. Pokud chcete aktualizovat přihlašovací údaje pro tento účet, postupujte podle [tyto pokyny](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Odinstalujte službu Mobility

### <a name="on-a-windows-machine"></a>Na počítači s Windows

Odinstalujte z uživatelského rozhraní nebo z příkazového řádku.

- **V uživatelském rozhraní**: V ovládacím panelu počítače vyberte **programy**. Vyberte **Microsoft Azure Site Recovery Mobility Service/hlavní cílový server** > **odinstalovat**.
- **Z příkazového řádku**: Otevřete okno příkazového řádku jako správce na počítači. Spusťte následující příkaz: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Na počítači s Linuxem
1. Na počítači s Linuxem, přihlaste se jako **kořenové** uživatele.
2. V terminálu přejděte do /user/local/ASR.
3. Spusťte následující příkaz:
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
