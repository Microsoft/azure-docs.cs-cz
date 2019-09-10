---
title: Zjišťování řešení ochrany koncových bodů a vyhodnocení stavu v Azure Security Center | Microsoft Docs
description: Jak jsou zjištěna a označena jako v pořádku řešení ochrany koncových bodů.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: a5cd0f88173abb65a120aa305206505af51d9f9e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861376"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Posouzení a doporučení služby Endpoint Protection v Azure Security Center

Posouzení a doporučení služby Endpoint Protection v Azure Security Center zjišťují a poskytují posouzení stavu [podporovaných](https://docs.microsoft.com/azure/security-center/security-center-os-coverage) verzí řešení Endpoint Protection. V tomto tématu najdete vysvětlení scénářů, které generují následující dvě doporučení pro řešení ochrany koncových bodů pomocí Azure Security Center.

* **Instalace řešení Endpoint Protection na virtuálním počítači**
* **Řešení problémů se stavem služby Endpoint Protection na vašich počítačích**

## <a name="windows-defender"></a>Windows Defender

* Doporučení **"instalovat řešení Endpoint Protection na virtuálním počítači"** se vygeneruje při spuštění [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) a výsledek je **AMServiceEnabled: Chybné**

* Doporučení **"vyřešit problémy stavu ochrany koncových bodů na vašich počítačích"** se vygeneruje, když se spustí [příkaz Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) a nastane jedna z následujících možností:

  * Alespoň jedna z následujících vlastností je false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Pokud je jedna nebo obě z následujících vlastností větší nebo rovna 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* Při importu **SCEPMpModule ("$env:P Rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1)** se vygeneruje doporučení **" instalovat řešení Endpoint Protection na virtuálním počítači** ") a spustí **se. Get-MProtComputerStatus** výsledky s **AMServiceEnabled = false**

* Doporučení **"vyřešit problémy stavu ochrany koncových bodů na vašich počítačích"** se vygeneruje, když se spustí **příkaz Get-MprotComputerStatus** a nastane jedna z následujících možností:

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

* Doporučení **"instalovat řešení Endpoint Protection na virtuálním počítači"** se vygeneruje, pokud se nesplní jedna nebo více následujících kontrol:
    * **HKLM: existuje Agent zabezpečení \ SOFTWARE\TrendMicro\Deep**
    * **HKLM: \ SOFTWARE\TrendMicro\Deep zabezpečení Agent\InstallationFolder existuje.**
    * Soubor **dsq_query. cmd** najdete v instalační složce.
    * Spuštění **dsa_query. cmd** Results s **komponentou. am. Mode: on-trend microed Security Agent byl zjištěn**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Doporučení **"instalovat řešení Endpoint Protection na virtuálním počítači"** se vygeneruje, pokud se nesplní některá z následujících kontrol:

* **HKLM: \ Software\Symantec\Symantec koncový bod Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM: \ Software\Symantec\Symantec koncový bod Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Nebo

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec koncový bod Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Pokud nejsou splněné některé z následujících kontrol, vygeneruje se doporučení **vyřešit problémy se stavem řešení Endpoint Protection na vašich počítačích** :  

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

Pokud nejsou splněné následující kontroly, vygeneruje se doporučení **instalovat řešení Endpoint Protection na virtuálním počítači** :

* **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existuje

* **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Pokud nejsou splněné následující kontroly, vygeneruje se doporučení **vyřešit problémy se stavem řešení Endpoint Protection na vašich počítačích** :

* Verze programu McAfee: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* Najít verzi signatury: **HKLM: \ Software\McAfee\AVSolution\DS\DS-value "dwContentMajorVersion"**

* Najít datum podpisu: **HKLM: \ Software\McAfee\AVSolution\DS\DS-value "szContentCreationDate" > = 7 dní**

* Najít datum kontroly: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-value "LastFullScanOdsRunTime" > = 7 dní**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Prevence hrozeb zabezpečení služby McAfee Endpoint Security pro Linux 

Pokud není splněna jedna nebo obě následující kontrola, je vygenerována **řešení instalovat Endpoint Protection v doporučeních virtuálního počítače** :  

- Výstupy souborů **/opt/iSEC/ENS/threatprevention/bin/isecav** 

- výstup **"/opt/iSEC/ENS/threatprevention/bin/isecav--Version"** je: **Název McAfee = McAfee Endpoint Security pro Linux Threat prevence a verze McAfee > = 10**

Pokud není splněna jedna nebo více následujících kontrol, **vyřešte problémy se stavem řešení Endpoint Protection na vašich počítačích** .

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--listtask"** vrátí **rychlou kontrolu, úplnou kontrolu** a obě kontroly < = 7 dní.

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--listtask"** vrací data **a čas aktualizace modulu** a oba < = 7 dní.

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--getoasconfig--Summary"** vrátí stav **kontroly přístupu**

## <a name="sophos-antivirus-for-linux"></a>Sophos antivirus pro Linux 

Pokud není splněna jedna nebo obě následující kontrola, je vygenerována **řešení instalovat Endpoint Protection v doporučeních virtuálního počítače** :

- **/Opt/Sophos-AV/bin/savdstatus** souboru se ukončí nebo vyhledá vlastní umístění **"readlink $ (které savscan)"** .

- **"/opt/Sophos-AV/bin/savdstatus--Version"** vrací Sophos name = **Sophos Anti-virus a Sophos verze > = 9**

Pokud není splněna jedna nebo více následujících kontrol, **vyřešte problémy se stavem řešení Endpoint Protection na vašich počítačích** .

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep-i "Naplánovaná kontrola. byl\* dokončen znak | Tail-1**, vrátí hodnotu.   

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep "Kontrola dokončena"** | koncová hodnota-1 vrátí hodnotu.   

- **"/opt/Sophos-AV/bin/savdstatus--LastUpdate"** vrátí LastUpdate, které by měly být < = 7 dní. 

- **"/opt/Sophos-AV/bin/savdstatus-v"** se rovná **"probíhá kontrola při přístupu"** 

- funkce **/opt/Sophos-AV/bin/savconfig Get LiveProtection** se povoluje.  

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Protokoly rozšíření Microsoft Antimalware jsou k dispozici na adrese:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Nebo můžete zasouborovat incident podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
