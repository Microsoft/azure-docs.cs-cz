---
title: Nastavení zotavení po havárii pro virtuální počítače Azure po migraci do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje postup přípravy počítačů k nastavení zotavení po havárii mezi oblastmi Azure po migraci do Azure pomocí Azure Site Recovery.
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: 85ebb141390e0fa6b4dfbd77d7b7d3f6844950d7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916458"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Nastavení zotavení po havárii pro virtuální počítače Azure po migraci do Azure 


Pomocí tohoto článku po [migraci místních počítačů na virtuálních počítačích Azure](tutorial-migrate-on-premises-to-azure.md) pomocí [Site Recovery](site-recovery-overview.md) služby. Tento článek pomůže připravit virtuální počítače Azure k nastavení zotavení po havárii do sekundární oblasti Azure, pomocí služby Site Recovery.



## <a name="before-you-start"></a>Než začnete

Před nastavením zotavení po havárii, ujistěte se, že migrace byla dokončena podle očekávání. K provedení migrace úspěšně, po převzetí služeb při selhání, byste měli vybrat **dokončit migraci** možnost pro každý počítač, který chcete migrovat. 



## <a name="install-the-azure-vm-agent"></a>Nainstalujte agenta virtuálního počítače Azure

Azure [agenta virtuálního počítače](../virtual-machines/extensions/agent-windows.md) musí být nainstalována na virtuálním počítači, tak, aby Site Recovery dokáže replikovat ho.


1. Chcete-li nainstalovat agenta virtuálního počítače na virtuální počítače se systémem Windows, stáhněte a spusťte [instalační program agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Budete potřebovat oprávnění správce na virtuálním počítači pro dokončení instalace.
2. Pokud chcete nainstalovat agenta virtuálního počítače na virtuálních počítačích s Linuxem, nainstalujte nejnovější [agenta pro Linux](../virtual-machines/extensions/agent-linux.md). Budete potřebovat oprávnění správce k dokončení instalace. Doporučujeme že nainstalovat ze svého úložiště distribuce. Nedoporučujeme ale, instalace agenta virtuálního počítače s Linuxem přímo z Githubu. 


## <a name="validate-the-installation-on-windows-vms"></a>Ověření instalace na virtuálních počítačích s Windows

1. Na virtuálním počítači Azure, ve složce C:\WindowsAzure\Packages měli byste vidět přítomný soubor WaAppAgent.exe.
2. Klikněte pravým tlačítkem na soubor a v **vlastnosti**, vyberte **podrobnosti** kartu.
3. Ověřte, že **verze produktu** pole zobrazí 2.6.1198.718 nebo vyšší.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>Migrace z virtuálních počítačů VMware nebo fyzické servery

Pokud provádíte migraci místních virtuálních počítačů VMware (nebo fyzické servery) do Azure, Všimněte si, že:

- Budete muset nainstalovat agenta virtuálního počítače Azure, pokud se službou Mobility nainstalovanou na migrovaných počítačů v9.6 nebo dřívější.
- Na virtuálních počítačích Windows spuštěná 9.7.0.0 verzi služby Mobility a vyšší nainstaluje instalační program služby nejnovější dostupný agent virtuálního počítače Azure. Při migraci, splňovat tyto virtuální počítače již instalace agenta požadované pro jakékoli rozšíření virtuálního počítače, včetně rozšíření služby Site Recovery.
- Budete muset ručně odinstalujte službu Mobility virtuálního počítače Azure, pomocí jedné z následujících metod. Než začnete konfigurovat replikaci, restartujte virtuální počítač.
    - Pro Windows v Ovládacích panelech > **přidat nebo odebrat programy**, odinstalujte **Microsoft Azure Site Recovery Mobility Service/hlavní cílový server**. Na příkazovém řádku se zvýšenými oprávněními spusťte příkaz:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Pro Linux, přihlášení uživatele root. V terminálu přejděte do **/user/local/ASR**, a spusťte následující příkaz:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>Další postup

[Rychle replikovat](azure-to-azure-quickstart.md) virtuálního počítače Azure do sekundární oblasti.
