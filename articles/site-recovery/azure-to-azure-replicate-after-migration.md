---
title: Nastavení zotavení po havárii pro virtuální počítače Azure po migraci do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje postup přípravy počítačů k nastavení zotavení po havárii mezi oblastmi Azure po migraci do Azure pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 4b764c8e7c3d97ff521add05033265f705c4136f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399524"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Nastavení zotavení po havárii virtuálních počítačů Azure po migraci do Azure 


Postupujte podle tohoto článku, pokud jste [migraci místních počítačů na virtuálních počítačích Azure](tutorial-migrate-on-premises-to-azure.md) pomocí [Site Recovery](site-recovery-overview.md) služby a teď chcete získat virtuálních počítačů nastavení pro zotavení po havárii do sekundární oblasti Azure. Tento článek popisuje Ujistěte se, že agent virtuálního počítače Azure je nainstalovaný na migrovaných virtuálních počítačů a odebrat službu Site Recovery Mobility, který se má po dokončení migrace už nepotřebujete.



## <a name="verify-migration"></a>Ověření migrace

Před nastavením zotavení po havárii, ujistěte se, že migrace byla dokončena podle očekávání. K provedení migrace úspěšně, po převzetí služeb při selhání, byste měli vybrat **dokončit migraci** možnost pro každý počítač, který chcete migrovat. 

## <a name="verify-the-azure-vm-agent"></a>Ověřte, že agent virtuálního počítače Azure

Musí mít každý virtuální počítač Azure [agenta virtuálního počítače Azure](../virtual-machines/extensions/agent-windows.md) nainstalované. Pokud chcete replikovat virtuální počítače Azure, Site Recovery nainstaluje rozšíření v agentovi.

- Pokud na počítači běží verze 9.7.0.0 nebo novější služby Site Recovery Mobility, které služba Mobility na virtuálních počítačích s Windows je automaticky nainstalován agent virtuálního počítače Azure. Ve starších verzích služby Mobility musíte agenta nainstalovat automaticky.
- Pro virtuální počítače s Linuxem je nutné nainstalovat agenta virtuálního počítače Azure ručně. Budete muset nainstalovat agenta virtuálního počítače Azure, pokud se službou Mobility nainstalovanou na migrovaných počítačů v9.6 nebo dřívější.


### <a name="install-the-agent-on-windows-vms"></a>Nainstalujte agenta na virtuálních počítačích s Windows

Pokud máte spuštěnou verzi starší než 9.7.0.0 službu mobility Site Recovery, nebo nemáte další požadavky pro instalaci agenta ručně, postupujte takto:  

1. Ujistěte se, že máte oprávnění správce na virtuálním počítači.
2. Stáhněte si [instalační program agenta virtuálního počítače](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Spusťte instalační soubor.

#### <a name="validate-the-installation"></a>Ověření instalace
Chcete-li zkontrolovat, zda je agent nainstalovaný:

1. Na virtuálním počítači Azure, ve složce C:\WindowsAzure\Packages měli byste vidět přítomný soubor WaAppAgent.exe.
2. Klikněte pravým tlačítkem na soubor a v **vlastnosti**, vyberte **podrobnosti** kartu.
3. Ověřte, že **verze produktu** pole zobrazí 2.6.1198.718 nebo vyšší.

[Další informace](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) o instalaci agenta pro Windows.

### <a name="install-the-agent-on-linux-vms"></a>Nainstalujte agenta na virtuální počítače s Linuxem

Nainstalujte [Linuxový virtuální počítač Azure](../virtual-machines/extensions/agent-linux.md) agenta ručně takto:

1. Ujistěte se, že máte oprávnění správce na počítači.
2. Důrazně doporučujeme nainstalovat agenta virtuálního počítače s Linuxem pomocí RPM nebo DEB balíček z úložiště balíčků vaší distribuce. Všechny [poskytovatelé distribuce schválené pro](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) balíček agenta Azure Linux integrovat do své Image a úložiště.
    - Důrazně doporučujeme aktualizovat agenta pouze prostřednictvím distribuce úložiště.
    - Nedoporučujeme instalace agenta virtuálního počítače s Linuxem přímo z Githubu a aktualizacích.
    -  Pokud nejnovější verzi agenta pro vaši distribuci není k dispozici, požádejte distribuce podporu pokyny o tom, jak ji nainstalovat. 

#### <a name="validate-the-installation"></a>Ověření instalace 

1. Spusťte tento příkaz: **ps -e** zajistit, že Azure agent běží na Linuxovém virtuálním počítači.
2. Pokud proces není spuštěn, můžete ji restartujte pomocí následujících příkazů:
    - Pro Ubuntu: **služby walinuxagent start**
    - Pro jiné distribuce: **služby waagent start**


## <a name="uninstall-the-mobility-service"></a>Odinstalujte službu Mobility

1. Ručně odinstalujte službu Mobility virtuálního počítače Azure, pomocí jedné z následujících metod. 
    - Pro Windows v Ovládacích panelech > **přidat nebo odebrat programy**, odinstalujte **Microsoft Azure Site Recovery Mobility Service/hlavní cílový server**. Na příkazovém řádku se zvýšenými oprávněními spusťte příkaz:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Pro Linux Přihlaste se jako uživatel root. V terminálu přejděte do **/user/local/ASR**, a spusťte následující příkaz:
        ```
        ./uninstall.sh -Y
        ```
2. Než začnete konfigurovat replikaci, restartujte virtuální počítač.

## <a name="next-steps"></a>Další postup

[Řešení potíží zkontrolujte](site-recovery-extension-troubleshoot.md) pro rozšíření služby Site Recovery na agenta virtuálního počítače Azure.
[Rychle replikovat](azure-to-azure-quickstart.md) virtuálního počítače Azure do sekundární oblasti.
