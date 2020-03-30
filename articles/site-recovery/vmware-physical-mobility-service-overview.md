---
title: O službě mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů s Azure Site Recovery | Dokumenty společnosti Microsoft
description: Získejte informace o agentovi služby Mobility Service pro zotavení po havárii virtuálních počítačích VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256832"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>O službě Mobilita pro virtuální zařízení VMware a fyzické servery

Když nastavíte zotavení po havárii pro virtuální počítače VMware a fyzické servery pomocí [Azure Site Recovery](site-recovery-overview.md), nainstalujete službu Mobility site recovery do každého místního virtuálního počítače VMware a fyzického serveru.  Služba Mobility zachytí zápisy dat v počítači a předá je na server procesu obnovení webu. Službu mobility můžete nasadit pomocí následujících metod:

- [Nabízená instalace](#push-installation): Site Recovery nainstaluje agenta mobility na server, když je ochrana povolena prostřednictvím portálu Azure.
- Ruční instalace: Službu mobility můžete nainstalovat ručně na každý počítač prostřednictvím [hlavního nastavení](#install-mobility-agent-through-ui) nebo [příkazového řádku](#install-mobility-agent-through-command-prompt).
- [Automatické nasazení](vmware-azure-mobility-install-configuration-mgr.md): Můžete automatizovat instalaci pomocí nástrojů pro nasazení softwaru, jako je configuration manager.

> [!NOTE]
> Agent mobility používá přibližně 6%-10 % paměti na zdrojových počítačích pro virtuální počítače VMware nebo fyzické počítače.

## <a name="anti-virus-on-replicated-machines"></a>Anti-virus na replikovaných počítačích

Pokud jsou počítače, které chcete replikovat, spuštěn aktivní antivirový software, vyloučíte instalační složku služby Mobility z antivirových operací (*C:\ProgramData\ASR\agent*). Tím je zajištěno, že replikace funguje podle očekávání.

## <a name="push-installation"></a>Push instalace

Nabízená instalace je nedílnou součástí úlohy[Povolit replikaci](vmware-azure-enable-replication.md#enable-replication)aktivované na portálu. Po výběru sady virtuálních počítačů, které chcete chránit, a aktivaci "Povolit replikaci" konfigurační server přesune agenta mobility na servery, nainstaluje agenta a dokončí registraci agenta s konfiguračním serverem. Pro úspěšné dokončení této operace

- Ujistěte se, že jsou splněny všechny požadavky na [nabízenou](vmware-azure-install-mobility-service.md) instalaci.
- Ujistěte se, že všechny konfigurace serverů spadají pod [matici podpory scénáře VMware do Azure ZOTAVENÍ](vmware-physical-azure-support-matrix.md).

Podrobnosti o pracovním postupu nabízená instalace byly popsány v následujících částech.

### <a name="from-923-version-onwards"></a>Od [verze 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) dále

Během push instalace agenta mobility jsou prováděny následující kroky

1. Odešle agenta do zdrojového počítače. Kopírování agenta do zdrojového počítače může selhat z důvodu několika chyb prostředí. Navštivte [naše pokyny](vmware-azure-troubleshoot-push-install.md) pro řešení potíží s chybami nabízené instalace.
2. Po úspěšném zkopírování agenta na server jsou na serveru provedeny kontroly požadavků. Instalace se nezdaří, pokud není splněn jeden nebo více [požadavků.](vmware-physical-azure-support-matrix.md) Pokud jsou splněny všechny požadavky, spustí se instalace.
3. Zprostředkovatel VSS azure site recovery je nainstalovaný na serveru jako součást instalace agenta mobility. Tento zprostředkovatel se používá ke generování konzistentní body aplikace. Pokud instalace zprostředkovatele VSS selže, bude tento krok přeskočen a instalace agenta bude pokračovat.
4. Pokud instalace agenta proběhne úspěšně, ale instalace zprostředkovatele VSS se nezdaří, je stav úlohy označen jako "Upozornění". To nemá vliv na generování bodů konzistence selhání.

    a. Chcete-li generovat konzistentní body aplikace, podívejte se do [našich pokynů](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) k dokončení instalace poskytovatele Site Recovery VSS ručně. </br>
    b.  Pokud si nepřejete, aby byly generovány konzistentní body aplikace, [upravte zásady replikace](vmware-azure-set-up-replication.md#create-a-policy) tak, aby vypnuly konzistentní body aplikace.

### <a name="before-922-versions"></a>Před verzemi 9.22

1. Odešle agenta do zdrojového počítače. Kopírování agenta do zdrojového počítače může selhat z důvodu několika chyb prostředí. Navštivte [naše pokyny](vmware-azure-troubleshoot-push-install.md) pro řešení potíží s chybami nabízené instalace.
2. Po úspěšném zkopírování agenta na server jsou na serveru provedeny kontroly požadavků. Instalace se nezdaří, pokud není splněn jeden nebo více [požadavků.](vmware-physical-azure-support-matrix.md) Pokud jsou splněny všechny požadavky, spustí se instalace.
3. Zprostředkovatel VSS azure site recovery je nainstalovaný na serveru jako součást instalace agenta mobility. Tento zprostředkovatel se používá ke generování konzistentní body aplikace. Pokud se instalace zprostředkovatele VSS nezdaří, instalace agenta se nezdaří. Chcete-li se vyhnout selhání instalace agenta mobility, použijte [verzi 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) nebo vyšší ke generování bodů konzistentních selhání a instalaci zprostředkovatele VSS ručně.

## <a name="install-mobility-agent-through-ui"></a>Instalace agenta mobility prostřednictvím ui

### <a name="prerequisite"></a>Požadavek

- Ujistěte se, že všechny konfigurace serverů spadají pod [matici podpory scénáře VMware do Azure ZOTAVENÍ](vmware-physical-azure-support-matrix.md).
- [Vyhledejte instalační program](#locate-installer-files) na základě operačního systému serveru.

>[!IMPORTANT]
> Pokud replikujete virtuální počítač Azure IaaS z jedné oblasti Azure do jiné, nepoužívejte tuto metodu. Místo toho použijte metodu instalace založenou na příkazovém řádku.

1. Zkopírujte instalační soubor do počítače a spusťte jej.
2. V **části Možnost instalace**vyberte možnost Instalovat **službu mobility**.
3. Vyberte umístění instalace > **Instalace**.

    ![Stránka možností instalace služby Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Sledujte instalaci v **průběhu instalace**. Po dokončení instalace vyberte **možnost Přejít ke konfiguraci** a zaregistrujte službu u konfiguračního serveru.

    ![Registrační stránka služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. V **poli Podrobnosti konfiguračního serveru**zadejte nakonfigurovanou adresu IP a přístupové heslo.

    ![Registrační stránka služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Chcete-li registraci dokončit, vyberte **možnost Registrovat.**

    ![Závěrečná stránka registrace služby Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instalace agenta mobility prostřednictvím příkazového řádku

### <a name="prerequisite"></a>Požadavek

- Ujistěte se, že všechny konfigurace serverů spadají pod [matici podpory scénáře VMware do Azure ZOTAVENÍ](vmware-physical-azure-support-matrix.md).
- [Vyhledejte instalační program](#locate-installer-files) na základě operačního systému serveru.

### <a name="on-a-windows-machine"></a>Na počítači se systémem Windows

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
Využití | UnifiedAgent.exe /Role \<MS/MT> \</InstallLocation Install Location> /Platform "VmWare" /Silent
Instalační protokoly | V části %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Povinný parametr instalace. Určuje, zda má být nainstalována služba Mobility (MS) nebo hlavní cíl (MT).
/InstallLocation| Volitelný parametr. Určuje umístění instalace služby Mobility (libovolnou složku).
/Platforma | Povinné. Určuje platformu, na které je služba Mobility Service nainstalována. **VMware** pro virtuální zařízení VMware/fyzické servery; **Virtuální** počítače Azure pro Azure.<br/><br/> Pokud virtuální počítače Azure považujete za fyzické počítače, zadejte **VMware**.
/Silent| Nepovinný parametr. Určuje, zda má být instalační program spuštěn v tichém režimu.

#### <a name="registration-settings"></a>Nastavení registrace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>
Protokoly konfigurace agenta | V části %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Povinný parametr. Určuje adresu IP konfiguračního serveru. Použijte libovolnou platnou adresu IP.
/PassphraseFilePath |  Povinné. Umístění přístupového slova. Použijte libovolnou platnou cestu UNC nebo místního souboru.

### <a name="on-a-linux-machine"></a>Na linuxovém počítači

1. Zkopírujte instalační program do místní složky (například /tmp) na serveru, který chcete chránit. V terminálu spusťte následující příkazy:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Nainstalujte následujícím způsobem:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Po dokončení instalace musí být služba Mobility Service zaregistrována na konfiguračním serveru. Chcete-li zaregistrovat službu Mobility Service u konfiguračního serveru, spusťte následující příkaz:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Nastavení instalace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | ./install -d \<Instalace umístění \<> -r MS/MT> -v VmWare -q
-r | Povinný parametr instalace. Určuje, zda má být nainstalována služba Mobility (MS) nebo hlavní cíl (MT).
-d | Volitelný parametr. Určuje umístění instalace služby Mobility: /usr/local/ASR.
-v | Povinné. Určuje platformu, na které je služba Mobility Service nainstalována. **VMware** pro virtuální zařízení VMware/fyzické servery; **Virtuální** počítače Azure pro Azure.
-q | Nepovinný parametr. Určuje, zda má být instalační program spuštěn v tichém režimu.

#### <a name="registration-settings"></a>Nastavení registrace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP \<> -P PassphraseFilePath>
-i | Povinný parametr. Určuje adresu IP konfiguračního serveru. Použijte libovolnou platnou adresu IP.
-P |  Povinné. Úplná cesta k souboru, do kterého je přístupové heslo uloženo. Použijte libovolnou platnou složku.

## <a name="azure-virtual-machine-agent"></a>Agent virtuálního počítače Azure

- **Virtuální počítače s Windows**: Od verze 9.7.0.0 služby Mobility je [agent virtuálního počítače Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) nainstalovaný instalačním programem služby Mobility. Tím zajistíte, že když počítač převezme služby při selhání do Azure, virtuální počítač Azure splňuje předpoklad instalace agenta pro použití libovolné horozšíření virtuálního počítače.
- **Virtuální počítače s Linuxem**: [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) musí být nainstalován ručně na virtuálním počítači Azure po převzetí služeb při selhání.

## <a name="locate-installer-files"></a>Vyhledání souborů instalačního programu

Na konfiguračním serveru přejděte do složky %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository. Zkontrolujte, který instalační program potřebujete na základě operačního systému. Následující tabulka shrnuje soubory instalačního programu pro každý virtuální virtuální v> VM vsystému VM ware a operační systém fyzického serveru. Před spuštěním si můžete prohlédnout [podporované operační systémy.](vmware-physical-azure-support-matrix.md#replicated-machines)

**Instalační soubor** | **Operační systém (pouze 64bitový)**
--- | ---
Microsoft-ASR\_\*UA\*Windows release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> Centos 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> Centos 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*Debian8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Další kroky

[Nastavte push instalaci pro službu Mobility](vmware-azure-install-mobility-service.md).
