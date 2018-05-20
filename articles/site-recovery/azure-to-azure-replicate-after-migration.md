---
title: Nastavit zotavení po havárii pro virtuální počítače Azure po migraci na Azure s Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje postup přípravy počítače, které chcete nastavit zotavení po havárii mezi oblastmi Azure po migraci na Azure pomocí Azure Site Recovery.
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 05/16/2018
ms.author: ponatara
ms.openlocfilehash: f64d61134f61476bcdd803f92019830b7cecf867
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Nastavit zotavení po havárii pro virtuální počítače Azure po migraci na Azure 

>[!NOTE]
> Zotavení po havárii pro virtuální počítače Azure pomocí Azure Site Recovery je aktuálně ve verzi preview.

Pomocí tohoto článku po jste [migrovat místní počítače na virtuálních počítačích Azure](tutorial-migrate-on-premises-to-azure.md) pomocí [Site Recovery](site-recovery-overview.md) služby. Tento článek vám umožňuje připravit virtuální počítače Azure pro nastavení zotavení po havárii sekundární oblasti Azure, pomocí Site Recovery.



## <a name="before-you-start"></a>Než začnete

Než nastavíte zotavení po havárii, ujistěte se, že migrace byla dokončena podle očekávání. K dokončení migrace úspěšně, po převzetí služeb při selhání, byste měli vybrat **dokončení migrace** možnost pro každý počítač, který chcete migrovat. 



## <a name="install-the-azure-vm-agent"></a>Nainstalujte agenta virtuálního počítače Azure

Azure [agenta virtuálního počítače](../virtual-machines/extensions/agent-windows.md) musí být nainstalován na virtuální počítač tak, aby Site Recovery dokáže replikovat ho.


1. Chcete-li nainstalovat agenta virtuálního počítače na virtuální počítače se systémem Windows, stáhněte a spusťte [instalační program agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Je třeba oprávnění správce ve virtuálním počítači pro dokončení instalace.
2. Chcete-li nainstalovat agenta virtuálního počítače na virtuální počítače se systémem Linux, nainstalujte nejnovější [agenta systému Linux](../virtual-machines/extensions/agent-linux.md). Budete potřebovat oprávnění správce k dokončení instalace. Doporučujeme že nainstalovat z úložiště distribuce. Není doporučeno, instalace agenta virtuálního počítače s Linuxem přímo z Githubu. 


## <a name="validate-the-installation-on-windows-vms"></a>Ověření instalace na virtuálních počítačích Windows

1. Ve virtuálním počítači Azure, ve složce C:\WindowsAzure\Packages měli byste vidět přítomný soubor WaAppAgent.exe.
2. Klikněte pravým tlačítkem na soubor a v **vlastnosti**, vyberte **podrobnosti** kartě.
3. Ověřte, zda **verze produktu** pole ukazuje 2.6.1198.718 nebo vyšší.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>Migrace z virtuálních počítačů VMware nebo fyzických serverů

Pokud provádíte migraci virtuálních počítačů VMware místně (nebo fyzických serverů) do Azure, Všimněte si, že:

- Potřebujete nainstalovat agenta virtuálního počítače Azure, pokud je služba Mobility na migrované počítači nainstalovány v9.6 nebo dřívější.
- Na virtuálních počítačích Windows verzí 9.7.0.0 služby Mobility a vyšší instalační program služby nainstaluje nejnovější dostupný agent virtuálního počítače Azure. Když provádíte migraci, splňovat tyto virtuální počítače již instalace agenta požadované pro jakékoli rozšíření virtuálního počítače, včetně rozšíření Site Recovery.
- Budete muset ručně odinstalujte službu Mobility z virtuálního počítače Azure, pomocí jedné z následujících metod. Restartujte virtuální počítač, než začnete konfigurovat replikace.
    - Pro Windows v Ovládacích panelech > **přidat nebo odebrat programy**, odinstalujte **Microsoft Azure Site Recovery Mobility služby nebo hlavní cílový server**. Na příkazovém řádku se zvýšenými oprávněními spusťte příkaz:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Pro Linux, přihlášení uživatele root. V terminálu, přejděte na **/user/local/ASR**, a spusťte následující příkaz:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>Další postup

[Rychle replikovat](azure-to-azure-quickstart.md) virtuálního počítače Azure v sekundární oblasti.
