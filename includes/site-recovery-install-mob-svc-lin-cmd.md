---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 2b92aba8b9a8d8f46ae2aeac3a7bfe60a4755f9b
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019088"
---
1. Zkopírujte instalační program do místní složky (například TMP) na serveru, který chcete chránit. V terminálu spusťte následující příkazy:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Pokud chcete nainstalovat službu Mobility, spusťte následující příkaz:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Po dokončení instalace služby Mobility, musí být zaregistrovaná ke konfiguračnímu serveru. Spusťte následující příkaz pro registraci služby Mobility s konfiguračním serverem:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Mobility Service instalační program příkazového řádku

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parametr|Typ|Popis|Možné hodnoty|
|-|-|-|-|
|-r |Povinné|Určuje, jestli se má nainstalovat služba Mobility (MS) nebo hlavní cílový server má (MT) by měly být nainstalovány.|MS </br> MT|
|-d |Nepovinné|Umístění, ve kterém je nainstalovaná služba Mobility.|/usr/local/ASR|
|-v|Povinné|Určuje platformu, na kterém je nainstalovaná služba Mobility. </br> </br>- **VMware**: tuto hodnotu použijte, pokud instalace služby Mobility na virtuálního počítače se systémem *hostitelů VMware vSphere ESXi*, *hostitele Hyper-V*, a *fyzických serverů*. </br> - **Azure**: tuto hodnotu použijte, pokud instalujete agenta na Virtuálním počítači Azure IaaS.| VMware </br> Azure|
|-q|Nepovinné|Určuje, ke spuštění instalačního programu v bezobslužném režimu.| neuvedeno|


#### <a name="mobility-service-configuration-command-line"></a>Mobility Service configuration příkazového řádku

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parametr|Typ|Popis|Možné hodnoty|
|-|-|-|-|
|-i |Povinné|IP adresu konfiguračního serveru|Libovolná platná IP adresa|
|-P |Povinné|Úplnou cestu k souboru, kde se heslo připojení se uloží.|Libovolné platné složky|
