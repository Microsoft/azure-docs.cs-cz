---
title: Zjišťování řešení ochrany koncových bodů a vyhodnocení stavu v Azure Security Center | Microsoft Docs
description: Jak jsou zjištěna a označena jako v pořádku řešení ochrany koncových bodů.
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
ms.date: 08/08/2019
ms.author: memildin
ms.openlocfilehash: 8de0caa5db4a7e1d97c7d6c055bcb01fed635821
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202260"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Posouzení a doporučení služby Endpoint Protection v Azure Security Center

Azure Security Center poskytuje posouzení stavu [podporovaných](https://docs.microsoft.com/azure/security-center/security-center-os-coverage) verzí řešení ochrany koncových bodů. Tento článek vysvětluje scénáře, které Security Center vedoucím generovat následující dvě doporučení:

* **Instalace řešení Endpoint Protection na virtuálním počítači**
* **Řešení problémů se stavem služby Endpoint Protection na vašich počítačích**

## <a name="windows-defender"></a>Windows Defender

* Security Center doporučuje při spuštění [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)  ****instalovat řešení Endpoint Protection na virtuálním počítači** a výsledek je AMServiceEnabled: Chybné**

* Security Center doporučuje, abyste při spuštění [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) **vyřešili problémy se stavem Endpoint Protection na vašich počítačích** a nastaly některé z těchto situací:

  * Kterákoli z následujících vlastností je false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Pokud jedna nebo obě následující vlastnosti jsou 7 nebo více.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* Security Center doporučuje při importu **SCEPMpModule ("$env:P Rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1"** **nainstalovat řešení Endpoint Protection na virtuálním počítači** ") a spustit  **Get-MProtComputerStatus** výsledky s **AMServiceEnabled = false**

* Security Center doporučuje, abyste při spuštění **Get-MprotComputerStatus** **vyřešili problémy se stavem Endpoint Protection na vašich počítačích** a nastaly některé z těchto situací:

    * Alespoň jedna z následujících vlastností je false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Je-li jedna nebo obě následující signatury aktualizace větší nebo rovny 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Security Center doporučuje **nainstalovat řešení Endpoint Protection na virtuálním počítači** , když se nesplní některá z následujících kontrol:
    * **HKLM: existuje Agent zabezpečení \ SOFTWARE\TrendMicro\Deep**
    * **HKLM: \ SOFTWARE\TrendMicro\Deep zabezpečení Agent\InstallationFolder existuje.**
    * Soubor **dsq_query. cmd** najdete v instalační složce.
    * Spuštění **dsa_query. cmd** Results s **komponentou. am. Mode: on-trend microed Security Agent byl zjištěn**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Security Center doporučuje **nainstalovat řešení Endpoint Protection na virtuálním počítači** , když se nesplní některá z následujících kontrol:

* **HKLM: \ Software\Symantec\Symantec koncový bod Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM: \ Software\Symantec\Symantec koncový bod Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Nebo

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec koncový bod Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Security Center doporučuje **vyřešit problémy stavu ochrany koncových bodů na vašich počítačích** , když některá z následujících kontrol nejsou splněné:

* Podívejte se na verzi Symantec > = 12:  Umístění registru: **HKLM: \ Software\Symantec\Symantec koncový bod Protection\CurrentVersion "-value" PRODUCTVERSION "**

* Kontrolovat stav ochrany v reálném čase: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Ověřte stav aktualizace signatury: **HKLM\Software\Symantec\Symantec koncový bod Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 dní**

* Zkontrolujte stav úplné kontroly: **HKLM: \ Software\Symantec\Symantec koncový bod Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 dní**

* Vyhledejte číslo verze podpisu cesta k verzi signatury pro Symantec 12: **Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Cesta k verzi podpisu pro Symantec 14: **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

Cesty registru:

* **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection "+ $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection pro Windows

Security Center doporučuje **nainstalovat řešení Endpoint Protection na virtuálním počítači** , když se nesplní některá z následujících kontrol:

* **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existuje

* **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Security Center doporučuje **vyřešit problémy stavu ochrany koncových bodů na vašich počítačích** , když některá z následujících kontrol nejsou splněné:

* Verze programu McAfee: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* Najít verzi signatury: **HKLM: \ Software\McAfee\AVSolution\DS\DS-value "dwContentMajorVersion"**

* Najít datum podpisu: **HKLM: \ Software\McAfee\AVSolution\DS\DS-value "szContentCreationDate" > = 7 dní**

* Najít datum kontroly: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-value "LastFullScanOdsRunTime" > = 7 dní**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Prevence hrozeb zabezpečení služby McAfee Endpoint Security pro Linux 

Security Center doporučuje **nainstalovat řešení Endpoint Protection na virtuálním počítači** , když se nesplní některá z následujících kontrol:

- Výstupy souborů **/opt/iSEC/ENS/threatprevention/bin/isecav** 

- výstup **"/opt/iSEC/ENS/threatprevention/bin/isecav--Version"** je: **Název McAfee = McAfee Endpoint Security pro Linux Threat prevence a verze McAfee > = 10**

Security Center doporučuje **vyřešit problémy stavu ochrany koncových bodů na vašich počítačích** , když některá z následujících kontrol nejsou splněné:

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--listtask"** vrátí **rychlou kontrolu, úplnou kontrolu** a obě kontroly < = 7 dní.

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--listtask"** vrací data **a čas aktualizace modulu** a oba < = 7 dní.

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--getoasconfig--Summary"** vrátí stav **kontroly přístupu**

## <a name="sophos-antivirus-for-linux"></a>Sophos antivirus pro Linux 

Security Center doporučuje **nainstalovat řešení Endpoint Protection na virtuálním počítači** , když se nesplní některá z následujících kontrol:

- **/Opt/Sophos-AV/bin/savdstatus** souboru se ukončí nebo vyhledá vlastní umístění **"readlink $ (které savscan)"** .

- **"/opt/Sophos-AV/bin/savdstatus--Version"** vrací Sophos name = **Sophos Anti-virus a Sophos verze > = 9**

Security Center doporučuje **vyřešit problémy stavu ochrany koncových bodů na vašich počítačích** , když některá z následujících kontrol nejsou splněné:

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep-i "Naplánovaná kontrola. byl\* dokončen znak | Tail-1**, vrátí hodnotu.   

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep "Kontrola dokončena"** | koncová hodnota-1 vrátí hodnotu.   

- **"/opt/Sophos-AV/bin/savdstatus--LastUpdate"** vrátí LastUpdate, který by měl být < = 7 dní. 

- **"/opt/Sophos-AV/bin/savdstatus-v"** se rovná **"probíhá kontrola při přístupu"** 

- funkce **/opt/Sophos-AV/bin/savconfig Get LiveProtection** se povoluje.  

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Protokoly rozšíření Microsoft Antimalware jsou k dispozici na adrese:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Podpora

Pokud chcete získat další informace, obraťte se na odborníky na Azure na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Nebo zasouborte incident podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).