---
title: O službě mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů s Azure Site Recovery | Microsoft Docs
description: Přečtěte si o agentovi služby mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362715"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>O službě mobility pro virtuální počítače VMware a fyzické servery

Při nastavování zotavení po havárii pro virtuální počítače VMware a fyzické servery pomocí [Azure Site Recovery](site-recovery-overview.md)nainstalujete službu Site Recovery mobility na každý místní virtuální počítač VMware a fyzický server.  Služba mobility zachycuje zápisy dat na počítači a předá je na Site Recovery procesový Server. Službu mobility můžete nasadit pomocí následujících metod:

- [Nabízená instalace](#push-installation): Site Recovery nainstaluje agenta mobility na server, pokud je ochrana povolená přes Azure Portal.
- Ruční instalace: službu mobility můžete nainstalovat ručně na každém počítači prostřednictvím [uživatelského rozhraní](#install-mobility-agent-through-ui) nebo [příkazového řádku](#install-mobility-agent-through-command-prompt).
- [Automatizované nasazení](vmware-azure-mobility-install-configuration-mgr.md): můžete automatizovat instalaci pomocí nástrojů pro nasazení softwaru, jako je Configuration Manager.

> [!NOTE]
> Agent mobility využívá přibližně 6% paměti na zdrojových počítačích pro virtuální počítače VMware nebo fyzické počítače.

## <a name="anti-virus-on-replicated-machines"></a>Antivirová ochrana na replikovaných počítačích

Pokud mají počítače, které chcete replikovat, spuštěné aktivní antivirový software, ujistěte se, že jste vyloučili instalační složku služby mobility z hlediska antivirové operace (*C:\ProgramData\ASR\agent*). Tím se zajistí, že replikace bude fungovat podle očekávání.

## <a name="push-installation"></a>Nabízená instalace

Nabízená instalace je nedílnou součástí úlohy "[Povolení replikace](vmware-azure-enable-replication.md#enable-replication)" aktivované na portálu. Po výběru sady virtuálních počítačů, které chcete chránit, a aktivaci možnosti Povolit replikaci, konfigurační server vloží agenta mobility na servery, nainstaluje agenta a dokončí registraci agenta pomocí konfiguračního serveru. Pro úspěšné dokončení této operace

- Ujistěte se, že jsou splněné všechny [požadavky](vmware-azure-install-mobility-service.md) na nabízenou instalaci.
- Ujistěte se, že všechny konfigurace serverů spadají [do scénáře podpory pro VMware do Azure Dr](vmware-physical-azure-support-matrix.md).

Podrobnosti pracovního postupu nabízené instalace byly popsány v následujících částech.

### <a name="from-923-version-onwards"></a>Od [verze 9,23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) a vyšší

Během nabízené instalace agenta mobility se provádí následující kroky.

1. Vloží agenta do zdrojového počítače. Kopírování agenta do zdrojového počítače může selhat kvůli několika chybám v životním prostředí. Pokud chcete řešit chyby nabízených instalací, navštivte [naše pokyny](vmware-azure-troubleshoot-push-install.md) .
2. Po úspěšném zkopírování agenta na kontrolu požadovaných součástí se na serveru provádí. Instalace se nezdařila, pokud není splněna jedna nebo více [požadavků](vmware-physical-azure-support-matrix.md) . Pokud jsou splněné všechny požadavky, spustí se instalace.
3. V rámci instalace agenta mobility je na serveru nainstalovaný Azure Site Recovery poskytovatel služby VSS. Tento zprostředkovatel se používá ke generování bodů konzistentních vzhledem k aplikacím. Pokud instalace poskytovatele služby VSS selže, bude tento krok přeskočen a instalace agenta bude pokračovat.
4. Pokud je instalace agenta úspěšná, ale instalace poskytovatele VSS selže, pak je stav úlohy označený jako upozornění. To nemá vliv na generování bodů konzistence selhání.

    a. Pokud chcete vygenerovat body konzistentní vzhledem k aplikacím, přečtěte si [naše pokyny](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) k ručnímu dokončení instalace Site Recovery poskytovatele služby Stínová kopie svazku. </br>
    b.  Pokud nechcete, aby se vygenerovaly body konzistentní vzhledem k aplikacím, [upravte zásady replikace](vmware-azure-set-up-replication.md#create-a-policy) tak, aby byly vypnuté body konzistentní vzhledem k aplikacím.

### <a name="before-922-versions"></a>Verze 9,22

1. Vloží agenta do zdrojového počítače. Kopírování agenta do zdrojového počítače může selhat kvůli několika chybám v životním prostředí. Pokud chcete řešit chyby nabízených instalací, navštivte [naše pokyny](vmware-azure-troubleshoot-push-install.md) .
2. Po úspěšném zkopírování agenta na kontrolu požadovaných součástí se na serveru provádí. Instalace se nezdařila, pokud není splněna jedna nebo více [požadavků](vmware-physical-azure-support-matrix.md) . Pokud jsou splněné všechny požadavky, spustí se instalace.
3. V rámci instalace agenta mobility je na serveru nainstalovaný Azure Site Recovery poskytovatel služby VSS. Tento zprostředkovatel se používá ke generování bodů konzistentních vzhledem k aplikacím. Pokud instalace poskytovatele služby VSS selže, instalace agenta se nezdaří. Aby nedošlo k selhání instalace agenta mobility, použijte [verzi 9,23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) nebo vyšší pro generování bodů konzistentních pro selhání a nainstalujte poskytovatele služby VSS ručně.

## <a name="install-mobility-agent-through-ui"></a>Instalace agenta mobility pomocí uživatelského rozhraní

### <a name="prerequisite"></a>Požadavek

- Ujistěte se, že všechny konfigurace serverů spadají [do scénáře podpory pro VMware do Azure Dr](vmware-physical-azure-support-matrix.md).
- [Vyhledejte instalační program](#locate-installer-files) na základě operačního systému serveru.

>[!IMPORTANT]
> Pokud provádíte replikaci virtuálního počítače Azure IaaS z jedné oblasti Azure do jiné, nepoužívejte tuto metodu. Místo toho použijte metodu instalace založenou na příkazovém řádku.

1. Zkopírujte instalační soubor do počítače a spusťte ho.
2. V **Možnosti instalace**vyberte **nainstalovat službu mobility**.
3. Vyberte umístění instalace > **nainstalovat**.

    ![Stránka možností instalace služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Sledujte instalaci v **průběhu instalace**. Po dokončení instalace vyberte **pokračovat ke konfiguraci** pro registraci služby na konfiguračním serveru.

    ![Registrační stránka služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. V části **Podrobnosti konfiguračního serveru**zadejte IP adresu a heslo, které jste nakonfigurovali.

    ![Registrační stránka služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Kliknutím na **Registrovat** dokončete registraci.

    ![Finální stránka registrace služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instalace agenta mobility přes příkazový řádek

### <a name="prerequisite"></a>Požadavek

- Ujistěte se, že všechny konfigurace serverů spadají [do scénáře podpory pro VMware do Azure Dr](vmware-physical-azure-support-matrix.md).
- [Vyhledejte instalační program](#locate-installer-files) na základě operačního systému serveru.

### <a name="on-a-windows-machine"></a>Na počítači s Windows

- Zkopírujte instalační program do místní složky (například C:\Temp) na serveru, který chcete chránit.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Nainstalujte následujícím způsobem:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Zaregistrujte agenta u konfiguračního serveru.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Nastavení instalace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | UnifiedAgent. exe/role \<MS/MT >/InstallLocation \<umístění instalace >/Platform "VmWare"/Silent
Instalační protokoly | Pod%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Povinný parametr instalace Určuje, jestli má být nainstalovaná služba mobility (MS) nebo hlavní cíl (MT).
/InstallLocation| Volitelný parametr. Určuje umístění instalace služby mobility (všechny složky).
/Platform | Povinné. Určuje platformu, na které je nainstalovaná služba mobility. **VMware** pro virtuální počítače VMware/fyzické servery; **Azure** pro virtuální počítače Azure.<br/><br/> Pokud pracujete s virtuálními počítači Azure jako s fyzickými počítači, zadejte **VMware**.
/Silent| Volitelné. Určuje, jestli se má spustit instalační program v tichém režimu.

#### <a name="registration-settings"></a>Nastavení registrace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | UnifiedAgentConfigurator. exe/CSEndPoint \<CSIP >/PassphraseFilePath \<PassphraseFilePath >
Protokoly konfigurace agenta | Pod%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte jakoukoli platnou IP adresu.
/PassphraseFilePath |  Povinné. Umístění přístupového hesla Použijte jakoukoli platnou cestu UNC nebo místní cestu k souboru.

### <a name="on-a-linux-machine"></a>V počítači se systémem Linux

1. Zkopírujte instalační program do místní složky (například adresáře/TMP) na serveru, který chcete chránit. V terminálu spusťte následující příkazy:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Nainstalujte následujícím způsobem:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Po dokončení instalace je potřeba službu mobility zaregistrovat na konfiguračním serveru. Spuštěním následujícího příkazu zaregistrujte službu mobility pomocí konfiguračního serveru:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Nastavení instalace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | ./Install-d \<umístění instalace >-r \<MS/MT >-v VmWare-q
-r | Povinný parametr instalace Určuje, jestli má být nainstalovaná služba mobility (MS) nebo hlavní cíl (MT).
-d | Volitelný parametr. Určuje umístění instalace služby mobility:/usr/local/ASR.
-v | Povinné. Určuje platformu, na které je nainstalovaná služba mobility. **VMware** pro virtuální počítače VMware/fyzické servery; **Azure** pro virtuální počítače Azure.
-q | Volitelné. Určuje, jestli se má spustit instalační program v tichém režimu.

#### <a name="registration-settings"></a>Nastavení registrace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | /usr/local/ASR/Vx/bin CD<br/><br/> UnifiedAgentConfigurator.sh-i \<CSIP >-P \<PassphraseFilePath >
-i | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte jakoukoli platnou IP adresu.
-P |  Povinné. Úplná cesta k souboru, ve kterém se heslo ukládá Použijte libovolnou platnou složku.

## <a name="azure-virtual-machine-agent"></a>Agent virtuálního počítače Azure

- **Virtuální počítače s Windows**: od verze 9.7.0.0 služby mobility se [Agent virtuálního počítače Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) nainstaluje pomocí instalačního programu služby mobility. Tím se zajistí, že při převzetí služeb při selhání počítače do Azure bude virtuální počítač Azure splňovat požadavky na instalaci agenta pro použití libovolného rozšíření virtuálního počítače.
- **Virtuální počítače se systémem Linux**: [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) musí být po převzetí služeb při selhání na virtuálním počítači Azure nainstalovaný ručně.

## <a name="locate-installer-files"></a>Vyhledání instalačních souborů

Přejít na složku%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository na konfiguračním serveru. Ověřte, který instalační program potřebujete na základě operačního systému. Následující tabulka shrnuje soubory instalačního programu pro každý virtuální počítač VMware a operační systém fyzického serveru. Než začnete, můžete zkontrolovat [podporované operační systémy](vmware-physical-azure-support-matrix.md#replicated-machines) .

**Instalační soubor** | **Operační systém (pouze 64 bitů)**
--- | ---
Microsoft-ASR\_UA\*Windows\*Release. exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*počítače RHEL6-64\*Release. tar. gz | Red Hat Enterprise Linux (RHEL) 6. * </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*Release. tar. gz | Red Hat Enterprise Linux (RHEL) 7. * </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*Release. tar. gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*Release. tar. gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*Release. tar. gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*Release. tar. gz | Oracle Enterprise Linux 6,4, 6,5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*Release. tar. gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*Release. tar. gz | Ubuntu Linux 16.04 LTS server
Microsoft-ASR_UA\*DEBIAN7-64\*Release. tar. gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*Release. tar. gz | Debian 8

## <a name="next-steps"></a>Další kroky

[Nastavte nabízenou instalaci služby mobility](vmware-azure-install-mobility-service.md).
