---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9fe3b66de83ebc2cd0bf3a56a45456668c069191
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301154"
---
1. Zkopírujte instalační program k místní složce (třeba C:\Temp) na serveru, který chcete chránit. Jako správce z příkazového řádku spusťte následující příkazy:

   ```
   cd C:\Temp
   ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
   MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
   cd C:\Temp\Extracted.
   ```
2. Pokud chcete nainstalovat službu Mobility, spusťte následující příkaz:

   ```
   UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
   ```
3. Nyní je zapotřebí agenta zaregistrovat u konfiguračního serveru.

   ```
   cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
   ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumenty příkazového řádku instalačního programu služby mobility

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Parametr|Type|Popis|Možné hodnoty|
|-|-|-|-|
|/ Role|Povinné|Určuje, jestli se má nainstalovat služba Mobility (MS) nebo hlavní cílový server má (MT) by měly být nainstalovány.|MS </br> MT|
|/InstallLocation|Nepovinné|Umístění, ve kterém je nainstalovaná služba Mobility.|Libovolná složka v počítači|
|/ Platform|Povinné|Určuje platformu, na kterém je nainstalovaná služba Mobility. </br> </br>- **VMware**: Tuto hodnotu použijte, pokud instalace služby Mobility na virtuálního počítače se systémem *hostitelů VMware vSphere ESXi*, *hostitele Hyper-V*, a *fyzických serverů*. </br> - **Azure**: Tuto hodnotu použijte, pokud instalujete agenta na virtuálním počítači Azure IaaS. | VMware </br> Azure|
|/ Silent|Nepovinné|Určuje, ke spuštění instalačního programu v bezobslužném režimu.| neuvedeno|

>[!TIP]
> Protokoly instalace najdete v části % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>Argumenty příkazového řádku registraci služby mobility

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Parametr|Type|Popis|Možné hodnoty|
  |-|-|-|-|
  |/CSEndPoint |Povinné|IP adresa konfiguračního serveru| Libovolná platná IP adresa|
  |/PassphraseFilePath|Povinné|Umístění heslo |Libovolný platný název UNC nebo místní cesta k souboru|


>[!TIP]
> Protokoly konfigurace agenta najdete v části % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
