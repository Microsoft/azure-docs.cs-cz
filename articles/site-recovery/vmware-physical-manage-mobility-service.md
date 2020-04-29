---
title: Správa agenta mobility pro VMware/fyzické servery pomocí Azure Site Recovery
description: Spravujte agenta služby mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79256962"
---
# <a name="manage-the-mobility-agent"></a>Správa agenta mobility 

Na serveru můžete nastavit agenta mobility, když používáte Azure Site Recovery pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. Agent mobility koordinuje komunikaci mezi vaším chráněným počítačem, konfiguračním serverem nebo procesovým serverem se škálováním na více systémů a spravuje replikaci dat. Tento článek shrnuje běžné úlohy pro správu agenta mobility po jeho nasazení.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Aktualizovat službu mobility z Azure Portal

1. Než začnete aktualizovat službu mobility na chráněných počítačích, ujistěte se, že se konfigurační server, procesové servery se škálováním na více instancí a všechny hlavní cílové servery, které jsou součástí vašeho nasazení, aktualizují.
2. Na portálu otevřete trezor > **replikované položky**.
3. Pokud má konfigurační server nejnovější verzi, zobrazí se oznámení, že je k dispozici nová aktualizace agenta replikace služby Site Recovery. Kliknutím nainstalujete.

     ![Okno replikované položky](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Klikněte na oznámení a v části **aktualizace agenta**vyberte počítače, na kterých chcete upgradovat službu mobility. Pak klikněte na **OK**.

     ![Seznam virtuálních počítačů replikovaných položek](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Spustí se úloha aktualizace služby mobility pro všechny vybrané počítače.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Aktualizace služby mobility prostřednictvím skriptu PowerShellu na Windows serveru

Než začnete aktualizovat službu mobility na chráněných počítačích, ujistěte se, že se konfigurační server, procesové servery se škálováním na více instancí a všechny hlavní cílové servery, které jsou součástí vašeho nasazení, aktualizují.

Pomocí následujícího skriptu upgradujte službu mobility na serveru pomocí rutiny Power Shell.

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Aktualizovat službu mobility ručně na každém chráněném serveru

1. Než začnete aktualizovat službu mobility na chráněných počítačích, ujistěte se, že se konfigurační server, procesové servery se škálováním na více instancí a všechny hlavní cílové servery, které jsou součástí vašeho nasazení, aktualizují.

2. [Vyhledejte instalační program agenta](vmware-physical-mobility-service-overview.md#locate-installer-files) na základě operačního systému serveru.

>[!IMPORTANT]
> Pokud provádíte replikaci virtuálního počítače Azure IaaS z jedné oblasti Azure do jiné, nepoužívejte tuto metodu. Informace o všech dostupných možnostech najdete v [našich pokynech](azure-to-azure-autoupdate.md) .

3. Zkopírujte instalační soubor do chráněného počítače a spusťte ho, aby se aktualizoval agent mobility.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Aktualizace účtu používaného pro nabízenou instalaci služby mobility

Když nasadíte Site Recovery a povolíte nabízenou instalaci služby mobility, zadali jste účet, který Site Recovery procesový Server používá pro přístup k počítačům a instalaci služby, pokud je pro tento počítač povolená replikace. Pokud chcete aktualizovat přihlašovací údaje pro tento účet, postupujte podle [těchto pokynů](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Odinstalace služby mobility

### <a name="on-a-windows-machine"></a>Na počítači s Windows

Odinstalujte z uživatelského rozhraní nebo z příkazového řádku.

- **Z uživatelského rozhraní**: v Ovládacích panelech počítače vyberte **programy**. Vyberte Microsoft Azure**odinstalace** **služby mobility Site Recovery nebo hlavního cílového serveru** > .
- **Z příkazového řádku**: Otevřete okno příkazového řádku jako správce v počítači. Spusťte následující příkaz: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>V počítači se systémem Linux
1. Na počítači se systémem Linux se přihlaste jako uživatel **root** .
2. V terminálu přejděte na/usr/local/ASR.
3. Spusťte následující příkaz:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Nainstalovat zprostředkovatele služby VSS Site Recovery na zdrojovém počítači

Pro generování bodů konzistence aplikací je na zdrojovém počítači vyžadován zprostředkovatel služby Stínová kopie svazku Azure Site Recovery. Pokud se instalace zprostředkovatele nezdařila prostřednictvím nabízené instalace, postupujte podle pokynů uvedených níže v těchto pokynech k ruční instalaci.

1. Otevřete okno Správce-cmd.
2. Přejděte do umístění instalace služby mobility. (Např. C:\Program Files (x86) \Microsoft Azure Site Recovery\agent)
3. Spusťte skript InMageVSSProvider_Uninstall. cmd. Tato akce odinstaluje službu, pokud již existuje.
4. Spusťte skript InMageVSSProvider_Install. cmd pro ruční instalaci poskytovatele služby VSS.

## <a name="next-steps"></a>Další kroky

- [Nastavení zotavení po havárii pro virtuální počítače VMware](vmware-azure-tutorial.md)
- [Nastavení zotavení po havárii pro fyzické servery](physical-azure-disaster-recovery.md)
