---
title: Informace o službě Mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů s Azure Site Recovery | Dokumentace Microsoftu
description: Další informace o agenta služby Mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 929a4e4366c9e94ed4e1915406914991624f6baa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565557"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informace o službě Mobility pro virtuální počítače VMware a fyzické servery

Při nastavení zotavení po havárii pro virtuální počítače VMware a fyzických serverů požíváním [Azure Site Recovery](site-recovery-overview.md), instalace služby Site Recovery Mobility na každý místní virtuální počítač VMware a fyzických serverů.  Služba Mobility zaznamenává datové zápisy na počítači a předává je na procesní server Site Recovery. Můžete nasadit službu Mobility pomocí následujících metod:

- [Nabízená instalace](#push-installation): Při povolení ochrany prostřednictvím portálu Azure Site Recovery nainstaluje agenta mobility na serveru.
- Ruční instalace: Můžete nainstalovat službu Mobility ručně na každém počítači prostřednictvím [uživatelského rozhraní](#install-mobility-agent-through-ui) nebo [příkazový řádek](#install-mobility-agent-through-command-prompt).
- [Automatické nasazení](vmware-azure-mobility-install-configuration-mgr.md): Můžete automatizovat instalaci pomocí nástrojů pro nasazení softwaru jako je System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Antivirový program replikované počítače

Pokud chcete replikovat počítače aktivní antivirový software spuštěný, ujistěte se, vyloučit složky instalace služby Mobility z operace antivirový program (*C:\ProgramData\ASR\agent*). Tím se zajistí, že replikace pracuje podle očekávání.

## <a name="push-installation"></a>Nabízená instalace

Nabízená instalace je nedílnou součástí "[povolením replikace](vmware-azure-enable-replication.md#enable-replication)" úloha spuštěna na portálu. Po zvolení sadu virtuálních počítačů, které chcete chránit a aktivovat "Povolit replikaci" konfiguračního serveru agenta mobility k nim, nabízených oznámení, nainstaluje agenta a dokončení registrace agenta pomocí konfiguračního serveru. Pro úspěšné dokončení této operace

- Ujistěte se, že všechny nabízená instalace [požadavky](vmware-azure-install-mobility-service.md) jsou splněny.
- Ujistěte se, že všechny konfigurace serverů spadají pod [systém podpory replikace z VMware na scénář zotavení po Havárii Azure](vmware-physical-azure-support-matrix.md).

Podrobnosti o nabízenou instalaci pracovního postupu je popsán v následujících částech.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Z [9.23 verze](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) a vyšší

Během nabízená instalace agenta mobility následující kroky se provádějí

1. Agent nabízených oznámení na zdrojový počítač. Kopírování agenta do zdrojového počítače může selhat kvůli několika chybám prostředí. Navštivte [naše doprovodné materiály](vmware-azure-troubleshoot-push-install.md) k řešení potíží se selháním nabízené instalace.
2. Poté, co agent byl úspěšně zkopírován do kontroly splnění podmínek na serveru se provádí na serveru. Instalace selže, pokud jeden nebo více následujících [požadavky](vmware-physical-azure-support-matrix.md) nejsou splněné. Pokud jsou splněny všechny požadavky, instalace se aktivuje.
3. Azure Site Recovery VSS provider nainstalovaný na serveru jako součást instalace agenta Mobility. Tento zprostředkovatel slouží ke generování konzistentní body aplikací. V případě selhání instalace zprostředkovatele služby VSS, tento krok se přeskočí a bude pokračovat v instalaci agenta.
4. Pokud bude úspěšné instalace agenta, ale instalace zprostředkovatele služby VSS se nezdaří, stav úlohy je označena jako "Upozornění". To ale nijak neovlivní generování body selhání konzistence.

    a. Generovat žádosti konzistentní body, najdete v tématu [naše doprovodné materiály](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) pro dokončení instalace nástroje Site Recovery VSS provider ručně. </br>
    b.  Pokud nechcete, aby byla konzistentní vzhledem k aplikacím body aplikace chcete vygenerovat, [zásady replikace změnit](vmware-azure-set-up-replication.md#create-a-policy) vypnout konzistentní body aplikací.

### <a name="before-922-versions"></a>Před 9.22 verze

1. Agent nabízených oznámení na zdrojový počítač. Kopírování agenta do zdrojového počítače může selhat kvůli několika chybám prostředí. Navštivte [naše doprovodné materiály](vmware-azure-troubleshoot-push-install.md) k řešení potíží se selháním nabízené instalace.
2. Poté, co agent byl úspěšně zkopírován do kontroly splnění podmínek na serveru se provádí na serveru. Instalace selže, pokud jeden nebo více následujících [požadavky](vmware-physical-azure-support-matrix.md) nejsou splněné. Pokud jsou splněny všechny požadavky, instalace se aktivuje.
3. Azure Site Recovery VSS provider nainstalovaný na serveru jako součást instalace agenta Mobility. Tento zprostředkovatel slouží ke generování konzistentní body aplikací. V případě selhání instalace zprostředkovatele služby VSS se nezdaří instalace agenta. Chcete-li zabránit selhání instalace agenta mobility, použijte [9.23 verze](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) nebo vyšší konzistentní pro případ chyby body vygenerovat a nainstalovat poskytovatele služby VSS ručně.

## <a name="install-mobility-agent-through-ui"></a>Instalace agenta mobility pomocí uživatelského rozhraní

### <a name="prerequisite"></a>Požadavek

- Ujistěte se, že všechny konfigurace serverů spadají pod [systém podpory replikace z VMware na scénář zotavení po Havárii Azure](vmware-physical-azure-support-matrix.md).
- [Vyhledejte instalační program](#locate-installer-files) založený na operačním systému serveru.

>[!IMPORTANT]
> Pokud replikujete virtuální počítač Azure IaaS z jedné oblasti Azure do jiné, nepoužívejte tuto metodu. Místo toho použijte metodu instalace na základě na příkazovém řádku.

1. Zkopírujte instalační soubor do počítače a spustíme ji.
2. V **možnost instalace**vyberte **nainstalovat službu mobility**.
3. Zvolte umístění instalace > **nainstalovat**.

    ![Stránka možnost instalace služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Sledování instalace v **průběh instalace**. Po dokončení instalace, vybrat **pokračovat na konfiguraci** zaregistrovat službu s konfiguračním serverem.

    ![Stránce pro registraci služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. v **podrobnosti konfiguračního serveru**, zadejte IP adresu a heslo, které jste nakonfigurovali.  

    ![Stránce pro registraci služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Vyberte **zaregistrovat** dokončete registraci.

    ![Poslední stránce pro registraci služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instalace agenta mobility prostřednictvím příkazového řádku

### <a name="prerequisite"></a>Požadavek

- Ujistěte se, že všechny konfigurace serverů spadají pod [systém podpory replikace z VMware na scénář zotavení po Havárii Azure](vmware-physical-azure-support-matrix.md).
- [Vyhledejte instalační program](#locate-installer-files) založený na operačním systému serveru.

### <a name="on-a-windows-machine"></a>Na počítači s Windows

- Zkopírujte instalační program k místní složce (třeba C:\Temp) na serveru, který chcete chránit.

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

- Registrace agenta s konfiguračním serverem.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Nastavení instalace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | UnifiedAgent.exe/role < MS/MT > /InstallLocation  <Install Location> /silent "VmWare" / Platform
Instalační protokoly | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/ Role | Instalace povinných parametrů. Určuje, zda by měla nainstalovat služba Mobility (MS) nebo hlavní cíl (MT).
/InstallLocation| Volitelný parametr. Určuje umístění instalace služby Mobility (libovolné složky).
/ Platform | Povinné. Určuje platformu, na kterém je nainstalovaná služba Mobility. **VMware** pro virtuální počítače VMware a fyzických serverů. **Azure** pro virtuální počítače Azure.
/ Silent| Volitelné. Určuje, jestli ke spuštění instalačního programu v bezobslužném režimu.

#### <a name="registration-settings"></a>Nastavení registrace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | / Csendpoint UnifiedAgentConfigurator.exe \<CSIP >/passphrasefilepath \<PassphraseFilePath >
Protokoly konfigurace agenta | V části % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte libovolná platná IP adresa.
/PassphraseFilePath |  Povinné. Umístění přístupové heslo. Použijte libovolný platný název UNC nebo místní cesta k souboru.

### <a name="on-a-linux-machine"></a>Na počítači s Linuxem

1. Zkopírujte instalační program do místní složky (například TMP) na serveru, který chcete chránit. V terminálu spusťte následující příkazy:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Nainstalujte následujícím způsobem:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Po dokončení instalace služby Mobility, musí být zaregistrovaná ke konfiguračnímu serveru. Spusťte následující příkaz pro registraci služby Mobility s konfiguračním serverem:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Nastavení instalace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | ./install -d <Install Location> -r <MS/MT> -v VmWare -q
-r | Instalace povinných parametrů. Určuje, zda by měla nainstalovat služba Mobility (MS) nebo hlavní cíl (MT).
-d | Volitelný parametr. Určuje umístění instalace služby Mobility: /usr/local/ASR.
-v | Povinné. Určuje platformu, na kterém je nainstalovaná služba Mobility. **VMware** pro virtuální počítače VMware a fyzických serverů. **Azure** pro virtuální počítače Azure.
-q | Volitelné. Určuje, jestli ke spuštění instalačního programu v bezobslužném režimu.

#### <a name="registration-settings"></a>Nastavení registrace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>
-i | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte libovolná platná IP adresa.
-P |  Povinné. Úplnou cestu souboru, ve kterém se heslo uloží. Použijte libovolnou platnou složku.

## <a name="azure-virtual-machine-agent"></a>Agent virtuálního počítače Azure

- **Virtuální počítače s Windows**: Oproti 9.7.0.0 verzi služby Mobility [agenta virtuálního počítače Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) nainstalovaného pomocí Instalační program služby Mobility. Tím se zajistí, že pokud je počítač převezme služby při selhání do Azure, virtuálního počítače Azure splňuje pro všechna rozšíření virtuálních počítačů pomocí požadovaných součástí instalace agenta.
- **Virtuální počítače s Linuxem**: [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) musí ručně doinstalovat na virtuálním počítači Azure po převzetí služeb při selhání.

## <a name="locate-installer-files"></a>Vyhledejte soubory Instalační služby systému

Přejděte do složky %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository na konfiguračním serveru. Zkontrolujte, které instalační program je nutné podle operačního systému. Následující tabulka shrnuje soubory instalačního programu pro každý virtuální počítač VMware a fyzických serverů operačního systému. Můžete zkontrolovat [podporované operační systémy](vmware-physical-azure-support-matrix.md#replicated-machines) před zahájením.

**Soubor Instalační služby systému** | **Operační systém (pouze 64bitové verze)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2 SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Další postup

[Nastavit nabízená instalace služby Mobility](vmware-azure-install-mobility-service.md).
