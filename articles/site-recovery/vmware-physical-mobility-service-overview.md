---
title: O službě Mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů s Azure Site Recovery | Dokumenty společnosti Microsoft
description: Získejte informace o agentovi služby Mobility pro zotavení po havárii virtuálních počítačích VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259784"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>O službě Mobilita pro virtuální zařízení VMware a fyzické servery

Když nastavíte zotavení po havárii pro virtuální počítače VMware (VM) a fyzické servery pomocí [Azure Site Recovery](site-recovery-overview.md), nainstalujete službu Site Recovery Mobility do každého místního virtuálního počítače VMware a fyzického serveru. Služba Mobility zachytí zápisy dat v počítači a předá je na server procesu obnovení webu. Služba Mobility je nainstalována softwarem agenta služby Mobility, který můžete nasadit pomocí následujících metod:

- [Nabízená instalace](#push-installation): Pokud je ochrana povolena prostřednictvím portálu Azure, site recovery nainstaluje službu Mobility na server.
- Ruční instalace: Službu mobility můžete nainstalovat ručně na každý počítač prostřednictvím [uživatelského rozhraní (UI)](#install-the-mobility-service-using-ui) nebo [příkazového řádku](#install-the-mobility-service-using-command-prompt).
- [Automatické nasazení](vmware-azure-mobility-install-configuration-mgr.md): Můžete automatizovat instalaci služby Mobility pomocí nástrojů pro nasazení softwaru, jako je configuration manager.

> [!NOTE]
> Služba Mobility využívá přibližně 6%-10 % paměti na zdrojových počítačích pro virtuální počítače VMware nebo fyzické počítače.

## <a name="antivirus-on-replicated-machines"></a>Antivirus na replikovaných počítačích

Pokud počítače, které chcete replikovat, používají antivirový software, vylučte instalační složku služby Mobility _C:\ProgramData\ASR\agent_ z antivirových operací. Toto vyloučení zajišťuje, že replikace bude fungovat podle očekávání.

## <a name="push-installation"></a>Push instalace

Nabízená instalace je nedílnou součástí úlohy, která je spuštěna z portálu Azure [a povoluje replikaci](vmware-azure-enable-replication.md#enable-replication). Po výběru sady virtuálních počítačů, které chcete chránit a povolit replikaci, konfigurační server odešle agenta služby Mobility na servery, nainstaluje agenta a dokončí registraci agenta na konfiguračním serveru.

### <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jsou splněny všechny požadavky na [nabízenou](vmware-azure-install-mobility-service.md) instalaci.
- Ujistěte se, že všechny konfigurace serveru splňují kritéria v [matici podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure](vmware-physical-azure-support-matrix.md).

Pracovní postup nabízená instalace je popsán v následujících částech:

### <a name="mobility-service-agent-version-923-and-higher"></a>Agent služeb mobility verze 9.23 a vyšší

Další informace o verzi 9.23 najdete [v tématu Kumulativní aktualizace 35 pro Azure Site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Během nabízené instalace služby Mobility se provádějí následující kroky:

1. Agent je posunut do zdrojového počítače. Kopírování agenta do zdrojového počítače může selhat z důvodu několika chyb prostředí. Navštivte [naše pokyny](vmware-azure-troubleshoot-push-install.md) pro řešení potíží s chybami nabízené instalace.
1. Po úspěšném zkopírování agenta na server se na serveru provede kontrola předpokladů.
   - Pokud jsou splněny všechny požadavky, instalace začíná.
   - Instalace se nezdaří, pokud nejsou splněny jeden nebo více [požadavků.](vmware-physical-azure-support-matrix.md)
1. Jako součást instalace agenta je nainstalován zprostředkovatel služby Stínová kopie svazku (VSS) pro Azure Site Recovery. Zprostředkovatel VSS se používá ke generování bodů obnovení konzistentní ch od aplikace. Pokud instalace zprostředkovatele VSS selže, tento krok je přeskočen a instalace agenta pokračuje.
1. Pokud je instalace agenta úspěšná, ale instalace zprostředkovatele VSS se nezdaří, je stav úlohy označen jako **upozornění**. To nemá vliv na generování bodů obnovení konzistentní chod selhání.

    - Chcete-li generovat body obnovení konzistentní s aplikací, podívejte se do [našich pokynů](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) k dokončení ruční instalace poskytovatele obnovení webu VSS.
    - Pokud nechcete generovat body obnovení konzistentní s aplikací, [upravte zásady replikace](vmware-azure-set-up-replication.md#create-a-policy) a vypněte body obnovení konzistentní s aplikací.

### <a name="mobility-service-agent-version-922-and-below"></a>Agent služeb mobility verze 9.22 a nižší

1. Agent je posunut do zdrojového počítače. Kopírování agenta do zdrojového počítače může selhat z důvodu několika chyb prostředí. Podívejte se na [naše pokyny](vmware-azure-troubleshoot-push-install.md) k řešení potíží s chybami nabízené instalace.
1. Po úspěšném zkopírování agenta na server se na serveru provede kontrola předpokladů.
   - Pokud jsou splněny všechny požadavky, instalace začíná.
   - Instalace se nezdaří, pokud nejsou splněny jeden nebo více [požadavků.](vmware-physical-azure-support-matrix.md)

1. Jako součást instalace agenta je nainstalován zprostředkovatel služby Stínová kopie svazku (VSS) pro Azure Site Recovery. Zprostředkovatel VSS se používá ke generování bodů obnovení konzistentní ch od aplikace.
   - Pokud se instalace zprostředkovatele VSS nezdaří, instalace agenta se nezdaří. Chcete-li se vyhnout selhání instalace agenta, použijte [verzi 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) nebo vyšší ke generování bodů obnovení konzistentních při selhání a ruční instalaci zprostředkovatele VSS.

## <a name="install-the-mobility-service-using-ui"></a>Instalace služby Mobility pomocí ui

### <a name="prerequisites"></a>Požadavky

- Ujistěte se, že všechny konfigurace serveru splňují kritéria v [matici podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure](vmware-physical-azure-support-matrix.md).
- [Vyhledejte instalační program](#locate-installer-files) operačního systému serveru.

>[!IMPORTANT]
> Nepoužívejte metodu instalace uznat, pokud replikujete virtuální počítač Azure Infrastructure as a Service (IaaS) z jedné oblasti Azure do jiné. Použijte instalaci [příkazového řádku.](#install-the-mobility-service-using-command-prompt)

1. Zkopírujte instalační soubor do počítače a spusťte jej.
1. V **části Možnost instalace**vyberte možnost Instalovat **službu mobility**.
1. Zvolte umístění instalace a vyberte **Instalovat**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Stránka možnostinstalace služby mobility.":::

1. Sledujte instalaci v **průběhu instalace**. Po dokončení instalace vyberte **možnost Přejít ke konfiguraci** a zaregistrujte službu u konfiguračního serveru.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Registrační stránka služby mobility.":::

1. V **poli Podrobnosti konfiguračního serveru**zadejte nakonfigurovanou adresu IP a přístupové heslo.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Registrační stránka služby mobility.":::

1. Chcete-li registraci dokončit, vyberte **možnost Registrovat.**

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Závěrečná stránka registrace služby mobility.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Instalace služby Mobility pomocí příkazového řádku

### <a name="prerequisites"></a>Požadavky

- Ujistěte se, že všechny konfigurace serveru splňují kritéria v [matici podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure](vmware-physical-azure-support-matrix.md).
- [Vyhledejte instalační program](#locate-installer-files) operačního systému serveru.

### <a name="windows-machine"></a>Počítač se systémem Windows

- Z příkazového řádku spusťte následující příkazy a zkopírujte instalační program do místní složky, například _C:\Temp_, na serveru, který chcete chránit. Nahraďte název souboru instalačního programu skutečným názvem souboru.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Spusťte tento příkaz a nainstalujte agenta.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Spusťte tyto příkazy a zaregistrujte agenta u konfiguračního serveru.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Nastavení instalace

Nastavení | Podrobnosti
--- | ---
Syntaxe | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Instalační protokoly | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Povinný parametr instalace. Určuje, zda má být nainstalována služba Mobility (MS) nebo hlavní cíl (MT).
`/InstallLocation`| Volitelný parametr. Určuje umístění instalace služby Mobility (libovolnou složku).
`/Platform` | Povinné. Určuje platformu, na které je služba Mobility nainstalována: <br/> **VMware** pro virtuální zařízení VMware/fyzické servery. <br/> **Virtuální** počítače Azure pro Azure.<br/><br/> Pokud virtuální počítače Azure považujete za fyzické počítače, zadejte **VMware**.
`/Silent`| Nepovinný parametr. Určuje, zda má být instalační program spuštěn v tichém režimu.

#### <a name="registration-settings"></a>Nastavení registrace

Nastavení | Podrobnosti
--- | ---
Syntaxe | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Protokoly konfigurace agenta | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Povinný parametr. `<CSIP>`určuje adresu IP konfiguračního serveru. Použijte libovolnou platnou adresu IP.
`/PassphraseFilePath` |  Povinné. Umístění přístupového slova. Použijte libovolnou platnou cestu UNC nebo místního souboru.

### <a name="linux-machine"></a>Linux stroj

1. Z terminálové relace zkopírujte instalační program do místní složky, například _/tmp_ na serveru, který chcete chránit. Nahraďte název souboru instalačního programu skutečným názvem souboru distribuce Linux a spusťte příkazy.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Nainstalujte následujícím způsobem:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Po dokončení instalace musí být služba Mobility zaregistrována na konfiguračním serveru. Chcete-li zaregistrovat službu Mobility u konfiguračního serveru, spusťte následující příkaz.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Nastavení instalace

Nastavení | Podrobnosti
--- | ---
Syntaxe | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Povinný parametr instalace. Určuje, zda má být nainstalována služba Mobility (MS) nebo hlavní cíl (MT).
`-d` | Volitelný parametr. Určuje umístění instalace služby `/usr/local/ASR`Mobility: .
`-v` | Povinné. Určuje platformu, na které je služba Mobility nainstalována. <br/> **VMware** pro virtuální zařízení VMware/fyzické servery. <br/> **Virtuální** počítače Azure pro Azure.
`-q` | Nepovinný parametr. Určuje, zda má být instalační program spuštěn v tichém režimu.

#### <a name="registration-settings"></a>Nastavení registrace

Nastavení | Podrobnosti
--- | ---
Syntaxe | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Povinný parametr. `<CSIP>`určuje adresu IP konfiguračního serveru. Použijte libovolnou platnou adresu IP.
`-P` |  Povinné. Úplná cesta k souboru, do kterého je přístupové heslo uloženo. Použijte libovolnou platnou složku.

## <a name="azure-virtual-machine-agent"></a>Agent virtuálního počítače Azure

- **Virtuální počítače s Windows**: Od verze 9.7.0.0 služby Mobility je [agent virtuálního počítače Azure](/azure/virtual-machines/extensions/features-windows#azure-vm-agent) nainstalovaný instalačním programem služby Mobility. Tím zajistíte, že když počítač převezme služby při selhání do Azure, virtuální počítač Azure splňuje předpoklad instalace agenta pro použití libovolného rozšíření virtuálního počítače.
- **Virtuální počítače s Linuxem**: [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent) musí být nainstalován ručně na virtuálním počítači Azure po převzetí služeb při selhání.

## <a name="locate-installer-files"></a>Vyhledání souborů instalačního programu

Na konfiguračním serveru přejděte do složky _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_. Zkontrolujte, který instalační program potřebujete na základě operačního systému. Následující tabulka shrnuje soubory instalačního programu pro každý virtuální virtuální v> VM vsystému VM ware a operační systém fyzického serveru. Než začnete, můžete zkontrolovat [podporované operační systémy](vmware-physical-azure-support-matrix.md#replicated-machines).

> [!NOTE]
> Názvy souborů používají syntaxi zobrazenou v následující tabulce s _verzí_ a _datem_ jako zástupné symboly pro skutečné hodnoty. Skutečné názvy souborů budou vypadat podobně jako v těchto příkladech:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Instalační soubor | Operační systém (pouze 64bitový)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> Centos 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Obsahuje aktualizace SP2 a SP3.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS server
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>Další kroky

[Nastavte push instalaci pro službu Mobility](vmware-azure-install-mobility-service.md).
