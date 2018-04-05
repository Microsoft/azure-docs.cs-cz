---
title: Automatizaci instalace služby Mobility pro Azure Site Recovery pomocí System Center Configuration Manager | Microsoft Docs
description: Tento článek vám umožňuje automatizovat instalaci služby Mobility pomocí System Center Configuration Manager.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 8382fadc02a7e80b6f28bd777f423013aed9add3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatizaci instalace služby Mobility pomocí nástroje System Center Configuration Manager

Služba Mobility je nainstalovaná na virtuální počítače VMware a fyzické servery, které chcete replikovat do Azure pomocí [Azure Site Recovery](site-recovery-overview.md)

Tento článek obsahuje příklad, jak nasadit službu Mobility Azure Site Recovery na virtuálním počítači VMware pomocí System Center Configuration Manager. Pomocí nástroje pro nasazení softwaru, jako jsou nástroje Configuration Manager má následující výhody:

* Plánování nové instalace a upgrady během plánované údržby pro aktualizace softwaru
* Změnit měřítko nasazení stovky serverů současně

Tento článek používá System Center Configuration Manager 2012 R2 k předvedení aktivity nasazení. Jsme předpokládá, že používáte verzi **9.9.4510.1** nebo vyšší služby Mobility.

Alternativně můžete automatizovat instalace služby Mobility s [Azure Automation DSC ](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Požadavky

1. Nástroj nasazení softwaru, jako jsou nástroje Configuration Manager, který je už nasazená ve vašem prostředí.
2. Je třeba vytvořit dva [kolekce zařízení](https://technet.microsoft.com/library/gg682169.aspx), jeden pro všechny **servery Windows**a druhou pro všechny **servery se systémem Linux**, které chcete chránit pomocí Site Recovery.
3. Konfigurační server, který už je zaregistrovaný v trezoru služeb zotavení.
4. Zabezpečené síťové sdílené složky (sdílená složka protokolu SMB), který je přístupný pro počítač. configuration manager.

## <a name="deploy-on-windows-machines"></a>Nasazení na počítače s Windows
> [!NOTE]
> Tento článek předpokládá, že je IP adresa serveru konfigurace 192.168.3.121 a zda zabezpečené sdílení souborů \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Příprava nasazení
1. Vytvořte složku ve sdílené síťové složce a pojmenujte ji **MobSvcWindows**.
2. Přihlaste se k konfigurační server a otevřete příkazový řádek pro správu.
3. Spusťte následující příkazy ke generování souboru přístupové heslo:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopírování **MobSvc.passphrase** soubor do **MobSvcWindows** složky do sdílené síťové složky.
5. Přejděte do instalačního programu úložiště na konfiguračním serveru tak, že spustíte následující příkaz:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Kopírování **Microsoft automatické obnovení systému\_uživatelský Agent\_*verze*\_Windows\_GA\_*datum*\_Release.exe**  k **MobSvcWindows** složky do sdílené síťové složky.
7. Zkopírujte následující kód a uložte ho jako **install.bat** do **MobSvcWindows** složky.

   > [!NOTE]
   > Nahraďte zástupné symboly [CSIP] ve skriptu skutečnými hodnotami IP adresy konfigurační server.

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

1. Přihlaste se ke konzole nástroje Configuration Manager.
2. Přejděte do **softwarová knihovna** > **Správa aplikací** > **balíčky**.
3. Klikněte pravým tlačítkem na **balíčky**a vyberte **vytvořit balíček**.
4. Zadejte hodnoty pro název, popis, výrobce, jazyk a verzi.
5. Vyberte **tento balíček obsahuje zdrojové soubory** zaškrtávací políčko.
6. Klikněte na tlačítko **Procházet**a vyberte síťové sdílené položce, kde je uložený instalační program (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Snímek obrazovky vytvořením balíčku a programu Průvodce](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Na **zvolte typ programu, který chcete vytvořit** vyberte **standardní Program**a klikněte na tlačítko **Další**.

  ![Snímek obrazovky vytvořením balíčku a programu Průvodce](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Na **zadejte informace o tomto standardním programu** stránky, zadejte následující vstupy a klikněte na tlačítko **Další**. (Další vstupních hodnot můžete použít výchozí hodnoty.)

  | **Název parametru** | **Hodnota** |
  |--|--|
  | Název | Instalaci služby Mobility Microsoft Azure (Windows) |
  | Příkazový řádek | install.bat |
  | Program lze spustit | Zda je přihlášený uživatel |

  ![Snímek obrazovky vytvořením balíčku a programu Průvodce](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Na další stránce vyberte cíl operační systémy. Služba mobility lze nainstalovat pouze na Windows Server 2012 R2, Windows Server 2012 a Windows Server 2008 R2.

  ![Snímek obrazovky vytvořením balíčku a programu Průvodce](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. Dokončete průvodce, klikněte na tlačítko **Další** dvakrát.


> [!NOTE]
> Skript podporuje i nové instalace služby Mobility agentů a aktualizace agentů, kteří jsou již nainstalovány.

### <a name="deploy-the-package"></a>Nasazení balíčku
1. V konzole nástroje Configuration Manager, klikněte pravým tlačítkem na váš balíček a vyberte **distribuovat obsah**.
  ![Snímek obrazovky nástroje Configuration Manager konzoly](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Vyberte **[distribuční body](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** na které balíčky by se měl zkopírovat.
3. Dokončete průvodce. Balíček pak spustí replikující se do určených distribučních bodů.
4. Po dokončení distribuci balíčku, klikněte pravým tlačítkem na balíček a vyberte **nasadit**.
  ![Snímek obrazovky nástroje Configuration Manager konzoly](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Vyberte kolekci zařízení systému Windows Server, kterou jste vytvořili v části předpoklady jako cílovou kolekci pro nasazení.

  ![Průvodce snímek nasazení softwaru](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Na **zadejte cílové umístění obsahu** vyberte vaše **distribuční body**.
7. Na **zadejte nastavení pro kontrolu způsobu nasazení tohoto softwaru** stránka, zkontrolujte, zda je účelem **požadované**.

  ![Průvodce snímek nasazení softwaru](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Na **zadejte plán pro toto nasazení** určete plán. Další informace najdete v tématu [plánování balíčky](https://technet.microsoft.com/library/gg682178.aspx).
9. Na **distribuční body** nakonfigurujte vlastnosti podle potřeb vašeho datového centra. Dokončete průvodce.

> [!TIP]
> Předejdete zbytečnému resetování Naplánujte instalaci balíčku během měsíčního časového období údržby nebo okno aktualizace softwaru.

Průběh nasazení můžete monitorovat pomocí konzoly nástroje Configuration Manager. Přejděte na **monitorování** > **nasazení** > *[váš název balíčku]*.

  ![Snímek obrazovky nástroje Configuration Manager možnost monitorování nasazení](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Nasazení na počítače se systémem Linux
> [!NOTE]
> Tento článek předpokládá, že je IP adresa serveru konfigurace 192.168.3.121 a zda zabezpečené sdílení souborů \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Příprava nasazení
1. Vytvořte složku ve sdílené síťové složce a pojmenujte ji jako **MobSvcLinux**.
2. Přihlaste se k konfigurační server a otevřete příkazový řádek pro správu.
3. Spusťte následující příkazy ke generování souboru přístupové heslo:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopírování **MobSvc.passphrase** soubor do **MobSvcLinux** složky do sdílené síťové složky.
5. Přejděte do instalačního programu úložiště na konfiguračním serveru spuštěním příkazu:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Zkopírujte následující soubory do **MobSvcLinux** složky do sdílené síťové složky:
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Zkopírujte následující kód a uložte ho jako **install_linux.sh** do **MobSvcLinux** složky.
   > [!NOTE]
   > Nahraďte zástupné symboly [CSIP] ve skriptu skutečnými hodnotami IP adresy konfigurační server.

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

1. Přihlaste se ke konzole nástroje Configuration Manager.
2. Přejděte do **softwarová knihovna** > **Správa aplikací** > **balíčky**.
3. Klikněte pravým tlačítkem na **balíčky**a vyberte **vytvořit balíček**.
4. Zadejte hodnoty pro název, popis, výrobce, jazyk a verzi.
5. Vyberte **tento balíček obsahuje zdrojové soubory** zaškrtávací políčko.
6. Klikněte na tlačítko **Procházet**a vyberte síťové sdílené položce, kde je uložený instalační program (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Snímek obrazovky vytvořením balíčku a programu Průvodce](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Na **zvolte typ programu, který chcete vytvořit** vyberte **standardní Program**a klikněte na tlačítko **Další**.

  ![Snímek obrazovky vytvořením balíčku a programu Průvodce](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Na **zadejte informace o tomto standardním programu** stránky, zadejte následující vstupy a klikněte na tlačítko **Další**. (Další vstupních hodnot můžete použít výchozí hodnoty.)

    | **Název parametru** | **Hodnota** |
  |--|--|
  | Název | Instalaci služby Mobility Microsoft Azure (Linux) |
  | Příkazový řádek | ./install_linux.sh |
  | Program lze spustit | Zda je přihlášený uživatel |

  ![Snímek obrazovky vytvořením balíčku a programu Průvodce](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. Na další stránce vyberte **tento program lze spustit na jakékoli platformě**.
  ![Snímek obrazovky vytvořením balíčku a programu Průvodce](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Dokončete průvodce, klikněte na tlačítko **Další** dvakrát.

> [!NOTE]
> Skript podporuje i nové instalace služby Mobility agentů a aktualizace agentů, kteří jsou již nainstalovány.

### <a name="deploy-the-package"></a>Nasazení balíčku
1. V konzole nástroje Configuration Manager, klikněte pravým tlačítkem na váš balíček a vyberte **distribuovat obsah**.
  ![Snímek obrazovky nástroje Configuration Manager konzoly](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Vyberte **[distribuční body](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** na které balíčky by se měl zkopírovat.
3. Dokončete průvodce. Balíček pak spustí replikující se do určených distribučních bodů.
4. Po dokončení distribuci balíčku, klikněte pravým tlačítkem na balíček a vyberte **nasadit**.
  ![Snímek obrazovky nástroje Configuration Manager konzoly](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Vyberte kolekci zařízení Linux Server, kterou jste vytvořili v části předpoklady jako cílovou kolekci pro nasazení.

  ![Průvodce snímek nasazení softwaru](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Na **zadejte cílové umístění obsahu** vyberte vaše **distribuční body**.
7. Na **zadejte nastavení pro kontrolu způsobu nasazení tohoto softwaru** stránka, zkontrolujte, zda je účelem **požadované**.

  ![Průvodce snímek nasazení softwaru](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Na **zadejte plán pro toto nasazení** určete plán. Další informace najdete v tématu [plánování balíčky](https://technet.microsoft.com/library/gg682178.aspx).
9. Na **distribuční body** nakonfigurujte vlastnosti podle potřeb vašeho datového centra. Dokončete průvodce.

Služba mobility získá nainstalovaná na kolekci zařízení Server Linux podle plánu, který jste nakonfigurovali.


## <a name="uninstall-the-mobility-service"></a>Odinstalujte službu Mobility
Můžete vytvořit balíčků nástroje Configuration Manager, odinstalujte službu Mobility. K tomu použijte následující skript:

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

## <a name="next-steps"></a>Další postup
Nyní jste připraveni k [povolení ochrany](vmware-azure-enable-replication.md) pro virtuální počítače.
