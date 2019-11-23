---
title: Automatizujte instalaci služby Azure Site Recovery mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí služby System Center Configuration Manager | Microsoft Docs
description: Tento článek vám pomůže automatizovat instalaci služby mobility s System Center Configuration Manager pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ee92ad6e0687018f69044bf3edde76b9f98cee52
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255586"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatizace instalace služby mobility pomocí System Center Configuration Manager

Služba mobility je nainstalovaná na virtuálních počítačích VMware a fyzických serverech, které chcete replikovat do Azure pomocí [Azure Site Recovery](site-recovery-overview.md)

V tomto článku najdete příklad toho, jak můžete pomocí System Center Configuration Manager nasadit službu Azure Site Recovery mobility na virtuálním počítači VMware. Použití nástroje pro nasazení softwaru, jako je Configuration Manager, má následující výhody:

* Naplánovat nové instalace a upgrady během plánovaného časového období údržby pro aktualizace softwaru
* Škálovat nasazení na stovky serverů současně

Tento článek používá System Center Configuration Manager 2012 R2 k předvedení aktivity nasazení. Předpokládáme, že používáte službu mobility verze **9.9.4510.1** nebo vyšší.

Alternativně můžete automatizovat instalaci služby mobility pomocí [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Požadavky

1. Nástroj pro nasazení softwaru, jako je například Configuration Manager, který je už ve vašem prostředí nasazený.
2. Měli byste vytvořit dvě [kolekce zařízení](https://technet.microsoft.com/library/gg682169.aspx), jednu pro všechny **servery s Windows**a další pro všechny **servery**se systémem Linux, které chcete chránit pomocí Site Recovery.
3. Konfigurační server, který je již zaregistrován v úložišti Recovery Services.
4. Zabezpečená síťová sdílená složka (sdílená složka SMB), ke které se dá přistupovat počítač nástroje Configuration Manager.

## <a name="deploy-on-windows-machines"></a>Nasazení na počítačích s Windows
> [!NOTE]
> V tomto článku se předpokládá, že je IP adresa konfiguračního serveru 192.168.3.121 a že je sdílená síťová složka zabezpečení \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Příprava nasazení
1. Vytvořte složku ve sdílené síťové složce a pojmenujte ji **MobSvcWindows**.
2. Přihlaste se ke konfiguračnímu serveru a otevřete příkazový řádek pro správu.
3. Spuštěním následujících příkazů vygenerujte soubor s heslem:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Zkopírujte soubor **MobSvc. hesla** do složky **MobSvcWindows** v síťové sdílené složce.
5. Spusťte následující příkaz a přejděte do úložiště instalačního programu na konfiguračním serveru:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Do složky **MobSvcWindows** ve sdílené síťové složce zkopírujte pomocí funkce **Microsoft-ASR\_UA\_*verze*\_Windows\_GA\_*Date*\_Release. exe** .
7. Zkopírujte následující kód a uložte ho jako **install. bat** do složky **MobSvcWindows** .

   > [!NOTE]
   > Nahraďte zástupné symboly [CSIP] v tomto skriptu skutečnými hodnotami IP adresy konfiguračního serveru.

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

### <a name="create-a-package"></a>Vytvoření balíčku

1. Přihlaste se ke konzole Configuration Manager.
2. Přejděte do **knihovny softwaru** > **balíčky** > **správy aplikací** .
3. Klikněte pravým tlačítkem na **balíčky**a vyberte **vytvořit balíček**.
4. Zadejte hodnoty pro název, popis, výrobce, jazyk a verzi.
5. Zaškrtněte políčko **Tento balíček obsahuje zdrojové soubory** .
6. Klikněte na **Procházet**a vyberte sdílenou síťovou složku, ve které je instalační program uložený (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

   ![Snímek obrazovky Průvodce vytvořením balíčku a programu](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Na stránce **Zvolte typ programu, který chcete vytvořit** vyberte možnost **standardní program**a klikněte na tlačítko **Další**.

   ![Snímek obrazovky Průvodce vytvořením balíčku a programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Na stránce **Zadejte informace o tomto standardním programu** zadejte následující vstupy a klikněte na **Další**. (Ostatní vstupy můžou používat výchozí hodnoty.)

   | **Název parametru** | **Hodnota** |
   |--|--|
   | Název | Instalace služby mobility Microsoft Azure (Windows) |
   | Příkazový řádek | install.bat |
   | Program lze spustit | Bez ohledu na to, jestli je uživatel přihlášený |

   ![Snímek obrazovky Průvodce vytvořením balíčku a programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Na další stránce vyberte cílové operační systémy. 
10. Průvodce dokončíte dvojím kliknutím na tlačítko **Další** .


> [!NOTE]
> Tento skript podporuje jak nové instalace agentů služby mobility, tak i aktualizace agentů, kteří jsou už nainstalovaná.

### <a name="deploy-the-package"></a>Nasadit balíček
1. V konzole Configuration Manager klikněte pravým tlačítkem myši na balíček a vyberte možnost **distribuovat obsah**.
   ![snímku Configuration Manager konzoly](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Vyberte **[distribuční body](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** , do kterých mají být balíčky zkopírovány.
3. Dokončete průvodce. Balíček se pak začne replikovat do zadaných distribučních bodů.
4. Po dokončení distribuce balíčku klikněte pravým tlačítkem na balíček a vyberte **nasadit**.
   ![snímku Configuration Manager konzoly](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Vyberte kolekci zařízení Windows serveru, kterou jste vytvořili v oddílu požadavky, jako cílovou kolekci pro nasazení.

   ![Snímek obrazovky Průvodce nasazením softwaru](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Na stránce **Zadejte cíl obsahu** vyberte **distribuční body**.
7. Na stránce **zadání nastavení pro řízení způsobu nasazení tohoto softwaru** se ujistěte, že je účel **vyžadován**.

   ![Snímek obrazovky Průvodce nasazením softwaru](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Na stránce **Zadejte plán pro toto nasazení** zadejte plán. Další informace najdete v tématu [plánování balíčků](https://technet.microsoft.com/library/gg682178.aspx).
9. Na stránce **distribuční body** nakonfigurujte vlastnosti podle potřeb vašeho datacentra. Potom dokončete průvodce.

> [!TIP]
> Aby nedocházelo k zbytečnému restartování, naplánujte instalaci balíčku během měsíčního časového období údržby nebo v okně aktualizace softwaru.

Průběh nasazení můžete monitorovat pomocí konzoly Configuration Manager. Přejít na **monitorování** > **nasazení** >  *[název balíčku]* .

  ![Snímek obrazovky s možností Configuration Manager pro monitorování nasazení](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Nasazení na počítačích se systémem Linux
> [!NOTE]
> V tomto článku se předpokládá, že je IP adresa konfiguračního serveru 192.168.3.121 a že je sdílená síťová složka zabezpečení \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Příprava nasazení
1. Vytvořte složku ve sdílené síťové složce a pojmenujte ji **MobSvcLinux**.
2. Přihlaste se ke konfiguračnímu serveru a otevřete příkazový řádek pro správu.
3. Spuštěním následujících příkazů vygenerujte soubor s heslem:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Zkopírujte soubor **MobSvc. hesla** do složky **MobSvcLinux** v síťové sdílené složce.
5. Spuštěním příkazu přejděte do úložiště instalačního programu na konfiguračním serveru:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Zkopírujte následující soubory do složky **MobSvcLinux** ve sdílené síťové složce:
   * Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Zkopírujte následující kód a uložte ho jako **install_linux. sh** do složky **MobSvcLinux** .
   > [!NOTE]
   > Nahraďte zástupné symboly [CSIP] v tomto skriptu skutečnými hodnotami IP adresy konfiguračního serveru.

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

1. Přihlaste se ke konzole Configuration Manager.
2. Přejděte do **knihovny softwaru** > **balíčky** > **správy aplikací** .
3. Klikněte pravým tlačítkem na **balíčky**a vyberte **vytvořit balíček**.
4. Zadejte hodnoty pro název, popis, výrobce, jazyk a verzi.
5. Zaškrtněte políčko **Tento balíček obsahuje zdrojové soubory** .
6. Klikněte na **Procházet**a vyberte sdílenou síťovou složku, ve které je instalační program uložený (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

   ![Snímek obrazovky Průvodce vytvořením balíčku a programu](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Na stránce **Zvolte typ programu, který chcete vytvořit** vyberte možnost **standardní program**a klikněte na tlačítko **Další**.

   ![Snímek obrazovky Průvodce vytvořením balíčku a programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Na stránce **Zadejte informace o tomto standardním programu** zadejte následující vstupy a klikněte na **Další**. (Ostatní vstupy můžou používat výchozí hodnoty.)

    | **Název parametru** | **Hodnota** |
   |--|--|
   | Název | Instalace služby mobility Microsoft Azure (Linux) |
   | Příkazový řádek | ./install_linux.sh |
   | Program lze spustit | Bez ohledu na to, jestli je uživatel přihlášený |

   ![Snímek obrazovky Průvodce vytvořením balíčku a programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. Na další stránce vyberte možnost **Tento program může běžet na libovolné platformě**.
   ![snímek obrazovky Průvodce vytvořením balíčku a programu](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Průvodce dokončíte dvojím kliknutím na tlačítko **Další** .

> [!NOTE]
> Tento skript podporuje jak nové instalace agentů služby mobility, tak i aktualizace agentů, kteří jsou už nainstalovaná.

### <a name="deploy-the-package"></a>Nasadit balíček
1. V konzole Configuration Manager klikněte pravým tlačítkem myši na balíček a vyberte možnost **distribuovat obsah**.
   ![snímku Configuration Manager konzoly](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Vyberte **[distribuční body](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** , do kterých mají být balíčky zkopírovány.
3. Dokončete průvodce. Balíček se pak začne replikovat do zadaných distribučních bodů.
4. Po dokončení distribuce balíčku klikněte pravým tlačítkem na balíček a vyberte **nasadit**.
   ![snímku Configuration Manager konzoly](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Vyberte kolekci zařízení serveru se systémem Linux, kterou jste vytvořili v oddílu požadavky, jako cílovou kolekci pro nasazení.

   ![Snímek obrazovky Průvodce nasazením softwaru](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Na stránce **Zadejte cíl obsahu** vyberte **distribuční body**.
7. Na stránce **zadání nastavení pro řízení způsobu nasazení tohoto softwaru** se ujistěte, že je účel **vyžadován**.

   ![Snímek obrazovky Průvodce nasazením softwaru](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Na stránce **Zadejte plán pro toto nasazení** zadejte plán. Další informace najdete v tématu [plánování balíčků](https://technet.microsoft.com/library/gg682178.aspx).
9. Na stránce **distribuční body** nakonfigurujte vlastnosti podle potřeb vašeho datacentra. Potom dokončete průvodce.

Služba mobility se nainstalují do kolekce zařízení serveru se systémem Linux podle plánu, který jste nakonfigurovali.


## <a name="uninstall-the-mobility-service"></a>Odinstalace služby mobility
Můžete vytvořit balíčky Configuration Manager pro odinstalaci služby mobility. K tomu použijte následující skript:

```
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
Nyní jste připraveni [Povolit ochranu](vmware-azure-enable-replication.md) pro virtuální počítače.
