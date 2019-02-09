---
title: Aktualizace služby Azure Stack 1901 | Dokumentace Microsoftu
description: Další informace o aktualizaci 1901 pro integrované systémy Azure Stack, včetně novinek, známé problémy a kde se stáhnout aktualizaci.
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
ms.date: 02/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.openlocfilehash: cf86fafc1fcb0ffd6513abc9d02da16d1f00f22b
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978920"
---
# <a name="azure-stack-1901-update"></a>Aktualizace služby Azure Stack 1901

*Platí pro: Integrované systémy Azure Stack*

Tento článek popisuje obsah balíčku 1901 aktualizace. Obsahuje vylepšení, opravy a nové funkce pro tuto verzi sady Azure Stack. Tento článek také popisuje známé problémy v této verzi a obsahuje odkaz ke stažení aktualizace. Známé problémy jsou rozděleny do problémy přímo souvisí s proces aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo sestavení aktualizace Azure Stack 1901 je **1.1901.0.95**.

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack vydává opravy hotfix v pravidelných intervalech. Nezapomeňte nainstalovat [nejnovější opravy hotfix Azure Stack](#azure-stack-hotfixes) pro 1811 před aktualizací služby Azure Stack na 1901.

Azure Stack opravy hotfix platí pouze pro integrované systémy Azure Stack; Nepokoušejte se nainstalovat ASDK opravy hotfix.

> [!TIP]  
> Předplatit následující *RSS* nebo *Atom* kanály, držet krok s Azure Stack opravy hotfix:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack opravy hotfix

- **1809**: [KB 4481548 – oprava hotfix Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: K dispozici žádná aktuální hotfix.
- **1901**: K dispozici žádná aktuální hotfix.

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
- Nainstalujte [nejnovější opravy hotfix Azure Stack](#azure-stack-hotfixes) pro 1811 (pokud existuje) před aktualizací na 1901.

- Před instalací této aktualizace, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) s následujícími parametry do ověřte stav služby Azure Stack a vyřešte všechny provozní problémy zjištěné, včetně všech upozornění a chyby. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci:

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

## <a name="new-features"></a>Nové funkce

Tato aktualizace zahrnuje následující nové funkce a vylepšení pro službu Azure Stack:

- Spravované Image na povolení služby Azure Stack můžete vytvořit objekt spravované image zobecněného virtuálního počítače (nespravované i spravovat), která může vytvářet pouze spravovaný disk virtuálních počítačů do budoucna. Další informace najdete v tématu [Azure Stack Managed Disks](user/azure-stack-managed-disk-considerations.md#managed-images).

## <a name="fixed-issues"></a>Oprava potíží

- Opravili jsme problém, ve kterém portálu jsme si ukázali, možnost pro vytvoření brány sítě VPN založené na zásadách, které nejsou podporovány ve službě Azure Stack. Tato možnost byla odebrána z portálu.

<!-- 16523695 – IS, ASDK -->
- Opravili jsme problém, ve kterém po aktualizaci nastavení DNS pro vaši virtuální síť z **DNS pomocí Azure Stack** k **vlastního DNS**, instance se neaktualizovala s novým nastavením.

- <!-- 3235634 – IS, ASDK --> Opravili jsme problém, ve které nasazení virtuální počítače s velikostí, který obsahuje **v2** přípony; například **Standard_A2_v2**, se vyžaduje zadání přípony jako **Standard_A2_v2** () malá písmena v). I s globální Azure, můžete nyní použít **Standard_A2_V2** (velká písmena V).

<!-- 2869209 – IS, ASDK --> 
- Opravili jsme problém při použití [rutiny Add-AzsPlatformImage](/powershell/module/azs.compute.admin/add-azsplatformimage), ve kterém jste museli používat **- OsUri** parametr jako identifikátor URI, kde je odeslána na disk účtu úložiště. Nyní můžete také použít místní cesta k disku.

<!--  2795678 – IS, ASDK --> 
- Opravili jsme problém, který generoval upozornění, když jste použili portál k vytvoření virtuálních počítačů (VM) o velikosti virtuálních počítačů úrovně premium (DS, Ds_v2, služby FS, FSv2). Virtuální počítač byl vytvořen v účtu úložiště úrovně standard. I když to neovlivnila funkčně vstupně-výstupních operací nebo fakturace, chyba byla opravena upozornění.

<!-- 1264761 - IS ASDK -->  
- Opravili jsme problém s **stavu řadiče** komponenta, která se generuje v případě následujících výstrah. Výstrahy může být bezpečně ignorovat:

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


<!-- 3507629 - IS, ASDK --> 
- Opravili jsme problém při nastavování hodnoty kvóty na Managed Disks v části [compute typy kvót](azure-stack-quota-types.md#compute-quota-types) na hodnotu 0, je ekvivalentní výchozí hodnotu 2 048 GB. Nyní je dodržena nulová hodnota kvóty.

<!-- 2724873 - IS --> 
- Opravili jsme problém při použití rutiny Powershellu **Start AzsScaleUnitNode** nebo **Stop-AzsScaleunitNode** ke správě jednotek škálování, ve kterých je první pokus o spuštění nebo zastavení jednotka škálování může selhat.

<!-- 2724961- IS ASDK --> 
- Opravili jsme problém, ve které jste zaregistrovali **Microsoft.Insight** poskytovatele prostředků v nastavení předplatného a vytvoření virtuálního počítače s Windows pomocí hostovaného operačního systému diagnostických povolené, ale nebyly uvedeny grafu procento využití procesoru na stránce Přehled virtuálních počítačů data metrik. Data teď zobrazí správně.

- Opravili jsme problém, ve které běží **Get-AzureStackLog** po spuštění se nezdařilo rutiny **testovací AzureStack** ve stejné relaci privileged koncový bod (období). Teď můžete použít stejné období relace, ve kterém jste spustili **testovací AzureStack**.

<!-- bug 3615401, IS -->
- Oprava potíží s automatické zálohování, kde se služba Plánovač přejde do stavu zakázáno. neočekávaně. 

<!--2850083, IS ASDK -->
- Odebrat **resetování brány** tlačítko na portálu Azure Stack, který došlo k chybě, pokud došlo ke kliknutí na tlačítko. Toto tlačítko slouží žádná funkce ve službě Azure Stack, jak Azure Stack je víceklientská brána spíše než vyhrazené instance virtuálních počítačů pro každého klienta VPN Gateway tak, aby nedošlo k záměně se odebrala. 

<!-- 3209594, IS ASDK -->
- Odebrat **efektivní pravidla zabezpečení** propojit **vlastnosti sítě** jako tato funkce není podporována ve službě Azure Stack. Máte k dispozici odkaz přiřadil dojem, že se tato funkce podporována, ale nebudou fungovat. Ke zmírnění nejasnosti, jsme odebrali odkazu.

<!-- 3139614 | IS -->
- Opravili jsme problém, ve kterém po aktualizaci se použít ke službě Azure Stack od výrobce OEM **k dispozici je aktualizace** oznámení nenacházely se v portálu Správce služby Azure Stack.

## <a name="changes"></a>Změny

<!-- 3083238 IS -->
- Vylepšení zabezpečení v této aktualizaci způsobit zvýšení velikosti zálohy služby role adresáře. Aktualizace, změny velikosti pokyny pro umístění externí úložiště, najdete v článku [dokumentace ke službě backup infrastruktury](azure-stack-backup-reference.md#storage-location-sizing). Tato změna má za následek delší dobu pro dokončení zálohování z důvodu větší velikost přenosu dat. Tato změna ovlivní integrované systémy. 

- Počínaje lednem 2019, můžete nasadit clustery Kubernetes v Active Directory Federated Services (AD FS) zaregistrovaný, propojené služby Azure Stack razítka (přístup k Internetu je vyžadováno). Postupujte podle pokynů [tady](azure-stack-solution-template-kubernetes-cluster-add.md) stáhnout novou položku Kubernetes Marketplace. Postupujte podle pokynů [tady](user/azure-stack-solution-template-kubernetes-adfs.md) k nasazení clusteru Kubernetes. Všimněte si nových parametrů pro označující, jestli je cílový systém přidat nebo službě AD FS zaregistrovaný. Pokud je služba AD FS, je možné zadat parametry služby Key Vault, ve kterých se nasazení certifikát je uložen nová pole.

   Všimněte si, že i s podporou služby AD FS, nasazení clusterů Kubernetes vyžaduje přístup k Internetu.

- Po instalaci aktualizace nebo opravy hotfix do služby Azure Stack, mohou být zavedeny nové funkce, které vyžadují nová oprávnění má být poskytnuta minimálně jedna identita aplikace. Udělení těchto oprávnění vyžaduje oprávnění správce na domovský adresář, a proto jej není možné automaticky. Příklad:

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Je v současnosti rozšíření ve službě Azure Stack, které úspěšně nasadit bez nutnosti explicitně stažení rozšíření prostřednictvím webu marketplace syndikace. Odeberou se následující verze těchto rozšíření. Operátoři Azure stacku musí nyní explicitně syndikovat markeplace Azure Stack tato rozšíření:

   | Type                     | Verze        |
   |--------------------------|----------------|
   | DSC                      | 2.19.0.0       |
   | IaaSAntimalware          | 1.4.0.0        |
   | BGInfo                   | 2.1            |
   | VMAccessAgent            | 2.0            |
   | CustomScriptExtension    | 1.8            |
   | MicrosoftMonitoringAgent | 1.0.10900.0    |
   | IaaSDiagnostics          | 1.10.1.1       |
   | VMAccessForLinux         | 1.4.0.0        |
   | CustomScriptForLinux     | 1.5.2.0        |
   | DockerExtension          | 1.1.1606092330 |
   | JsonADDomainExtension    | 1.3            |
   | OSPatchingForLinux       | 2.3.0.1        |
   | WebRole                  | 4.3000.14.0    |

   Doporučuje se, že při zavádění rozšíření uživatele Azure stacku nastavit `autoUpgradeMinorVersion` k **true**. Příklad:

   ```json
   "type": "Extension",
           "publisher": "ExtensionPublisher",
           "typeHandlerVersion": "1.2",
           "autoUpgradeMinorVersion": "true"
   ```

- Není k dispozici nové posouzení pro přesné plánování kapacity služby Azure Stack. Nastavili jsme omezení celkového počtu virtuálních počítačů, které je možné nasadit v rámci Azure Stack, ujistěte se, že všechny naše interní služby splnění škálování, kdy zákazníci spouštět. Limit je 60 virtuálních počítačů na hostitele, s délkou maximálně 700 pro celý razítko (Pokud je dosaženo 60 za limit hostitele). Další informace najdete v tématu [novou verzi sady capacity planner](http://aka.ms/azstackcapacityplanner).

- Verze rozhraní API výpočtu zvýšil na 2017-12-01.

- Infrastruktura zálohování nyní vyžaduje certifikát s veřejným klíčem (. CER) pro šifrování dat záloh. Podpora klíčů symetrického šifrování je zastaralá, počínaje 1901. Pokud je infrastruktura zálohování je nakonfigurované před aktualizací na 1901, šifrovací klíče zůstanou zachována. Budete mít aspoň 2 více aktualizuje zpětné kompatibility podpory aktualizace nastavení zálohování. Další informace najdete v tématu [osvědčené postupy Azure Stack infrastruktura zálohování](azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Běžnými ohroženími zabezpečení a rizika

Tuto aktualizaci nainstaluje následující aktualizace:  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


Další informace o těchto ohrožení zabezpečení, klikněte na výše uvedené odkazy nebo najdete v článcích znalostní báze Microsoft [4480977](https://support.microsoft.com/en-us/help/4480977).

## <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

- Při spuštění [testovací AzureStack](azure-stack-diagnostic-test.md), pokud **AzsInfraRoleSummary** nebo **AzsPortalApiSummary** test se nezdaří, zobrazí se výzva ke spuštění  **Test-AzureStack** s `-Repair` příznak.  Pokud spustíte tento příkaz, selže s následující chybová zpráva:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

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
- Po použití 1901 aktualizace, může dojít k následujícím problémům při nasazování virtuálních počítačů se spravovanými disky:

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
- Azure Stack nepodporuje připojení více než 4 síťových rozhraní (NIC) k instancím virtuálních počítačů ještě dnes, bez ohledu na velikost instance.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Před vytvořením první funkce Azure v rámci předplatného, zaregistrujte poskytovatele prostředků úložiště.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Můžete stáhnout aktualizace balíčku Azure Stack 1901 z [tady](https://aka.ms/azurestackupdatedownload). 

Nasazení Azure Stack v propojených scénářích pouze, pravidelně kontrolovat zabezpečeného koncového bodu a automaticky upozorní, pokud je aktualizace k dispozici pro váš cloud. Další informace najdete v tématu [Správa aktualizací pro Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Další postup

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  
