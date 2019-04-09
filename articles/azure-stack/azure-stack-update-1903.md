---
title: Aktualizace služby Azure Stack 1903 | Dokumentace Microsoftu
description: Další informace o aktualizaci 1903 pro integrované systémy Azure Stack, včetně novinek, známé problémy a kde se stáhnout aktualizaci.
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
ms.date: 04/05/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/05/2019
ms.openlocfilehash: 218af82d2385632e7e7a0e77060c5deb758d1e83
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057049"
---
# <a name="azure-stack-1903-update"></a>Aktualizace služby Azure Stack 1903

*Týká se Integrované systémy pro službu Azure Stack*

Tento článek popisuje obsah balíčku aktualizace 1903. Obsahuje vylepšení, opravy a nové funkce pro tuto verzi sady Azure Stack. Tento článek také popisuje známé problémy v této verzi a obsahuje odkaz ke stažení aktualizace. Známé problémy jsou rozděleny do problémy přímo souvisí s proces aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo sestavení aktualizace Azure Stack 1903 je **1.1903.0.35**.

> [!IMPORTANT]
> Datová část 1903 nezahrnuje o ASDK verzi.

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack vydává opravy hotfix v pravidelných intervalech. Nezapomeňte nainstalovat [nejnovější opravy hotfix Azure Stack](#azure-stack-hotfixes) pro 1902 před aktualizací služby Azure Stack na 1903.

Azure Stack opravy hotfix platí pouze pro integrované systémy Azure Stack; Nepokoušejte se nainstalovat ASDK opravy hotfix.

> [!TIP]  
> Předplatit následující *RSS* nebo *Atom* kanály, držet krok s Azure Stack opravy hotfix:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack opravy hotfix

- **1902**: [KB 4481548 – oprava hotfix Azure Stack 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="improvements"></a>Vylepšení

- Datová část aktualizace 1903 obsahuje aktualizace komponenty služby Azure Stack, které neobsahují příslušný operační systém hostitele služby Azure Stack. To umožňuje určitá aktualizace zařadit do oboru. V důsledku toho očekávaná doba, jak trvá dokončení aktualizace 1903 menší (asi 16 hodin, ale přesné časy se může lišit). Tento pokles v modulu runtime je určený k aktualizaci 1903 a následné aktualizace mohou obsahovat aktualizace operačního systému, zdání různé běhové moduly. Aktualizace budoucí pokyny, podobně jako očekávaný čas, který aktualizace v závislosti na payload zahrnuta.

- Oprava chyby v síťové služby, které brání změny **časový limit nečinnosti (minuty)** hodnotu **veřejnou IP adresu** z neprojeví. Dříve byly ignorovány změny této hodnotě, tak, aby bez ohledu na všechny provedené změny, bude použita výchozí hodnota 4 minuty. Toto nastavení určuje, kolik minut pro uchování připojení TCP otevřít bez nutnosti spoléhat se na klientských počítačích k odesílání zpráv keep-alive. Všimněte si, že se tato chyba vliv pouze na instance na úrovni veřejné IP adresy, není veřejné IP adresy přiřazené ke službě Vyrovnávání zatížení.

- Vylepšení spolehlivosti modulu pro aktualizace, včetně automatickou nápravu běžných problémů, aby se aktualizace bez přerušení.

- Vylepšení pro zjišťování a náprava podmínek místa místu na disku.

### <a name="secret-management"></a>Správa tajných kódů

- Azure Stack teď podporuje otočení kořenového certifikátu používá certifikáty pro rotaci externích tajných kódů. Další informace najdete [najdete v článku](azure-stack-rotate-secrets.md).

- 1903 obsahuje vylepšení výkonu pro rotaci tajných kódů, které zkrátit dobu, která je potřebná k provedení interní otočení tajného kódu.

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Nainstalujte [nejnovější opravy hotfix Azure Stack](#azure-stack-hotfixes) pro 1902 (pokud existuje) před aktualizací na 1903.

- Ujistěte se, že používáte nejnovější verzi [Plánovač kapacity Azure Stack](https://aka.ms/azstackcapacityplanner) provedete úlohy plánování a velikosti. Nejnovější verze obsahuje opravy chyb a nabízí nové funkce, které se vydávají s každou aktualizaci Azure Stack.

- Před instalací této aktualizace, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) s následující parametr ověřte stav služby Azure Stack a vyřešte všechny provozní problémy zjištěné, včetně všech upozornění a chyby. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci:

    ```PowerShell
   Test-AzureStack -Group UpdateReadiness  
    ```

- Když Azure Stack je spravované nástrojem System Center Operations Manager, nezapomeňte aktualizovat [sady Management Pack pro Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) verzi 1.0.3.11 před použitím 1903.

- Formát balíčku pro aktualizaci služby Azure Stack se změnil z **.bin/.exe/.xml** k **.zip/.xml** od verze 1902. Zákazníci s připojené jednotky škálování služby Azure Stack **k dispozici je aktualizace** zpráva na portálu. Zákazníci, kteří nejsou připojené teď můžete jednoduše stáhnout a naimportovat soubor ZIP se odpovídající soubor XML.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

- Při spuštění [testovací AzureStack](azure-stack-diagnostic-test.md), zobrazí se zpráva s upozorněním z řadič správy základní desky (BMC). Toto upozornění můžete ignorovat.

- <!-- 2468613 - IS --> Při instalaci této aktualizace, může se zobrazit upozornění s názvem **chyba – šablona pro typ FaultType UserAccounts.New chybí.** Tyto výstrahy můžete bezpečně ignorovat. Výstrahy automaticky zavře po dokončení instalace této aktualizace.

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

<!-- Daniel 3/28 -->
- Na portálu user portal, když přejdete do objektu blob v rámci účtu úložiště a zkuste otevřít **zásady přístupu** z navigačního stromu, se následující okno nepodaří načíst.

<!-- Daniel 3/28 -->
- Na portálu user portal, při pokusu nahrát objekt blob pomocí **OAuth(preview)** možnost, úloha se nezdaří s chybovou zprávou. Chcete-li tento problém obejít, nahrát objekt blob pomocí **SAS** možnost.

- Při přihlášení na portálech Azure Stack se může zobrazit oznámení o veřejného webu Azure portal. Tato oznámení můžete klidně ignorovat podle aktuálně nevztahují ke službě Azure Stack (například "1 nová aktualizace - následující aktualizace jsou teď k dispozici: 2019 dubna aktualizace webu Azure portal").

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Při vytváření virtuálního počítače pro nové Windows (VM), může se zobrazit následující chyba:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění dojde k chybě. Chcete-li tento problém obejít, znovu vytvořte účet úložiště se stejným názvem, jako jste použili dříve.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Prostředí pro vytváření Škálovací sady virtuálních počítačů poskytuje založené na CentOS 7.2 jako možnost pro nasazení. Vzhledem k tomu, že obrázek není k dispozici ve službě Azure Stack, vyberte jiný operační systém pro vaše nasazení, nebo pomocí zadání jiné image CentOS, který byl stažen před jejich nasazením na Marketplace pomocí šablony Azure Resource Manageru operátor.  

<!-- TBD - IS ASDK --> 
- Po použití 1903 aktualizace, může dojít k následujícím problémům při nasazování virtuálních počítačů se spravovanými disky:

   - Pokud předplatné bylo vytvořeno před aktualizací. 1808, nasazení virtuálního počítače se spravovanými disky může selhat s interní chybovou zprávu. Chcete chybu vyřešit, postupujte podle těchto kroků pro každé předplatné:
      1. Portál pro klienty, přejděte na **předplatná** a vyhledejte předplatné. Vyberte **poskytovatelů prostředků**a pak vyberte **Microsoft.Compute**a potom klikněte na tlačítko **přeregistrovat**.
      2. V rámci stejného předplatného, přejděte na **řízení přístupu (IAM)** a ověřte, že **Azure Stack – spravovaný Disk** je uvedena.
   - Pokud jste nakonfigurovali prostředí s více tenanty, nasazování virtuálních počítačů v rámci služby předplacené asociovaná s adresářem hosta může selhat s interní chybovou zprávu. Pokud chcete chybu vyřešit, postupujte podle kroků v [v tomto článku](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) změna konfigurace všech vašich adresářů hosta.

- Virtuální počítač s Ubuntu 18.04 vytvořené pomocí SSH autorizace povolená neumožňuje použití klíčů SSH pro přihlášení. Jako alternativní řešení použít přístup k virtuálním počítačům pro Linuxové rozšíření k implementaci klíče SSH po zřízení, nebo ověřování pomocí hesla.

- Azure Stack teď podporuje novější než verze 2.2.20 agentů Windows Azure s Linuxem. Tato podpora byla součástí 1901 a 1902 opravy hotfix a umožňuje zákazníkům udržovat konzistentní linuxové Image mezi Azure a Azure Stack.


- Pokud nemáte hardwaru životního cyklu hostitelů (HLH): před sestavením 1902, museli jste nastavit zásady skupiny **počítače Konfigurace počítače\Nastavení systému Windows\Místní Policies\Security Options** k **Odeslat LM NTLM – Použijte NTLMv2 relace zabezpečení, pokud vyjedná**. Od sestavení 1902 musí necháváme ji jak **není definována** nebo ji nastavte na **pouze odpovědi odeslat NTLMv2** (což je výchozí hodnota). V opačném případě nebude schopný vytvořit vzdálené relace prostředí PowerShell a zobrazí se **přístup byl odepřen** Chyba:

   ```shell
   PS C:\Users\Administrator> $session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint  -Credential $cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the 
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

### <a name="networking"></a>Sítě  

<!-- 3239127 - IS, ASDK -->
- Na portálu Azure Stack při změně statickou IP adresu pro konfiguraci IP, která je vázána na síťový adaptér připojený k instanci virtuálního počítače, zobrazí se zpráva s oznámením 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

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

Můžete stáhnout aktualizace balíčku Azure Stack 1903 z [tady](https://aka.ms/azurestackupdatedownload). 

Nasazení Azure Stack v propojených scénářích pouze, pravidelně kontrolovat zabezpečeného koncového bodu a automaticky upozorní, pokud je aktualizace k dispozici pro váš cloud. Další informace najdete v tématu [Správa aktualizací pro Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Další postup

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  
