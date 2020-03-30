---
title: Nastavení zotavení po havárii po migraci do Azure pomocí Azure Site Recovery
description: Tento článek popisuje, jak připravit počítače pro nastavení zotavení po havárii mezi oblastmi Azure po migraci do Azure pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 874c282ff878126297dc46ca0e7a4c19910e40a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74159108"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Nastavení zotavení po havárii virtuálních počítačů Azure po migraci do Azure 


Postupujte podle tohoto článku, pokud jste [migrovali místní počítače do virtuálních počítačů Azure](tutorial-migrate-on-premises-to-azure.md) pomocí služby [Site Recovery](site-recovery-overview.md) a teď chcete získat virtuální počítače nastavené pro zotavení po havárii do sekundární oblasti Azure. Článek popisuje, jak zajistit, aby se agent virtuálního počítače Azure nainstaloval na migrovaných virtuálních počítačích, a jak odebrat službu Mobility site recovery, která už po migraci není potřeba.



## <a name="verify-migration"></a>Ověření migrace

Před nastavením zotavení po havárii se ujistěte, že migrace byla dokončena podle očekávání. Chcete-li úspěšně dokončit migraci, měli byste po převzetí služeb při selhání vybrat možnost **Dokončit migraci** pro každý počítač, který chcete migrovat. 

## <a name="verify-the-azure-vm-agent"></a>Ověření agenta virtuálního počítače Azure

Každý virtuální počítač Azure musí mít nainstalovaného [agenta virtuálního počítače Azure.](../virtual-machines/extensions/agent-windows.md) Chcete-li replikovat virtuální počítače Azure, site recovery nainstaluje rozšíření na agenta.

- Pokud je v počítači verze 9.7.0.0 nebo novější služby Site Recovery Mobility, agent virtuálního počítače Azure se automaticky nainstaluje službou Mobility na virtuálních počítačích s Windows. V dřívějších verzích služby Mobility nainstalujete agenta ručně.
- Pro virtuální počítače s Linuxem je nutné nainstalovat agenta virtuálního počítače Azure ručně. Agenta virtuálního počítače Azure je potřeba nainstalovat jenom v případě, že služba Mobility nainstalovaná v migrovaném počítači je v9.6 nebo starší.


### <a name="install-the-agent-on-windows-vms"></a>Instalace agenta na virtuálních počítačích se systémem Windows

Pokud používáte verzi služby mobility Site Recovery starší než 9.7.0.0 nebo máte jinou potřebu nainstalovat agenta ručně, postupujte takto:  

1. Ujistěte se, že máte oprávnění správce na virtuálním počítači.
2. Stáhněte si [instalační program agenta virtuálního softwaru](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Spusťte soubor instalačního programu.

#### <a name="validate-the-installation"></a>Ověření instalace
Chcete-li zkontrolovat, zda je agent nainstalován:

1. Na virtuálním počítači Azure ve složce C:\WindowsAzure\Packages byste měli vidět soubor WaAppAgent.exe.
2. Klepněte pravým tlačítkem myši na soubor a v **části Vlastnosti**vyberte kartu **Podrobnosti.**
3. Ověřte, zda je v poli **Verze produktu** zobrazeno číslo 2.6.1198.718 nebo vyšší.

[Přečtěte si další informace](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) o instalaci agenta pro Windows.

### <a name="install-the-agent-on-linux-vms"></a>Instalace agenta na virtuální počítače s Linuxem

Nainstalujte agenta [virtuálního počítače Azure Linux](../virtual-machines/extensions/agent-linux.md) ručně takto:

1. Ujistěte se, že máte oprávnění správce na počítači.
2. Důrazně doporučujeme nainstalovat agenta virtuálního počítače s Linuxem pomocí RPM nebo balíčku DEB z úložiště balíčků vaší distribuce. Všichni [schválení poskytovatelé distribuce](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrují balíček agenta Azure Linuxu do svých ibiaa a úložišť.
    - Důrazně doporučujeme aktualizovat agenta pouze prostřednictvím distribučního úložiště.
    - Nedoporučujeme instalovat agenta virtuálního počítače SIP přímo z GitHubu a aktualizovat ho.
    -  Pokud nejnovější agent pro vaši distribuci není k dispozici, obraťte se na podporu distribuce pokyny, jak ji nainstalovat. 

#### <a name="validate-the-installation"></a>Ověření instalace 

1. Spusťte tento příkaz: **ps -e** a ujistěte se, že agent Azure běží na virtuálním počítači SIP.
2. Pokud proces není spuštěn, restartujte jej pomocí následujících příkazů:
    - Pro Ubuntu: **služba walinuxagent start**
    - Pro ostatní distribuce: **spuštění servisního waagentu**


## <a name="uninstall-the-mobility-service"></a>Odinstalace služby Mobility

1. Ručně odinstalujte službu Mobility z virtuálního počítače Azure pomocí jedné z následujících metod. 
    - V systému Windows v Ovládacích panelech > **přidat nebo odebrat programy**odinstalujte **službu Microsoft Azure Site Recovery Mobility Service/Master Target .** Na příkazovém řádku se zvýšenými oprávněními spusťte:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Pro Linux se přihlaste jako uživatel root. V terminálu přejděte na **/user/local/ASR**a spusťte následující příkaz:
        ```
        ./uninstall.sh -Y
        ```
2. Před konfigurací replikace restartujte virtuální počítač.

## <a name="next-steps"></a>Další kroky

[Zkontrolujte řešení potíží](site-recovery-extension-troubleshoot.md) s rozšířením Site Recovery na agenta virtuálního počítače Azure.
[Rychle replikovat](azure-to-azure-quickstart.md) virtuální počítač Azure do sekundární oblasti.
