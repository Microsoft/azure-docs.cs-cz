---
title: Doporučení pro ochranu koncových bodů v Centrech zabezpečení Azure
description: Jak jsou zjištěna a identifikována jako v pořádku řešení ochrany koncového bodu.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208538"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Posouzení ochrany koncových bodů a doporučení v Azure Security Center

Azure Security Center poskytuje posouzení stavu [podporovaných](security-center-services.md#endpoint-supported) verzí řešení ochrany koncových bodů. Tento článek vysvětluje scénáře, které vedou Centrum zabezpečení ke generování následujících dvou doporučení:

* **Instalace řešení ochrany koncových bodů na virtuální můře**
* **Řešení problémů se stavem ochrany koncových bodů na počítačích**

## <a name="windows-defender"></a>Windows Defender

* Security Center doporučuje **"Nainstalovat řešení ochrany koncového bodu na virtuálním počítači"** při spuštění [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) a výsledkem je **AMServiceEnabled: False**

* Security Center doporučuje **"Vyřešit problémy se stavem ochrany koncového bodu na vašich počítačích"** při spuštění [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) a dojde k některé z následujících akcí:

  * Některé z následujících vlastností jsou false:

    **AMServiceEnabled**

    **AntispywareEnabled**

    **Funkce RealTimeProtectionPovolená**

    **BehaviorMonitorPovoleno**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * Pokud jedna nebo obě následující vlastnosti jsou 7 nebo více.

    **AntispywareSignatureAge**

    **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Ochrana koncového bodu Microsoft System Center

* Security Center doporučuje **"Instalovat řešení ochrany koncových bodů ve virtuálním počítači"** při importu **modulu SCEPMpModule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** a spuštění výsledků **Get-MProtComputerStatus** s **amserviceenabled = false**

* Security Center doporučuje **"Vyřešit problémy se stavem ochrany koncového bodu na vašich počítačích"** při spuštění **Get-MprotComputerStatus** a dojde k některé z následujících akcí:

    * Alespoň jedna z následujících vlastností je false:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Pokud jeden nebo oba z následujících aktualizací podpisu je větší nebo rovna 7. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Security Center doporučuje **"Instalace řešení ochrany koncových bodů na virtuálním počítači",** pokud nejsou splněny některé z následujících kontrol:
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** existuje
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** existuje
    * Soubor **dsa_query.cmd** se nachází v instalační složce
    * Spuštění **výsledků dsa_query.cmd** s **komponentou Component.AM.mode: on - Trend Micro Deep Security Agent detected**

## <a name="symantec-endpoint-protection"></a>Ochrana koncových bodů společnosti Symantec
Security Center doporučuje **"Instalace řešení ochrany koncových bodů na virtuálním počítači",** pokud nejsou splněny některé z následujících kontrol:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Nebo

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Security Center doporučuje **"Vyřešit problémy se stavem ochrany koncových bodů na vašich počítačích",** pokud nejsou splněny některé z následujících kontrol:

* Kontrola verze společnosti Symantec >= 12: Umístění registru: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Kontrola stavu ochrany v reálném čase: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Zkontrolovat stav aktualizace podpisu: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 dní**

* Kontrola stavu úplnékontroly: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 dní**

* Najít číslo verze podpisu Cesta k verzi podpisu pro společnost Symantec 12: **Registry Paths+ "CurrentVersion\SharedDefs" -Hodnota "SRTSP"** 

* Cesta k podpisové verzi aplikace Symantec 14: **Cesty registru+ "CurrentVersion\SharedDefs\SDSDefs" -Hodnota "SRTSP"**

Cesty registru:

* **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Ochrana koncových bodů mcafee pro Windows

Security Center doporučuje **"Instalace řešení ochrany koncových bodů na virtuálním počítači",** pokud nejsou splněny některé z následujících kontrol:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existuje

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Security Center doporučuje **"Vyřešit problémy se stavem ochrany koncových bodů na vašich počítačích",** pokud nejsou splněny některé z následujících kontrol:

* Verze společnosti McAfee: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Verze aplikace Najít podpis: **HKLM:\Software\McAfee\AVSolution\DS\DS -Hodnota "dwContentMajorVersion"**

* Datum nalezení podpisu: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 dní**

* Datum hledání: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Hodnota "LastFullScanOdsRunTime" >= 7 dní**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security for Linux Threat Prevention 

Security Center doporučuje **"Instalace řešení ochrany koncových bodů na virtuálním počítači",** pokud nejsou splněny některé z následujících kontrol:

- Soubor **/opt/isec/ens/threatprevention/bin/isecav** ukončí 

- **Výstup "/opt/isec/ens/threatprevention/bin/isecav --version"** je: **Název McAfee Endpoint Security for Linux Threat Prevention a McAfee verze >= 10**

Security Center doporučuje **"Vyřešit problémy se stavem ochrany koncových bodů na vašich počítačích",** pokud nejsou splněny některé z následujících kontrol:

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** vrátí **rychlé skenování, úplné skenování** a obě skeny <= 7 dní

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** vrátí **dat a čas aktualizace motoru** a oba <= 7 dní

- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** vrátí stav **Při přístupu ke skenování.**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus pro Linux 

Security Center doporučuje **"Instalace řešení ochrany koncových bodů na virtuálním počítači",** pokud nejsou splněny některé z následujících kontrol:

- Soubor **/opt/sophos-av/bin/savdstatus** ukončí nebo hledat vlastní umístění **"readlink $(which savscan)"**

- **"/opt/sophos-av/bin/savdstatus --version"** vrátí název Sophos = **Sophos Anti-Virus a Sophos verze >= 9**

Security Center doporučuje **"Vyřešit problémy se stavem ochrany koncových bodů na vašich počítačích",** pokud nejsou splněny některé z následujících kontrol:

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep -i "Plánované skenování . \* dokončena" | ocas -1"**, vrátí hodnotu

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep "skenování dokončeno"** | ocas -1", vrátí hodnotu

- **"/opt/sophos-av/bin/savdstatus --lastupdate"** vrátí lastUpdate, který by měl být <= 7 dní 

- **"/opt/sophos-av/bin/savdstatus -v"** se rovná **"Prohledávání při přístupu je spuštěno"** 

- **"/opt/sophos-av/bin/savconfig get LiveProtection"** vrátí povoleno

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Protokoly rozšíření antimalwarových produktů společnosti Microsoft jsou k dispozici na adrese: **%Systemdrive%\WindowsAzure\Logs\Microsoft.Azure.Security.IaaSAntimalware(Nebo PaaSAntimalware)\1.5.5.x(verze#)\CommandExecution.log**

### <a name="support"></a>Podpora

Další pomoc získáte od odborníků na Azure na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Nebo soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).