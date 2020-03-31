---
title: Automatizace služby mobility pro zotavení po havárii instalace v Azure Site Recovery
description: Jak automaticky nainstalovat službu mobility pro obnovu po havárii v systému VMware /fyzického serveru pomocí azure site recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252223"
---
# <a name="automate-mobility-service-installation"></a>Automatizace instalace služby Mobility Service

Tento článek popisuje, jak automatizovat instalaci a aktualizace pro agenta služby mobility v [Azure Site Recovery](site-recovery-overview.md).

Když nasadíte site recovery pro zotavení po havárii místních virtuálních počítačích VMware a fyzických serverů do Azure, nainstalujete agenta služby mobility do každého počítače, který chcete replikovat. Služba mobility zachytí zápisy dat v počítači a předá je serveru obnovení webu k replikaci. Službu mobility můžete nasadit několika způsoby:

- **Nabízená instalace**: Umožněte site recovery nainstalovat agenta služby Mobility, když povolíte replikaci pro počítač na webu Azure Portal.
- **Ruční instalace**: Nainstalujte službu Mobility ručně na každý počítač. [Přečtěte si další informace](vmware-physical-mobility-service-overview.md) o nabízení a ruční instalaci.
- **Automatické nasazení**: Automatizujte instalaci pomocí nástrojů pro nasazení softwaru, jako je microsoft endpoint configuration manager nebo nástroje jiných výrobců, jako je JetPatch.

Automatická instalace a aktualizace poskytují řešení, pokud:

- Vaše organizace neumožňuje nabízenou instalaci na chráněných serverech.
- Zásady společnosti vyžadují pravidelnou změnu hesel. Je nutné zadat heslo pro nabízenou instalaci.
- Zásady zabezpečení nepovolují přidávání výjimek brány firewall pro konkrétní počítače.
- Jednáte jako poskytovatel hostingových služeb a nechcete poskytnout pověření počítače zákazníka, které jsou potřebné pro nabízenou instalaci s site recovery.
- Je třeba škálovat instalace agentů na velké množství serverů současně.
- Chcete naplánovat instalace a upgrady během plánovaných oken údržby.

## <a name="prerequisites"></a>Požadavky

Chcete-li instalaci automatizovat, potřebujete následující položky:

- Nasazené řešení instalace softwaru, například [Configuration Manager](/configmgr/) nebo [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Předpoklady nasazení v [Azure](tutorial-prepare-azure.md) a [místně](vmware-azure-tutorial-prepare-on-premises.md) pro zotavení po havárii v systému VMware nebo pro zotavení [po havárii fyzického serveru.](physical-azure-disaster-recovery.md) Zkontrolujte požadavky na [podporu](vmware-physical-azure-support-matrix.md) pro zotavení po havárii.

## <a name="prepare-for-automated-deployment"></a>Příprava na automatizované nasazení

Následující tabulka shrnuje nástroje a procesy pro automatizaci nasazení služby Mobility Service.

**Nástroj** | **Podrobnosti** | **Pokyny**
--- | --- | ---
**Configuration Manager** | 1. Ověřte, zda máte výše uvedené [požadavky.](#prerequisites) <br/><br/> 2. Nasazení zotavení po havárii nastavením zdrojového prostředí, včetně stažení souboru OVA pro nasazení konfiguračního serveru site recovery jako virtuálního počítače VMware pomocí šablony OVF.<br/><br/> 3. Konfigurační server zaregistrujete službou Site Recovery, nastavíte cílové prostředí Azure a nakonfigurujete zásady replikace.<br/><br/> 4. Pro automatizované nasazení služby Mobility Service vytvoříte sdílenou síťovou složku obsahující přístupové heslo konfiguračního serveru a instalační soubory služby Mobility Service.<br/><br/> 5. Vytvoříte balíček nástroje Configuration Manager obsahující instalaci nebo aktualizace a připravíte se na nasazení služby Mobility Service.<br/><br/> 6. Potom můžete povolit replikaci do Azure pro počítače, které mají nainstalovanou službu mobility. | [Automatizace pomocí nástroje Configuration Manager](#automate-with-configuration-manager)
**JetPatch** | 1. Ověřte, zda máte výše uvedené [požadavky.](#prerequisites) <br/><br/> 2. Nasazení zotavení po havárii nastavením zdrojového prostředí, včetně stahování a nasazování JetPatch Agent Manager pro Azure Site Recovery v prostředí site recovery pomocí šablony OVF.<br/><br/> 3. Konfigurační server zaregistrujete pomocí site recovery, nastavíte cílové prostředí Azure a nakonfigurujete zásady replikace.<br/><br/> 4. Pro automatické nasazení inicializovat a dokončit konfiguraci JetPatch Agent Manager.<br/><br/> 5. V JetPatch můžete vytvořit zásady obnovení webu pro automatizaci nasazení a upgradu agenta služby mobility. <br/><br/> 6. Potom můžete povolit replikaci do Azure pro počítače, které mají nainstalovanou službu mobility. | [Automatizace pomocí správce agentů JetPatch](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Poradce při potížích s instalací agenta v jetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatizace pomocí nástroje Configuration Manager

### <a name="prepare-the-installation-files"></a>Příprava instalačních souborů

1. Ujistěte se, že máte předpoklady na místě.
1. Vytvořte zabezpečenou sdílenou síťovou složku (sdílenou složku SMB), ke které má počítač se systémem konfiguračního serveru přístup.
1. V nástroji Configuration Manager [zahrávat servery,](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) na které chcete nainstalovat nebo aktualizovat službu mobility. Jedna kolekce by měla obsahovat všechny servery Windows, druhá všechny servery Linux.
1. Ve sdílené síťové složce vytvořte složku:

   - Pro instalaci na počítačích se systémem Windows vytvořte složku s názvem _MobSvcWindows_.
   - Pro instalaci na linuxových počítačích vytvořte složku s názvem _MobSvcLinux_.

1. Přihlaste se k počítači konfiguračního serveru.
1. V počítači konfiguračního serveru otevřete příkazový řádek pro správu.
1. Chcete-li vygenerovat soubor hesel, spusťte tento příkaz:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Zkopírujte soubor _MobSvc.passphrase_ do složky Windows a linuxové složky.
1. Chcete-li přejít do složky obsahující instalační soubory, spusťte tento příkaz:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Zkopírujte tyto instalační soubory do sdílené síťové složky:

   - V systému Windows zkopírujte _program Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ do _programu MobSvcWindows_.
   - Pro Linux, zkopírujte následující soubory _mobsvcLinux_:
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. Jak je popsáno v následujících postupech, zkopírujte kód do složek systému Windows nebo Linux. Předpokládáme, že:

   - Adresa IP konfiguračního serveru je `192.168.3.121`.
   - Zabezpečená síťová `\\ContosoSecureFS\MobilityServiceInstallers`sdílená složka je .

### <a name="copy-code-to-the-windows-folder"></a>Kopírování kódu do složky Windows

Zkopírujte následující kód:

- Uložte kód ve složce _MobSvcWindows_ jako _install.bat_.
- Nahraďte `[CSIP]` zástupné symboly v tomto skriptu skutečnými hodnotami adresy IP konfiguračního serveru.
- Skript podporuje nové instalace agenta služby Mobility Service a aktualizace agentů, kteří jsou již nainstalováni.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Kopírování kódu do složky Linux

Zkopírujte následující kód:

- Uložte kód do složky _MobSvcLinux_ jako _install_linux.sh_.
- Nahraďte `[CSIP]` zástupné symboly v tomto skriptu skutečnými hodnotami adresy IP konfiguračního serveru.
- Skript podporuje nové instalace agenta služby Mobility Service a aktualizace agentů, kteří jsou již nainstalováni.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Vytvoření balíčku

1. Přihlaste se ke konzole Configuration Manager a přejděte na**balíčky**pro**správu** > aplikací **knihovny** > softwaru .
1. Klepněte pravým tlačítkem myši na **položku Balíčky** > **vytvořit balíček**.
1. Zadejte podrobnosti o balíčku včetně názvu, popisu, výrobce, jazyka a verze.
1. Vyberte **Tento balíček obsahuje zdrojové soubory**.
1. Klepněte na **tlačítko Procházet**a vyberte sdílenou síť ovou složku, která obsahuje příslušný instalační program (_MobSvcWindows_ nebo _MobSvcLinux_. Potom vyberte **Další**.

   ![Snímek obrazovky s průvodcem Vytvořit balíček a program](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. V **části Vyberte typ programu, který chcete vytvořit,** vyberte **možnost Standardní program** > **Další**.

   ![Snímek obrazovky s průvodcem Vytvořit balíček a program](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. V části Určit informace o této stránce **standardního programu** zadejte následující hodnoty:

    **Parametr** | **Hodnota systému Windows** | **Hodnota Linuxu**
    --- | --- | ---
    **Název** | Instalace služby Microsoft Azure Mobility Service (Windows) | Nainstalujte službu Microsoft Azure Mobility Service (Linux).
    **Příkazový řádek** | install.bat | ./install_linux.sh
    **Program lze spustit** | Zda je uživatel přihlášen | Zda je uživatel přihlášen
    **Další parametry** | Použít výchozí nastavení | Použít výchozí nastavení

   ![Snímek obrazovky s průvodcem Vytvořit balíček a program](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. V **části Určení požadavků pro tento standardní program**proveďte následující úkoly:

   - U počítačů se systémem Windows vyberte **možnost Tento program lze spustit pouze na určených platformách**. Potom vyberte [podporované operační systémy Windows](vmware-physical-azure-support-matrix.md#replicated-machines) a vyberte **Další**.
   - Pro linuxové počítače vyberte **Tento program lze spustit na libovolné platformě**. Pak vyberte **Další**.

1. Dokončete průvodce.

### <a name="deploy-the-package"></a>Nasazení balíčku

1. V konzole Configuration Manager klepněte pravým tlačítkem myši na balíček a vyberte **distribuovat obsah**.

   ![Snímek obrazovky konzoly Nástroje pro přizpůsobení](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Vyberte distribuční body, do kterých mají být balíčky zkopírovány. [Další informace](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Dokončete průvodce. Balíček se pak začne replikovat do zadaných distribučních bodů.
1. Po dokončení distribuce balíčku klepněte pravým tlačítkem myši na balíček > **deploy**.

   ![Snímek obrazovky konzoly Nástroje pro přizpůsobení](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Vyberte kolekci zařízení windows nebo linux, kterou jste vytvořili dříve.
1. Na stránce **Určení cíle obsahu** vyberte Distribuční **body**.
1. V **části Určení nastavení určujícízpůsob nasazení tohoto softwaru** nastavte možnost **Účel** na **Povinné**.

   ![Snímek obrazovky s průvodcem Nasazení softwaru](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. V **části Zadejte plán pro toto nasazení**nastavte plán. [Další informace](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - Služba mobility je nainstalována v souladu se zadaným plánem.
   - Chcete-li se vyhnout zbytečným restartováním, naplánujte instalaci balíčku během okna měsíční údržby nebo okna aktualizací softwaru.

1. Na stránce **Distribuční body** nakonfigurujte nastavení a dokončete průvodce.
1. Sledujte průběh nasazení v konzole nástroje Configuration Manager. Přejděte na **možnost Sledování** > **nasazení** > _\<názvu\>balíčku_.

### <a name="uninstall-the-mobility-service"></a>Odinstalace služby mobility

Můžete vytvořit balíčky nástroje Configuration Manager pro odinstalaci služby Mobility. Služba mobility například odinstaluje následující skript:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Další kroky

[Povolte replikaci](vmware-azure-enable-replication.md) pro virtuální chod.
