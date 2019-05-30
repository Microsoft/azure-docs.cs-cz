---
title: Endpoint protection řešení stavu a zjišťování posouzení ve službě Azure Security Center | Dokumentace Microsoftu
description: Způsob řešení ochrany koncových bodů zjišťování a identifikovat jako v pořádku.
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
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247961"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint protection posouzení a doporučení ve službě Azure Security Center

Endpoint protection posouzení a doporučení ve službě Azure Security Center detekuje a poskytuje posouzení stavu [podporované](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) verze řešení ochrany koncových bodů. Toto téma popisuje scénáře, které generují následující dvě doporučení pro řešení ochrany koncových bodů Azure Security Center.

* **Nainstalujte řešení ochrany koncových bodů na virtuálním počítači**
* **Řešení potíží stavu ochrany koncového bodu na vašich počítačích.**

## <a name="windows-defender"></a>Windows Defender

* **"Instalace řešení ochrany koncových bodů na virtuálním počítači"** doporučení se vygeneruje při [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) spuštění a výsledek je **AMServiceEnabled: False**

* **"Řešení problémů se stavem ochrany koncového bodu na počítačích"** se vygeneruje doporučení při [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) nastane, spuštění a jedno nebo obě z následujících akcí:

  * Nejmíň jedno z následujících vlastností je false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Pokud jedna nebo obě z následujících vlastností je větší nebo rovna hodnotě 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Aplikace Microsoft System Center endpoint protection

* **"Instalace řešení ochrany koncových bodů na virtuálním počítači"** doporučení se vygeneruje při importu **SCEPMpModule ("$env: ProgramFiles\Microsoft zabezpečení Client\MpProvider\MpProvider.psd1")** a spouštění **Get-MProtComputerStatus** výsledky s **AMServiceEnabled = false**

* **"Řešení problémů se stavem ochrany koncového bodu na počítačích"** se vygeneruje doporučení při **Get-MprotComputerStatus** nastane, spuštění a jedno nebo obě z následujících akcí:

    * Nejmíň jedno z následujících vlastností je false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Pokud se jedno nebo obě následující aktualizace podpisu je větší nebo rovna hodnotě 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* **"Instalace řešení ochrany koncových bodů na virtuálním počítači"** doporučení se vygeneruje, když jeden nebo více z následujících kontroly nejsou splněny:
    * **Agent zabezpečení HKLM:\SOFTWARE\TrendMicro\Deep** existuje
    * **Zabezpečení Agent\InstallationFolder HKLM:\SOFTWARE\TrendMicro\Deep** existuje
    * **Dsq_query.cmd** soubor nachází v instalační složce Nástroje
    * Spuštění **dsa_query.cmd** výsledky s **Component.AM.mode: na - Trend Micro Deep Security Agent zjistil**

## <a name="symantec-endpoint-protection"></a>Aplikace Symantec endpoint protection
**"Instalace řešení ochrany koncových bodů na virtuálním počítači"** doporučení se vygeneruje, pokud nejsou splněny všechny následující kontroly:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Nebo

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

**"Řešení problémů se stavem ochrany koncového bodu na počítačích"** doporučení se vygeneruje, pokud nejsou splněny všechny následující kontroly:  

* Zkontrolujte verzi Symantec > = 12:  Umístění v registru: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Zkontrolujte stav ochrany v reálném čase: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Kontrola stavu aktualizace signatur: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 days**

* Postup kontroly stavu úplné prohledávání: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* Najdete číslo verze podpisu cesta k podpisu verze 12 Symantec: **Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Cesta ke signatura verzi Symantec 14: **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

Cesty registru:

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee endpoint protection pro Windows

**"Instalace řešení ochrany koncových bodů na virtuálním počítači"** doporučení se vygeneruje, pokud nejsou splněny následující kontroly:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existuje

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

**"Řešení problémů se stavem ochrany koncového bodu na počítačích"** doporučení se vygeneruje, pokud nejsou splněny následující kontroly:

* McAfee verze: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* Verze signatury pro hledání: **HKLM:\Software\McAfee\AVSolution\DS\DS – hodnota "dwContentMajorVersion"**

* Vyhledání podpisu data: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* Najdete datum kontroly: **HKLM:\Software\McAfee\Endpoint\AV\ODS – hodnota "LastFullScanOdsRunTime" > = 7 dní**

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Protokoly rozšíření Microsoft Antimalware najdete na adrese:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Nebo můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
