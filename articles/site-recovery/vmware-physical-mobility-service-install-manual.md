---
title: Nainstalovat službu Mobility ručně pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů s Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat agenta služby Mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: raynew
ms.openlocfilehash: dfed0209131379843b97ff8050c2f2ba7294537d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019137"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>Ruční instalace služby Mobility na virtuální počítače VMware a fyzické servery

Při nastavení zotavení po havárii pro virtuální počítače VMware a fyzických serverů požíváním [Azure Site Recovery](site-recovery-overview.md), můžete nainstalovat [služby Site Recovery Mobility](vmware-physical-mobility-service-overview.md) na každém místních virtuálních počítačů VMware a fyzických serverů.  Služba Mobility zaznamenává datové zápisy na počítači a předává je na procesní server Site Recovery.

Tento článek popisuje, jak nainstalovat službu Mobility ručně na každém počítači, který chcete chránit.

## <a name="create-a-passphrase"></a>Vytvořit heslo

Před instalací, vytvořte přístupové heslo, který se použije při instalaci.

1. Přihlaste se konfigurační server.
2. Otevřete příkazový řádek jako správce.
3. Změňte adresář na složku bin a poté vytvořit soubor přístupové heslo.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Store souboru s heslem v zabezpečeném umístění. 


## <a name="install-the-service-from-the-ui"></a>Instalace služby z uživatelského rozhraní

>[!IMPORTANT]
> Pokud provádíte replikaci virtuálních počítačů Azure IaaS z jedné oblasti Azure do jiné, nepoužívejte tuto metodu. Použijte metodu insteadl instalace na základě na příkazovém řádku.

1. Vyhledejte verzi Instalační služby systému, které potřebujete pro operační systém počítače. Instalační programy jsou umístěny ve složce %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository. [Zkontrolujte](vmware-physical-mobility-service-overview.md#installer-files) instalačního programu budete potřebovat.
2. Zkopírujte instalační soubor do počítače a spustíme ji.
3. V **možnost instalace**vyberte **nainstalovat službu mobility**.
4. Zvolte umístění instalace > **nainstalovat**.

    ![Stránka možnost instalace služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. Sledování instalace v **průběh instalace**. Po dokončení instalace, vybrat **pokračovat na konfiguraci** zaregistrovat službu s konfiguračním serverem.

    ![Stránce pro registraci služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  v **podrobnosti konfiguračního serveru**, zadejte IP adresu a heslo, které jste nakonfigurovali.  

    ![Stránce pro registraci služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. Vyberte **zaregistrovat** dokončete registraci.

    ![Poslední stránce pro registraci služby mobility](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>Nainstalujte službu z příkazového řádku

### <a name="on-a-windows-machine"></a>Na počítači s Windows

1. Zkopírujte instalační program k místní složce (třeba C:\Temp) na serveru, který chcete chránit. 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Nainstalujte následujícím způsobem:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. Registrace agenta s konfiguračním serverem.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Nastavení instalace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | UnifiedAgent.exe/role < MS|MT – > /InstallLocation  <Install Location> /silent "VmWare" / Platform
Instalační protokoly | V části % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/ Role | Instalace povinných parametrů. Určuje, zda by měla nainstalovat služba Mobility (MS) nebo hlavní cíl (MT).
/InstallLocation| Volitelný parametr. Určuje umístění instalace služby Mobility (libovolné složky).
/ Platform | Povinné. Určuje platformu, na kterém je nainstalovaná služba Mobility. **VMware** pro Mware virtuálních počítačů a fyzických serverů. **Azure** pro virtuální počítače Azure. 
/ Silent| Volitelné. Určuje, jestli ke spuštění instalačního programu v bezobslužném režimu.

#### <a name="registration-settings"></a>Nastavení registrace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | / Csendpoint UnifiedAgentConfigurator.exe  <CSIP> /passphrasefilepath <PassphraseFilePath>
Protokoly konfigurace agenta | V části % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/ CSEndPoint | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte libovolná platná IP adresa.
/PassphraseFilePath |  Povinné. Umístění přístupové heslo. Použijte libovolný platný název UNC nebo místní cesta k souboru.


### <a name="on-a-linux-machine"></a>Na počítači s Linuxem

1. Zkopírujte instalační program do místní složky (například TMP) na serveru, který chcete chránit. V terminálu spusťte následující příkazy:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
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
Využití | . / install -d <Install Location> - r < MS|MT – > - v, VmWare - q
-r | Instalace povinných parametrů. Určuje, zda by měla nainstalovat služba Mobility (MS) nebo hlavní cíl (MT).
-d | Volitelný parametr. Určuje umístění instalace služby Mobility: /usr/local/ASR.
-v | Povinné. Určuje platformu, na kterém je nainstalovaná služba Mobility. **VMware** pro Mware virtuálních počítačů a fyzických serverů. **Azure** pro virtuální počítače Azure. 
-q | Volitelné. Určuje, jestli ke spuštění instalačního programu v bezobslužném režimu.

#### <a name="registration-settings"></a>Nastavení registrace
**Nastavení** | **Podrobnosti**
--- | ---
Využití | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> - P <PassphraseFilePath>
-i | Povinný parametr. Určuje IP adresu konfiguračního serveru. Použijte libovolná platná IP adresa.
-P |  Povinné. Úplnou cestu souboru, ve kterém se heslo uloží. Všechny platné složky

## <a name="next-steps"></a>Další postup
- [Nastavení zotavení po havárii pro virtuální počítače VMware](vmware-azure-tutorial.md)
- [Nastavení zotavení po havárii pro fyzické servery](physical-azure-disaster-recovery.md)
