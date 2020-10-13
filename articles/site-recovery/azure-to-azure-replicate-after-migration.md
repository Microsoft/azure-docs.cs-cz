---
title: Nastavení zotavení po havárii po migraci do Azure pomocí Azure Site Recovery
description: Tento článek popisuje, jak připravit počítače na nastavení zotavení po havárii mezi oblastmi Azure po migraci do Azure pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a71e476a214c44514c7d57c54a09a38218ad6d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135673"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Nastavení zotavení po havárii virtuálních počítačů Azure po migraci do Azure 


Postupujte podle tohoto článku, pokud jste [migrovali místní počítače do virtuálních počítačů Azure](./migrate-tutorial-on-premises-azure.md) pomocí služby [Site Recovery](site-recovery-overview.md) a teď chcete získat virtuální počítače nastavené pro zotavení po havárii do sekundární oblasti Azure. Tento článek popisuje, jak zajistit, že agent virtuálního počítače Azure je nainstalovaný na migrovaných virtuálních počítačích a jak odebrat službu Site Recovery mobility, kterou už po migraci nepotřebujete.



## <a name="verify-migration"></a>Ověření migrace

Před nastavením zotavení po havárii se ujistěte, že se migrace dokončila podle očekávání. Po úspěšném dokončení migrace byste měli po převzetí služeb při selhání vybrat možnost **kompletní migrace** pro každý počítač, který chcete migrovat. 

## <a name="verify-the-azure-vm-agent"></a>Ověření agenta virtuálního počítače Azure

Každý virtuální počítač Azure musí mít nainstalovaný [Agent virtuálního počítače Azure](../virtual-machines/extensions/agent-windows.md) . Pokud chcete replikovat virtuální počítače Azure, Site Recovery do agenta nainstaluje rozšíření.

- Pokud na počítači běží verze 9.7.0.0 nebo novější služby Site Recovery mobility, agent virtuálního počítače Azure se automaticky nainstaluje ve službě mobility na virtuální počítače s Windows. V dřívějších verzích služby mobility nainstalujete agenta ručně.
- Pro virtuální počítače se systémem Linux je nutné ručně nainstalovat agenta virtuálního počítače Azure. Agenta virtuálního počítače Azure je potřeba nainstalovat jenom v případě, že je služba mobility nainstalovaná na migrovaném počítači v 9.6 nebo starší verzi.


### <a name="install-the-agent-on-windows-vms"></a>Instalace agenta na virtuální počítače s Windows

Pokud používáte verzi služby Site Recovery mobility starší než 9.7.0.0 nebo máte nějaké jiné potřebné k ruční instalaci agenta, udělejte toto:  

1. Ujistěte se, že máte na virtuálním počítači oprávnění správce.
2. Stáhněte si [instalační program agenta virtuálního počítače](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Spusťte instalační soubor.

#### <a name="validate-the-installation"></a>Ověření instalace
Chcete-li ověřit, zda je agent nainstalován:

1. Na virtuálním počítači Azure ve složce C:\WindowsAzure\Packages by se měl zobrazit soubor WaAppAgent.exe.
2. Klikněte na soubor pravým tlačítkem a v okně **vlastnosti**vyberte kartu **Podrobnosti** .
3. Ověřte, že se v poli **verze produktu** zobrazuje 2.6.1198.718 nebo vyšší.

[Přečtěte si další informace](../virtual-machines/extensions/agent-windows.md) o instalaci agenta pro Windows.

### <a name="install-the-agent-on-linux-vms"></a>Instalace agenta na virtuální počítače se systémem Linux

Nainstalujte agenta [virtuálního počítače Azure Linux](../virtual-machines/extensions/agent-linux.md) ručně následujícím způsobem:

1. Ujistěte se, že máte na počítači oprávnění správce.
2. Důrazně doporučujeme nainstalovat agenta virtuálního počítače se systémem Linux pomocí balíčku ot./min. nebo balíčku DEB z úložiště balíčků distribuce. Všichni [poskytovatelé schválené distribuce](../virtual-machines/linux/endorsed-distros.md) integrují balíček agenta Azure Linux do svých imagí a úložišť.
    - Důrazně doporučujeme, abyste agenta aktualizovali pouze prostřednictvím distribučního úložiště.
    - Nedoporučujeme instalovat agenta virtuálního počítače se systémem Linux přímo z GitHubu a aktualizovat ho.
    -  Pokud není k dispozici nejnovější agent pro distribuci, obraťte se na podporu distribuce, kde najdete pokyny k jeho instalaci. 

#### <a name="validate-the-installation"></a>Ověření instalace 

1. Spusťte tento příkaz: **PS-e** a ujistěte se, že na virtuálním počítači se systémem Linux běží agent Azure.
2. Pokud proces není spuštěný, restartujte ho pomocí následujících příkazů:
    - Pro Ubuntu: **Služba walinuxagent Start**
    - Pro ostatní distribuce: **Service waagent Start**


## <a name="uninstall-the-mobility-service"></a>Odinstalace služby mobility

1. Ručně odinstalujte službu mobility z virtuálního počítače Azure pomocí jedné z následujících metod. 
    - V případě systému Windows v Ovládacích panelech > **Přidat nebo odebrat programy**, odinstalovat **Microsoft Azure Site Recovery službu mobility nebo hlavní cílový server**. Na příkazovém řádku se zvýšenými oprávněními spusťte příkaz:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Pro Linux se přihlaste jako uživatel root. V terminálu přejděte na **/User/Local/ASR**a spusťte následující příkaz:
        ```
        ./uninstall.sh -Y
        ```
2. Před konfigurací replikace restartujte virtuální počítač.

## <a name="next-steps"></a>Další kroky

[Přečtěte si téma řešení potíží](site-recovery-extension-troubleshoot.md) s rozšířením Site Recovery na AGENTOVI virtuálního počítače Azure.
[Rychlá replikace](azure-to-azure-quickstart.md) virtuálního počítače Azure do sekundární oblasti.
