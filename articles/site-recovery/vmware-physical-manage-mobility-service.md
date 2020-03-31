---
title: Správa agenta mobility pro vmware/fyzické servery pomocí Azure Site Recovery
description: Spravujte agenta služby Mobility Service pro zotavení po havárii virtuálních počítačích VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256962"
---
# <a name="manage-the-mobility-agent"></a>Správa agenta mobility 

Agenta mobility na serveru nastavíte, když používáte Azure Site Recovery pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. Agent mobility koordinuje komunikaci mezi chráněným počítačem, konfiguračním serverem/horizontálním navýšením kapacity a spravuje replikaci dat. Tento článek shrnuje běžné úkoly pro správu agenta mobility po jeho nasazení.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Aktualizace služby mobility z webu Azure Portal

1. Než začnete, ujistěte se, že konfigurační server, horizontální navýšení kapacity procesu servery a všechny hlavní cílové servery, které jsou součástí vašeho nasazení jsou aktualizovány před aktualizací služby mobility na chráněných počítačích.
2. Na portálu otevřete úschovnu > **položky replikované**.
3. Pokud konfigurační server je nejnovější verze, zobrazí se oznámení s nápisem "Nová aktualizace agenta obnovení sítě je k dispozici. Klepnutím nainstalujete."

     ![Okno Replikované položky](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Klikněte na oznámení a v **aktualizaci agenta**vyberte počítače, na kterých chcete upgradovat službu Mobility. Pak klikněte na **OK**.

     ![Seznam replikovaných položek virtuálních věcí](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Úloha služby aktualizovat mobilitu se spustí pro každý z vybraných počítačů.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Aktualizace služby Mobility prostřednictvím skriptu powershellu na serveru Windows

Než začnete, ujistěte se, že konfigurační server, horizontální navýšení kapacity procesu servery a všechny hlavní cílové servery, které jsou součástí vašeho nasazení jsou aktualizovány před aktualizací služby mobility na chráněných počítačích.

Použití následujícího skriptu k upgradu služby mobility na serveru pomocí rutiny prostředí power shell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Ruční aktualizace služby mobility na každém chráněném serveru

1. Než začnete, ujistěte se, že konfigurační server, horizontální navýšení kapacity procesu servery a všechny hlavní cílové servery, které jsou součástí vašeho nasazení jsou aktualizovány před aktualizací služby mobility na chráněných počítačích.

2. [Vyhledejte instalační program agenta](vmware-physical-mobility-service-overview.md#locate-installer-files) na základě operačního systému serveru.

>[!IMPORTANT]
> Pokud replikujete virtuální počítač Azure IaaS z jedné oblasti Azure do jiné, nepoužívejte tuto metodu. Informace o všech dostupných možnostech naleznete v [našich pokynech.](azure-to-azure-autoupdate.md)

3. Zkopírujte instalační soubor do chráněného počítače a spusťte jej a aktualizujte agenta mobility.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Aktualizovat účet používaný pro nabízenou instalaci služby Mobility

Při nasazení site recovery, chcete-li povolit nabízenou instalaci služby Mobility, jste zadali účet, který server procesu site recovery používá pro přístup k počítačům a instalaci služby, když je pro počítač povolena replikace. Chcete-li aktualizovat pověření pro tento účet, postupujte podle [těchto pokynů](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Odinstalovat službu Mobility

### <a name="on-a-windows-machine"></a>Na počítači se systémem Windows

Odinstalujte z hlavního i nového než v příkazovém řádku.

- **V ovládacím panelu**počítače vyberte možnost **Programy**. Vyberte **možnost Odinstalace služby Obnovení webu Microsoft Azure nebo hlavnícílový server** > **.**
- **Z příkazového řádku**: Otevřete okno příkazového řádku jako správce v počítači. Spusťte následující příkaz: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Na linuxovém počítači
1. Na linuxovém počítači se přihlaste jako **uživatel root.**
2. V terminálu přejděte na /usr/local/ASR.
3. Spusťte následující příkaz:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Instalace zprostředkovatele Obnovení webu VSS ve zdrojovém počítači

Zprostředkovatel VSS azure site recovery je vyžadován ve zdrojovém počítači ke generování bodů konzistence aplikací. Pokud instalace zprostředkovatele neproběhla úspěšně prostřednictvím push instalace, postupujte podle níže uvedených pokynů pro ruční instalaci.

1. Otevřete okno cmd správce.
2. Přejděte do umístění instalace služby mobility. (Např.
3. Spusťte skript InMageVSSProvider_Uninstall.cmd . Služba bude odinstalována, pokud již existuje.
4. Spusťte skript InMageVSSProvider_Install.cmd a nainstalujte zprostředkovatele VSS ručně.

## <a name="next-steps"></a>Další kroky

- [Nastavení zotavení po havárii pro virtuální virtuální měna VMware](vmware-azure-tutorial.md)
- [Nastavení zotavení po havárii pro fyzické servery](physical-azure-disaster-recovery.md)
