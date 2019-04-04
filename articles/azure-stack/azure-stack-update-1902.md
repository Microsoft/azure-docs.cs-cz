---
title: Aktualizace služby Azure Stack 1902 | Dokumentace Microsoftu
description: Další informace o aktualizaci 1902 pro integrované systémy Azure Stack, včetně novinek, známé problémy a kde se stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/03/2019
ms.openlocfilehash: 5971692b3e6447bc790b2e34cf84eae66979f7f5
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862076"
---
# <a name="azure-stack-1902-update"></a>Aktualizace služby Azure Stack 1902

*Týká se Integrované systémy pro službu Azure Stack*

Tento článek popisuje obsah balíčku 1902 aktualizace. Obsahuje vylepšení, opravy a nové funkce pro tuto verzi sady Azure Stack. Tento článek také popisuje známé problémy v této verzi a obsahuje odkaz ke stažení aktualizace. Známé problémy jsou rozděleny do problémy přímo souvisí s proces aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo sestavení aktualizace Azure Stack 1902 je **1.1902.0.69**.

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack vydává opravy hotfix v pravidelných intervalech. Nezapomeňte nainstalovat [nejnovější opravy hotfix Azure Stack](#azure-stack-hotfixes) pro 1901 před aktualizací služby Azure Stack na 1902.

Azure Stack opravy hotfix platí pouze pro integrované systémy Azure Stack; Nepokoušejte se nainstalovat ASDK opravy hotfix.

> [!TIP]  
> Předplatit následující *RSS* nebo *Atom* kanály, držet krok s Azure Stack opravy hotfix:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack opravy hotfix

- **1809**: [KB 4481548 – oprava hotfix Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: K dispozici žádná aktuální hotfix.
- **1901**: [KB 4495662 – oprava hotfix Azure Stack 1.1901.3.105](https://support.microsoft.com/help/4495662)
- **1902**: [KB 4494719 – oprava hotfix Azure Stack 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> 1902 můžete nainstalovat buď přímo z [1.1901.0.95 nebo 1.1901.0.99](azure-stack-update-1901.md#build-reference) vydání bez první instalace všech oprav hotfix 1901. Ale pokud jste nainstalovali starší **1901.2.103** oprav hotfix, musíte nainstalovat novější [1901.3.105 opravu hotfix](https://support.microsoft.com/help/4495662) 1902 předtím, než budete pokračovat.

- Před instalací této aktualizace, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) s následujícími parametry do ověřte stav služby Azure Stack a vyřešte všechny provozní problémy zjištěné, včetně všech upozornění a chyby. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Azure Stack je spravovaná službou System Center Operations Manager (SCOM), nezapomeňte aktualizovat [sady Management Pack pro Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) verzi 1.0.3.11 před použitím 1902.

- Formát balíčku pro aktualizaci služby Azure Stack se změnil z **.bin/.exe/.xml** k **.zip/.xml** od verze 1902. Zákazníci s připojené jednotky škálování služby Azure Stack **k dispozici je aktualizace** zpráva na portálu. Zákazníci, kteří nejsou připojené teď můžete jednoduše stáhnout a naimportovat soubor ZIP se odpovídající soubor XML.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Vylepšení

- Sestavení 1902 zavádí nové uživatelské rozhraní na portálu Správce služby Azure Stack pro vytváření plánů, nabídek, kvót a doplňkové plány. Další informace, včetně snímků obrazovky najdete v části [vytváření plánů, nabídek a kvót](azure-stack-create-plan.md).

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Vylepšení spolehlivosti rozšiřování kapacity při přidání uzlu při přechodu stavu škálovací jednotku z "Úložiště Expanding" do stavu spuštění.

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Ke zlepšení balíček integrity a zabezpečení, jakož i snadnější správu za účelem ingestování datových offline, Microsoft změnil formátu balíčku aktualizace ze souborů .exe a Bin na soubor .zip. Nový formát přidá další spolehlivost dekomprimace procesu, který v některých případech může způsobit přípravy aktualizace zpomalí. Stejný formát balíčku platí také pro aktualizace balíčků z vašeho OEM dodavatele.
- Při spuštění testu AzureStack, vylepšit celkovou funkčnost operátor Azure stacku, operátory teď můžete jednoduše použít, "Test-AzureStack-UpdateReadiness skupiny" na rozdíl od předání dalších parametrů deset po příkazu zahrnout.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- Pokud chcete zlepšit celkovou spolehlivost a dostupnost základní služby infrastruktury během procesu aktualizace, nativního zprostředkovatele prostředků aktualizace jako součást plánu aktualizace akce rozpozná a vyvolat automatické nápravy globální podle potřeby. Pracovní postupy nápravy globální "opravy" patří:
    - Kontrola pro infrastrukturu virtuálních počítačů, které jsou v neoptimálních stavu a zkusit opravit podle potřeby 
    - Zkontrolovat problémy se službou SQL jako součást plánu ovládacího prvku a zkusit opravit podle potřeby
    - Zkontrolujte stav služby nástroje pro vyrovnávání zatížení softwaru (SLB) jako součást sady řadiči sítě (NC) a zkusit opravit podle potřeby
    - Zkontrolujte stav služby Network síťovým Adaptérem a pokusí se ji opravit, podle potřeby
    - Zkontrolujte stav nouze obnovení konzoly služby (ERCS) služby prostředků infrastruktury uzly a opravit, podle potřeby
    - Zkontrolujte stav uzly XRP service fabric a opravit, podle potřeby
    - Kontrola stavu prostředků infrastruktury uzly služby konzistentní úložiště Azure (ACS) a opravit, podle potřeby

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Vylepšení spolehlivosti rozšiřování kapacity při přidání uzlu při přechodu stavu škálovací jednotku z "Úložiště Expanding" do stavu spuštění.    

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Vylepšení Azure stack zlepšit výkon a spolehlivost shromažďování protokolů diagnostické nástroje. Dodatečné protokolování pro sítě a služby identit. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Vylepšení spolehlivosti Test AzureStack otočení tajného kódu podle připravenosti na test.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Vylepšení pro zvýšení spolehlivosti AD Graph při komunikaci s prostředí služby Active Directory zákazníka

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Vylepšení kolekce inventáře hardwaru v Get AzureStackStampInformation.

- Abychom vylepšili spolehlivost operací spuštěných v infrastruktuře ERCS, zvyšuje paměť pro každou instanci ERCS z 8 GB až 12 GB. Při instalaci Azure Stack integrované systémy výsledkem je 12 GB zvýšení celkové.

> [!IMPORTANT]
> Aby se zajistilo procesu opravy a aktualizace výsledkem minimem výpadků tenanta, ujistěte se, že vaše služby Azure Stack razítko má více než 12 GB volného místa v **kapacity** okno. Zobrazí se tato paměť zvýšit v **kapacity** okno po úspěšné instalaci aktualizace.

## <a name="common-vulnerabilities-and-exposures"></a>Běžnými ohroženími zabezpečení a rizika

Tuto aktualizaci nainstaluje následující aktualizace:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

Další informace o těchto ohrožení zabezpečení, klikněte na výše uvedené odkazy nebo najdete v článcích znalostní báze Microsoft [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

- Při spuštění [testovací AzureStack](azure-stack-diagnostic-test.md), zobrazí se zpráva s upozorněním z řadič správy základní desky (BMC). Toto upozornění můžete ignorovat.

- <!-- 2468613 - IS --> Při instalaci této aktualizace, může se zobrazit upozornění s názvem `Error – Template for FaultType UserAccounts.New is missing.` tato upozornění můžete klidně ignorovat. Výstrahy automaticky zavře po dokončení instalace této aktualizace.

## <a name="post-update-steps"></a>Postup po aktualizaci

- Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v tématu [opravy hotfix](#hotfixes), stejně jako naše [zásady obsluhy](azure-stack-servicing-policy.md).  

- Načíst data na zbývající šifrovacích klíčů a bezpečně je uložte mimo nasazení Azure Stack. Postupujte podle [pokyny, jak k načtení klíčů](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Toto jsou známé problémy této verze sestavení po instalaci.

### <a name="portal"></a>Portál

<!-- 2930820 - IS ASDK --> 
- Na portálech pro správce i uživatele Pokud dáte vyhledat "Dockeru," položka je nesprávně vrátila. Není k dispozici ve službě Azure Stack. Pokud se pokusíte k jeho vytvoření, zobrazí se okno s uvedením chyby. 

<!-- 2931230 – IS  ASDK --> 
- Plány, které jsou přidány na předplatné uživatele jako doplňkový plán nelze odstranit, i když odebrat plán ze předplatné uživatele. Plán zůstane, dokud se také odstraní předplatné, které odkazují na doplňkový plán. 

<!-- TBD - IS ASDK --> 
- Se nemá používat dva typy pro správu předplatného, které byly představeny s nástrojem verzi 1804. Typy předplatného jsou **měření předplatné**, a **využití předplatného**. Tyto typy předplatného jsou viditelné v novým prostředím Azure Stack od verze 1804, ale ještě nejsou připravené k použití. By měla dál používat **výchozí zprostředkovatel** typu předplatného.

<!-- 3557860 - IS ASDK --> 
- Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

<!-- 1663805 - IS ASDK --> 
- Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení použít [Powershellu mohl ověřit oprávnění](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Při vytváření virtuálního počítače pro nové Windows (VM), může se zobrazit následující chyba:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění dojde k chybě. Chcete-li tento problém obejít, znovu vytvořte účet úložiště se stejným názvem, jako jste použili dříve.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Prostředí pro vytváření virtuálního počítače škálovací sady (VMSS) poskytuje založené na CentOS 7.2 jako možnost pro nasazení. Vzhledem k tomu, že obrázek není k dispozici ve službě Azure Stack, vyberte jiný operační systém pro vaše nasazení, nebo pomocí zadání jiné image CentOS, který byl stažen před jejich nasazením na Marketplace pomocí šablony Azure Resource Manageru operátor.  

<!-- TBD - IS ASDK --> 
- Po použití 1902 aktualizace, může dojít k následujícím problémům při nasazování virtuálních počítačů se spravovanými disky:

   - Pokud předplatné bylo vytvořeno před aktualizací. 1808, nasazení virtuálního počítače se spravovanými disky může selhat s interní chybovou zprávu. Chcete chybu vyřešit, postupujte podle těchto kroků pro každé předplatné:
      1. Portál pro klienty, přejděte na **předplatná** a vyhledejte předplatné. Vyberte **poskytovatelů prostředků**a pak vyberte **Microsoft.Compute**a potom klikněte na tlačítko **přeregistrovat**.
      2. V rámci stejného předplatného, přejděte na **řízení přístupu (IAM)** a ověřte, že **Azure Stack – spravovaný Disk** je uvedena.
   - Pokud jste nakonfigurovali prostředí s více tenanty, nasazování virtuálních počítačů v rámci služby předplacené asociovaná s adresářem hosta může selhat s interní chybovou zprávu. Pokud chcete chybu vyřešit, postupujte podle kroků v [v tomto článku](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) změna konfigurace všech vašich adresářů hosta.

- Virtuální počítač s Ubuntu 18.04 vytvořené pomocí SSH autorizace povolená neumožňuje použití klíčů SSH pro přihlášení. Jako alternativní řešení použít přístup k virtuálním počítačům pro Linuxové rozšíření k implementaci klíče SSH po zřízení, nebo ověřování pomocí hesla.

### <a name="networking"></a>Sítě  

<!-- 3239127 - IS, ASDK -->
- Na portálu Azure Stack při změně statickou IP adresu pro konfiguraci IP, která je vázána na síťový adaptér připojený k instanci virtuálního počítače, zobrazí se zpráva s oznámením 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Můžete bezpečně ignorovat této zprávy. IP adresa se změní i v případě neprovede restartování instance virtuálního počítače.

<!-- 3632798 - IS, ASDK -->
- Na portálu, je-li přidat příchozí pravidlo zabezpečení a vyberte **značka služby** jako zdroj, se zobrazí několik možností, jak v **značka zdroje** seznamu, které nejsou k dispozici pro službu Azure Stack. Pouze možnosti, které jsou platné ve službě Azure Stack jsou následující:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  Další možnosti nejsou podporovány jako zdroj značky ve službě Azure Stack. Obdobně je-li přidat odchozí pravidlo zabezpečení a vyberte **značka služby** jako cíl stejný seznam možností pro **značka zdroje** se zobrazí. Pouze platné možnosti jsou stejné jako v případě **značka zdroje**, jak je popsáno v předchozím seznamu.

- Skupiny zabezpečení sítě (Nsg) nebudou fungovat ve službě Azure Stack v stejným způsobem jako globální Azure. V Azure, můžete nastavit víc portů na jedno pravidlo skupiny zabezpečení sítě (pomocí portálu, Powershellu a šablon Resource Manageru). Ve službě Azure Stack však nelze nastavit více portů na jedno pravidlo skupiny zabezpečení sítě přes portál. Pokud chcete tento problém obejít, nastavte tyto další pravidla pomocí šablony Resource Manageru a Powershellu.

<!-- 3203799 - IS, ASDK -->
- Azure Stack nepodporuje připojení více než 4 síťových rozhraní (NIC) k instanci virtuálního počítače ještě dnes, bez ohledu na velikost instance.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Před vytvořením první funkce Azure v rámci předplatného, zaregistrujte poskytovatele prostředků úložiště.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Můžete stáhnout aktualizace balíčku Azure Stack 1902 z [tady](https://aka.ms/azurestackupdatedownload). 

Nasazení Azure Stack v propojených scénářích pouze, pravidelně kontrolovat zabezpečeného koncového bodu a automaticky upozorní, pokud je aktualizace k dispozici pro váš cloud. Další informace najdete v tématu [Správa aktualizací pro Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Další postup

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  
