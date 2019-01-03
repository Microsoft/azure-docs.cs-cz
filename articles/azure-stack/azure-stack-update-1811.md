---
title: Aktualizace služby Azure Stack 1811 | Dokumentace Microsoftu
description: Další informace o aktualizaci 1811 pro integrované systémy Azure Stack, včetně novinek, známé problémy a kde se stáhnout aktualizaci.
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
ms.date: 12/22/2018
ms.author: sethm
ms.reviewer: adepue
ms.openlocfilehash: da481a2b6a20b5b5dbd75a23aafffc0bc0a8fef9
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752754"
---
# <a name="azure-stack-1811-update"></a>Aktualizace služby Azure Stack 1811

*Platí pro: Integrované systémy Azure Stack*

Tento článek popisuje obsah balíčku 1811 aktualizace. Balíček aktualizace zahrnuje vylepšení, opravy a nové funkce pro tuto verzi sady Azure Stack. Tento článek také popisuje známé problémy v této verzi a zahrnuje odkaz, takže si můžete stáhnout aktualizace. Známé problémy jsou rozděleny do problémy přímo souvisí s proces aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo sestavení aktualizace Azure Stack 1811 je **1.1811.0.101**.

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack vydává opravy hotfix v pravidelných intervalech. Nezapomeňte nainstalovat [nejnovější opravy hotfix Azure Stack](#azure-stack-hotfixes) pro 1809 před aktualizací služby Azure Stack na 1811.

> [!TIP]  
> Předplatit následující *RRS* nebo *Atom* kanály, držet krok s Azure Stack opravy hotfix:
> - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
> - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...

### <a name="azure-stack-hotfixes"></a>Azure Stack opravy hotfix

- **1809**: [KB 4481548 – oprava hotfix Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: K dispozici žádná aktuální hotfix.

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Během instalace aktualizace 1811 musíte zajistit, že všechny instance portálu správce zavřená. Portál user portal zůstat otevřené, ale na portálu pro správu musí být uzavřeny.

- Příprava vašeho nasazení Azure stacku pro Azure Stack rozšíření hostitele. Příprava operačního systému pomocí následujících pokynů: [Příprava pro rozšíření hostitele pro Azure Stack](azure-stack-extension-host-prepare.md). 
 
- Nainstalujte [nejnovější opravy hotfix Azure Stack](#azure-stack-hotfixes) pro 1809 před aktualizací na 1811.

- Před instalací této aktualizace, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) s následujícími parametry do ověřte stav služby Azure Stack a vyřešte všechny provozní problémy zjištěné, včetně všech upozornění a chyby. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci.  

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Pokud není nutné splnit, požadavky na rozšíření hostitele `Test-AzureStack` výstupu se zobrazí následující zpráva: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- Aktualizace Azure Stack 1811 vyžaduje správně importované certifikáty povinné rozšíření hostitele do vašeho prostředí Azure Stack. Chcete-li pokračovat v instalaci aktualizace 1811, je nutné naimportovat certifikáty SSL, vyžaduje se pro hostitele rozšíření. Pokud chcete importovat certifikáty, naleznete v tématu [v této části](azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Pokud každý upozornění ignorovat a stále se rozhodnete instalovat aktualizace 1811, aktualizace se nezdaří v přibližně 1 hodina s následující zprávou:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Jakmile správně naimportujete certifikáty povinné rozšíření hostitele, můžete obnovit 1811 aktualizace z portálu správce. Když Microsoft vás informuje o tom operátorům Azure stacku umožní jednotka škálování uvést do režimu údržby během procesu aktualizace, selhání z důvodu chybějící certifikáty hostitele rozšíření by neměla mít vliv existující úlohy nebo služby.  

    Při instalaci této aktualizace není k dispozici na portálu user portal pro Azure Stack, zatímco hostitel rozšíření je konfigurován. Konfigurace hostitele rozšíření může trvat až 5 hodin. Během této doby můžete zkontrolovat stav příkazu update nebo pokračování instalace aktualizace se nezdařila pomocí [Powershellu Azure Stack správce nebo privileged koncový bod](azure-stack-monitor-update.md).

## <a name="new-features"></a>Nové funkce

Tato aktualizace zahrnuje následující nové funkce a vylepšení pro službu Azure Stack:

- V této verzi [hostitel rozšíření](azure-stack-extension-host-prepare.md) je povolená. Hostitel rozšíření zjednodušuje integraci sítě a zlepšuje stavu zabezpečení služby Azure Stack.

- Přidali jsme podporu pro ověřování zařízení s Active Directory Federated Services (AD FS), při použití zejména rozhraní příkazového řádku Azure. Další informace najdete v tématu [profilů verzí použití rozhraní API pomocí Azure CLI ve službě Azure Stack](./user/azure-stack-version-profiles-azurecli2.md)

- Přidání podpory pro instanční objekty pomocí tajného klíče klienta s Active Directory Federated Services (AD FS). Další informace najdete v tématu [vytvořit instanční objekt služby AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Tato verze přidává podporu pro následující verze rozhraní API služby Azure Storage: **2017-07-29**, **2017-11-09**. Podpora je taky přidaný v následujících verzích rozhraní API poskytovatele prostředků úložiště Azure: **2016-05-01**, **2016-12-01**, **2017-06-01**, a **2017-10-01**. Další informace najdete v tématu [úložiště služby Azure Stack: Rozdíly a aspekty](./user/azure-stack-acs-differences.md).

- Přidání nových koncový bod privilegovaných příkazů aktualizovat a odebírat principů služby pro služby AD FS. Další informace najdete v tématu [vytvořit instanční objekt služby AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Přidání nové operace uzel jednotek škálování, které umožňují operátory Azure stacku spustit, zastavit a vypněte uzel jednotek škálování. Další informace najdete v tématu [akcí uzel jednotky škálování ve službě Azure Stack](azure-stack-node-actions.md).

- Přidat nové okno Vlastnosti oblasti, která zobrazuje podrobnosti o registraci prostředí. Tyto informace můžete zobrazit kliknutím **Správa oblastí** dlaždici na výchozí řídicí panel portálu správce a pak vyberete **vlastnosti**.

- Přidání nového příkazu privilegovaných koncový bod k aktualizaci pomocí uživatelského jména a hesla, používaný ke komunikaci s fyzickými počítači pověření pro řadič BMC. Další informace najdete v tématu [aktualizujte řadič správy základní desky \(BMC) přihlašovacích údajů](azure-stack-rotate-secrets.md).

- Přidali jsme možnost ale přístup k Azure do budoucna ikonu nápovědy a podpory (otazník) v pravém horním rohu portály správce a uživatele, podobným způsobem, který je k dispozici na webu Azure Portal.

- Přidat pro odpojené prostředí lepší správu webu Marketplace. Proces odesílání k publikování položky Marketplace v odpojeném prostředí je zjednodušenou jeden krok, namísto odesílání bitové kopie a balíček Marketplace samostatně. Nahraný produktu budou viditelné i v okně správy webu Marketplace. Další informace najdete v tématu [v této části](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher). 

- Tato verze zmenší okno požadovaná Údržba pro rotaci tajných kódů tak, že přidáte obměňovat jenom externí certifikáty během [otočení tajných kódů služby Azure Stack](azure-stack-rotate-secrets.md).

- [Azure Stack Powershellu](azure-stack-powershell-install.md) byl aktualizován na verze 1.6.0. Tato aktualizace zahrnuje podporu pro nové funkce související s úložiště ve službě Azure Stack. Další informace najdete v tématu poznámky k verzi pro [modul správy Azure Stack 1.6.0 v galerii prostředí PowerShell](https://www.powershellgallery.com/packages/AzureStack/1.6.0) informace týkající se aktualizací nebo instalace Azure Stack Powershellu najdete v tématu [instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md).

- Spravované disky je nyní povolena ve výchozím nastavení při vytváření virtuálních počítačů pomocí portálu Azure Stack. Zobrazit [známé problémy](#known-issues-post-installation) části Další kroky potřebné pro Managed Disks, aby se zabránilo selhání vytvoření virtuálního počítače.

- Tato verze přináší upozornění **opravit** akce pro operátory Azure stacku. Některé výstrahy v 1811 poskytují **opravit** tlačítko ve výstraze, které můžete vybrat k vyřešení daného problému. Další informace najdete v tématu [monitorovat stav a výstrahy ve službě Azure Stack](azure-stack-monitor-health.md).

## <a name="fixed-issues"></a>Oprava potíží

<!-- TBD - IS ASDK --> 
- Opravili jsme problém, ve kterém veřejná data IP adres pro měření využití jsme si ukázali, stejné **EventDateTime** hodnotu pro každý záznam místo **TimeDate** razítko, které ukazuje, kdy se záznam vytvořil. Teď můžete tato data pro přesné monitorování použití veřejné IP adresy.

<!-- 3099544 – IS, ASDK --> 
- Opravili jsme problém, ke které došlo při vytváření nového virtuálního počítače (VM) pomocí portálu Azure Stack. Výběr velikosti virtuálního počítače způsobila **USD/měsíc** sloupec, který se zobrazí **není k dispozici** zprávy. Tento sloupec nezobrazuje; zobrazení virtuální počítač cenové sloupec není podporován ve službě Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Opravili jsme problém, ve kterém portálu správce při přístupu k podrobnosti libovolné předplatné uživatele po zavření okna a kliknutím na **poslední**, nenacházely se název předplatného uživatele. Název předplatného uživatele se teď zobrazí.

<!-- 3060156 - IS ASDK --> 
- Opravili jsme problém, správce i uživatele portálech: Kliknutím na nastavení portálu a následným výběrem **odstranit všechna nastavení a privátní řídicí panely** nebude fungovat podle očekávání a se zobrazí oznámení o chybě. Tato možnost teď funguje správně.

<!-- 2930799 - IS ASDK --> 
- Opravili jsme problém na portálech pro správce i uživatele: v části **všechny služby**, asset **plány DDoS protection** byl nesprávně uveden. Není k dispozici ve službě Azure Stack. Seznam byl odebrán.
 
<!--2760466 – IS  ASDK --> 
- Opravili jsme problém, ke které došlo při instalaci nového prostředí Azure Stack, ve kterém výstrahu, která označuje **vyžadována aktivace** nezobrazovala. Nyní správně zobrazuje.

<!--1236441 – IS  ASDK --> 
- Opravili jsme problém, která bránila použití zásady RBAC pro skupinu uživatelů při použití služby AD FS.

<!--3463840 - IS, ASDK --> 
- Opravili jsme problém s infrastruktura zálohování nedaří zálohovat z důvodu serveru nepřístupný soubor z veřejné sítě VIP. Tato oprava infrastruktura zálohování služba přejde zpět k síti infrastruktury veřejných. Pokud se použije nejnovější [Azure Stack oprava hotfix pro 1809](#azure-stack-hotfixes) , která řeší tento problém, 1811 aktualizace nebude provádět žádné další změny. 

<!-- 2967387 – IS, ASDK --> 
- Opravili jsme problém, ve kterém se účet použitý k přihlášení k portálu správce nebo uživatele služby Azure Stack zobrazovat jako **neidentifikovaný uživatel**. Tato zpráva se zobrazí, když účet buď neměl **první** nebo **poslední** zadaný název.   

<!--  2873083 - IS ASDK --> 
- Opravili jsme problém, který pomocí portálu k vytvoření škálovací sady virtuálních počítačů (VMSS) způsobené **velikost instance** rozevírací nabídku, která se nenačetl správně při použití aplikace Internet Explorer. Tento prohlížeč teď funguje správně.  

<!-- 3190553 - IS ASDK -->
- Opravili jsme problém, který vygeneroval hlučného výstrahy indikující, že byla infrastruktury Instance Role není k dispozici nebo uzel jednotek škálování byla ve stavu offline.

## <a name="changes"></a>Změny

- Nový způsob, jak zobrazit a upravit kvóty v plánu je zavedený 1811. Další informace najdete v tématu [zobrazit existující kvótu](azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Vylepšení zabezpečení v této aktualizaci způsobit zvýšení velikosti zálohy služby role adresáře. Aktualizace, změny velikosti pokyny pro umístění externí úložiště, najdete v článku [dokumentace ke službě backup infrastruktury](azure-stack-backup-reference.md#storage-location-sizing). Tato změna má za následek delší dobu pro dokončení zálohování z důvodu větší velikost přenosu dat. Tato změna ovlivní integrované systémy. 

- Přejmenování stávajících období rutina pro načtení klíče pro obnovení Bitlockeru v 1811 z Get-AzsCsvsRecoveryKeys do Get-AzsRecoveryKeys. Další informace o tom, jak načíst klíče pro obnovení Bitlockeru, naleznete v tématu [pokyny, jak k načtení klíčů](azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

Tuto aktualizaci nainstaluje následující aktualizace:  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)


Další informace o těchto ohrožení zabezpečení, klikněte na výše uvedené odkazy nebo najdete v článcích znalostní báze Microsoft [4467684](https://support.microsoft.com/help/4467684).

## <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

- Během instalace 1811 aktualizace, ujistěte se, že jsou všechny výskyty portálu správce během této doby zavře. Portál user portal zůstat otevřené, ale na portálu pro správu musí být uzavřeny.

- Při spuštění [testovací AzureStack](azure-stack-diagnostic-test.md), pokud **AzsInfraRoleSummary** nebo **AzsPortalApiSummary** test se nezdaří, zobrazí se výzva ke spuštění  **Test-AzureStack** s `-Repair` příznak.  Pokud spustíte tento příkaz, selže s následující chybová zpráva:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  Tento problém bude opraven v budoucí verzi.

- Během instalace aktualizace 1811 použijte portál Azure Stack není k dispozici, když konfigurujete hostitele rozšíření. Konfigurace hostitele rozšíření může trvat až 5 hodin. Během této doby můžete zkontrolovat stav příkazu update nebo pokračování instalace aktualizace se nezdařila pomocí [Powershellu Azure Stack správce nebo privileged koncový bod](azure-stack-monitor-update.md). 

- Během instalace aktualizace 1811 řídicímu panelu portálu uživatele nemusí být k dispozici a vlastní nastavení může dojít ke ztrátě. Řídicí panel můžete obnovit výchozí nastavení po dokončení aktualizace, že otevřete nastavení portálu a vyberete **obnovit výchozí nastavení**.

- Při spuštění [testovací AzureStack](azure-stack-diagnostic-test.md), zobrazí se zpráva s upozorněním z řadič správy základní desky (BMC). Toto upozornění můžete ignorovat.

- <!-- 2468613 - IS --> Při instalaci této aktualizace, může se zobrazit upozornění s názvem `Error – Template for FaultType UserAccounts.New is missing.` tato upozornění můžete klidně ignorovat. Výstrahy automaticky zavře po dokončení instalace této aktualizace.

- <!-- 3139614 | IS --> Pokud jste použili aktualizaci do služby Azure Stack od výrobce OEM **k dispozici je aktualizace** oznámení se nemusí zobrazit na portálu Správce služby Azure Stack. Chcete-li nainstalovat služby Microsoft update, stáhnout a naimportovat ručně podle pokynů tady [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).

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

<!-- TBD - IS ASDK --> 
- Odstraňuje se předplatné uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.

<!-- TBD - IS ASDK --> 
- Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack. Jako alternativní řešení použít [Powershellu mohl ověřit oprávnění](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Monitorování stavu a

<!-- 1264761 - IS ASDK -->  
- Může se zobrazit upozornění **stavu řadiče** komponenta, která mají následující podrobnosti:  

    - Upozornění #1:
       - JMÉNO:  Infrastrukturu role není v pořádku
       - ZÁVAŽNOST: Upozornění
       - KOMPONENTY: Stav kontroleru
       - POPIS: Kontroler stavu prezenčního signálu skener není k dispozici. To může mít vliv sestav o stavu a metriky.  

    - Upozornění #2:
       - JMÉNO:  Infrastrukturu role není v pořádku
       - ZÁVAŽNOST: Upozornění
       - KOMPONENTY: Stav kontroleru
       - POPIS: Stav řadiče skener selhání není k dispozici. To může mít vliv sestav o stavu a metriky.

    Obě výstrahy můžete bezpečně ignorovat. Zavře se automaticky v čase.  

### <a name="compute"></a>Compute

- Při vytváření nového Windows virtuální počítač (virtuálního počítače), **nastavení** vyžaduje okně vyberte veřejné příchozí port aby bylo možné pokračovat. V 1811 toto nastavení je povinný, ale nemá žádný vliv. Je to proto závisí na Brána Firewall služby Azure, které není implementované ve službě Azure Stack. Můžete vybrat **žádné veřejné příchozí porty**, nebo některou z možností a pokračujte ve vytváření virtuálních počítačů. Nastavení nebude mít žádný efekt.

<!-- 3235634 – IS, ASDK -->
- K nasazení virtuálních počítačů s velikostí, který obsahuje **v2** přípony; například **Standard_A2_v2**, zadejte příponu jako **Standard_A2_v2** (malá písmena v). Nepoužívejte **Standard_A2_V2** (velká písmena V). To funguje v globální Azure a nekonzistence ve službě Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Při použití [ **přidat AzsPlatformImage** rutiny](/powershell/module/azs.compute.admin/add-azsplatformimage), je nutné použít **- OsUri** parametr jako identifikátor URI, kde je odeslána na disk účtu úložiště. Pokud používáte místní cesta na disku, rutina selže s následující chybou: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Při použití portálu k vytvoření virtuálních počítačů (VM) o velikosti virtuálních počítačů úrovně premium (DS, Ds_v2, služby FS, FSv2) virtuální počítač se vytvoří v účtu úložiště úrovně standard. Vytvoření účtu úložiště úrovně standard neovlivňuje funkčně vstupně-výstupních operací, nebo fakturace. Můžete bezpečně ignorovat upozornění, že: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- Prostředí pro vytváření virtuálního počítače škálovací sady (VMSS) poskytuje založené na CentOS 7.2 jako možnost pro nasazení. Vzhledem k tomu, že obrázek není k dispozici ve službě Azure Stack, vyberte jiný operační systém pro vaše nasazení, nebo pomocí zadání jiné image CentOS, který byl stažen před jejich nasazením na Marketplace pomocí šablony Azure Resource Manageru operátor.  

<!-- 2724873 - IS --> 
- Při použití rutiny Powershellu **Start AzsScaleUnitNode** nebo **Stop-AzsScaleunitNode** ke správě jednotek škálování, první pokus o spuštění nebo zastavení jednotka škálování může selhat. Pokud se rutina nezdaří při prvním spuštění, spusťte rutinu znovu. Druhé spuštění by měl úspěšně dokončit operaci. 

<!-- TBD - IS ASDK --> 
- Pokud zřizování rozšíření na nasazení virtuálního počítače trvá příliš dlouho, dejte zřizování vypršení časového limitu namísto pokusu o zastavení procesu uvolnění nebo odstranění virtuálního počítače.  

<!-- 1662991 IS ASDK --> 
- Diagnostika virtuálních počítačů Linux není podporována ve službě Azure Stack. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky.  

<!-- 2724961- IS ASDK --> 
- Když se zaregistrujete **Microsoft.Insight** poskytovatele prostředků v nastavení předplatného a vytvoření virtuálního počítače s Windows pomocí hostovaného operačního systému diagnostických povolené, procentuální využití procesoru grafu na stránce Přehled virtuálních počítačů nemá zobrazit data metrik.

   Pokud chcete najít data metrik, jako je například grafu procento využití procesoru pro virtuální počítač, přejděte na **metriky** okno a zobrazit všechny podporované Windows virtuálního počítače hosta metriky.

<!-- 3507629 - IS, ASDK --> 
- Spravované disky vytvoří dva nové [compute typy kvót](azure-stack-quota-types.md#compute-quota-types) omezit maximální kapacita spravované disky, které je možné zřídit. Ve výchozím nastavení 2048 GiB přidělených pro každý typ kvóty spravované disky. Však může dojít k následujícím problémům:

   - Vytvořené před aktualizací. 1808 kvót kvóta Managed Disks se zobrazí hodnoty 0 na portálu správce, i když je přiděleno 2048 GiB. Můžete zvýšit nebo snížit hodnotu podle skutečných potřeb a nově nastavené hodnoty kvóty na přepíše výchozí hodnotu 2048 GiB.
   - Při aktualizaci hodnoty kvóty na hodnotu 0, je ekvivalentní výchozí hodnotu 2 048 GB. Jako alternativní řešení nastavte hodnoty kvóty na 1.

<!-- TBD - IS ASDK --> 
- Po použití 1811 aktualizace, může dojít k následujícím problémům při nasazování virtuálních počítačů se spravovanými disky:

   - Pokud předplatné bylo vytvořeno před aktualizací. 1808, nasazení virtuálního počítače se spravovanými disky může selhat s interní chybovou zprávu. Chcete chybu vyřešit, postupujte podle těchto kroků pro každé předplatné:
      1. Portál pro klienty, přejděte na **předplatná** a vyhledejte předplatné. Vyberte **poskytovatelů prostředků**a pak vyberte **Microsoft.Compute**a potom klikněte na tlačítko **přeregistrovat**.
      2. V rámci stejného předplatného, přejděte na **řízení přístupu (IAM)** a ověřte, že **Azure Stack – spravovaný Disk** je uvedena.
   - Pokud jste nakonfigurovali prostředí s více tenanty, nasazování virtuálních počítačů v rámci služby předplacené asociovaná s adresářem hosta může selhat s interní chybovou zprávu. Pokud chcete chybu vyřešit, postupujte podle kroků v [v tomto článku](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) změna konfigurace všech vašich adresářů hosta.

- Virtuální počítač s Ubuntu 18.04 vytvořené pomocí SSH autorizace povolená neumožňuje použití klíčů SSH pro přihlášení. Jako alternativní řešení použít přístup k virtuálním počítačům pro Linuxové rozšíření k implementaci klíče SSH po zřízení, nebo ověřování pomocí hesla.

### <a name="networking"></a>Sítě  

<!-- 1766332 - IS ASDK --> 
- V části **sítě**, vyberete-li **vytvořit bránu VPN** nastavit připojení k síti VPN, **založenou na zásadách** je uveden jako typ sítě VPN. Nevybírejte tuto možnost. Pouze **trasy na základě** možnost je podporovaná ve službě Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack podporuje jediný *bránu místní sítě* na IP adresu. To platí ve všech předplatných tenanta. Po vytvoření první místní síťové brány připojení, následné pokusech o vytvoření prostředku brány místní sítě se stejnou IP adresou odmítají.

<!-- 16309153 - IS ASDK --> 
- Ve virtuální síti, který byl vytvořen s nastavením serveru DNS **automatické**, změna na vlastní server DNS se nezdaří. Aktualizované nastavení nenasdílí do virtuálních počítačů v dané virtuální síti.

<!-- 2529607 - IS ASDK --> 
- Během Azure Stack *tajný klíč otočení*, je období, ve kterém veřejné IP adresy nedostupné přibližně 2 až 5 minut.

<!-- 2664148 - IS ASDK --> 
-   Ve scénářích, kde tenanta je přístup k virtuální počítače s použitím tunelu S2S VPN kterými se mohou setkat scénář, kde pokusy o připojení selhat, pokud místní podsítě se přidal k bráně místní sítě po již vytvoření brány. 

- Na portálu Azure Stack při změně statickou IP adresu pro konfiguraci IP, která je vázána na síťový adaptér připojený k instanci virtuálního počítače, zobrazí se zpráva s oznámením 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Můžete bezpečně ignorovat této zprávy. IP adresa se změní i v případě neprovede restartování instance virtuálního počítače.

- Na portálu na **vlastnosti sítě** je odkaz na okno **efektivní pravidla zabezpečení** pro každý síťový adaptér. Pokud vyberete tento odkaz, otevře se nové okno, které zobrazuje chybovou zprávu `Not Found.` k této chybě dochází, protože Azure Stack zatím nepodporuje **efektivní pravidla zabezpečení**.

- Na portálu, je-li přidat příchozí pravidlo zabezpečení a vyberte **značka služby** jako zdroj, se zobrazí několik možností, jak v **značka zdroje** seznamu, které nejsou k dispozici pro službu Azure Stack. Pouze možnosti, které jsou platné ve službě Azure Stack jsou následující:

    - **Internet**
    - **Virtuální síť**
    - **AzureLoadBalancer**
  
    Další možnosti nejsou podporovány jako zdroj značky ve službě Azure Stack. Obdobně je-li přidat odchozí pravidlo zabezpečení a vyberte **značka služby** jako cíl stejný seznam možností pro **značka zdroje** se zobrazí. Pouze platné možnosti jsou stejné jako v případě **značka zdroje**, jak je popsáno v předchozím seznamu.

- **New-AzureRmIpSecPolicy** rutinu Powershellu nepodporuje nastavení **DHGroup24** pro `DHGroup` parametru.

### <a name="infrastructure-backup"></a>Infrastruktura zálohování

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Jakmile povolíte automatické zálohování, služba Plánovač přejde do stavu zakázáno neočekávaně. Služba zálohování řadiče zjistí, že jsou zakázány automatické zálohování a vyvolat upozornění na portálu správce. Když jsou zakázány automatické zálohování, očekává se toto upozornění. 
    - Příčina: Tento problém je z důvodu chyby ve službě, která vede ke ztrátě konfiguraci plánovače. Tato chyba se nezmění, umístění úložiště, uživatelské jméno, heslo nebo šifrovací klíč.   
    - Náprava: Chcete-li tento problém zmírnit, otevře se okno nastavení zálohování řadiče v poskytovateli prostředků infrastruktury zálohování a vyberte **povolit automatické zálohování**. Nezapomeňte nastavit požadovanou četnost a uchování období.
    - Výskyt: Nízká 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Před vytvořením první funkce Azure v rámci předplatného, zaregistrujte poskytovatele prostředků úložiště.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Stáhnout aktualizaci.

Můžete stáhnout aktualizace balíčku Azure Stack 1811 z [tady](https://aka.ms/azurestackupdatedownload). 

Nasazení Azure Stack v propojených scénářích pouze, pravidelně kontrolovat zabezpečeného koncového bodu a automaticky upozorní, pokud je aktualizace k dispozici pro váš cloud. Další informace najdete v tématu [Správa aktualizací pro Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Další postup

- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  
- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).  
