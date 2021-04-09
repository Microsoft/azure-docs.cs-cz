---
title: Automatizovaná služba mobility pro zotavení po havárii při instalaci v Azure Site Recovery
description: Jak automaticky nainstalovat službu mobility pro zotavení po havárii VMware/Physical serveru pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: 2159ab8c2639f0f87fd53e8559dad518a3daa663
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92544813"
---
# <a name="automate-mobility-service-installation"></a>Automatizace instalace služby mobility

Tento článek popisuje, jak automatizovat instalaci a aktualizace pro agenta služby mobility v [Azure Site Recovery](site-recovery-overview.md).

Když nasadíte Site Recovery pro zotavení po havárii místních virtuálních počítačů VMware a fyzických serverů do Azure, nainstalujete agenta služby mobility na každý počítač, který chcete replikovat. Služba mobility zachycuje zápisy dat na počítači a předá je do Site Recovery procesového serveru pro replikaci. Službu mobility můžete nasadit několika způsoby:

- **Nabízená instalace**: umožňuje Site Recovery nainstalovat agenta služby mobility, když povolíte replikaci pro počítač v Azure Portal.
- **Ruční instalace**: Nainstalujte službu mobility ručně na každý počítač. [Přečtěte si další informace](vmware-physical-mobility-service-overview.md) o nabízených a ručních instalacích.
- **Automatizované nasazení**: automatizujte instalaci pomocí nástrojů pro nasazení softwaru, jako je Microsoft Endpoint Configuration Manager nebo nástrojů třetích stran, jako je JetPatch.

Automatizovaná instalace a aktualizace poskytuje řešení v těchto případech:

- Vaše organizace neumožňuje nabízenou instalaci na chráněných serverech.
- Zásady vaší společnosti vyžadují pravidelné změny hesel. Je nutné zadat heslo pro nabízenou instalaci.
- Vaše zásady zabezpečení nedovolují přidávání výjimek brány firewall pro konkrétní počítače.
- Pracujete jako poskytovatel hostingové služby a nechcete poskytnout přihlašovací údaje počítače zákazníka potřebné pro nabízenou instalaci s Site Recovery.
- Je potřeba škálovat instalace agenta na hodně serverů současně.
- Chcete naplánovat instalace a upgrady během plánovaných časových období údržby.

## <a name="prerequisites"></a>Požadavky

K automatizaci instalace potřebujete následující položky:

- Nasazené řešení instalace softwaru, například [Configuration Manager](/configmgr/) nebo [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Požadavky na nasazení, které jsou v [Azure](tutorial-prepare-azure.md) [a místní pro](vmware-azure-tutorial-prepare-on-premises.md) zotavení po havárii VMware, nebo pro zotavení po havárii [fyzického serveru](physical-azure-disaster-recovery.md) . Projděte si [požadavky na podporu](vmware-physical-azure-support-matrix.md) pro zotavení po havárii.

## <a name="prepare-for-automated-deployment"></a>Příprava na automatizované nasazení

Následující tabulka shrnuje nástroje a procesy pro automatizaci nasazení služby mobility.

**Nástroj** | **Podrobnosti** | **Pokyny**
--- | --- | ---
**Configuration Manager** | 1. Ověřte, že jsou splněné [požadavky](#prerequisites) uvedené výše. <br/><br/> 2. Nasaďte zotavení po havárii nastavením zdrojového prostředí, včetně stažení souboru s VAJÍČKy k nasazení Site Recovery konfiguračního serveru jako virtuálního počítače VMware pomocí šablony OVF.<br/><br/> 3. zaregistrujete konfigurační server pomocí služby Site Recovery, nastavíte cílové prostředí Azure a nakonfigurujete zásady replikace.<br/><br/> 4. pro automatizované nasazení služby mobility vytvoříte síťovou sdílenou složku obsahující heslo konfigurační server a instalační soubory služby mobility.<br/><br/> 5. vytvoříte balíček Configuration Manager obsahující instalaci nebo aktualizace a připravíte nasazení služby mobility.<br/><br/> 6. potom můžete povolit replikaci do Azure pro počítače, ve kterých je nainstalovaná služba mobility. | [Automatizace pomocí Configuration Manager](#automate-with-configuration-manager)
**JetPatch** | 1. Ověřte, že jsou splněné [požadavky](#prerequisites) uvedené výše. <br/><br/> 2. Nasaďte zotavení po havárii nastavením zdrojového prostředí, včetně stažení a nasazení správce agentů JetPatch pro Azure Site Recovery ve vašem Site Recovery prostředí pomocí šablony OVF.<br/><br/> 3. zaregistrujete konfigurační server pomocí Site Recovery, nastavíte cílové prostředí Azure a nakonfigurujete zásady replikace.<br/><br/> 4. pro automatizované nasazení inicializujte a dokončete konfiguraci správce agentů JetPatch.<br/><br/> 5. v JetPatch můžete vytvořit zásady Site Recovery pro automatizaci nasazení a upgradu agenta služby mobility. <br/><br/> 6. potom můžete povolit replikaci do Azure pro počítače, ve kterých je nainstalovaná služba mobility. | [Automatizace pomocí Správce agenta JetPatch](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Řešení potíží s instalací agenta v JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatizace pomocí Configuration Manager

### <a name="prepare-the-installation-files"></a>Příprava instalačních souborů

1. Ujistěte se, že máte zavedené požadavky.
1. Vytvořte zabezpečenou síťovou sdílenou složku (SMB), ke které se dá dostat z počítače, na kterém běží konfigurační server.
1. V Configuration Manager [zařaďte do kategorií servery](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) , na kterých chcete nainstalovat nebo aktualizovat službu mobility. Jedna kolekce by měla obsahovat všechny servery Windows, ostatní všechny servery Linux.
1. Ve sdílené síťové složce vytvořte složku:

   - Pro instalaci na počítačích s Windows vytvořte složku s názvem _MobSvcWindows_.
   - Pro instalaci na počítače se systémem Linux vytvořte složku s názvem _MobSvcLinux_.

1. Přihlaste se k počítači konfiguračního serveru.
1. Na počítači konfiguračního serveru otevřete příkazový řádek pro správu.
1. Chcete-li vygenerovat soubor s heslem, spusťte tento příkaz:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Zkopírujte soubor _MobSvc. hesla_ do složky Windows a do složky Linux.
1. Chcete-li přejít do složky, která obsahuje instalační soubory, spusťte tento příkaz:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Zkopírujte tyto instalační soubory do sdílené síťové složky:

   - Pro Windows zkopírujte _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ do _MobSvcWindows_.
   - Pro Linux zkopírujte následující soubory do _MobSvcLinux_:
     - _Microsoft-ASR_UARHEL6 -64release. tar. gz_
     - _Microsoft-ASR_UARHEL7 -64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP3-64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP4-64release. tar. gz_
     - _Microsoft-ASR_UAOL6 -64release. tar. gz_
     - _Microsoft-ASR_UAUBUNTU-14.04 -64release. tar. gz_

1. Jak je popsáno v následujících postupech, zkopírujte kód do složky systému Windows nebo Linux. Předpokládáme, že:

   - IP adresa konfiguračního serveru je `192.168.3.121` .
   - Zabezpečená síťová sdílená složka je `\\ContosoSecureFS\MobilityServiceInstallers` .

### <a name="copy-code-to-the-windows-folder"></a>Zkopírovat kód do složky Windows

Zkopírujte následující kód:

- Uložte kód ve složce _MobSvcWindows_ jako _install.bat_.
- Nahraďte `[CSIP]` zástupné symboly v tomto skriptu skutečnými hodnotami IP adresy konfiguračního serveru.
- Skript podporuje nové instalace agenta služby mobility a aktualizuje agenty, které jsou už nainstalované.

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

### <a name="copy-code-to-the-linux-folder"></a>Kopírovat kód do složky Linux

Zkopírujte následující kód:

- Uložte kód ve složce _MobSvcLinux_ jako _install_linux. sh_.
- Nahraďte `[CSIP]` zástupné symboly v tomto skriptu skutečnými hodnotami IP adresy konfiguračního serveru.
- Skript podporuje nové instalace agenta služby mobility a aktualizuje agenty, které jsou už nainstalované.

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

1. Přihlaste se ke konzole Configuration Manager a pokračujte na  >  balíčky pro **správu aplikací** knihovny softwaru  >  .
1. Klikněte pravým tlačítkem na **balíčky**  >  **vytvořit balíček**.
1. Zadejte podrobnosti balíčku, včetně názvu, popisu, výrobce, jazyka a verze.
1. Vyberte **Tento balíček obsahuje zdrojové soubory**.
1. Klikněte na tlačítko **Procházet** a vyberte sdílenou síťovou složku a složku obsahující příslušný instalační program (_MobSvcWindows_ nebo _MobSvcLinux_). Pak vyberte **Další**.

   ![Snímek obrazovky Průvodce vytvořením balíčku a programu](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. Na stránce **Zvolte typ programu, který chcete vytvořit** vyberte možnost **standardní program**  >  **Další**.

   ![Snímek obrazovky Průvodce vytvořením balíčku a programu, který zobrazuje standardní možnost programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. Na stránce **Zadejte informace o tomto standardním programu** zadejte následující hodnoty:

    **Parametr** | **Hodnota Windows** | **Hodnota Linux**
    --- | --- | ---
    **Název** | Instalace služby mobility Microsoft Azure (Windows) | Nainstalujte službu Microsoft Azure mobility (Linux).
    **Příkazový řádek** | install.bat | ./install_linux. sh
    **Program lze spustit** | Bez ohledu na to, jestli je uživatel přihlášený | Bez ohledu na to, jestli je uživatel přihlášený
    **Další parametry** | Použít výchozí nastavení | Použít výchozí nastavení

   ![Snímek obrazovky, který zobrazuje informace, které můžete zadat pro standardní program.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. V části **Zadejte požadavky pro tento standardní program** proveďte následující úlohy:

   - V případě počítačů se systémem Windows vyberte možnost **Tento program může běžet pouze na zadaných platformách**. Pak vyberte [podporované operační systémy Windows](vmware-physical-azure-support-matrix.md#replicated-machines) a vyberte **Další**.
   - V případě počítačů se systémem Linux vyberte možnost **Tento program může běžet na libovolné platformě**. Pak vyberte **Další**.

1. Dokončete průvodce.

### <a name="deploy-the-package"></a>Nasazení balíčku

1. V konzole Configuration Manager klikněte pravým tlačítkem na balíček a vyberte **distribuovat obsah**.

   ![Snímek obrazovky s konzolou Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Vyberte distribuční body, do kterých mají být balíčky zkopírovány. [Další informace](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Dokončete průvodce. Balíček se pak začne replikovat do zadaných distribučních bodů.
1. Po dokončení distribuce balíčku klikněte pravým tlačítkem na balíček > **nasadit**.

   ![Snímek obrazovky konzoly Configuration Manager zobrazující možnost nabídky nasadit](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Vyberte kolekci zařízení se systémem Windows nebo Linux, kterou jste vytvořili dříve.
1. Na stránce **Zadejte cíl obsahu** vyberte možnost **distribuční body**.
1. V **Možnosti zadejte nastavení pro řízení způsobu nasazení tohoto softwaru** nastavte **účel** na **požadováno**.

   ![Snímek obrazovky Průvodce nasazením softwaru](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. V **části zadejte plán pro toto nasazení** nastavte plán. [Další informace](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - Služba mobility se nainstaluje podle zadaného plánu.
   - Aby nedocházelo k zbytečnému restartování, naplánujte instalaci balíčku během měsíčního časového období údržby nebo v okně aktualizace softwaru.

1. Na stránce **distribuční body** nakonfigurujte nastavení a dokončete průvodce.
1. Sledujte průběh nasazení v konzole Configuration Manager. Přejít na **monitorování**  >  **nasazení**  >  _\<your package name\>_ .

### <a name="uninstall-the-mobility-service"></a>Odinstalace služby mobility

Můžete vytvořit balíčky Configuration Manager pro odinstalaci služby mobility. Například následující skript odinstaluje službu mobility:

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

[Povolte replikaci](vmware-azure-enable-replication.md) pro virtuální počítače.
