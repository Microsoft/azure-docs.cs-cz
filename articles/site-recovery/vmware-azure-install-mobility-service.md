---
title: Instalace služby Mobility (VMware nebo fyzické prostředky do Azure) | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat agenta služby Mobility na ochranu místních virtuálních počítačů VMware a fyzické servery s využitím Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 094c1776c0760c04d85aff6ad3d812a2ad7afa56
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526993"
---
# <a name="install-the-mobility-service"></a>Instalace služby Mobility 

Službu Mobility Azure Site Recovery je nainstalován na virtuálních počítačích VMware a fyzické servery, které chcete replikovat do Azure. Služba zaznamenává datové zápisy na počítači a předává je na procesní server. Nasazení služby Mobility do každého počítače (virtuální počítač VMware nebo fyzický server), který chcete replikovat do Azure. Můžete nasadit službu Mobility na serverech a virtuálních počítačů VMware, které chcete chránit pomocí následujících metod:


* [Instalace pomocí nástrojů pro nasazení softwaru jako je System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)
* [Instalace pomocí služby Azure Automation a Desired State Configuration (DSC služby Automation)](vmware-azure-mobility-deploy-automation-dsc.md)
* [Nainstalovat ručně z uživatelského rozhraní](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [Ruční instalace z příkazového řádku](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Nainstalovat pomocí nabízené instalace Site Recovery](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> Počínaje verzí 9.7.0.0, **na virtuálních počítačích s Windows**, instalační program služby Mobility nainstaluje také k dispozici nejnovější [agenta virtuálního počítače Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent). Když počítač převezme služby při selhání do Azure, tento počítač splňuje požadavky všechna rozšíření virtuálních počítačů pomocí instalace agenta.
> </br>Na **virtuální počítače s Linuxem**, WALinuxAgent musí ručně nainstalovat.

## <a name="prerequisites"></a>Požadavky
Než ručně nainstalovat službu Mobility na serveru, proveďte tyto požadované kroky:
1. Přihlaste se ke konfiguračnímu serveru a pak otevřete okno příkazového řádku jako správce.
2. Změňte adresář na složku bin a poté vytvořit soubor přístupové heslo.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Store souboru s heslem v zabezpečeném umístění. Použijte soubor během instalace služby Mobility.
4. Instalace služby mobility pro všechny podporované operační systémy se ve složce %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Mapování mobility služby Instalační služby operačního systému

Pokud chcete zobrazit seznam operační systém verze kompatibilní balíček služby Mobility s odkazují na seznam [podporované operační systémy pro virtuální počítače VMware a fyzické servery](vmware-physical-azure-support-matrix.md#replicated-machines).

| Název šablony souboru instalačního programu| Operační systém |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 bitů) </br> Windows Server 2012 (64 bitů) </br> Windows Server 2012 R2 (64 bitů) </br> Windows Server 2016 (64 bitů) |
|Microsoft Azure Site Recovery\_UA\*počítače RHEL6 64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* (pouze 64bitové verze) </br> CentOS 6.* (pouze 64bitové verze) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* (pouze 64bitové verze) </br> CentOS 7.* (pouze 64bitové verze) |
|Microsoft Azure Site Recovery\_UA\*SLES12 64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2 SP3 (pouze 64bitové verze)|
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (pouze 64bitové verze)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (pouze 64bitové verze)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (pouze 64bitové verze)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (pouze 64bitové verze)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Server se systémem Ubuntu Linux 16.04 LTS (pouze 64bitové verze)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (pouze 64bitová verze)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (pouze 64bitové verze)|

## <a name="install-mobility-service-manually-by-using-the-gui"></a>Nainstalovat službu Mobility ručně pomocí grafického uživatelského rozhraní

>[!IMPORTANT]
> Pokud používáte k replikaci virtuálních počítačů Azure IaaS z jedné předplatné/oblasti Azure do jiného konfiguračního serveru, použijte metodu instalace na základě na příkazovém řádku.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Nainstalovat službu Mobility ručně z příkazového řádku

### <a name="command-line-installation-on-a-windows-computer"></a>Instalace z příkazového řádku na počítači Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Instalace z příkazového řádku na počítači s Linuxem
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Nainstalovat službu Mobility pomocí nabízené instalace ze služby Azure Site Recovery
Provedení nabízené instalace služby Mobility s využitím Site Recovery. Všechny cílové počítače, musí splňovat následující požadavky.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Po instalaci služby Mobility na webu Azure Portal, vyberte **+ replikovat** chcete začít chránit tyto virtuální počítače.

## <a name="update-mobility-service"></a>Aktualizace služby Mobility

> [!WARNING]
> Ujistěte se, že konfigurační server, horizontální navýšení kapacity procesových serverů a všechny hlavní cílové servery, které jsou součástí vašeho nasazení jsou aktualizované před zahájením aktualizace služby Mobility na chráněné servery.

1. Na portálu Azure portal, přejděte *název trezoru* > **replikované položky** zobrazení.
2. Pokud již byla aktualizována konfigurační server na nejnovější verzi, se zobrazí oznámení, který čte "je k dispozici nová aktualizace agenta replikace Site recovery. Klikněte na tlačítko nainstalovat."

     ![Okno replikované položky](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. Vyberte oznámení a otevřete stránku výběru virtuálního počítače.
4. Vyberte virtuální počítače, kterou chcete upgradovat službu mobility na a vyberte **OK**.

     ![Replikované položky seznamu virtuálních počítačů](.\media\vmware-azure-install-mobility-service\update-okpng.png)

Úloha aktualizace služby Mobility se spustí pro každou z vybraných virtuálních počítačů.

> [!NOTE]
> [Přečtěte si další](vmware-azure-manage-configuration-server.md) o tom, jak aktualizovat heslo pro účet použitý k instalaci služby Mobility.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Odinstalujte službu Mobility na počítač s Windows serverem
Odinstalujte službu Mobility na počítač s Windows serverem pomocí jedné z následujících metod.

### <a name="uninstall-by-using-the-gui"></a>Odinstalace pomocí grafického uživatelského rozhraní
1. V Ovládacích panelech vyberte **programy**.
2. Vyberte **Microsoft Azure Site Recovery Mobility Service/hlavní cílový server**a pak vyberte **odinstalovat**.

### <a name="uninstall-at-a-command-prompt"></a>Odinstalovat z příkazového řádku
1. Otevřete okno příkazového řádku jako správce.
2. Odinstalace služby Mobility, spusťte následující příkaz:

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Odinstalujte službu Mobility na počítači s Linuxem
1. Na serveru Linux, přihlaste se jako **kořenové** uživatele.
2. V terminálu přejděte do /user/local/ASR.
3. Odinstalace služby Mobility, spusťte následující příkaz:

    ```
    uninstall.sh -Y
    ```
